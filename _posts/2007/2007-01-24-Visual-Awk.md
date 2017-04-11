---
layout: post
title: Visual AWK
description: Una herramienta visual para trabajr con Awk 
date: 2007-01-24
tag:
  - awk
  - desarrollo
show_meta: true
comments: true
---

**Vawk** es un pequeño utilitario pensado para escribir y probar sencillos
scripts de AWK, tal vez en algún momento llegué a ser un verdadero IDE para
Awk. Supongo que conoces lo que es Awk sino te recomiendo esta
[introducción](http://es.wikipedia.org/wiki/AWK).  Normalmente cuando se estó
trabajando en un script en este leguaje, no importa en que plataforma, se
necesita un editor de texto y una ventana de comando, de esta forma se edita el
script y se lo “testea” invocando al interprete, el script y un input a
procesar. Para optimizar este procedimiento y aprovechando las ganas de
estudiar [Freebasic](http://www.freebasic.net/) es que construí esta
herramienta (lamentablemente solo para Windows). La interfaz es simple, un
campo de texto donde se puede copiar un “Input” de prueba, otro donde podemos
ir escribiendo el script y un tercero donde vemos el resultado luego de
ejecutar el script. No hay mucha ciencia en esto, pero la verdad, al menos a mí
me hizo la vida un poco más simple. 

Algunos comentarios más: 

* El gawk.exe (se puede bajar desde [aquí](http://gnuwin32.sourceforge.net/packages/gawk.htm)) 
 debe estar en el mismo directorio donde ejecutamos el vawk 
* El código fuente no tiene ninguna licencia en particular, pero lo pueden usar para cualquier fin.

[Descargar Vawk](https://www.box.com/s/z7wq79eawvqld7bgxwjr)
