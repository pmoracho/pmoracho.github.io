---
layout: post
published: true
date: '2019-02-04'
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
title: Ejemplos de gráficos con Ggplot2
description: Ejemplos de gráficos con Ggplot2 y el theme_elegante()
tags:
  - desarrollo
  - R
---

Ejemplos de gráficos con Ggplot2
--------------------------------

### Cómo se ven con theme\_elegante()

Box plot
--------

El Boxplot es probablemente uno de los tipos de gráficos más comunes. Da un buen resumen de una o varias variables numéricas. La línea que divide la caja en 2 partes representa la mediana de los datos. El final de la caja muestra los cuartiles superior e inferior. Las líneas extremas muestran el valor más alto y el más bajo excluyendo los valores atípicos. Tenga en cuenta que Boxplot oculta el número de valores detrás de la variable. Por lo tanto, es muy recomendable imprimir el número de observaciones, añadir una observación única con fluctuaciones o usar un Violinplot si tiene muchas observaciones.

![]({{site.baseurl}}/images/2017/plot_negate_11346db71bfd.png)
![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-1-1.png)
![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-1-2.png)

Violin plot
-----------

Las gráficas de violín permiten visualizar la distribución de una variable numérica para uno o varios grupos. Está muy cerca de una boxplot, pero permite una comprensión más profunda de la densidad. Los violines se adaptan especialmente cuando la cantidad de datos es enorme y resulta imposible mostrar observaciones individuales. Los gráficos de violín son una forma muy conveniente de mostrar los datos y probablemente merezcan más atención en comparación con los gráficos de caja que a veces pueden ocultar características de los datos.


![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-2-1.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-2-2.png) \#\# Scatter plot

Una gráfica de dispersión (scatterplot) muestra el valor de 2 conjuntos de datos en 2 dimensiones. Cada punto representa una observación. La posición en los ejes X (horizontal) e Y (vertical) representa los valores del valor de 2 variables. Es realmente útil estudiar la relación entre ambas variables. Es común proporcionar aún más información usando colores o formas (para mostrar grupos, o una tercera variable). También es posible mapear otra variable al tamaño de cada punto, lo que hace un gráfico de burbujas. Si tiene muchos puntos y lucha con el sobretrazado, considere la posibilidad de usar un gráfico de densidad en 2D.

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-3-1.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-3-2.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-3-3.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-3-4.png)

Connected scatter plot
----------------------

Una gráfica de dispersión conectada está realmente cerca de una gráfica de línea, excepto que cada una de las rupturas en las líneas se muestran usando un punto. También está bastante cerca de la gráfica de dispersión, pero tiene una especificidad: su eje X debe ser ordenado para hacer este tipo de representación. Por lo tanto, las gráficas de dispersión conectadas se utilizan a menudo para series temporales en las que el eje X representa el tiempo. Si desea rellenar el área debajo de la línea, obtendrá un gráfico de área

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-4-1.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-4-2.png)

Density plot
------------

Un gráfico de densidad muestra la distribución de una variable numérica. Sólo toma como entrada un conjunto de valores numéricos. Está muy cerca de un histograma. Tenga en cuenta que es muy recomendable jugar con el argumento de ancho de banda para no perder un patrón específico en los datos. Tenga en cuenta que puede comparar la distribución de varias variables graficándolas en el mismo eje, usando facetas o a través de un gráfico de gozo.

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-5-1.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-5-2.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-5-3.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-5-4.png)

Stacked area chart
------------------

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-6-1.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-6-2.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-6-3.png)

Sreamgraphs
-----------

Un gráfico Stream está muy cerca de un gráfico de área apilada. Muestra la evolución de un valor numérico (eje Y) en función de otro valor numérico (eje X). Esta evolución está representada por varios grupos, todos con un color distinto. A diferencia de un área apilada, no hay ninguna esquina: los bordes son redondeados, lo que da esta agradable impresión de flujo. Los gráficos de streaming son muy útiles cuando se muestran en modo interactivo: resaltar un grupo le da directamente una idea de su evolución. R permite crear fácilmente gráficos streaming gracias a la biblioteca de gráficos streaming.

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-7-1.png)

2D density plot
---------------

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-8-1.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-8-2.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-8-3.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-8-4.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-8-5.png) \#\# Buble plot

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-9-1.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-9-2.png)

Ridgeline plot
--------------

Un gráfico Ridgeline o Joyplot muestra la distribución de un valor numérico para varios grupos. Pueden ser muy útiles para visualizar los cambios en las distribuciones a lo largo del tiempo o del espacio. La distribución puede representarse mediante histogramas o gráficos de densidad, todos alineados a la misma escala horizontal y presentados con un ligero solapamiento. Las gráficas son especialmente interesantes cuando el número de grupos a representar es alto y, por lo tanto, una separación clásica de ventanas requeriría de mucho más espacio. Sin embargo, hay que tener cuidado, ya que está gráfica suele ocultar una parte de los datos, donde se produce el solapamiento. En R, los gráficos de Ridgeline se pueden hacer fácilmente gracias a la librería [ggridges](https://github.com/clauswilke/ggridges) de [Claus Wilke](http://wilkelab.org/), que es una extensión de ggplot2.

source: <https://www.r-graph-gallery.com/ridgeline-plot/>

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-10-1.png)

Correlograma
------------

Un correlograma o matriz de correlación permite analizar la relación entre cada par de variables numéricas de una matriz. La correlación entre cada par de variables se visualiza a través de una gráfica de dispersión, o un símbolo que representa la correlación (burbuja, línea, número...). La diagonal representa la distribución de cada variable, utilizando un histograma o un gráfico de densidad. Esta técnica es ampliamente utilizada para el análisis exploratorio ya que evita hacer cientos de gráficos para observar una matriz.

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-11-1.png)

Line plot
---------

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-12-1.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-12-2.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-12-3.png)

Dendograma
----------

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-13-1.png)

Line plot
---------

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-14-1.png)

Circular bar plot
-----------------

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-15-1.png)

Mosaic Plot
-----------

Para instalarlo: `devtools::install_github("haleyjeppson/ggmosaic")`

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-16-1.png) ![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-17-1.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-17-2.png)

ggTimeSeries
------------

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-18-1.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-18-2.png)

fuentes:

-   <https://www.r-graph-gallery.com>
