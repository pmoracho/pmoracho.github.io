---
layout: post
title: "NLTK -  mi primer Tutorial"
date: 2017-01-05
description: Introducción a la NLTK
tag:
  - nltk
  - desarrollo
  - python
show_meta: true
comments: true
---
# NLTK - Tutorial 1

Hay muchos tutoriales y ejemplos del uso de esta fantástica librería, lamentablemente casi todo está orientado a la lengua inglesa. Vamos a tratar de ir desarrollando el contenido de este tutorial orientado al español. 

## Análisis de texto básico

Hay algunas funciones elementales de análisis muy interesantes que podremos hacer con **NLTK**, para empezar lo que se necesitará obviamente es el texto que se quiere analizar. Una forma sencilla y que se repite en cientos de tutoriales, es la de descargarse los ejemplos del libro de la **NLTK**, mucha funcionalidad va a requerir descargarse distintos "corpus" o "modelos", estos paquetes se necesitan para distintas funcionalidades de la librería, algunos como "Panles lite Corpus" pueden ocupar varios Gbytes. Los ejemplos del libro oficial de *NLTK* se agrupan en la colección "Book" y ofrece cosas como "Moby Dick", los discursos inaugurales de los presidentes norteamericanos, etc. 

### Para descargar "corpus" o "modelos"


	>>> import nltk
	>>> nltk.download()

Hay que seleccionar el "corpus" Book, tengan en cuenta que si todo está correctamente instalado, debiera abrirse un ventana gráfica (Si se cuenta con TKinter/Tcl) o bien la selección se haría en modo texto.

**Nota**: la mejor definición de lo que es un "corpus" la ofrece la [wikipedia](https://es.wikipedia.org/wiki/Corpus_ling%C3%BC%C3%ADstico): _Un corpus lingüístico es un conjunto amplio y estructurado de ejemplos reales de uso de la lengua_


### Trabajando con los ejemplos

Lo primero es importar los textos, esto creara unas variables `text<n>`, cada una contiene el contenido completo del libro

	>>> from nltk.book import *
	>>> text1
	<Text: Moby Dick by Herman Melville 1851>

Conceptualmente, un objeto `nltk.text` no es más que una lista ordenada de `tokens`, siendo esto la unidad mínima de un texto, simplemente palabras o signos de puntuación. Esto podemos verlo fácilmente de la siguiente manera

	>>> text1[0:9] # hacemos un "slice" de los primeros 10 tokens 
	['[', 'Moby', 'Dick', 'by', 'Herman', 'Melville', '1851', ']', 'ETYMOLOGY', '.']


Si revisáramos el texto en la carpeta `nltk_data/corpora/gutemberg', veríamos en el caso del archivo de "Moby Dick" que justamente comienza de la siguiente forma:
	
	[Moby Dick by Herman Melville 1851]
	
	ETYMOLOGY.

Vemos entonces que "tokenización" que se hizo hace algunas cosas interesantes y para nada triviales:

* Separa convenientemente signos de puntuación y palabras
* Elimina caracteres en blanco y saltos de línea  


### Trabajando con nuestros propios textos

Vamos a empezar a trabajar sobre nuestros propios ejemplos, la idea es crear un `nltk.text` desde el principio y aprovecharlo para ir mostrando las funcionalidades elementales de análisis. Para empezar debemos contar con un "texto", que puede ser cualquier cosa: un mail, un libro, una nota, un twit, un chat, etc. Lo importante es partir de un "texto" claro. Lo que voy a hacer es importar directamente del Proyecto Gutemberg, un libro que nos hacían leer, al menos en mi época, en la secundaria, se trata de [Juvenilia](http://www.gutenberg.org/ebooks/41575.txt.utf-8).


	from urllib import request
	from nltk import word_tokenize

	"""
	Descarga de un texto
	"""
	url = "http://www.gutenberg.org/cache/epub/41575/pg41575.txt"
	response = request.urlopen(url)
	raw = response.read().decode('utf8')

	print(type(raw))
	print(len(raw))
    print(raw[:75])
    
(*) El ejemplo descarga directamente un documento, pero se ve claro que lo que necesitamos en principio es una cadena de texto, por lo que se entiende que tranquilamente podemos leer el archivo, recuperarlo de una base de datos, etc.

La salida:

    <class 'str'>
    532748
    The Project Gutenberg EBook of Juvenilla; Prosa ligera, by Miguel Cané
    
Como primer paso hay que "tokenizar" el texto de la siguiente forma:

    """
    Tokenización del texto
    """
    tokens = word_tokenize(raw)
    print(type(tokens))
    print(len(tokens))
    print(tokens[:10])
    
La salida:

    <class 'list'>
    101103
    ['\ufeffThe', 'Project', 'Gutenberg', 'EBook', 'of', 'Juvenilla', ';', 'Prosa', 'ligera', ',']

Lo interesante que la rutina word_tokenize de nltk transformo una larga cadena de caracteres en una lista de tokens, lo siguiente es convertir esta lista en un `nltk.text`:

    text = nltk.Text(tokens)
    print(type(tokens))
    print(len(tokens))
    print(tokens[:10])
    
La salida:

    <class 'list'>
    101103
    ['\ufeffThe', 'Project', 'Gutenberg', 'EBook', 'of', 'Juvenilla', ';', 'Prosa', 'ligera', ',']


Ya conformada la variable `nltk.text` podemos empezar a hacer análisis interesantes:

* Obtener las colocaciones. Las [colocaciones](https://es.wikipedia.org/wiki/Colocaci%C3%B3n) son una unidades fraseologicas de dos o más palabras que se usan muy habitualmente combinadas, más de lo que probabilísticamente se daría, por ejemplo "alto riesgo", "renta fija", "tomar en cuenta", "sin duda", etc. 

        text.collocations()
        
* Obtener los [hapaxes](https://es.wikipedia.org/wiki/H%C3%A1pax) que no son más que una palabra que sólo aparece una vez dentro de un contexto, ya sea en el registro escrito de un idioma entero, en las obras de un autor o dentro de un solo texto. 

        # Obtener 20 hapaxes
        from nltk import FreqDist
        fdist1 = FreqDist(text)
        fdist1.hapaxes()[:20]
        
### Que es el "Stemming" y para que es útil?

El "Stemming" es un algoritmo de reducción de una palabra a su raíz o "stem".  Es muy útil en aplicaciones o funcionalidad de recuperación de información (los buscadores como el de Google usan este mecanismo), pensemos al buscar por el término "familia" usando una búsqueda exacta perderíamos documentos dónde aparecen los términos "familiar", "familias", etc. El "Stemming" reduce  los términos mencionados a la raíz "famili", por lo que la búsqueda
