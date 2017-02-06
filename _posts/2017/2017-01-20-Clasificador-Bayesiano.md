---
layout: post
title: Clasificador Bayesiano
date: 2017-01-20
description: Clasificador Bayesiano
tag:
  - nltk
  - desarrollo
  - python
  - ia
show_meta: true
comments: true
published: true
imagefeature: sitelogo.png
mathjax: true
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
---

### $$ \mathrm{classify}(f_1,\dots,f_n) = \underset{c}{\operatorname{argmax}} \ p(C=c) \displaystyle\prod_{i=1}^n p(F_i=f_i\vert C=c). $$

O en español "Clasificador Bayesiano ingenuo", sugún la
[wikipedia](https://es.wikipedia.org/wiki/Clasificador_bayesiano_ingenuo), se
trata de un clasificador probabilístico fundamentado en el teorema de
[Bayes](https://es.wikipedia.org/wiki/Teorema_de_Bayes), el cual expresa en
términos poco matemáticos, la probabilidad de que se de un evento **A**
sabiendo que ha ocurrido un evento **B** (probabilidad condicionada), o lo que
sería la causalidad de **B** sobre **A**.  

Un ejemplo que se suele usar para explicar este teorema es el siguiente:
Sabemos que el 90% de los pacientes con gripe tienen fiebre, por lo que la
relación causa/efecto en este caso es del 90%, sin embargo ¿podemos calcular lo
inverso, es decir, la probabilidad que la fiebre nos este indicando la
existencia de la gripe (efecto/causa)? Para esto necesitamos más información,
por un lado el total de los casos de pacientes con gripe y el total del los
casos de pacientes con fiebre.

Vamos a dibujar unos números a modo de ejemplo. Partimos de un conjunto amplio de pacientes:

* Se sabe que el 90% de el conjunto de pacientes con gripe tienen fiebre **(A)** 
* Se sabe que del universo total de pacientes, el 5% sufren de gripe **(B)** 
* Y que del universo total hay un 10% con fiebre (algunos por gripe y otros por alguna otra causa) **(C)** 


Tenemos ahora un paciente que acaba de llegar a nuestra "clínica", tiene
temperatura, ¿podemos determinar la probabilidad de que tenga gripe?. Si, de la
siguiente forma:

* **(A)** = 0.9
* **(B)** = 0.05
* **(C)** = 0.1


$$ P(enfermedad/síntoma) = P(síntoma/enfermedad)*P(enfermedad)/P(síntoma) $$

Es decir $$ P(enfermedad/síntoma) $$ = 0.9 * 0.05 / 0.1 = 0.45 

Es decir la existencia de temperatura en un paciente nos da una probabilidad de
un 45% que la misma se deba a una gripe.

Ahora bien, si hacemos esto mismo, con otros síntomas y calculamos la
probabilidad que estos sean efectivamente síntomas de una enfermedad y lo
hacemos además para un conjunto amplio de enfermedades y combinamos todos estos
números, lo que estamos construyendo es un **"Clasificador Bayesiano
Ingenuo"**, que podría llegar a indicar con bastante precisión a que enfermedad
correspondería una serie de síntomas relevados en el paciente. De hecho esto es
en definitiva algo bastante cercano a lo que hace un médico clínico todo el
tiempo, "pesar" mentalmente cada síntoma y llegar al diagnóstico más probable.

> **Nota**: Lo de "ingenuo", se debe a que este tipo de algoritmo considera
> cada "síntoma" de manera independiente, no contemplando las relaciones que
> pudieran existir entre ellos, esto es sin duda una debilidad para ciertas
> aplicaciones aún así, este clasificador es sumamente óptimo para muchas
> otras.

Un hecho importante y positivo de este tipo de clasificador, es que el "peso" o
"probabilidad" de una variable	en la clasificación puede ser muy importante en
relación a la causa pero ser además muy común en otras, con lo cual el "peso"
final disminuye.  Pensemos este mismo ejemplo, pero suponiendo que el valor
**C** en vez de ser 10% es de un 20% (un 20% del universo total tiene fiebre
actualmente), la formula final quedaría así:

$$ P(enfermedad/síntoma) = P(síntoma/enfermedad)*P(enfermedad)/P(síntoma) $$

Es decir $$ P(enfermedad/síntoma) $$ = 0.9 * 0.05 / **0.2** = 0.225

Es decir el peso de la "fiebre" para clasificar un enfermedad como gripe habría
bajado al 22.5%. Lo que finalmente hace un clasificador de este tipo es "sumar"
los pesos de cada síntoma con relación a cada enfermedad que participe en
nuestro cálculo y llegar a un "score" final, y las "enfermedades" que
tengan el valor más alto son las que mayor probabilidad tienen de ser con
respecto a los síntomas.

Planteado de esta manera parece ser un "algoritmo" sencillo, y en la realidad
lo es. Implementarlo en cualquier lenguaje es algo bastante "trivial", lo
notable es que a pesar de su "simplicidad" es tremendamente efectivo en
muchísimos escenarios. 

## Implementando un "Bayesiano"

> **Nota**: Antes de empezar, recomiendo leer este muy buen
> [artículo](https://chatbotslife.com/text-classification-using-algorithms-e4d50dcba45#.ig9im3fie)
> de **George Kassabgi**, aclaro que seguramente no voy a agregar mucho más a lo
> que el expuso. 

No lo comenté antes, pero uno de los primeros y más extendidos usos de este
clasificador fue en la construcción de filtros de detección de Spam, si bien
estos filtros son cada vez más inteligentes es muy probable que cualquier
herramienta de correo hoy en dís siga implementando en parte un "Bayesiano" para
la detección de correo basura. Un problema similar a éste es el que voy a
plantear para ir desarrollando paso a paso.

### El problema

Soy un rutinario lector de "feeds", y como muchos uso
[feedly](https://feedly.com) en su versión gratuita, permite manejar hasta
100 fuentes de noticias distintas. Uno de los "feeds" con más actividad y que
actualmente suelo leer bastante, es el de noticias de
[Python](https://www.reddit.com/r/Python/.rss). No todo me interesa,
habitualmente lo que hago es tener una vista únicamente de títulos, y con la
misma voy haciendo una selección de lo que me interesa. Este proceso mental, de
discernir los temas que me resultan interesantes de los otros que no, es muy
parecido al lo que se espera de un clasificador "Bayesiano".

### Armando la solución

Es muy habitual que en cualquier algoritmo de IA y este clasificador entra de
esta categoría, se requiera tres elementos iniciales:

1. La implementación del algoritmo
2. Un set de datos para "entrenar" al algoritmo
3. Un set de datos para "probar" al algoritmo

Como soy ordenado, voy a empezar por el punto 2 y 3. Nuestro conjunto de
entrenamiento del algoritmo no va a ser más que un archivo tipo CSV dónde
tendremos una primer columna con el titulo del feed y una segunda dónde
indicaremos 1 = Me interesa, 0 = No me interesa. Algo así como esto:

```
What's everyone working on this week?,0
YAPF: A formatter for Python files,0
Python beginner courses,0
Deep Learning for Chess using Theano,1

``` 

Como pueden apreciar, un feed cuyo titulo diga **"What's everyone working on
this week?"** no me interesa, pero otro que diga **"Deep Learning for Chess using
Theano"** si capta mi atención.

¿Como armamos entonces el archivo de entrenamiento del clasificador? Lo primero
es recuperar los últimos "N" feeds y tomarse el tedioso trabajo de
clasificarlos (me interesa/no me interesa). Obtener los feeds es tan sencillo
como escribir y ejecutar este script en **Python**:


``` python
import feedparser

feed = feedparser.parse( "https://www.reddit.com/r/Python/.rss?limit=100" )
for post in feed.entries:
	print("{0},0".format(post.title.replace(',', ' ')))
```

Antes que nada, como verán estoy usando
[feedparser](http://pythonhosted.org/feedparser/) para la lectura de un
**rss**, la instalación es el habitual `pip install feedparser`, funciona bien
en **Python 3x**. Noten además que:

* Estoy recuperando los últimos 100 feeds mediante el parámetro de url
  `?limit=100`.
* Limpiamos el caractér `,` para que no nos traiga problemas de formato, ya
  veremos que este y otros caracteres no tienen participación en la clasificación

Ejecutamos y redirigimos la salida a un archivo de texto. Luego con la
herramienta que más nos guste, editamos el archivo y colocamos un 1 cuando un
titulo nos interese.

Con este archivo tendremos nuestro set de entrenamiento, pero además, nada nos
impide usarlo también como nuestro set de prueba, por lo cual ya hemos acabado
con los puntos 2 y 3. Pasemos ahora a lo jugoso del tema:

### Implementando el clasificador

**..en construcción**
