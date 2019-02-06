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

Un pequeño adelanto de lo que se trata todo esto: 

```r
> library(tidyverse)
> any(ls() == "tidyverse")
[1] FALSE
```

Notesé que la cadena `tidyverse` lo hemos indicado sin comillas como parámetro
de entrada de la función `library()`, la cual  realiza la carga de un
determinado paquete o librería. Con la siguiente sentencia `any(ls() ==
"tidyverse")`, verificamos que no existe ningún objeto llamado `tidyverse` y a
pesar de esto, la función `library()`, de foma "mágica", logró entender que lo
que queríamos era cargar el paquete `tidyverse`. Está "magia" se llama
Evaluación No estándar (**ENE**), `library()` la implementa para evaluar los
parámetros de entrada, pero también es capaz de evaluar de la forma estándar,
es decir: `library("tidyverse")`.

Así como lo cuento, parece un tema casi trivial y no demasiado útil, limitado a
la utilidad de usar o no comillas, pero es mucho más que esto y lo iremos
viendo paso a paso. El problema es que para entender completamente la
Evaluación No estándar (**ENE**), es necesario entender otros conceptos dónde
se apoya toda la idea de la **ENE** y que sin duda pueden resultar muy
originales para el que venga de otros lenguajes. 

## Evaluación perezosa de los parámetros de una función

Seguramente has escuchado el término "lazy evaluation", que hace referencia a
una evaluación diferida, una estrategia que se usa en varios lenguajes de
programación para retrasar el calculo o ejecución de una expresión hasta el
momento en que sea realmente necesario. Digamos, sin profundizar demasiado, que
este tipo de estrategia tiene múltiples beneficios. En la gran
mayoría de estos lenguajes, este tipo de evaluación debe codificarse
especialmente, normalmente, creando objetos que implementan este tipo de
evaluación, es decir este tipo de evaluación es un excepción.

Sin embargo, en R la evaluación perezosa es la norma, todo es evaluado de manera
"lazy", y particularmente en esta sección, hablaremos en realidad de la
evaluación perezosa de los parámetros de cualquier función, algo que ninguno de los
lenguajes mencionados anteriormente lo permite.

En casi cualquier lenguaje, digamos Python por ejemplo, algo como esto, es
normalmente aceptado:

```python

def mi_funcion(a, b):
	print("hola")

mi_funcion()

> Traceback (most recent call last):
>   File "main.py", line 4, in <module>
>     mi_funcion()
> TypeError: mi_funcion() takes exactly 2 arguments (0 given)
```

Al intentar ejecutar `mi_funcion()` el interprete evalúa la misma y los
parámetros definidos en su firma, como la llamada no los tiene, se nos entrega
un mensaje de error muy claro `mi_funcion()` espera 2 parámetros y no le estamos
indicando ninguno.

En R las cosas son un poco distintas (hay que acostumbrarse)

```r
mi_funcion <- function(a, b) {
    print("hola")
}

mi_funcion()

[1] "hola"
```

¿Qué magia del demonio esta ocurriendo aquí? Tenemos una función que espera dos
parámetros, la llamamos sin pasarle ninguno, lo parámetros ni siquiera tienen
valores por defecto y no tenemos ningún error y encima la función hace lo que
esperaríamos. Acá es donde vemos la evaluación perezosa de los parámetros de una
función. En R, no sé si no lo dijimos ya, todo se evalúa recién cuando lo
usamos, en este caso, los parámetros no se usan para nada, entonces no se
evalúan. Veamos el siguiente caso:

```r
mi_funcion <- function(a, b) {
    print(paste("hola", a))
}

mi_funcion()

> Error in paste("hola", a) : argument "a" is missing, with no default 

mi_funcion("amigo")
[1] "hola amigo"

mi_funcion("amigo", "del alma")
[1] "hola amigo"
```

Ahora hemos modificado la función para que use el parámetro `a`, si invocamos
sin pasar éste, obtendremos un error, muy claro también: _falta el parámetro `a`
o el mismo no tiene un valor por defecto_. También podemos constatar, que si
llamamos con este parámetro como lo espera la función, no tendremos ningún
error, incluso si agregamos (aunque no se use) el parámetro `b`.

Aquí quiero señalar un concepto fundacional para entender la **ENE**:

> Hay un "gap" o intervalo entre la ejecución de la función y el momento en que
> cualquiera de sus parámetros es usado (evaluado) realmente. En este intervalo,
> nosotros como programadores, podremos intervenir y trabajar sobre los parámetros
> aún sin necesidad de evaluarlos efectivamente

## Entornos

## Quotation y Quasicuotation

TO DO




