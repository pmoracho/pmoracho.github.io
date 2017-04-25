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
title: Algunos de mis "tips" en R
description: Algunos de mis "tips" en R, cosas que fuí aprendiendo
tags:
  - desarrollo
  - R
---
## Merge de dataframes con valores no exactos

El [merge] en R permite justamente mezclar dos [dataframes] agrupando
columnas en la que haya valores coincidentes, sin embargo no permite manejar
un valor de desvío de modo tal que de tolerar ciertas diferencias. Una forma de
resolverlo es "alinear" los valores de uno de los [dataframes] con los del otro y
cuando no supere la "tolerancia" lo que hacemos es igualarlos para que entonces
el merge funcione como esatamos buscando. Algo así:

``` R
usuarios <- c(1,2,3,4)
distạncias <- c(20.005,21.000,23.000,19.009)
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

También es interesante la solución de este problema presentada en
[stackoverflow] mediante el uso del paquete [fuzzyjoin]

``` R
library(fuzzyjoin)

difference_join(df1, df2, by = "distancias", max_dist = 0.001)
#>   usuarios.x distancias.x usuarios.y distancias.y
#> 1          2           21          6           21
#> 2          3           23          7           23

difference_join(df1, df2, by = "distancias", max_dist = 0.1)
#>   usuarios.x distancias.x usuarios.y distancias.y
#> 1          1       20.005          5       20.006
#> 2          2       21.000          6       21.000
#> 3          3       23.000          7       23.000
#> 4          4       19.009          8       19.008


difference_join(df1, df2, by = "distancias", max_dist = Inf)
#>    usuarios.x distancias.x usuarios.y distancias.y
#> 1           1       20.005          5       20.006
#> 2           1       20.005          6       21.000
#> 3           1       20.005          7       23.000
#> 4           1       20.005          8       19.008
#> 5           2       21.000          5       20.006
#> 6           2       21.000          6       21.000
#> 7           2       21.000          7       23.000
#> 8           2       21.000          8       19.008
#> 9           3       23.000          5       20.006
#> 10          3       23.000          6       21.000
#> 11          3       23.000          7       23.000
#> 12          3       23.000          8       19.008
#> 13          4       19.009          5       20.006
#> 14          4       19.009          6       21.000
#> 15          4       19.009          7       23.000
#> 16          4       19.009          8       19.008

```

# Obtener el nombre de cualquier objeto

``` R
prueba <-0
substitute(prueba) ## Retorna "prueba"
```

# Un split inteligente

Por ejemplo, tenemos esta cadena `'Marca: Avon Marca: Imusa Marca: Falabella'`
y queremos quedarnos solo con los nombres de las marcas, en ese caso podemos
hacer lo siguiente:

``` R
s <- 'Marca: Avon Marca: Imusa Marca: Falabella'
result<-sapply(strsplit(s, 'Marca: '), function(e) {trimws(e[e!=""])})  
result
[1,] "Avon"     
[2,] "Imusa"    
[3,] "Falabella"
```


[merge]:https://stat.ethz.ch/R-manual/R-devel/library/base/html/merge.html
[dataframes]:https://stat.ethz.ch/R-manual/R-devel/library/base/html/data.frame.html
[fuzzyjoin]:https://cran.r-project.org/web/packages/fuzzyjoin/fuzzyjoin.pdf
[stackoverflow]:https://es.stackoverflow.com/questions/64480/combinar-dos-data-frames-con-merge-permitiendo-un-peque%C3%B1o-error/64509#64509,

