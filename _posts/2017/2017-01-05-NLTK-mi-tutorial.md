---
layout: post
title: NLTK -  mi primer Tutorial
date: 2017-01-04
description: Introducción a la NLTK
tag:
  - nltk
  - desarrollo
  - python
show_meta: true
comments: true
published: true
modified: '2017-01-05 14:31 -0300'
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
image:
  feature: 2017/img-feature-003.jpeg
  credit: Patricio Moracho
---

Hay muchos tutoriales y ejemplos del uso de esta fantástica librería,
lamentablemente casi todo está orientado a la lengua inglesa. Vamos a tratar de
ir desarrollando el contenido de este tutorial orientado al español. 

## Análisis de texto básico

Hay algunas funciones elementales de análisis muy interesantes que podremos
hacer con **NLTK**, para empezar lo que se necesitará obviamente es el texto
que se quiere analizar. Una forma sencilla y que se repite en cientos de
tutoriales, es la de descargarse los ejemplos del libro de la **NLTK**, mucha
funcionalidad va a requerir descargarse distintos "corpus" o "modelos", estos
paquetes se necesitan para distintas funcionalidades de la librería, algunos
como "Panles lite Corpus" pueden ocupar varios Gbytes. Los ejemplos del libro
oficial de *NLTK* se agrupan en la colección "Book" y ofrece cosas como "Moby
Dick", los discursos inaugurales de los presidentes norteamericanos, etc. 

### Para descargar "corpus" o "modelos"

```python
import nltk
nltk.download()
```

Hay que seleccionar el "corpus" Book, tengan en cuenta que si todo está
correctamente instalado, debiera abrirse un ventana gráfica (Si se cuenta con
TKinter/Tcl) o bien la selección se haría en modo texto.

**Nota**: la mejor definición de lo que es un "corpus" la ofrece la
[wikipedia](https://es.wikipedia.org/wiki/Corpus_ling%C3%BC%C3%ADstico): _Un
corpus lingüístico es un conjunto amplio y estructurado de ejemplos reales de
uso de la lengua_


### Trabajando con los ejemplos

Lo primero es importar los textos, esto creara unas variables `text<n>`, cada
una contiene el contenido completo del libro

```python
from nltk.book import *
text1
<Text: Moby Dick by Herman Melville 1851>
```

Conceptualmente, un objeto `nltk.text` no es más que una lista ordenada de
`tokens`, siendo esto la unidad mínima de un texto, simplemente palabras o
signos de puntuación. Esto podemos verlo fácilmente de la siguiente manera

```python
text1[0:9] # hacemos un "slice" de los primeros 10 tokens 
['[', 'Moby', 'Dick', 'by', 'Herman', 'Melville', '1851', ']', 'ETYMOLOGY', '.']
```

Si revisáramos el texto en la carpeta `nltk_data/corpora/gutemberg`, veríamos
en el caso del archivo de "Moby Dick" que justamente comienza de la siguiente
forma:
	
	[Moby Dick by Herman Melville 1851]
	
	ETYMOLOGY.

Vemos entonces que "tokenización" que se hizo hace algunas cosas interesantes y
para nada triviales:

* Separa convenientemente signos de puntuación y palabras
* Elimina caracteres en blanco y saltos de línea 


### Trabajando con nuestros propios textos

Vamos a empezar a trabajar sobre nuestros propios ejemplos, la idea es crear un
`nltk.text` desde el principio y aprovecharlo para ir mostrando las
funcionalidades elementales de análisis. Para empezar debemos contar con un
"texto", que puede ser cualquier cosa: un mail, un libro, una nota, un twit, un
chat, etc. Lo importante es partir de un "texto" claro. Lo que voy a hacer es
importar directamente del Proyecto Gutemberg, un libro que nos hacían leer, al
menos en mi época, en la secundaria, se trata de
[Juvenilia](http://www.gutenberg.org/ebooks/41575.txt.utf-8).


```python 
from urllib import request
"""
Descarga de un texto
"""
url = "http://www.gutenberg.org/cache/epub/41575/pg41575.txt"
response = request.urlopen(url)
raw = response.read().decode('utf8')

print(type(raw))
print(len(raw))
print(raw[:75])
``` 

(*) El ejemplo descarga directamente un documento, pero se ve claro que lo que
necesitamos en principio es una cadena de texto, por lo que se entiende que
tranquilamente podemos leer el archivo, recuperarlo de una base de datos, etc.

La salida:

``` text
<class 'str'>
532748
The Project Gutenberg EBook of Juvenilla; Prosa ligera, by Miguel Cané
```

Como primer paso hay que "tokenizar" el texto de la siguiente forma:

```python 
"""
Tokenización del texto
"""
from nltk import word_tokenize

tokens = word_tokenize(raw)
print(type(tokens))
print(len(tokens))
print(tokens[:10])
``` 

La salida:

```python 
<class 'list'>
101103
['\ufeffThe', 'Project', 'Gutenberg', 'EBook', 'of', 'Juvenilla', ';', 'Prosa', 'ligera', ',']
``` 

Lo interesante que la rutina word_tokenize de nltk transformo una larga cadena
de caracteres en una lista de tokens, lo siguiente es convertir esta lista en
un `nltk.text`:

```python 
text = nltk.Text(tokens)
print(type(tokens))
print(len(tokens))
print(tokens[:10])
```	

La salida:

```python 
<class 'list'>
101103
['\ufeffThe', 'Project', 'Gutenberg', 'EBook', 'of', 'Juvenilla', ';', 'Prosa', 'ligera', ',']
```	

Ya conformada la variable `nltk.text` podemos empezar a hacer análisis interesantes:

* Buscar 

* Obtener las colocaciones. Las
  [colocaciones](https://es.wikipedia.org/wiki/Colocaci%C3%B3n) son una
  unidades fraseológicas de dos o más palabras que se usan muy habitualmente
  combinadas, más de lo que probabilísticamente se daría, por ejemplo "alto
  riesgo", "renta fija", "tomar en cuenta", "sin duda", etc. 

```python	
text.collocations()
```	
 
* Obtener los [hapaxes](https://es.wikipedia.org/wiki/H%C3%A1pax) que no son
  más que una palabra que sólo aparece una vez dentro de un contexto, ya sea en
  el registro escrito de un idioma entero, en las obras de un autor o dentro de
  un solo texto. 

```python 
# Obtener 20 hapaxes
from nltk import FreqDist
fdist1 = FreqDist(text)
fdist1.hapaxes()[:20]
``` 

### Que es el "Stemming" y para que es útil?

El "Stemming" es un algoritmo de reducción de una palabra a su raíz o "stem".
Es muy útil en aplicaciones o funcionalidad de recuperación de información (los
buscadores como el de Google usan este mecanismo), pensemos al buscar por el
término "familia" usando una búsqueda exacta perderíamos documentos dónde
aparecen los términos "familiar", "familias", etc. El "Stemming" reduce  los
términos mencionados a la raíz "famili", por lo que la búsqueda se hace
bastante más precisa.

## Datos de interés

### Alphabetical list of part-of-speech tags used in the Penn Treebank Project:

Numero|Etiqueta|Descripción
1.|CC|Coordinating conjunction -> [Conjunciones coordinadas](https://es.wikipedia.org/wiki/Conjunci%C3%B3n_(gram%C3%A1tica))
2.|CD|Cardinal number -> [Número cardinal](http://www.wikilengua.org/index.php/N%C3%BAmero_cardinal)
3.|DT|Determiner -> [Determinante](https://es.wikipedia.org/wiki/Determinante_(ling%C3%BC%C3%ADstica))
4.|EX|Existential there -> Clausula existencial
5.|FW|Foreign word -> [Extranjerismo](https://es.wikipedia.org/wiki/Extranjerismo)
6.|IN|Preposition or subordinating conjunction -> [Preposición o Conjunciones subordinantes](https://es.wikipedia.org/wiki/Conjunci%C3%B3n_(gram%C3%A1tica))
7.|JJ|Adjective -> [Adjetivo](https://es.wikipedia.org/wiki/Adjetivo)
8.|JJR|Adjective, comparative -> [Adjetivo Comparativo](https://es.wikipedia.org/wiki/Adjetivo_comparativo)
9.|JJS|Adjective, superlative -> [Superlativo](https://es.wikipedia.org/wiki/Superlativo)
10.|LS|List item marker -> [Enumeración](http://www.wikilengua.org/index.php/Enumeraci%C3%B3n)
11.|MD|Modal -> [Verbos modales](https://en.wikipedia.org/wiki/Modal_verb)
12.|NN|Noun, singular or mass -> [Sustantivo, singular o incontable](https://es.wikipedia.org/wiki/Sustantivo)
13.|NNS|Noun, plural -> [Sustantivo, plural](https://es.wikipedia.org/wiki/Sustantivo)
14.|NNP|Proper noun, singular -> [Nombre propio, singular](https://es.wikipedia.org/wiki/Nombre_propio)
15.|NNPS|Proper noun, plural -> [Nombre propio, plural](https://es.wikipedia.org/wiki/Nombre_propio)
16.|PDT|Predeterminer -> [Predeterminante](https://es.wikipedia.org/wiki/Determinante_(ling%C3%BC%C3%ADstica))
17.|POS|Possessive ending -> No hay similar en español, es el `´s` de `this is Peter´s car`
18.|PRP|Personal pronoun -> [Pronombre personal](https://es.wikipedia.org/wiki/Pronombre_personal)
19.|PR$|Possessive pronoun -> [Pronombre posesivo](http://www.wikilengua.org/index.php/Pronombre_posesivo)
20.|RB|Adverb -> [Adverbio](https://es.wikipedia.org/wiki/Adverbio)
21.|RBR|Adverb, comparative [Adverbio comparativo](http://www.gramaticas.net/2010/09/ejemplos-de-adverbios-comparativos.html)
22.|RBS|Adverb, superlative [Adverbio superlativo](http://www.wikilengua.org/index.php/Grado_superlativo#El_grado_superlativo_en_adverbios)
23.|RP|Particle
24.|SYM|Symbol
25.|TO|to
26.|UH|Interjection ->[Interjección](https://es.wikipedia.org/wiki/Interjecci%C3%B3n)
27.|VB|Verb, base form
28.|VBD|Verb, past tense
29.|VBG|Verb, gerund or present participle
30.|VBN|Verb, past participle
31.|VBP|Verb, non-3rd person singular present
32.|VBZ|Verb, 3rd person singular present
33.|WDT|Wh-determiner
34.|WP|Wh-pronoun
35.|W$|Possessive wh-pronoun
36.|WRB|Wh-adverb


----
## ..Continuara

