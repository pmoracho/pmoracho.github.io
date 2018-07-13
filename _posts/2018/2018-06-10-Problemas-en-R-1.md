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

fuente: [Eliminar observaciones que no aparecen en múltiples años en R][2]

## Resolver un sistema de ecuaciones con matrices no cuadradas

Supongamos que tenemos un sistema de ecuaciones lineales como el siguiente:

    -a + b/2             = 0
     a - b   + c/2       = 0
         b/2 + -c  + d   = 0
             + c/2 - d   = 0
     a + b   +  c  + d   = 1

Si lo llevamos a una matriz:

```r
a1 <- rbind(c(-1,0.5,0,0),c(1,-1,0.5,0),c(0,0.5,-1,1),
				c(0,0,0.5,-1),c(1,1,1,1))
a1
     [,1] [,2] [,3] [,4]
[1,]   -1  0.5  0.0    0
[2,]    1 -1.0  0.5    0
[3,]    0  0.5 -1.0    1
[4,]    0  0.0  0.5   -1
[5,]    1  1.0  1.0    1
```

Y el vector de igualación sería:

```r
b1 <- c(0,0,0,0,1)
b1
[1] 0 0 0 0 1
```

Para resolver este sistema, no nos sirve `solve()` ya que su funcionamiento esta
restringido a matrices cuadradas. Pero si podríamos usar [`qr.solve()`][1] de la
siguiente manera:

```r
    qr.solve(a1,b1)
    [1] 0.1666667 0.3333333 0.3333333 0.1666667
```

O si preferimos ver el resultado como fracciones, puedes usar `as.fractions()`
del paquete `MASS`:

```r
library(MASS) 
as.fractions(qr.solve(a1,b1))
[1] 1/6 1/3 1/3 1/6
```

fuente: [¿Cómo resolver un sistema de ecuaciones lineales en R con matriz NO cuadrada?][3]

## Usando [`reshape()`][4] y no morir en el intento

Es cierto, es un poco confuso para usar, en general cada vez que lo necesito,
tengo que volver a leer la ayuda y los ejemplos ( `?reshape`) para acordarme.
Primero preparemos un ejemplo:

```r
df <- read.table(text="Code Espece avreviada 2005 2006 2007 2008 2009
ST stypopodium trascend   15  22.5 19.5  2.5 23.14
AG  anfiroa fragilisim    2.2 12.5 15.7 11.3 12.2", header=T, strip.white = T, stringsAsFactors=F)
colnames(df) <- c('Code','Espece','avreviada','2005','2006','2007','2008','2009')

df
	Code      Espece  avreviada 2005 2006 2007 2008  2009
1   ST stypopodium   trascend 15.0 22.5 19.5  2.5 23.14
2   AG     anfiroa fragilisim  2.2 12.5 15.7 11.3 12.20
```

Claramente está en un formato "ancho" y lo que buscaríamos es pasarlo al formato
"largo", lo que hay que expandir son los valores de los años. La forma más
básica de hacerlo es la siguiente:

```r
reshape(data=df, 
		direction="long", 
		varying=list(4:8), 
		times=names(df)[4:8], 
		timevar="Year", 
		v.names="Valor")
```

Tres parámetros mínimos son suficientes en este caso:

* `data`: Es el `data.frame` a ajustar
* `direction`: `long` o `wide`
* `varying` : Independientemente de lo que diga la ayuda, es una lista con los nombres o lo índices de las columnas que transformaremos de su distribución horizontal a vertical, en nuestro ejemplo son las columnas `2005`, `2006`, `2007`, `2008`, `2009`, se pueden indicar por el número: `list(4:8)` o por nombre `list(names(df)[4:8])`.

Parámetros opcionales:

* `times`: Para indicar que valores vamos a trasladar, si no indicamos nada será un índice, haciendo `times=names(df)[4:8]`trasladamos directamente el nombre de la columna.
* `timevar`, para indicar el nombre de la columna dónde se trasladarán el nombre de cada año.
* `v.names`, para indicar el nombre de la columna dónde se trasladarán los valores

La salida:

           Code      Espece  avreviada Year Valor id
    1.2005   ST stypopodium   trascend 2005 15.00  1
    2.2005   AG     anfiroa fragilisim 2005  2.20  2
    1.2006   ST stypopodium   trascend 2006 22.50  1
    2.2006   AG     anfiroa fragilisim 2006 12.50  2
    1.2007   ST stypopodium   trascend 2007 19.50  1
    2.2007   AG     anfiroa fragilisim 2007 15.70  2
    1.2008   ST stypopodium   trascend 2008  2.50  1
    2.2008   AG     anfiroa fragilisim 2008 11.30  2
    1.2009   ST stypopodium   trascend 2009 23.14  1
    2.2009   AG     anfiroa fragilisim 2009 12.20  2

Los `rowname` y la columna `id` son datos colaterales que genera la función.

fuente: [¿Cómo convertir un data.frame en un formato horizontal a uno
vertical?][5]


  [1]: http://stat.ethz.ch/R-manual/R-devel/library/base/html/qr.html
  [2]: https://es.stackoverflow.com/questions/170891/eliminar-observaciones-que-no-aparecen-en-m%C3%BAltiples-a%C3%B1os-en-r
  [3]: https://es.stackoverflow.com/questions/165852/c%C3%B3mo-resolver-un-sistema-de-ecuaciones-lineales-en-r-con-matriz-no-cuadrada
  [4]: http://stat.ethz.ch/R-manual/R-patched/library/stats/html/reshape.html
  [5]: https://es.stackoverflow.com/questions/170847/c%C3%B3mo-convertir-un-data-frame-en-un-formato-horizontal-a-uno-vertical

