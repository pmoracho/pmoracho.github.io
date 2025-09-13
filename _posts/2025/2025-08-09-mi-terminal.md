---
title: "Mi Terminal"
author: "Patricio Moracho"
date: 2025-08-10
post_date: 2025-08-10
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
description: Mi terminal es un espacio donde comparto mis configuraciones y herramientas favoritas para trabajar en la terminal de Linux. Aquí encontrarás desde configuraciones de terminal hasta scripts útiles que he desarrollado a lo largo del tiempo.
tags:
  - linux
  - terminal
output:
  github_page:
    jekyllthat::jekylldown
  pdf_document: default
  html_document:
    df_print: paged
---

# Mi Terminal

## Mis herramientas favoritas

### `kitty`

[kitty](https://sw.kovidgoyal.net/kitty/) es un emulador de terminal de Kovid Goyal el autor de [Calibre](https://calibre-ebook.com/),  se destaca por su rendimiento y características avanzadas:

- Renderizado de texto y gráficos acelerado por GPU.
- Soporte para ligaduras tipográficas y fuentes personalizadas.
- Soporte para múltiples pestañas y ventanas.
- Personalización avanzada a través de archivos de configuración.
- Soporte para múltiples esquemas de color y temas.
- Extensiones y complementos para agregar funcionalidades adicionales.

#### Configuración de `kitty`


#### Atajos de teclado

##### Generales

|Atajo | Acción |
|---|---|
| `ctrl+shift+f2` | Abre el archivo de configuración de `kitty` (lo crea si no existe) |
| `ctrl+alt+m` | Lanza Midnight Commander (mc) |
| `ctrl+shift+f5` | Recarga la configuración de `kitty` |
| `ctrl+shift+u` | Seleccionar caracter Unicode |
| `ctrl+shift+f10` | Alterna el modo maximizado |
| `ctrl+shift+f11` | Alterna el modo pantalla completa |
| `ctrl+shift+n` | lanza una nueva instancia de `kitty` |

##### Pestañas / Tabs

|Atajo | Acción |
|---|---|
| `altr+shift+t` | Abre una nueva pestaña |
| `alt+shift+q` | Cierra la pestaña actual |
| `alt+shift+left` | Cambia a la pestaña anterior |
| `alt+shift+right` | Cambia a la siguiente pestaña |

##### Ventanas

|Atajo | Acción |
|---|---|
| `ctrl+shift+enter` | Abre un ventana nueva |
| `ctrl+shift+right` | Cambia a la siguiente ventana |
| `ctrl+shift+left` | Cambia a la ventana anterior |
| `ctrl+shift+l` | Modifica el "layout" de las ventanas |
| `ctrl+shift+r` | Redimensiona la ventana actual y reajusta el layout |
| `ctrl+shift+1 to 9` | Cambia a la ventana número 1 a 9 |
| `ctrl+shift+f` | Mueve la posición de la ventana actual en el layout hacia adelante |
| `ctrl+shift+b` | Mueve la posición de la ventana actual en el layout hacia atrás |
| `ctrl+shift+f7` | Visualiza los números de ventanas en el layout actual y permite cambiar a una ventana específica presionando su número correspondiente |
| `ctrl+shift+f8` | Alterna la posición de la ventana visualmente |

##### Scrolling

|Atajo | Acción |
|---|---|
| `ctrl+shift+z` | Desplazamiento hacia el comando anterior |
| `ctrl+shift+x` | Desplazamiento hacia el comando siguiente |
| `ctrl+shift+up` | Desplazamiento hacia arriba |
| `ctrl+shift+down` | Desplazamiento hacia abajo |
| `ctrl+shift+pageup` | Desplazamiento hacia arriba una página |
| `ctrl+shift+pagedown` | Desplazamiento hacia abajo una página |
| `ctrl+shift+home` | Desplazamiento al inicio del buffer |
| `ctrl+shift+end` | Desplazamiento al final del buffer |
| `ctrl+shift+h` | Navega mediante `less` |

### BLE - Bash Line Editor

[BLE](github.com/akinomyoga/ble.sh) es un editor de línea de comandos para Bash
que mejora la experiencia de edición y navegación en la terminal. La
funcionalidad más destacada de BLE es sin duda, el autocompletado inteligente e
interactivo al estilo de `fish` o `zsh`, pero con la ventaja de que funciona en
Bash. Cuenta ademas con `syntax highlighting`, `undo/redo`, y una serie de
comandos útiles para mejorar la productividad en la terminal.

#### Comandos útiles
|Comando | Acción |
|---|---|
| `ble-update` | Actualiza BLE a la última versión |
| `ble-bind -l` | Lista los atajos de teclado disponibles |


#### Atajos de teclado


##### Generales

|Atajo | Acción |
|---|---|