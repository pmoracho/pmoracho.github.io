---
layout: post
published: true
date: '2020-02-13'
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
title: El texto plano nunca fue plano
description: Un resumen rápido de las codificaciones y por que no deberíamos desentendernos de este tema
tags:
  - desarrollo
---


## Hubo un tiempo que fue hermoso

Recuerdo mis primeros años en sistemas, debo admitirlo, fue en el siglo pasado.
En esos tiempo los que escribíamos código, distinguíamos entre los _archivos
binarios_, cualquier cosa que había que verla con un editor hexadecimal, y los
_archivos de texto_, a los que les decíamos "son texto plano", que eran esos
que abríamos con cualquier editor de texto, tipo el **edlin** o alguno más
moderno. La vida era simple, el texto era  una sucesión de
caracteres de un solo byte, podíamos saber cuantos había simplemente viendo el
tamaño del archivo. Todo buen programador en ese entonces, tenía una hoja
impresa al lado del monitor con un montón de letras y caracteres extraños y un
número debajo de cada uno de ellos.  La llamábamos la tabla **ASCII**. Para
nosotros esa era la verdad absoluta, una de las tantas biblias que manejábamos,
tal vez la mas importante de todas: cada caracter que se "tipeaba" tenía una
correlación en esa tabla. Y escribíamos muchos, y a veces, encima,
necesitábamos escribir unos caracteres nada habituales en los teclados de esos
años, letras `ñ`, acentos de todo tipo, y era entonces dónde apelábamos a
nuestra biblia de una página. De tanto hacerlo, ya habíamos aprendido los
trucos, la tecla `alt` y `165` en el teclado numérico nos hacía aparecer por
arte de magia esa `ñ` imposible de encontrar en el teclado, o el `alt` y `160`
para hacer una simple `á`. También teníamos unos elegantes y algo esotéricos
caracteres especiales para dibujar todo tipo de recuadros, que nos cansábamos
de usar en las interfaces de texto de nuestros programas, junto con alguna
rutina escrita en **assembler**, copiada seguramente de alguna revista, para
escribir directamente en la memoria de vídeo y hacer que nuestras ventanitas de
texto viajaran a la velocidad de la luz en nuestros monitores.

De vez en cuando nos enterábamos que existían equipos a los que les decían
"Mainframes" y que, particularmente los de **IBM**, usaban una codificación
distinta que se llamaba **EBCDIC**, pero iba pasar bastante tiempo hasta que
nos cruzáramos con un archivo de esos, así que para nosotros, el texto seguía
siendo simple y sobre todo "plano".

## Pero un día empezó la debacle

Y fue el día que nos enteramos que la famosa tablita **ASCII** que usábamos, no
necesariamente era la misma que manejaba un programador en el país del tío Sam,
ni mucho menos uno detrás de la cortina de hierro (si.. todavía existía). Y fue
duro, como cuando un compañerito nos revelaba que los reyes eran los padres. Se
nos empezaba a caer la idea del texto "plano", y sobre todo el concepto de
universalidad de dicho texto. En ese entonces, teníamos que empezar a pensar en
el "código de página", o "codepage", que, sin ser demasiado académico, eran
"adaptaciones" regionales o mas bien de los fabricantes de hardware, sobre el
estándar **ASCII**. La idea fundamental y tremendamente disruptiva de esto, es
que el texto finalmente no es tan plano como pensábamos, y que teníamos que
empezar a considerar el concepto de la codificación.

## ¿Códificación?

Un concepto básico: las computadoras no entienden de caracteres ni palabras ni
textos, son calculadoras avanzadas, solo manejan números. A la hora de definir
un caracter cualquiera, se le asigna un número. El estándar **ASCII** es,
justamente una forma de definir que número representa cada
letra. Y desde 1967, establece que::

* Los números del 0 al 31 y el 127, definen lo que se conoce como
  caracteres de control, nada que sirva para representar un verdadero caracter
  de un texto, son simplemente instrucciones especificas que podían ser
  interpretadas por distintos equipos, lo más común: las impresoras (modems y
  otros) . De lo códigos más clásicos de este conjunto y que era habitual usar
  (al menos en mi caso), el 9 (horizontal tab) el 10 (line feed) y 13 (carriage
  return) o el 12 (form feed) Otros muy comunes, el 0 o caracter nulo (Todo
  programador en C lo conoce muy bien(, el 8 (back space) que usábamos para
  simular un texto en negrita con el simple truco de imprimir un texto,
  retroceder mediante tantos códigos 8 como la longitud del texto y luego
  repetir nuevamente el mismo texto, es decir, imprimir en doble pasada, o el
  clásico 27 (escape).

* El rango de números que va del 32 al 126 es lo que se conoce como caracteres
  "imprimibles", este es el rango de códigos/caracteres más básico y estándar,
  definidos claro, a las necesidades de un inglés americano. Dispone de un gran
  número de símbolos, de los dígitos y del abecedario inglés, tanto en
  minúsculas como mayúsculas (en un principio solo tenía mayúsculas).  Para
  nosotros los de habla hispánica, obviamente, nos falta letras como la `ñ` o
  las vocales acentuadas

Increíblemente, durante más de una década la informática mainstream, no
consideraba que existíamos los hispanohablantes, de hecho el estándar no
suponía que existiera otro idioma que no sea el inglés Americano. Hasta 1981,
cada caracter tenía 7 bits, lo que es lo mismo decir, que se contemplaba un
total de 128 caracteres, pero a partir de ese año,  **IBM** por las suyas,
define una extensión al código **ASCCI** sumándole un bit adicional para llegar
a los 8 bits que conocemos todos. Con lo que logra dar forma a otros 128
caracteres más, esta extensión se la llamó "código de página 437", tenía
algunas modificaciones a los códigos de control por obsoletos y agregaba varios
caracteres gráficos y en particular varias letras latinas tan necesarias para
la gente del occidente europeo. Está codificación se incorporó en la primera de
las máquinas conocida luego como computadora personal (PC), el resto es
historia conocida. La versión "extendida", se populariza y hoy cuando uno habla
de **ASCII** se lo hace siempre suponiendo la versión extendida, **que no es el
estándar** (esto es bueno tenerlo en claro). 

Agregar esta simple extensión, le dio la posibilidad a los fabricantes de
hardware de adaptarla a las necesidades, normalmente regionales, y por que no,
eventualmente a caprichos de cada uno, después de todos, esos pocos bits no
forman parte del estándar. Y este es el momento donde aparece el concepto
"codepage" o el código de página, y por supuesto el comienzo de la pesadilla
que aún nos acompaña. 

## ¿Pesadilla?

Bueno, debo reconocer que soy un poco exagerado.

## Resumen

* No existe algo llamado texto plano
* Un archivo de texto siempre tiene una codificación, podemos ignorar esto,
  pero el archivo de todas formas la tiene, normalmente la aplicaciones tienen
  una configuración por defecto
* Es fundamental tener conciencia de la codificación de todo lo que escribamos
* La codificación mas conocida, generalizada y antigua (1967) es la del
  estándar **Ascii**, que contiene 33 caracteres de control y 95 caracteres
  "imprimibles", entre letras (mayúsculas y minúsculas), dígitos y símbolos
  varios. Es de origen inglés/americano, por lo que carece de una enorme
  multitud de caracteres incluso básicos, como en el caso del castellano, son
  la `ñ`, las vocales acentuadas o las diéresis. 
* Originalmente para este estándar solo era necesario contar con 7 bits para
  representar un total de 128 caracteres y códigos de control.
* IBM varios años después (en 1981) extendió el Ascii, al agregar un bit más y
  llegar finalmente al byte tradicional. Esto introdujo la posibilidad de
  representar 128 caracteres más. Es importante notar que hoy, cuando hablamos
  de **Ascii**, hablamos de la versión extendida que no es el estándar
  propiamente dicho.
* Estos 128 caracteres adicionales, son variables, ya que no forman parte del
  estándar. A partir de estos bits adicionales es que aparece el concepto de
  código de página.
* El código de página son adaptaciones regionales o de los fabricantes de
  hardware que se hace solo sobre esos 128 caracteres adicionales
* Para los de habla española, o Europeos occidentales, cuando hablamos de
  codficaciones de 1 byte por caracter, lo habitual es hablar de **Latin1** o
  **ISO8859-1**, incluso **Windows 1252** (llamada Ansi en algunos casos),
  originalmente el codepage **850**, si bien con algunas diferencias mínimas,
  son las codificaciones mas habituales, que incluye los acentos, las letras
  con diéresis, y por supuesto la `ñ`. 
* Un texto **Ascii** es universal únicamente en los 128 caracteres básicos,
  cualquier otro caracter en el grupo de los extendidos ya deja de serlo y para
  poder representarlos correctamente es necesario saber la correcta
  codificación o código de página. La `ñ` que vemos en un texto de 1 byte por
  caracter puede ser un caracter totalmente distinto en cualquier otra
  codificación, lo unico que es univeral son los 128 caracteres del estándar
* El gran problema es que la codificación no es un metadato que se traslada con
  el archivo, es una información que debemos conocer antes de intentar abrir
  este y por supuesto, no debemos ignorar esto a la hora de salvar cualquiera
  de estos. Poder saber la codificación, solo con el archivo no es un tarea
  sencilla, en el mejor de los casos se puede iendificarlo con un determinado
  grado de certeza.
* El estándar Ascii aún agregando la parte extendida, tiene algunos
  inconvenientes: un número muy limitado de caracteres, insuficiente para
  representar la totalidad de estos en muchos idiomas y el problema del código
  de página, dónde un mismo código puede representar algo en un determinado
  codepage y otra cosa diferente en otro. 
* Unicode fue el siguiente estándar creado con el objetivo de superar los
  problemas mencionados, lograr ser realmente universal y poder dar
  representación a todos los caracteres en una sola codificación.
* Unicode incluye todos los caracteres de uso común actuales, unos 137374 (a la
  fecha), contiene muchos alfabetos/idiomas, sistemas ideograficos y símbolos y
  hasta emoticones. Es un estándar en constante evolución. En Unicode cada
  caracter tiene un único código numérico, por lo que una letra ñ no dependerá
  de ningún código de página, siempre será un `ñ` en cualquier aplicación que
  sea compatible con Unicode.
* La forma de codificación más sencilla es la conocida como **UTF-32**, donde
  cada caracter es representado por un número de 32 bits / 4 bytes fijos
* Sin embargo la forma más extendida de codificación Unicode es **UTF-8**, en
  sus variantes con y sin **BOM**
* El **BOM** o Byte order mark, es un conjunto de bytes (de 2 a 4) al comienzo
  del archivo como indicativo de la codificación unicode y del orden de los
  bytes (little rnfian/big enddian)
* **UTF-8** (y **UTF-16**) manejan codificaciones de longitud variable, esto
  implica, que cada caracter puede tener una longitud variable, en el caso de
  **UTF-8** puede ser de 8 bits y llegar hasta 32 bits, esto según el caracter.
  Por compatibilidad con la codificación **Ascii**, cualquier caracter de los
  128 estándares, se codifica con un único byte y respeta el número del
  estándar antiguo. Esto implica que un archivo **Ascii** con caracteres no
  extendidos es totalmente intercambiable con uno **UTF-8** o **UTF-16**. El
  problema claro lo tendremos siempre con los caracteres extendidos, dónde en
  **ASCII** seguirá siendo de un byte (y deberemos saber el codepage) y en
  **UTF-8** será de 2 o 3 bytes, dependiendo del caracter. 


## Recomendaciones

* **UTF-8** sin **BOM** es la codificación más práctica y cómoda para usar.
  Está lo suficientemente extendida entre aplicaciones y sistemas operativos
  como para considerarla la codificación universal. 
* El **BOM** esta totalmente desaconsejado por que no todas las aplicaciones lo
  sopotan.
* Al usar **UTF-8** nos evitamos cualquier problema relacionado con el
  "codepage", saber que un archivo es **UTF-8** es el único dato que
  necesitamos saber antes de abrir el archivo, puede ser un texto con
  ideogramas chinos o un simple texto en español, si es **UTF-8** lo podremos
  abrir y "ver" lo mismo que "ve" el autor original del archivo. 
* Si por alguna razón necesitamos trabajar con archivos en una codificación de
  un solo byte (No **Unicode**), en primer lugar tenemos que tener claro que
  estaremos limitados a un conjunto reducido de caracteres, ni intentar agregar
  emoticones ni otros simbolos del mundo **Unicode**, y en segundo lugar
  deberemos usar, al menos los que somos del mundo occidental, las
  codificaciones comunes de este lado del planeta, normalmente **"Latin1"**, o
  bien **"ISO-8859-1"** o  **Windows 1252**. Y si intercambiamos un archivo, es
  fundamental saber o dar a conocer cual es exactamente la codificación usada.

