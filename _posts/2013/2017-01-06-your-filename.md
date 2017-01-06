---
layout: post
published: true
date: 201-03-01
modified: 201-03-01
imagefeature: sitelogo.png
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
title: ''
description: Cuantos múltiplos de X hay en un rango n..m?
tags:
  - desarrollo
  - sql
---
Parece increíble que a esta altura de mi carrera me este preocupando por esto, pero a raíz de un ejercicio propuesto y pensando en que la solución típica era bastante ineficiente me puse a pensar. Debe haber algo, fórmula o algoritmo mas eficiente que la clásica y querida fuerza bruta.

Veamos el problema:

## Cuantos múltiplos de 3 hay entre 7 y 18988?

Solución típica, recorrer el rango y verificar cada número, en SQL simplemente por que lo tengo más a mano, pero es fácil de entender

``` sql
DECLARE @Minimo INT
DECLARE @Maximo INT
DECLARE @Multiplo INT
DECLARE @I INT
DECLARE @Cant INT

SELECT @Minimo = 7
SELECT @Maximo = 18988
SELECT @Multiplo = 3
SELECT @I = @Minimo
SELECT @Cant = 0

WHILE @I < = @Maximo
BEGIN
   IF @I % @Multiplo = 0
   BEGIN
      SELECT @Cant = @Cant + 1
   END
   SELECT @I = @I + 1
END

-- Resultado final
SELECT @Cant
```

6327 es la cantidad de múltiplos de 3 entre 7 y 18988. Sin embargo este método es ineficiente, recorrer todos los números???!!!, debería haber algún teorema, alguna fórmula que permita calcular esto de manera más rápida. Lamentablemente con las matemáticas tengo una cierta relación de amor-odio, tal vez algún matemático que ande dando vueltas me pueda dar una pista. Encontré un algoritmo que hace una aproximación muy cercana de manera muy simple, en este ejemplo sería hacer: (( 18988-7 ) + 1) / 3 = 6327,33.... , se toma la parte entera del resultado y esa sería la cantidad. El problema es que a veces da bien y a veces uno menos,por ej entre 2 y 3 hay un solo mútiplo de 3, sin embargo esta fórmula nos daría ((3 - 2) + 1) / 3 = 0. Mejorando un poco esto, llegué al siguiente algoritmo

``` sql
DECLARE @Minimo INT
DECLARE @Minimo  INT 
DECLARE @Maximo  INT
DECLARE @Multiplo INT
DECLARE @Cant  INT
DECLARE @PrimerMultiplo INT

SELECT @Minimo  = 7
SELECT @Maximo  = 18988
SELECT @Multiplo = 3


SELECT  @PrimerMultiplo = @Minimo + ( CASE WHEN @Multiplo - ( (@Minimo + @Multiplo) % @Multiplo ) = @Multiplo THEN 0 ELSE @Multiplo - ( (@Minimo + @Multiplo) % @Multiplo ) END )
SELECT  @Cant = CASE WHEN ( @Maximo - @PrimerMultiplo ) >= 0 THEN ( ( @Maximo - @PrimerMultiplo )  / @Multiplo ) + 1 ELSE 0 END

-- Resultado final
SELECT  @Cant
```

Es bastante claro, busco el primer múltiplo a partir del mínimo y divido la diferencia entre éste y el máximo por el múltiplo, en la parte entera estaría el valor buscado, hay un poco más de lógica para evitar una división por 0 cuando en el rango no existen múltiplos, en principio diría que funciona.



**ACTUALIZACIÓN AL 23/04/2013**: Hay que ser bobo, por suerte estos temas siempre me quedan "picando" en la "capocha", la forma más simple entiendo que es así

``` sql
DECLARE @Minimo         INT 
DECLARE @Maximo         INT
DECLARE @Multiplo       INT
DECLARE @Cant           INT

SELECT @Minimo  = 7
SELECT @Maximo  = 18988
SELECT @Multiplo = 3


SELECT  @Cant = CONVERT(INT, ( @Maximo / @Multiplo ) ) - CONVERT(INT, ( (@Minimo - 1) / @Multiplo ) )
-- Resultado final
SELECT  @Cant
```