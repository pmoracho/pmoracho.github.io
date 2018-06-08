---
layout: post
published: true
date: '2018-06-08'
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
title: Problemas resueltos en R (I)
description: Algunos problemas y como resolverlos mediante R
tags:
  - desarrollo
  - R
---

## Seleccionar filas segun resultados de una agregación

Suponiendo que tengamos un `data.frame` o similar como el siguiente:

```r
df <- data.frame(year=c(2010, 2010, 2011, 2012, 2012, 2010, 2010, 
						2011, 2010, 2011, 2011, 2012), 
                 colegio=c(rep("A",5),rep("B",3),rep("C",4)),
                 alumno=c(1,2,1,1,2,1,2,1,1,2,2,1))
df

   year colegio alumno
1  2010       A      1
2  2010       A      2
3  2011       A      1
4  2012       A      1
5  2012       A      2
6  2010       B      1
7  2010       B      2
8  2011       B      1
9  2010       C      1
10 2011       C      2
11 2011       C      2
12 2012       C      1
```

Y lo que buscamos es filtrar aquellas filas cuyos colegios tengan observaciones
en los tres años. Para esto lo primero es hacer una agregación por colegio y
contar la cantidad de años distintos que tiene cada uno:

```r
colegio <- aggregate(year ~ colegio, df, function(x) {length(unique(x))})
colegio

  colegio year
1       A    3
2       B    2
3       C    3
```

El uso de `aggregate()` no ofrece mucha dificultad, lo unico particular es la
función de agregación que hemos definido, nos quedamos con los valores únicos de
cada año mediante `unique()` y luego sencillamente conatmos cuanto son mediante
`length()`

Ahora simplemente nos quedamos con las filas cuyos colegios tengan una cantidad
en `year` igual al valor maximo de esta columna:

```r
df[df$colegio %in% as.character(colegio[colegio$year == max(colegio$year),1]),]

  year colegio alumno
1  2010       A      1
2  2010       A      2
3  2011       A      1
4  2012       A      1
5  2012       A      2
9  2010       C      1
10 2011       C      2
11 2011       C      2
12 2012       C      1
```

En caso de usar `dplyr`, la mecanica es similar, aunque notablemente más clara:


```r
library(dplyr)
df %>% 
    inner_join(df %>%
                   group_by(colegio) %>%
                   summarize(cant = n_distinct(year)) %>%
                   filter(cant == max(cant))
    )
```

fuente: [Eliminar observaciones que no aparecen en múltiples años en R](https://es.stackoverflow.com/questions/170891/eliminar-observaciones-que-no-aparecen-en-m%C3%BAltiples-a%C3%B1os-en-r)

## Resolver un sistema de ecuaciones con matrices no cuadradas

En tu caso estás tratando de resolver el siguiente sistema:

    -a + b/2             = 0
     a - b   + c/2       = 0
         b/2 + -c  + d   = 0
             + c/2 - d   = 0
     a + b   +  c  + d   = 1

Si lo llevamos a una matriz:

    > a1 <- rbind(c(-1,0.5,0,0),c(1,-1,0.5,0),c(0,0.5,-1,1),c(0,0,0.5,-1),c(1,1,1,1))
    > a1
         [,1] [,2] [,3] [,4]
    [1,]   -1  0.5  0.0    0
    [2,]    1 -1.0  0.5    0
    [3,]    0  0.5 -1.0    1
    [4,]    0  0.0  0.5   -1
    [5,]    1  1.0  1.0    1

Y el vector de igualación es:

    > b1 <- c(0,0,0,0,1)
    > b1
    [1] 0 0 0 0 1

Para resolver este sistema, ya has confirmado que `solve()` no sirve ya que su funcionamiento esta restringido a matrices cuadradas. Lo que puedes usar es [`qr.solve()`][1] de la siguiente manera:

    > qr.solve(a1,b1)
    [1] 0.1666667 0.3333333 0.3333333 0.1666667

O si prefieres ver el resultado como fracciones, puedes usar `as.fractions()` del paquete `MASS`:

    > library(MASS) 
    > as.fractions(qr.solve(a1,b1))
    [1] 1/6 1/3 1/3 1/6


  [1]: http://stat.ethz.ch/R-manual/R-devel/library/base/html/qr.html
