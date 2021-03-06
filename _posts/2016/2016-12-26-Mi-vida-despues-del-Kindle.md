---
layout: post
title: Mi vida después del Kindle
date: 2016-12-26
show_meta: true
comments: true
published: true
modified: '2019-05-30 22:28 -0300'
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
description: Mi vida después del Kindle
tags:
  - literatura
  - opinion
  - kindle
---

![kindle.jpg]({{site.baseurl}}/images/kindle.jpg)

Hace algo más de tres años compré un **kindle paperwhite v2 de Amazon**, más
precisamente Julio de 2013, fue una decisión que demoré mucho tiempo en tomar.
Soy uno de esos románticos del papel escrito y al olor a libro nuevo o viejo.
Debo decir, pasado tanto tiempo, que no me equivoqué. Los beneficios de la
lectura en dispositivos de tinta electrónica (no estoy hablando de "tablets")
son muchos como para despreciarlos.  <!-- break -->

## Espacio

Es tal la cantidad de libros que tengo y tan poco el espacio que dispongo, que
seguir adquiriéndolos en papel es sumamente complicado de sostener. Por otro
lado, suponiendo que algún día tuviera espacio suficiente como para armar ese
concepto romántico del estudio / biblioteca personal, realmente, vale la pena?
o acaso, ¿no deja de ser un "esnobismo" como tantos otros? ¿un capricho del siglo
pasado?. Te lo entiendo si sos un escritor, periodista, cineasta, filósofo o te
sobra espacio y plata, pero para un simple mortal como yo, con familia y un
departamento chico es un lujo casi imposible.

## Costos

El impacto (positivo) que tiene un lector electrónico en los costos, es
sumamente alto.  Tan solo pensar en los miles de libros que todos los años
entran al dominio público y que podemos descargar libremente. La amortización
del dispositivo es rápida pensando en el precio de la versión física de un
libro. Por ejemplo: un "Madame Bovary" de Flaubert, actualmente en Buenos
Aires, en ediciones impresas, ronda en el orden de los u$s 10, hoy un
Paperwhite cuesta unos u$s 130, es decir que solo 13 libros del estilo del
escrito por el señor Gustave amortizan la compra de un equipo.

> Cuándo un libro pasa a dominio público, depende de la legislación de cada
> país. Los plazos más habituales de ver son 50 o 70 años desde la muerte del
> autor. Por ejemplo en Argentina el plazo es de 70 años, pero hay que tener en
> el tema de la traducción que también entra en el mismo régimen, "Madame
> Bovary" puede ser de dominio público desde mucho tiempo atrás, sin embargo,
> una traducción moderna eventualmente tendría activos los derechos del
> traductor. 

## Diccionarios

Una funcionalidad del Kindle que realmente disfruto, es la del diccionario
integrado. Simple, una duda en una palabra, se la selecciona y automáticamente
se desplegará un "popup" con la descripción de la misma, incluso tenemos el
acceso a la Wikipedia en caso de contar con conectividad por medio de Wifi.
Los diccionarios son configurables, incluso pueden instalarse diccionarios de
traducción, por ejemplo para inglés - castellano. El kindle además lleva esto
mucho más allá, cada palabra consultada se va incorporando a un índice que
podemos consultar en cualquier momento para repasar las dudas que tengamos.
Esto con un libro físico implicaría tener un diccionario (también físico)
importante al lado, ni pensar en tener que transportar esta configuración.

## Transportabilidad

Llevar encima la obra completa de Dostoyevsky ¿cuánto pesaría semejante
conjunto de libros? En un Kindle ocupan una porción muy pequeña de la capacidad
total. Incluso un modelo antiguo como el que tengo, con 2 gbytes, luego de más
de tres años no llegué a completar su capacidad, estamos hablando de más de 250
libros. 

## Búsquedas

Impensable. Buscar una palabra en uno o más libros, es simplemente cuestión de
unos segundos.

## Contenidos heterogéneos

Prácticamente cualquier documento, página web, nota periodística, etc. puede
ser convertida a un libro electrónico y visualizada como tal en el Kindle. Hay
dos grandes opciones (hay más obviamente):

* La herramienta [Send to kindle](https://www.amazon.com/gp/sendtokindle) de
Amazon. Convierte múltiples tipos de archivo en documentos electrónicos y los
sube automáticamente a la nube personal que cada usuario de Kindle cuenta de
forma gratuita y que permite extender el volumen de nuestro dispositivo. Cuenta
con versión de "escritorio" para hacer el clásico botón derecho -> "Send to"
con los archivos que es capaz de convertir o bien un complemento para
navegadores que permite hacer lo mismo sobre cada página interesante que
estemos visualizando. Créanme que esto es realmente funcional, es muy habitual
que la lectura de páginas web sea muy superficial, muchas veces no colabora el
diseño, la publicidades y demás distracciones, usando esta herramienta podemos
diferir la lectura para momentos más tranquilos, además que en el caso de una
página web el complemento limpia completamente la página de elementos inútiles,
convirtiéndola prácticamente en la página de un libro. Todo esto con
conectividad "over the air", usando wifi el Kindle descarga automáticamente
todos estos contenidos.


* [Calibre](https://calibre-ebook.com/), por otro lado permite convertir varios
tipos de documentos, pero me resulto fantástico el trabajo que puede llegar a
hacer con los feeds RSS, por ejemplo, podemos eventualmente de manera
automatizada, descargarnos las noticias de algún medio tradicional y leerles
por la mañana cual diario de papel.

## Obsolescencia tecnológica

Es mitad del 2019 y estoy actualizando algo el contenido de este texto, mi
kindle aún activo, funcional y con espacio para seguir cargándole libros. La
batería aún trabaja los suficientemente bien como para cargarla cada dos o tres
semanas. Notable. Pocos equipos electrónicos que tengo han sobrevivido tanto
tiempo y en inmejorables condiciones como mi pequeño lector de libros
electrónicos.

# Trucos

* En Calibre, el formato por defecto que mejor me funciona es **AZW3** por
  sobre **MOBI**. Las notas en los textos, con **MOBI** me abren una nueva
  página, con **AZW3**, me mantiene en la página actual y se abre un "pop up"

* Soy muy fan del [blog de Daniel Marin](https://danielmarin.naukas.com/), por
  lo que me gusta cada cierto tiempo bajarme las últimas actualizaciones y
  leerlas en mi kindle. Para esto configuré la siguiente "receta" en el
  programador de descargas:  

    ```python
    #!/usr/bin/env python2
    # vim:fileencoding=utf-8
    from __future__ import unicode_literals, division, absolute_import, print_function
    from calibre.web.feeds.news import BasicNewsRecipe

    class AdvancedUserRecipe1426093931(BasicNewsRecipe):
    title          			= 'Eureka - Ciencias del Espacio by Daniel Marin'
    oldest_article 			= 30
    max_articles_per_feed 	= 100
    auto_cleanup   			= True
    __author__  			= 'Daniel Marin'
    description 			= 'Noticias del Blog Eureka'  
    conversion_options = { 
                           'language'    : 'Español',
                           'publisher'   : 'Daniel Marin',
                           'authors'     : 'Daniel Marin',
                           'smarten_punctuation' : True
                         }          
    feeds          = [
        ('Eureka', 'http://danielmarin.naukas.com/feed/'),
    ]
    ```

# Enlaces imprescindibles

* [Calibre](https://calibre-ebook.com/): Fundamental instalarlo, el
  administrador de tu biblioteca electrónica. Conversión al vuelo de formato
  **epub** al que requiere el dispositivo. Descarga de sitios sindicados por
  rss a un libro electrónico. Nota: por lo que veo Kovid Goyal el autor de
  Calibre ya está trabajando en la migración a [Python
  3](https://github.com/kovidgoyal/calibre/blob/master/README.python3), muy
  buena noticia.

* [Epublibre](https://www.epublibre.org): Espectacular sitio con un catálogo
  sensacional de títulos en español principalmente y muy actualizados, por
  supuesto solo lo recomiendo para la descarga de libros de dominio público
  únicamente.
