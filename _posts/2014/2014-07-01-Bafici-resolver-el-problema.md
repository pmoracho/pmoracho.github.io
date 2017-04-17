---
layout: post
title: Bafici Resolver. Pensar el problema.
date: 2014-07-07
description: Como resolver el problema de combinar peliculas
tag:
  - desarrollo
  - python
  - cine
show_meta: true
comments: true
published: true
mathjax: true
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
image:
  feature: 2017/img-feature-004.jpeg
  credit: Patricio Moracho
---

![bafici][bafici]{: style="float:left;margin-right:20px;margin-bottom:10px;"}

Ya hemos estado viendo en entradas anteriores el uso de la fuerza bruta para
resolver las combinaciones de películas que queremos ver durante el BAFICI. Lo
llamo fuerza bruta, simplemente por que la idea inicial era generar todas las
combinaciones y luego verificar todas para ver si cada proyección puede ser
combinable con el resto (en términos de horarios y distancias entre las salas),
de esta manera obtendríamos todas las combinaciones realmente posibles.

Esta aproximación es elegante en el sentido que es simple y tiene la ventaja que
si o si llegaríamos a la mejor combinación (la que nos permita ver la mayor
cantidad de películas seleccionadas posibles), sin embargo es inviable por el
volumen de datos, que se traduce sobre todo en tiempo de proceso. Podría ser
aplicable a no más de 6 o 7 películas, pero más allá lo descartaría.

Con lo cual lo único que nos queda es generar combinaciones de alguna manera y
verificar si cumplen la condición que todas las proyecciones sean combinables
entre si.

## ¿Que alternativas se me ocurren?

1. Al Azar: Es una idea válida, hay que armar un procedimiento que genere una
   combinación cualquiera de $$ N $$ elementos y verificar si es posibles dicha
   combinación. Se repite esto $$ N $$ veces (las que que nos den tiempos de
   proceso razonables). Podríamos perfeccionar esto, para que en cada
   combinación generada, si no es exitosa, se quite alguno de los elementos y
   se vuelva a probar. Al finalizar los $$ N $$ intentos que definimos
   podríamos ver cual o cuales combinaciones resultan las mejores (las que nos
   ofrecen la mayor cantidad de películas).
2. La combinación secuencial por hito: Se arman tantas combinaciones como
   proyecciones de la primer película de la selección, luego se recorre las
   siguientes películas y se trata de combinar las siguientes proyecciones.
   Este método arrojará distintos resultados en función del orden de las
   películas seleccionadas, por lo que una mejora sería agregarle N intentos y
   "randomizando" el orden de los temas seleccionados.
3. El método cronológico: Se arma una lista de todas las proyecciones ordenadas
   cronológicamente. Se selecciona la primer proyección y se intenta combinar
   con la siguiente, si se puede se suma la proyección a la combinación, se
   continua este procedimiento hasta el final. En realidad es una variante de
   la opción 2.
4. El método estadístico: Me resulto  bastante bueno en cuanto a los
   resultados. La idea es armar una lista con parejas de proyecciones que son
   combinables, es decir una lista más manejable de combinaciones de 2
   elementos de un universo de $$ Cantidad de películas deseadas * Cantidad
   total de proyecciones $$. Con esta lista elaboramos una segunda que
   contendrá todas las proyecciones y la cantidad de combinaciones en las que
   aparece. Luego se ordena esta lista para tener primero las proyecciones más
   "combinables".  Utilizando la misma procedemos a recorrerla y a intentar
   combinar. Este método prioriza las proyecciones más "combinables", lo cual
   parece algo más óptimo que el resto de lo métodos.


[bafici]: {{site.baseurl}}/images/2014/bafici_01.jpg
