---
layout: post
title: Clasificador Bayesiano
date: 2017-01-20
description: Clasificador Bayesiano
tag:
  - nltk
  - desarrollo
  - python
  - ia
show_meta: true
comments: true
published: true
imagefeature: sitelogo.png
mathjax: true
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
---

### $$ \mathrm{classify}(f_1,\dots,f_n) = \underset{c}{\operatorname{argmax}} \ p(C=c) \displaystyle\prod_{i=1}^n p(F_i=f_i\vert C=c). $$

O en español "Clasificador Bayesiano ingenuo", sugún la
[wikipedia](https://es.wikipedia.org/wiki/Clasificador_bayesiano_ingenuo), se
trata de un clasificador probabilístico fundamentado en el teorema de
[Bayes](https://es.wikipedia.org/wiki/Teorema_de_Bayes), el cual expresa en
términos poco matemáticos, la probabilidad de que se de un evento **A**
sabiendo que ha ocurrido un evento **B** (probabilidad condicionada), o lo que
sería la causalidad de **B** sobre **A**.  

Un ejemplo que se suele usar para explicar este teorema es el siguiente: 

* Se sabe que el 90% de los enfermos de gripe tienen fiebre **(A)** 
* Se sabe que estadísticamente el porcentaje de personas que actualmente tienen gripe es de un 5% **(B)** 
* Y que el porcentaje de la población con fiebre (independientemente de la causa) es del 10% **(C)** 


Tenemos ahora un paciente que acaba de llegar a nuestra "clínica", tiene
temperatura, ¿podemos determinar la probabilidad de que tenga gripe?. Si, de la
siguiente forma:

* **(A)** = 0.9
* **(B)** = 0.05
* **(C)** = 0.1


P(enfermedad/síntoma) = P(sintoma/enfermedad)*P(enfermedad)/P(síntoma) = 0.9 * 0.05 / 0.1 = 0.45 

Es decir la existencia de temperatura en un paciente nos da una probabilidad de
un 45% que la misma se deba a una gripe.

Ahora bien, si hacemos esto mismo, con otros síntomas y calculamos la
probabilidad que estos sean efectivamente síntomas de una enfermedad y lo
hacemos además para un conjunto amplio de enfermedades y combinamos todos estos
números, lo que estamos construyendo es un **"Clasificador Bayesiano Ingenuo"**
que nos puede llegar a indicar con bastante precisión a que enfermedad
correspondería una serie de síntomas relevados con el paciente. De hecho esto
es en definitiva lo que hace un médico clínico todo el tiempo", "pesar"
mentalmente cada síntoma y llegar a un diagnóstico más probable.

En este "modelo", hay síntomas que se repiten en otras enfermedades, aumenta
nuestro valor **C**, por lo cual bajará su peso en la clasificación. Pensemos
este mismo ejemplo, pero suponiendo que el valor **C** en vez de ser 10% es de
un 20% (un 20% de la población tiene fiebre actualmente), la formula final
quedaría así:

P(enfermedad/síntoma) = P(sintoma/enfermedad)*P(enfermedad)/P(síntoma) = 0.9 * 0.05 / **0.2** = 0.225

Es decir el peso de la "fiebre" para clasificar un enfermedad como gripe habría
bajado al 22.5%. Lo que hace un clasificador de este tipo es "sumar" los pesos
de cada síntoma con relación a cada enfermedad que participe en nuestro cálculo
y llegar a un "score" final para cada enfermedad, y las que tengan el valor más
alto son las que mayor probabilidad tienen de ser con respecto a los síntomas.

