---
title: "¿Qué puede salir mal? (II)"
author: "Patricio Moracho"
date: 2025-12-18
post_date: 2025-12-18
layout: post
categories: cat
excerpt_separator: <!--more-->
published: true
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
description: Sobre la fragilidad de los sistemas de software y las mil y una forma en que los desarrolladores pueden meter la pata.
tags:
  - desarrollo
  - seguridad
output:
  github_page:
    jekyllthat::jekylldown
  pdf_document: default
  html_document:
    df_print: paged
---


Retomando el post anterior: [¿Qué puede salir mal? (I)], y a mode de
"previosly": tenemos una librería popular en el ecosistema de JavaScript, con
una función `fsSize()` que recibe un parámetro `drive` y obtiene información
sobre los sistemas de archivos montados en un sistema Windows, mediante la
ejecución de un comando PowerShell. Al no sanitizar el parámetro `drive`, un
usuario malintencionado puede inyectar comandos arbitrarios. Como decía antes,
el mensaje es nunca confiar en la entrada del usuario. Pero me han surgido
preguntas de todo esto en las que me gustaría desvariar un poco.

## ¿Debiera el autor de la librería haber cuidado el parámetro `drive`?

Es una pregunta totalmente válida, supongo que los fundamentalistas de la
seguridad dirán que si, ahora bajo ese principio, deberíamos aplicar
sanitizadado a cada una de las eventuales capas de software, imaginemos
simplemente un árbol de llamadas:

```
procesa_entrada_usuario(drive) -> proceso_datos(drive) -> fsSize(drive)
```

¿Debería cada una de esas funciones sanitizar el parámetro `drive`? ¿tiene
sentido? Posiblemente es una redundancia innecesaria, sin embargo, hay un
criterio que a mi me parce fundamental: **la remediación lo más cerca del
problema**, con esto quiero decir que si la función `fsSize()` es la que ejecuta
el comando PowerShell, entonces es la que debiera sanitizar el contenido de los
parámetros recibidos. El otro problema podría ser la entrada misma de los datos,
por lo que podríamos considerar que la sanitización debiera hacerse sobre
`procesa_entrada_usuario()`, un criterio bastante extendido, pero para que esto
sea completamente efectivo, nos exige un conocimiento profundo de toda la cadena
de llamadas hacia `fsSize()`, lo cual no siempre es posible.

Por otro lado, si la función `fsSize()` es parte de una librería pública, cómo
lo es en este caso [systeminformation], dónde justamente no hay control sobre la
cadena de llamadas, hay una cuestión si se quiere hasta económica: asegurar la
librería va a ser más eficiente que pedir a cada uno de los usuarios de la misma
sanitice antes los parámetros.

Por lo que a esta pregunta, yo respondería si, la función `fsSize()` debiera
sanitizar el parámetro `drive`. De hecho el autor ya ha publicado una fix para
el problema. 

![codesnapshot-fixed.png]({{site.baseurl}}/images/2025/codesnapshot-fixed.png)

Aunque, como desarrollador de software, siempre me reservaría el
derecho a crear funciones totalmente inseguras, siempre y cuando quede claro
esto en la documentación o que forme parte de un proyecto cerrado donde hay solo
una cadena de llamadas perfectamente clara y controlada, o dónde la seguridad no
sea un requisito.

## ¿Por que debería preocuparme de problemas por fuera del alcance del requerimiento que busca resolver la función?

Como desarrolladores de la era clásica, la seguridad no ha sido nunca un
requisito salvo dominios muy específicos. ¿Por que debería preocuparme de
problemas por fuera del alcance del requerimiento que se busca resolver? ¿No es
suficiente ya tener que lograr que la función o el programa hagan bien su
trabajo? Bueno, creo que la época dorada de la programación ya pasó hace tiempo.

Hoy en día, la seguridad es un requisito transversal a todo desarrollo de
software, y no podemos darnos el lujo de ignorarlo. Si no nos ocupamos, alguien,
tarde o temprano, lo hará por nosotros, y las consecuencias pueden ser nefastas.
Podés seguir pensando que la seguridad es un tema del equipo de Ciberseguridad,
que eso no es tu problema, pero la realidad es que cuando se comprometa un
servidor o una infraestructura por que no te tomaste el trabajo de sanitizar un
parámetro, la respuesta "eso no es era mi problema" no va a ser muy bien
recibida. Ocuparse de la seguridad hoy en día debiera ser parte del ADN de todo
desarrollador de software, casi diría que hoy las escuelas y universidades
debieran enseñar cuestiones básicas de seguridad antes siquiera de ver
algoritmos o lenguajes. Ocuparse de la seguridad ¿nos hará inmunes a los
ataques? claro que no, no nos engañemos, siempre estaremos atrás del problema, y
además somos humanos, y algunos tenemos una naturaleza más propensa a cometer
errores, pero, en definitiva la dicotomía es: mantener una serie de hábitos
seguros, de buenas prácticas, y una mentalidad orientada a la seguridad versus
"eso no es era mi problema".

## ¿Es correcto que una librería de software ejecute un shell command para averiguar una información puntual?

Acá no tengo muchas dudas. El acceso a una shell en cualquier sistema, librería
o base de datos debería estar prohibido en la constitución nacional de cada
país. Es super cómodo, sin duda, necesitás listar una carpeta, averiguar el
espacio libre, mover un archivo, todo disponible desde un solo lugar, pero es el
clásico "matar mosquitos a cañonazos". Acá juega el principio de mínima
responsabilidad, tratemos de evitar a toda costa el uso de shell commands, es
preferible usar APIs nativas del lenguaje o del mismo sistema operativo si las
hay. El uso de shell commands es peligroso, debiera ser la última opción, cuando
realmente con contamos con otras alternativas y debiéramos prestar especial
cuidado a el tratamiento de los parámetros de entrada.

## ¿Como desarrollador que consume esta librería, no debería haber sanitizado el parámetro `drive` antes de pasarlo a la función `fsSize()`?

Pues claro que sí, pero para hacerlo, deberíamos en primer lugar confirmar que
la función es insegura y para eso hay que invertir tiempo y voluntad en revisar
el código, y entender por dónde "nos pueden entrar" o ser más salomónico y
considerar que todo código de terceros que usemos es inherentemente inseguro.
Cualquiera de las dos visiones nos imponen un costo extra de tiempo y recursos,
que la mayoría de las veces, no disponemos y para peor, las empresas
(seguramente con excepciones) ni siquiera están dispuestas a pagar.

Sin embargo, el horizonte no es tan negro, un entrenamiento constante en formar
el hábito de la seguridad junto con las multiples herramientas de análisis de
código, nos va a permitir identificar más rápidos estos posibles patrones
inseguros, en código propio y en los de terceros.

## ¿Nos olvidamos del principio de mínima responsabilidad al hacer más segura una función?

Si pero no. Desde la perspectiva del desarrollo seguro, podríamos redefinir este
concepto, al del **principio de mínima responsabilidad para una función
segura**: dónde una función debiera cumplir un único objetivo específico tal
como fue diseñada, evitando cualquier comportamiento inesperado. Con esta
actualización, hacer más segura una función no significa que le estemos
imponiendo más tareas, sino, que la única responsabilidad que tiene se cumpla de
la forma que dicta el principio.

## ¿Deberíamos depender menos de librerías de terceros para funciones tan puntuales como esta?

Y depende, por ejemplo: ¿tengo la capacidad, el tiempo o los recursos para
desarrollar y mantener una funcionalidad como la de `newtonsoft/json` para el
manejo de JSON en .NET? ¿Y podría desarrollarlo de forma mas segura? Ahora, si
solo necesito una función puntual como `fsSize()`, que eventualmente no sería
tan difícil de implementar, ¿voy a buscar una librería de terceros? Creo que
entre estos dos extremos, hay un abanico de posibilidades que dependen del
contexto y los recursos disponibles. Hay algo algunos principios que podemos
seguir:

* Menos código = menos problemas
* Menos dependencias = mayor seguridad
* No reinventar la rueda = mas tiempo para otras cosas
* Función sensible o crítica = desarrollar internamente

[CVE]: https://nvd.nist.gov/vuln/detail/CVE-2025-68154
[systeminformation]: https://github.com/sebhildebrandt/systeminformation
[¿Qué puede salir mal? (I)]: {{site.baseurl}}/_posts/2025/2025-12-18-que-puede-salir-mal-1.md