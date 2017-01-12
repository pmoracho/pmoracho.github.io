---
layout: default
permalink: index.html
title: Blog de pmoracho
description: El blog de Patricio Moracho en github
published: true
---
![img006.xcf.bz2.jpg]({{site.baseurl}}/images/media/img006.xcf.bz2.jpg)

# Bienvenido

Esto no es más que otro blog personal. No esperen demasiado de él, es simplemente un lugar para dejar asentado aquello que necesito transcribir, datos, ideas, trabajos, proyectos, etc. Mis intereses son bien amplios, por lo seguramente el contenido va a ser bastante heterogéneo. Tiene varias formas para acceder a este sitio:

* Usando el teclado, las teclas ESC o / le abrirán un cuadro de texto para escribir un búsqueda completa en el sitio, hablando técnicamente de los últimos 1000 artículos o páginas, ya veremos cuando lleguemos a ese límite.
* Accediendo a [blog](/blog) podrá ver el contenido de forma histórica
* Sino, por medio de [etiquetas](/tags) se accede a una vista de las "etiquetas" que caracterizan los distintos contenidos
* Por último [Archivo](/archive) para ver el contenido cronológicamente

Suerte!!

---------------------------------------

<div class="home">
  <ul class="post-list">
    {% for post in site.posts %}
      <li>
          <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">
            {{ post.title }}
            <div class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</div>
          </a>
      </li>
    {% endfor %}
  </ul>
</div>
