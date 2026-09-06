---
title: "Patrones inseguros"
author: "Patricio Moracho"
date: 2026-08-30
post_date: 2026-08-30
layout: post
categories: cat
excerpt_separator: <!--more-->
published: true
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
description: "Path traversal"
tags:
  - desarrollo
  - linux
output:
  github_page:
    jekyllthat::jekylldown
  pdf_document: default
  html_document:
    df_print: paged
---

# Path Traversal

Como Beethoven o un Boca-River: un clásico que nunca pasa de moda. Y como
todo clásico, garantiza vergüenza ajena a cualquier desarrollador que se
crea prolijo. La receta se repite siempre igual: una estructura jerárquica
de archivos, la posibilidad de moverse con rutas relativas, y algo ahí
adentro que en teoría no debería tocarse. El ejemplo de manual, el que
todos escribimos al menos una vez en la vida: un intento adorable e inútil
de bloquear el acceso a ciertos archivos con una lista negra.

```python
import os

def read_file(filename):
    # Lista negra de directorios y archivos prohibidos
    blacklist = ['..', '/etc/passwd', '/var/log']

    # Verificar si el nombre del archivo contiene elementos prohibidos
    for item in blacklist:
        if filename.startswith(item):
            raise ValueError("Acceso denegado: el archivo solicitado está en la lista negra.")

    # Leer el contenido del archivo
    with open(filename, 'r') as file:
        content = file.read()

    return content
```

La intención es noble: que el usuario no toque `/etc/passwd` ni `/var/log`.
El problema es que una lista negra solo bloquea lo que se te ocurrió
escribir, y el sistema de archivos tiene más imaginación que vos. Si el
usuario manda `/home/../etc/passwd`, ningún string de la lista negra
matchea, y sin embargo el resultado final —una vez que el sistema operativo
resuelve el `..`— es exactamente `/etc/passwd`. La lista negra revisó el
texto; el filesystem interpretó la ruta. Cuando esas dos cosas no
coinciden, ya perdiste.

## ¿Qué recordar en estos casos?

1. Nunca confiar en la entrada del usuario. 
2. Olvidar la lista negra y usar una lista blanca de directorios y archivos permitidos.
3. Una cadena no es un directorio: Comparar directorios como directorios, no como texto.

```python
import os

def read_file(filename):
    # Lista blanca de directorios permitidos, resuelta de antemano
    whitelist = [os.path.realpath(p) for p in
                 ['/home/user/documents', '/home/user/pictures']]

    user_path = os.path.expanduser('~')
    full_path = os.path.join(user_path, filename)

    # realpath resuelve '..' Y symlinks; abspath solo resuelve '..'
    real_path = os.path.realpath(full_path)

    # commonpath compara componentes de ruta reales, no caracteres
    if not any(os.path.commonpath([real_path, allowed]) == allowed
               for allowed in whitelist):
        raise ValueError("Acceso denegado: el archivo solicitado no está en la lista blanca.")

    with open(real_path, 'r') as file:
        return file.read()
```

Nótese que acá no comparamos strings, comparamos *rutas ya resueltas*. Es la
misma diferencia que hay entre preguntar "¿el nombre empieza con tal
palabra?" y preguntar "¿este archivo vive, de verdad, adentro de esta
carpeta?". Dos detalles que hacen la diferencia:

- **`os.path.realpath` en vez de `os.path.abspath`.** `abspath` normaliza
`..` pero no toca symlinks. Si dentro de `/home/user/documents` hay un
symlink apuntando a `/etc`, `abspath` no lo detecta y la whitelist queda
igual de rota.
- **`os.path.commonpath` en vez de `startswith`.** Compara rutas por
componentes de directorio reales, no por caracteres, así que
`/home/user/documents_LEAKED` deja de colarse.

La opción todavía mejor, cuando el caso de uso lo permite: no dejar que el
usuario decida ningún fragmento del path. Guardar un ID en una base de
datos que mapee a la ruta real del lado del servidor. Cero interpretación
de rutas ingresadas por el usuario, cero superficie de ataque.

## Un poco de historia

El caso de estudio de manual es **CVE-2000-0884**, documentado en el
boletín **MS00-078** de Microsoft (octubre de 2000). IIS 4.0 y 5.0
filtraban `../` en la URL de forma literal, pero no la misma secuencia
codificada en Unicode sobrelargo: `%c0%af` como representación alternativa
de `/`. Una URL tipo `/scripts/..%c0%af..%c0%af../winnt/system32/cmd.exe`
pasaba el filtro sin problema —porque `..%c0%af` no es `../`— y recién
después IIS decodificaba el Unicode y ejecutaba la ruta real, corriendo
`cmd.exe` con los privilegios del usuario anónimo del servidor.

Es el mismísimo error conceptual del `startswith('..')` de más arriba:
validar una representación del input distinta de la que termina
interpretando el sistema. Veintiséis años después, Python en vez de IIS,
mismo cadáver en el placard.

## Referencias

- [CWE-22 — Improper Limitation of a Pathname to a Restricted Directory (MITRE)](https://cwe.mitre.org/data/definitions/22.html)
- [OWASP Path Traversal Prevention Cheat Sheet](https://owasp.org/www-community/attacks/Path_Traversal)
- [Python docs — módulo `os.path`](https://docs.python.org/3/library/os.path.html)
- [Microsoft Security Bulletin MS00-078](https://learn.microsoft.com/en-us/security-updates/securitybulletins/2000/ms00-078)
- [CERT/CC VU#111677](https://www.kb.cert.org/vuls/id/111677)