---
layout: post
title: Bafici Resolver. El problema.
date: 2014-07-01
description: Como resolver el problema de combinar peliculas
tag:
  - desarrollo
  - python
  - cine
show_meta: true
comments: true
published: true
mathjax: false
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
Algunos hacen sudokus, otros crucigramas, a mí me gusta pensar en soluciones a
problemas. Seguramente una (de?)formación profesional. Uno de esos problemas
que no me deja dormir hace rato, metafóricamente hablando ya que dudo que
exista algo que me complique el sueño, es como resolver la cuestión **"BAFICI"**.
Vamos para atrás con un breve introducción.

Me gusta el cine, yo diría me gusta mucho, para colmo mi mujer comparte conmigo
este gusto (no sé si incluso no es más fanática). Todos los años, desde hace
unos dieciséis se realiza en Buenos Aires un conocido y bastante importante
festival de cine independiente **BAFICI**. Esas cosas buenas que de vez en cuando
transcienden los gobiernos. Una maravilla en todo sentido que alguna vez me
permitió ver a Tom Waits, por ejemplo.

Volviendo al punto, Tanto mi mujer como el que escribe somos "habitúes" del
festival, incluso mucho antes de conocernos, por lo que todos los años se nos
presentan dos grande problemas, que nos insumen mucho tiempo.

El primero, es elegir que películas ver, tarea complicada si las hay, estamos
hablando, tomando como ejemplo la edición 2014, de unas casi 350 producciones
entre cortos, medios y largometrajes, producciones variadas de todas las
latitudes del planeta. Este problema se resuelve con paciencia, tiempo, un poco
de investigación, un poco de criterio y algo de suerte.

Una vez resuelto que cosas ver, surge el segundo problema (el que me quita el
sueño) es como ver las películas elegidas. Acá ya entramos en el "meollo" de la
cuestión, veamos el escenario primero:

* El **BAFICI** presenta todos los años unas x cantidad de películas, digamos unas
  350 por tomar un ejemplo del 2014
* Del total de las películas, generalmente mi universo de films seleccionados 
  es de unos 25
* Cada película puede tener de 1 a n proyecciones, normalmente 3.
* Cada proyección se realiza en distintas salas y por lo general en días y
  horarios también distintos Las salas pueden en algunos casos ubicarse en un 
  mismo complejo (por ej, Village Recoleta) pero también se ubican en lugares 
  variados de la capital (Belgrano, Recoleta, Centro, Caballito, etc) 
* El trasladarse de una sala a otra insume un tiempo que puede ir desde unos 
  pocos minutos hasta más de una hora 
* Si bien solemos tomarnos vacaciones para al menos una de las semanas del 
  festival, hay horarios que descartamos definitivamente, los de la mañana 
  sobre todo.  
* La gente del festival publica una grilla con todas las proyecciones.

Ahora está más claro el problema. Se necesita poder combinar las proyecciones
de los films seleccionados de tal modo que nos permita poder verlos todos (o la
mayor cantidad de estos) de una manera "humanamente" posible. Esto es un
trabajo que hasta ahora venimos haciendo con lápiz y papel, la idea es poder
automatizar este proceso con alguna herramienta informática.

Así planteado, parece un problema "de facultad", esos que te plantean en las
carreras de sistemas, invito a usarlo como tal.  

[bafici]: {{site.baseurl}}/images/2014/bafici_03.jpg
