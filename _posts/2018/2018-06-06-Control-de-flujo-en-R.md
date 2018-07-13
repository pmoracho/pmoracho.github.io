---
layout: post
published: true
date: '2018-06-06'
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
title: ¿Cómo funciona el control de flujo en un lenguaje que es enteramente vectorial?
description: 
tags:
  - desarrollo
  - R
image:
  feature: 2018/img-feature-003.jpeg
  credit: Patricio Moracho
---
En todo lenguaje existen clausulas para controlar el flujo de ejecución, en
**R** en particular hablo del `if/else`, el `while` y el `repeat`. Estas, no son
muy distintas a las que podemos encontrar en cualquier otro lenguaje, evalúan
una determinada condición, en función a un `Vedadero/Falso` de la misma, será la
dirección que tomen. Pero en **R**, hay una pequeña gran diferencia.

Al ser un lenguaje netamente vectorial, no existen datos "atomicos", si bien hay
distintos tipos de datos, esto únicamente pueden existir en un "contenedor" (el
más elemental es el vector), cuando hacemos `a = 1` en cualquier lenguaje,
estamos asignando un espacio para guardar un único dato entero, en **R**, lo
mismo, crea un vector de tipo numérico, con un solo elemento. 

Ahora bien, volviendo al tema en cuestión, con la evaluación de las condiciones
sucede lo mismo, no retorna un `TRUE/FALSE` atómico, sino que retorna un vector
booleano, sin embargo el control de flujo es netamente "atómico", un solo
`TRUE/FALSE` determinará el flujo a seguir. Entonces: **¿Como se compatibiliza
en el lenguaje está situación de necesitar un dato atómico para la evaluación,
cuando en realidad el lenguaje no lo tiene?**

Vamos a usar el `if` como ejemplo, pero el funcionamiento es el mismo en
cualquiera de las demás clausulas. En primer lugar, nunca esta de más consultar
la documentación, y lo que podremos notar que `if/else`, el `while` y el
`repeat` comparten la misma ayuda y es muy significativo lo que dice acerca de
la `condición` a evaluar:

> **cond**:	 A length-one logical vector that is not NA. Conditions of length greater than one are currently accepted with a warning, but
> only the first element is used. An error is signalled instead when the
> environment variable _R_CHECK_LENGTH_1_CONDITION_ is set to true.
> Other types are coerced to logical if possible, ignoring any class.

Resumiendo:

+ Se espera que la condición retorne un vector de un solo elemento y que no sea `NA`
+ Eventualmente se aceptan más de un elemento, pero solo se evalúa el primero, y se emite un `Warning`
+ Eventualmente se puede configurar, que en vez de un `Warning` se genere un `Error`

Veamos, con esto, evidentemente no hay problemas:

    > condicion <- c(TRUE)
    > if (condicion) print ("True")
    [1] "True"

Ahora ¿que ocurre con un vector demás elemento?

    > condicion <- c(TRUE,FALSE)
    > if (condicion) print ("True")
    [1] "True"
    Warning message:
    In if (condicion) print("True") :
      the condition has length > 1 and only the first element will be used

Exactamente lo que se menciona en la documentación, se permite pero solo se
evalúa el primer elemento del vector, y se emite un `Warning`. Por último: ¿Hay
forma de evitar este `Warning`? si, varias, pero dependerá de lo que estemos
buscando, veamos un poco:

### Si queremos verificar solo el primer elemento

    > # Si nos sirve solo evaluar el primer caso
    > if (condicion[1]) print ("True")
    [1] "True"

    > # Lo mismo pero usamos el doble && (and) ya que el mismo
    > # solo retorna el and lógico del primer elemento consigo mismo
    > if (condicion && condicion) print ("True")
    [1] "True"

    > # Con el || (Or) es igual
    > if (condicion || condicion) print ("True")
    [1] "True"

### Si cualquiera de los elementos es TRUE

    > # any, como su nombre lo indica, evalua que cualquier 
    > # elemento sea TRUE
    > if (any(condicion)) print ("True")
    [1] "True"

### Si todos los elementos deben ser TRUE

    > # all, solo será TRUE si todos lo son
    > condicion <- c(TRUE,FALSE)
    > if (all(condicion)) print ("True")
    > 
    > condicion <- c(TRUE,TRUE)
    > if (all(condicion)) print ("True")
    [1] "True"
