---
layout: post
title: Bafici Resolver. Pensar el problema.
date: 2014-07-03
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

Continuando con [esta](Bafici-resolver-el-problema) serie
dedicada al "problema BAFICI" y pensando un poco más de forma más abstracta, o
más bien, si en realidad no lo pensamos demasiado, podemos verlo como un simple
tema de combinaciones. O en relación con la [Teoría de la complejidad
computacional](http://es.wikipedia.org/wiki/Teor%C3%ADa_de_la_complejidad_computacional),
podríamos hablar de un problema de tipo
[NP-C](http://es.wikipedia.org/wiki/NP-completo). Lo cierto que en este momento
no tengo ningún algoritmo que pueda resolver este problema en tiempo tiempo
polinómico ([P](http://es.wikipedia.org/wiki/P_(clase_de_complejidad))).
**Nota**: De hecho si lo tuviera, sería bastante probable que estuviera panza
arriba en una playa del caribe, o mirando crecer las margaritas desde abajo.

Primero que nada, que es un problema P, NP o NP-C en las ciencias
computacionales y en criollo? Solo para para poder seguir con esto y de una
manera no muy académica: Un problema P es un problema que puede resolverse en
tiempos y con recursos "razonables", los problemas NP son aquellos en la que la
solución del problema es la que puede verificarse en tiempos y recursos
"razonables". Un dato importante, un problema P es un subconjunto de NP, sin
embargo no hay certeza que todos los NP sean P ni que no lo sean, de hecho,
esto es el "santo grial" de las ciencias computacionales.

Por ejemplo:

* Ordenar una lista de elementos es un problema de tipo P, porque existen
algoritmos que resuelven esto de manera precisa y matemáticamente certera en
tiempos más que razonables. Pero, asimismo es un problema NP, por que dada una
solución es posible verificar eficientemente si está es válida (por ej.
recorriendo la lista y viendo que cada elemento sea mayor al anterior).

* Averiguar si en una lista de enteros hay un subconjunto de estos cuya suma de
0, es un clásico problema NP-C, no hay forma de resolverlo sin pasar por combinar
todos los elementos y verificar cada caso, o sea tiempos y recursos
exponenciales con relación al tamaño de la lista (claramente no es un problema
P), sin embargo dado un conjunto es muy fácil saber si es una solución válida
en un tiempo razonable (NP).

## Cómo resolvemos entonces un problema NP como el del BAFICI?

La solución obvia: aplicando fuerza bruta. Es decir, podríamos generar todas
las posibles combinaciones y verificar que cada una de las mismas sea correcta,
para lo cual se deberían dar las siguientes condiciones:

1. Todas las proyecciones de cada combinación deberían ser de distintas
   películas. Simplemente por que no queremos repetir películas.
2. Ninguna de las proyecciones deberían solaparse, y además entre el inicio de
   una y el final de otra debería existir suficiente tiempo para trasladarse de
   una sala a otra.

Nota: se pueden seguir agregando condiciones, como ser:

* Quiero ver películas pero recién a partir de las 15 horas
* Quiero no ver más de 3 películas por día
* No quiero cambiar de barrio
* Etc, etc.

A los efectos del problema y de la solución por fuerza bruta, da lo mismo la
cantidad de condiciones que hace que dos proyecciones sean "combinables",
siempre habrá que generar primero todas las combinaciones de proyecciones y
luego recorrerlas  para ir descartando aquellas que no cumplan con las
condiciones. Al final nos quedarán las válidas. 

Aclaración importante: tampoco sabemos a priori si todas las películas que hemos
elegido se pueden ver, es decir que puedan ser combinables entre sí.

Ahora bien, cuando entran a jugar las combinaciones, entran los grandes
volúmenes de datos y de tiempos de proceso. Pensemos un poco esta solución en
términos matemáticos y con un ejemplo puntual:

* Dijimos que en la edición 2014 había unas 350 películas, este número no
  importa tanto, porque el que verdaderamente importa es el de las películas
  seleccionadas, digamos que queremos ver en total unas 25
* Cada película tiene 3 proyecciones, o sea un conjunto de 75 posibles
  elementos (25 x 3)

La formula para calcular las combinaciones de 25 (r) elementos  sobre un
universo de 75 (n) es: 

$$ \frac{n!}{r!*(n-r)!} $$

Esto se lee: el factorial de $$ n $$, dividido el factorial de $$ r $$ por el factorial
de $$ n $$ menos $$ r $$. Viene muy bien
[este](http://www.disfrutalasmatematicas.com/combinatoria/combinaciones-permutaciones-calculadora.html)
enlace para el calculo. Con los número que manejamos en el ejemplo nos daría
algo así:

$$ 5.25885471411489e+19 $$

En "criollo" y si no me equivoco, sería: 52.588.547.141.148.900.000, ni
siquiera sé como se lee un número de semejante magnitud. Pensar solo en generar
esta cantidad de combinaciones me da vértigo, ni me imagino recorrer luego cada
una para verificar si es válida.

Pero además hay una complejidad extra, recuerdan que dije que es posible que
las 25 películas podrían no ser "combinables"? Si esto ocurriera, bueno me
gustaría al menos ver 24, y si esta cantidad no fuera posible ver 23, y así...
O sea que agotado una cantidad de casos se debería volver a intentar la misma
fórmula, pero con 24 (r) elementos sobre el mismo universo de 75 (n), o se

$$ 2.57786995789946e+19 $$


Y así seguimos hasta encontrar la solución, no continuo con los ejemplos por
que me parece que se entiende la idea.

## Conclusión:

La solución obvia (fuerza bruta) es la mejor, por que nos da no solo una sino
todas las soluciones posibles, sin embargo es absolutamente imposible aplicarla
por que el tiempo para el cálculo crece de manera exponencial al tamaño del
problema (cantidad de películas).

Tengan paciencia, esto no termina todavía...

[bafici]: {{site.baseurl}}/images/2014/bafici_01.jpg
