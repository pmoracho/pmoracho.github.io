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
title: Evaluación No Estándar en R
description: ¿Que es la evaluación de expresiones en R? la forma estándar y particularmente la No estándar.
tags:
  - desarrollo
  - R
image:
  feature: 2018/img-feature-002.jpeg
---

Hacía rato que no publicaba ni escribía nada y me dieron ganas. Nada mejor que
hacerlo sobre un tema que me sorprendió mucho al terminar de entenderlo (algo
al menos), como es la evaluación de expresiones en R. Digo que me sorprendió,
por que entender como R evalúa las expresiones y la forma en que podemos, como
programadores, "intervenir" en este proceso, es darse cuenta que es posible (en
R) modificar directamente la forma "estándar" y "natural" de evaluación. Esto
es algo único, al menos para mí y mi limitado conocimiento en lenguajes. Como
programador, darse cuenta de esto, es como un "cross a la mandíbula" (Arlt
dixit), un golpe inmediato al sentido común, formado por años de trabajo en
tantos otros lenguajes: C, C++, Java, Basic, Python, SQL, etc. 

Para empezar, digamos que todos estos lenguajes (también R) evalúan lo que
ingresamos por teclado de una forma prácticamente idéntica, la llamaremos
evaluación estándar, pero en R en particular se puede llegar a modificar esto,
de una forma muy útil a la que llamaremos evaluación No estándar.

Es sin duda un tópico avanzado, posiblemente nunca te encuentres con necesidad
de aplicarlo, pero estoy seguro, que si has escrito algo de código en R, ya lo
has usado. Tal vez no te haya llamado la atención cosas como estas:


```r
library(tidyverse)

mtcars %>% group_by(cyl)

subset(ToothGrowth, supp == "VC")

ggplot(df, aes(x=Y2003, y=Y2016)) + geom_point()

```

Son cuatro expresiones bastante ordinarias, sin embargo todas tiene la
particularidad que están haciendo uso de la evaluación No estándar, a partir de
ahora la llamaremos **ENS** en contraste con la estándar que llamaremos **ES**.

## ¿Qué es una evaluación?

Si nos guiamos por lo que dice la RAE, sería _"Acción de estimar, apreciar o
calcular el valor de algo"_, nada mal como definición, pero para ser más
justos, en el caso de cualquier lenguaje de programación, podríamos decir que
la evaluación es el proceso de interpretación de una o mas sentencias
ingresadas por el usuario. No importa si el lenguaje es "interpretado" como R o
"compilado" como C, existe en cada caso un proceso que interpreta el código
escrito en dicho lenguaje. Este proceso de evaluación se compone de múltiples
subprocesos, cada compilador o interprete se encargará a su manera de hacerlo,
pero a los efectos de este artículo, nos basta con tener claro que la
evaluación es la interpretación de un código ingresado y escrito en un
lenguaje en particular.

## ¿Y la evaluación estándar?

Hay algunos rasgos en la forma de evaluar las sentencias que son comunes en
casi todos los lenguajes, tan comunes que podríamos considerarlos tan
universales como la fuerza de gravedad. Veamos:

```r
> print("mi_variable")
[1] "mi_variable"

> print(mi_variable)
Error in print(mi_variable) : object 'mi_variable' not found

```

Ningún programador, ni los menos avispados, debería tener dificultad en
explicar lo que está ocurriendo, no importa de que lenguaje venga, es
absolutamente claro.  En la primer expresión, lo que se intenta es mostrar por
consola o pantalla, una cadena fija ("hardcoded" le diríamos). R (y cualquier
otro lenguaje) "ve" las comillas dobles y evalúa que lo que sigue es una cadena
literal. 

El segundo caso, vemos que `mi_variable` no tiene comillas, ¿que hace el
interprete de R (y nuevamente de cualquier lenguaje")?  Evalúa que lo que se
estaría indicando es un identificador, un nombre, o más precisamente el nombre
de un objeto. En este caso `mi_variable` sin las comillas, es una referencia y
no una cadena literal. El mensaje de error luego de `print(mi_variable)` nos
está claramente confirmando esto, no está diciendo que el objeto `mi_variable`
no existe. Claro, si hasta ahora no lo hemos definido. Lo podemos hacer:

```r
> mi_variable <- "Este es el valor al que apunta el objeto llamado mi_variable"
> print(mi_variable)

[1] "Este es el valor al que apunta el objeto llamado mi_variable" 

``` 

Y ahora sí, tenemos una sentencia que el interprete puede evaluar sin ningún
error aparente. Estos son rasgos del lo que llamaríamos Evaluación estándar
(**ES**). 

Un pequeño adelanto de lo que sigue: 

```r
> library(tidyverse)
> any(ls() == "tidyverse")
[1] FALSE
``` 

Notesé que `tidyverse` lo hemos indicado sin comillas como parámetro de entrada
de la función `library()`, la cual  realiza la carga de un determinado
paquete o librería. Con la siguiente sentencia verificamos que no existe ningún
objeto llamado `tidyverse` y a pesar de esto, la función `library()`, de foma
"mágica", logró entender que lo que queríamos era cargar el paquete
`tidyverse`. Está "magia" se llama evaluación No estándar (**ENE**). La forma
estándar hubiera sido `library("tidyverse")`.


## Evaluación perezosa

TO DO




