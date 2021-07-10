---
title: "La trampa del punto flotante"
author: "Patricio Moracho"
date: 2020-09-27
post_date: 2020-09-27
layout: post
categories: cat
excerpt_separator: <!--more-->
published: false
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
description: Traducción del capítulos "Falling into the Floating
Point Trap" de "The R Inferno" de Patrick Burns
tags:
  - R
  - desarrollo
output:
  github_page:
    jekyllthat::jekylldown
  pdf_document: default
  html_document:
    df_print: paged
---


Una vez que cruzamos el Aqueronte, llegamos al primer Círculo, hogar de los
virtuosos paganos. Estas son personas que viven en la ignorancia de los Dioses
del punto Flotante. Estos paganos esperan que:

``` r
.1 == .3 / 3
```

sea verdadero. Los virtuosos paganos también esperarán que con:

``` r
seq(0, 1, by=.1) == .3
```

se obtendrá exactamente un valor que sea verdadero. Pero no deberías esperar
que algo como:

``` r
unique(c(.3, .4 - .1, .5 - .2, .6 - .3, .7 - .4))
```

Tenga una longitud de uno.


Escribí mi primer programa a finales de la edad de piedra. La tarea era
programar la ecuación cuadrática. La edad de piedra tardía significa que el
medio de expresión eran las tarjetas perforadas. No hay vuelta atrás en una
máquina de tarjetas perforadas, una vez que los agujeros están ahí, no hay forma
de volver a rellenarlos. Así que un error tipografico al final de una línea
significa que tienes que tirar la tarjeta y empezar la línea de nuevo. Un
procedimiento con el que me he familiarizado mucho.

La alegría se produjo al final de la larga prueba de adquirir un paquete de
tarjetas. Alegría de corta duración. El siguiente paso fue poner la pila de
cartas en un cesta de entrada supervisada por el operador de la computadora.
Unas horas después, el papel (grande) la salida del trabajo estaría en un
casillero. Por supuesto, hubo un error en el programa. Después de otra lucha con
la máquina de tarjetas perforadas (relativamente breve esta vez), la baraja
estaba de vuelta en la canasta de entrada.
