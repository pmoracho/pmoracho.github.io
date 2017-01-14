---
layout: default
permalink: index.html
title: Blog de pmoracho
description: El blog de Patricio Moracho en github
published: true
---
![front-page.jpg]({{site.baseurl}}/images/front-page.jpg)

# Bienvenido

Esto no es más que otro blog personal. No esperen demasiado de él, es simplemente un lugar para dejar asentado aquello que necesito transcribir, datos, ideas, trabajos, proyectos, etc. Mis intereses son bien amplios, por lo seguramente el contenido va a ser bastante heterogéneo. Tiene varias formas para acceder a este sitio:

* Usando el teclado, las teclas ESC o / le abrirán un cuadro de texto para escribir un búsqueda completa en el sitio, hablando técnicamente de los últimos 1000 artículos o páginas, ya veremos cuando lleguemos a ese límite.
* Accediendo a [blog](/blog) podrá ver el contenido de forma histórica
* Sino, por medio de [etiquetas](/tags) se accede a una vista de las "etiquetas" que caracterizan los distintos contenidos
* Por último [Archivo](/archive) para ver el contenido cronológicamente

Suerte!!





..
--------------------------------------

# Últimos 10 artículos

<div class="home">
  <ul class="post-list">
    {% for post in site.posts limit: 10 %}
      <li>
          <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">
          {{ post.title }}
          </a>
          <div class="post-meta">
		  {% assign m = post.date | date: "%-m" %}
		  {{ post.date | date: "%-d" }}
		  {% case m %}
			{% when '1' %}Ene
			{% when '2' %}Feb
			{% when '3' %}Mar
			{% when '4' %}Abr
			{% when '5' %}May
			{% when '6' %}Jun
			{% when '7' %}Jul
			{% when '8' %}Ago
			{% when '9' %}Sep
			{% when '10' %}Oct
			{% when '11' %}Nov
			{% when '12' %}Dec
		  {% endcase %}
		  {{ post.date | date: "%Y" }}
		  </div>
      </li>
    {% endfor %}
  </ul>
</div>
