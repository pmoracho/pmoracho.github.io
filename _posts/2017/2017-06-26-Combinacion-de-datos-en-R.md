---
layout: post
published: true
date: '2017-06-26'
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
title: Combinación de dataframes en R
description: Operaciones típicas de combinación de datos en R
tags:
  - desarrollo
  - R
image:
  feature: 2017/img-feature-002.jpeg
---

La transición entre `SQL` y `R` puede resultar complicada al principio, pero
teniendo clara algunas pautas vamos a ver que no es tan así. Hoy vamos a
estudiar los mecanismos de combinación de datos, esto en SQL es el ABC, estamos
hablando de la familia de sentencias `JOIN`. En `R` contrariamente a lo que uno
naturalmente pensaría, estas operaciones suelen ser mucho más simples de
realizar. En principio, vamos a asumir que un `[data.frame][dataframe]` en `R` equivale a
una tabla o recordset de datos en SQL, no es tan así pero para este ejemplo nos
alcanza.

Vamos a "jugar" entonces con dos `[data.frame][dataframe]` o tablas: `clientes` y `ventas`, el
primero obviamente representa un conjunto de clientes y el segundo las
eventuales ventas acumuladas de los mismos, los creamos así:

``` R
clientes = data.frame(ClienteId = c(1:4), 
						RazonSocial = c("Cliente 1", "Cliente 2", "Cliente 3", "Cliente 4"))

ventas = data.frame(ClienteId = c(1, 2, 3, 5), 
					Monto = c(110, 50, 60, 90))
```

En definitiva así son ambos:

``` R
> clientes
	ClienteId RazonSocial
1         1   Cliente 1
2         2   Cliente 2
3         3   Cliente 3
4         4   Cliente 4

> ventas
	ClienteId Monto
1         1   110
2         2    50
3         3    60
4         5    90
```

Hay que notar que:

- Puede haber clientes sin ventas, por ejemplo `Cliente 4`
- A modo de ejemplo también tenemos `ventas` que no pertenecen a ningún cliente,
  por ejemplo el registro # 4 que pertenecería a un Cliente 5 inexistente

Veamos ahora las opciones de combinación de datos, en este ejemplo, vamos a
trabajar con una rutina estándar de `R` se trata de
[`merge`](https://stat.ethz.ch/R-manual/R-devel/library/base/html/merge.html)

## inner join

Todos los registros coincidentes entre ambos `[data.frame][dataframe]` por `by="ClienteId"`

``` R
merge(x = clientes, y = ventas, by = "ClienteId")

	ClienteId RazonSocial Monto
1         1   Cliente 1   110
2         2   Cliente 2    50
3         3   Cliente 3    60
```

Notar que Cliente 4 no tiene ventas por eso no aparece, y obviamente las ventas
del cliente inexistente 5 tampoco aparecerán

## Left Join

Un clásico `left join` dónde se muestra todos los registros del `[data.frame][dataframe]`
izquierdo (`x`) y solo los coincidentes por `ClienteId` de la tabla derecha
(`y`)

``` R
df <-merge(x = clientes, y = ventas, by = "ClienteId", all.x = TRUE)
df$Monto[is.na(df$Monto)] <- 0
df
	ClienteId RazonSocial Monto
1         1   Cliente 1   110
2         2   Cliente 2    50
3         3   Cliente 3    60
4         4   Cliente 4    NA 
```

Vemos que el Cliente 4 que no tiene registros en el `[data.frame][dataframe]` `ventas` muestra
la columna de `Monto` como `NA`, en SQL se nos mostraría el valor `NULL`.
Algunas veces nos puede servir así, pero en este ejemplo lo correcto sería
indicar esta columna en 0 de la siguiente forma:

``` R
df$Monto[is.na(df$Monto)] <- 0
df
	ClienteId RazonSocial Monto
1         1   Cliente 1   110
2         2   Cliente 2    50
3         3   Cliente 3    60
4         4   Cliente 4     0
```

## Right join

En este caso mostraremos todos los registros del `[data.frame][dataframe]` derecho (`y =
ventas`) y solo los coincidentes del izquierdo (`x = clientes`) y también
reeplazamos los valores `NA` de las columnas que correspondan.

``` R
df <-merge(x = clientes, y = ventas, by = "ClienteId", all.y = TRUE)
df$RazonSocial[is.na(df$RazonSocial)] <- "Sin Cliente"

	ClienteId RazonSocial Monto
1         1   Cliente 1   110
2         2   Cliente 2    50
3         3   Cliente 3    60
4         5        <NA>    90
```

Modificar el `<NA>` en `RazonSocial` es algo diferente a lo que hicimos con el
monto, debido a que `RazonSocial` se convierte en un `Factor`, debemos primero
transformar la columna en un `character`, reemplazar los `NA` y reconvertirla
nuevamente en `Factor`.

``` R
df$RazonSocial <- as.character(df$RazonSocial)
df$RazonSocial[is.na(df$RazonSocial)] <- "Sin Cliente"
df$RazonSocial <- factor(df$RazonSocial)

	ClienteId RazonSocial Monto
1         1   Cliente 1   110
2         2   Cliente 2    50
3         3   Cliente 3    60
4         5 Sin Cliente    90
```

## Outer Join

Al contrario del inner, el outer va a combinar todos los registros de ambos
dataframes, coincidan por `ClienteId` o no:

``` R
merge(x = clientes, y = ventas, by = "ClienteId", all = TRUE)

	ClienteId RazonSocial Monto
1         1   Cliente 1   110
2         2   Cliente 2    50
3         3   Cliente 3    60
4         4   Cliente 4    NA
5         5        <NA>    90
```

Ahora vemos el Cliente 4 que no tiene ventas y las Ventas del Cliente 5
inexistente. Vemos que las columnas no coincidentes se completan con el valor
`NA`, en SQL se completan con el valor `NULL`.

## Cross Join

O producto cartesiano, cada fila de un `[data.frame][dataframe]` se combina con la fila del otro

``` R
merge(x = clientes, y = ventas, by = NULL)
   ClienteId.x RazonSocial ClienteId.y Monto
1            1   Cliente 1           1   110
2            2   Cliente 2           1   110
3            3   Cliente 3           1   110
4            4   Cliente 4           1   110
5            1   Cliente 1           2    50
6            2   Cliente 2           2    50
7            3   Cliente 3           2    50
8            4   Cliente 4           2    50
9            1   Cliente 1           3    60
10           2   Cliente 2           3    60
11           3   Cliente 3           3    60
12           4   Cliente 4           3    60
13           1   Cliente 1           5    90
14           2   Cliente 2           5    90
15           3   Cliente 3           5    90
16           4   Cliente 4           5    90
```

[dataframe]:https://stat.ethz.ch/R-manual/R-devel/library/base/html/data.frame.html

