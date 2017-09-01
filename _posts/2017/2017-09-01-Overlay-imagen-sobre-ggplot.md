---
layout: post
published: true
date: '2017-09-01'
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
title: Overlay de imagenes sobre areas en ggplot (R)
description: Un posible forma de sobreimponer una imágen sobre áreas de un gráfico ggplot en R
tags:
  - desarrollo
  - R
  - ggplot
image:
  feature: 2017/img-feature-002.jpeg
---

A raíz de [esta pregunta][pregunta] estuve varios días pensando que solución
encontrarle. Hasta dónde pude investigar [ggplot][ggplot] no ofrece ningún
mecanismo para superponer imagenes sobre areas de un gráfico cualquiera. Existe
sí, la posibilidad de incorporar una imágen como fondo general de un gráfico,
podríamos hacer coincidir una imágen que tenga solo  "dibujo" sobre el área del
`plot` y el resto sea un fondo en blanco, pero esto es muy complejo ya que este
fondo solo aplica a el sector de los ejes y no a todo el "canvas". Tampoco
encontré algún paquete que ofrezca algúna solución al respecto, así que en
definitiva tuve que pensar alguna idea para resolverlo.

Tanto tiempo invertido en tratamiento de imagenes (como hobby) dieron sus
frutos: recordé las opciones de "enmascaramiento" para componer distintas
imagenes en capas. El concepto para resolver este tema es sencillo: tenemos una
imágen principal (`PLOT`), una "máscara" (`MASK`) que es otra imágen
de igual dimensión que tiene un area blanca y otra negra y por último la imágen
a sobreimponer (`AREA`). Haciendo una especie de "sandwich": `PLOT + MASK +
AREA` obtenemos la imágen final, la imagen `MASK`, dependiendo si un pixel es
banco o negro, determina si el pixel final será el de `PLOT` o el de `AREA`.

El problema es siempre: como construir nuestra `MASK`, hacerlo con una
herramienta tipo [gimp][gimp] es relativamente sencillo, pero trabajoso, ya que
debemos ir seleccionado con alguna de las opciones, el area del gráfico orginal
a sobreimponer. 

### ¿Se puede automatizar esto? ¿Cómo?

Claro que se puede, por empezar vamos a usar [ImageMagick][im] para todas las
tareas. En **R** hay un paquete que encapsula las principales funcionalidades
de [ImageMagick][im], se trata de [magick][magick], no tuve tiempo de
profundizar mucho en él, en principio encontré algunas limitaciones, así que
opté por trabajar directamente con la línea de comandos haciendo llamadas a
[system()][system], veamos como:

Imaginemos que tenemos un gráfico de área como este:

![img1][img1]



[pregunta]:ttps://es.stackoverflow.com/questions/95753/composici%C3%B3n-de-imagen-y-gr%C3%A1fico-en-r
[ggplot]:http://ggplot2.org
[gimp]:https://www.gimp.org
[im]:https://www.imagemagick.org
[magick]:https://cran.r-project.org/web/packages/magick/vignettes/intro.html
[system()]:https://stat.ethz.ch/R-manual/R-devel/library/base/html/system.html
[img1]:{{site.baseurl}}/images/2017/plot_11345999580f.png
