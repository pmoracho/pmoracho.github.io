---
layout: post
published: true
date: '2017-04-22'
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
title: Algunos de mis de R
description: Algunos de mis "tips" en R, cosas que fuí aprendiendo
tags:
  - desarrollo
  - R
---
## Merge de dataframes con valores no exactos

El [merge](merge) en R permite justamente mezclar dos dataframes agrupando
columnas en la que haya valkores coincidentes, sin embargo no permite manejar
un valor de desvío de modo tal que de tolerar ciertas diferencias. Una forma de
resolverlo es "alinear" los valores de uno de los dataframe con los del otro y
cuando no supere la "tolerancia" lo que hacemos es igualarlos para que entonces
el merge funcione como esatamos buscando. Algo así:

``` R
usuarios <- c(1,2,3,4)
distancias <- c(20.005,21.000,23.000,19.009)
df1 <- data.frame(usuarios,distancias)

usuarios <- c(5,6,7,8)
distancias <- c(20.006,21.000,23.000,19.008)
df2 <- data.frame(usuarios,distancias)

df1$distancias <- sapply(df1$distancias, function(x)
{
    # El elemento de df1 coincide con tolerancia con el de df2 ?
    ifelse(min(abs(df2$distancias - x), na.rm=TRUE) < 0.001 * x,
           # Si, entonces reemplazo el elemento de df1 por el de df2 para que el merge funcione
           df2[which.min(abs(df2$distancias - x)),"distancias"], 0)
})
df3 <- merge(df1, df2, by = "distancias")
```
El resultado sería algo así:

```
 distancias usuarios.x usuarios.y
1     19.008          4          8
2     20.006          1          5
3     21.000          2          6
4     23.000          3          7
```


[merge]:https://stat.ethz.ch/R-manual/R-devel/library/base/html/merge.html
