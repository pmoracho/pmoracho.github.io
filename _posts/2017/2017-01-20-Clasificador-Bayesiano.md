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
* Y que el porcentaje de la población con fiebre es del 10% **(C)** 


Tenemos ahora un paciente que acaba de llegar a nuestra "clínica", tiene
temperatura, ¿podemos determinar la probabilidad de que tenga gripe?. Si, de la
siguiente forma:

* **(A)** = 0.9
* **(B)** = 0.05
* **(C)** = 0.1


P(enfermedad|síntoma) = P(sintoma|enfermedad)*P(enfermedad)/P(síntoma) = 0.9 * 0.05 / 0.1 = 0.45 

Es decir la existencia de temperatura en un paciente nos da una probabilidad de
un 45% que la misma se deba a una gripe.

Ahora bien, si hacemos esto mismo, con otros síntomas detectados y combinamos
la probabilidad que estos sean efectivamente síntomas de una enfermedad lo que
estamos construyendo es un "Clasificador Bayesiano Ingenuo"

