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
df <- data.frame(year=c(2010, 2010, 2011, 2012, 2012, 2010, 2010, 2011, 2010, 2011, 2011, 2012), 
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
Y ahora simplemente nos quedamos con los colegios cuya cantidad en `year` sea
igual al valor maximo de esta columna:

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

fuente: [Eliminar observaciones que no aparecen en múltiples años en R](https://es.stackoverflow.com/questions/170891/eliminar-observaciones-que-no-aparecen-en-m%C3%BAltiples-a%C3%B1os-en-r)


