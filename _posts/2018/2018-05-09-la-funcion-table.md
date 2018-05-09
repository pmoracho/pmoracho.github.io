---
layout: post
published: true
date: '2018-09-05'
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
title: La función `table()`
tags:
  - desarrollo
  - R
  - table
image:
---

La funcionalidad básica de **R** es enorme, muchas veces la olvidamos por
herramientas más avanzadas (léase dplyr), sin embargo hay un conjunto enorme de
problemas dónde no es necesario buscar ayuda de otros paquetes. Un ejemplo muy
claro es este problema:

Tenemos una matriz como la siguiente:

```r
dat <- read.table(text='N1, N2
                        "jara",   "moreno" 
                        "moreno",  "lopez"  
                        "diaz",    "Swanson"
                        "powell",  "jara"   
                        "Mckinze", "jenner" 
                        "jenner",  "londra" 
                        "londra",  "kennedy"', 
                  header=T, sep=',', stringsAsFactors = F, quote = '"', strip.white = T)

dat <- as.matrix(dat)
colnames(dat) <- NULL
dat

     [,1]      [,2]     
[1,] "jara"    "moreno" 
[2,] "moreno"  "lopez"  
[3,] "diaz"    "Swanson"
[4,] "powell"  "jara"   
[5,] "Mckinze" "jenner" 
[6,] "jenner"  "londra" 
[7,] "londra"  "kennedy"
```

Supongamos que queremos encontrar los nombres que se repiten en ambas columnas,
pero además tenemos una cantidad enorme de columnas dónde buscar estas
repeticiones, esto lo digo por que la forma natural de hacer esto sería
`dat[dat[,1] %in% dat[,2], 1]`, pero claro esto solo nos dice los valores de la
columna 1 que se repiten en la 2, debiéramos hacer el control inverso, los
valores de la columna 2 que se repiten en la 1 y así con cada una de las `n`
variables/columnas que tengamos. Impracticable.

### [`table()`][table] al rescate

Esta función es muy interesante, según la documentación

	table() utiliza los factores de clasificación cruzada para construir una tabla de
	contingencia de los conteos en cada combinación de niveles de factores.

El primer parámetro es el objeto sobre el cual se armará la tabla de
contingencia, el cual puede uno o más objetos que eventualmente puedan ser
convertidos a u `factor`. Esto implica que casi cualquier cosa puede pasar por
`table()`, desde vectores, matrices o dataframes, solo por nombrar algunos.


[table]: http://stat.ethz.ch/R-manual/R-devel/library/base/html/table.html
[fuente]: https://es.stackoverflow.com/a/162665/31764

