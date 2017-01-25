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
Sabemos que el 90% de los pacientes con gripe tienen fiebre, por lo que la
relación causa/efecto en este caso es del 90%, sin embargo ¿podemos calcular lo
inverso, es decir, la probabilidad que la fiebre nos este indicando la
existencia de la gripe (efecto/causa)? Para esto necesitamos más información,
por un lado el total de los casos de pacientes con gripe y el total del los
casos de pacientes con fiebre.

Vamos a dibujar unos números a modo de ejemplo. Partimos de un conjunto amplio de pacientes:

* Se sabe que el 90% de el conjunto de pacientes con gripe tienen fiebre **(A)** 
* Se sabe que del universo total de pacientes, el 5% sufren de gripe **(B)** 
* Y que del universo total hay un 10% con fiebre (algunos por gripe y otros por alguna otra causa) **(C)** 


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
números, lo que estamos construyendo es un **"Clasificador Bayesiano
Ingenuo"**, que podría llegar a indicar con bastante precisión a que enfermedad
correspondería una serie de síntomas relevados en el paciente. De hecho esto es
en definitiva algo bastante cercano a lo que hace un médico clínico todo el
tiempo, "pesar" mentalmente cada síntoma y llegar al diagnóstico más probable.

	**Nota**: Lo de "ingenuo", se debe a que este tipo de algoritmo considera
	cada "síntoma" de manera independiente, no contemplando las relaciones que
	pudieran existir entre ellos, esto es sin duda una debilidad para ciertas
	aplicaciones aún así, este clasificador es sumamente óptimo para muchas
	otras.

Un hecho importante y positivo de este tipo de clasificador, es que el "peso" o
"probabilidad" de una variable	en la clasificación puede ser muy importante en
relación a la causa pero ser además muy común en otras, con lo cual el "peso"
final disminuye.  Pensemos este mismo ejemplo, pero suponiendo que el valor
**C** en vez de ser 10% es de un 20% (un 20% del universo total tiene fiebre
actualmente), la formula final quedaría así:

P(enfermedad/síntoma) = P(sintoma/enfermedad)*P(enfermedad)/P(síntoma) = 0.9 * 0.05 / **0.2** = 0.225

Es decir el peso de la "fiebre" para clasificar un enfermedad como gripe habría
bajado al 22.5%. Lo que finalmente hace un clasificador de este tipo es "sumar"
los pesos de cada síntoma con relación a cada enfermedad que participe en
nuestro cálculo y llegar a un "score" final, y las "enfermedades" que
tengan el valor más alto son las que mayor probabilidad tienen de ser con
respecto a los síntomas.

Planteado de esta manera parece ser un "algoritmo" sencillo, y en la realidad lo
es. Implementarlo en cualquier lenguaje es algo bastante "trivial", lo notable
es que a pesar de su "simplicidad" es tremendamente efectivo.
