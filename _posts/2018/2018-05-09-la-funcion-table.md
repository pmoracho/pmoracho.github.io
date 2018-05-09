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
title: La función table
tags:
  - desarrollo
  - R
  - table
image:
---

La funcionalidad básica de **R** es enorme, muchas veces la olvidamos por
herramientas más avanzadas (léase [dplyr][dplyr]), sin embargo hay un conjunto
enorme de problemas dónde no es necesario buscar ayuda de otros paquetes. Un
ejemplo muy claro es este problema:

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
                  header=T, sep=',', 
				  stringsAsFactors = F, 
				  quote = '"', 
				  strip.white = T)

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

## [`table()`][table] al rescate

Esta función es muy interesante, según la documentación

> table() utiliza los factores de clasificación cruzada para construir una tabla de
> contingencia de los conteos en cada combinación de niveles de factores.

El primer parámetro es el objeto sobre el cual se armará la tabla de
contingencia, el cual puede uno o más objetos que eventualmente puedan ser
convertidos a u `factor`. Esto implica que casi cualquier cosa puede pasar por
`table()`, desde vectores, matrices o dataframes, solo por nombrar algunos.
Pero otra cosa importante que dice la ayuda, es que acepta múltiples objetos,
por ejemplo, para hacer la tabla de contingencia de un vector:

```r
table(c("A","B","A"))

A B 
2 1 
```

Esto no ofrece mucha dificultad para entender, tenemos dos ocurrencias del
valor `A` y uno del `B`. ¿Y si agregamos una nueva dimensión?

```r
table(c("A","B","A"), c("A","B","A"))

    A B
  A 2 0
  B 0 1
```

Interesante, nos retornó una matriz, en este caso lo que nos dice si sabemos
leer el resultado, es que la combinación `A,A` se repite dos veces que `A,B` y
`B,A` ninguna y que `B,B` apareció una vez. Podemos seguir agregando
"dimensiones" sin problemas, la representación en la consola se hará un poco
más difícil de leer, pero siempre el objeto retornado seguirá siendo de la
clase `table`.

En nuestro problema, la aplicación es bien sencilla:


```r
tbl <- table(dat)
tbl 

dat
   diaz    jara  jenner kennedy  londra   lopez Mckinze  moreno  powell Swanson 
      1       2       2       1       2       1       1       2       1       1 

names(tbl[tbl > 1])

[1] "jara"   "jenner" "londra" "moreno"
```

La representación en consola del objeto `table` nos da una información muy
útil, lo primero que nos dice es el nombre del objeto sobre el cual estamos
aplicando la tabla de consistencia, en este caso `dat` y luego nos da las
frecuencias de cada valor de cada una de las columnas de la matriz original. 

Por último lo que hemos hecho es recuperar los nombres de las columnas de
aquellos valores que se repitieran más de una vez.


[table]: http://stat.ethz.ch/R-manual/R-devel/library/base/html/table.html
[fuente]: https://es.stackoverflow.com/a/162665/31764
[dplyr]: https://dplyr.tidyverse.org/
