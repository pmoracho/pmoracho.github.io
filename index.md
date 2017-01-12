---
layout: default
permalink: index.html
title: Blog de pmoracho
description: El blog de Patricio Moracho en github
published: true
---
![img006.xcf.bz2.jpg]({{site.baseurl}}/images/media/img006.xcf.bz2.jpg)

# Bienvenido

Esto no es más que otro blog personal. No esperen demasiado de él, es simplemente un lugar para dejar asentado aquello que necesito transcribir, datos, ideas, trabajos, proyectos, etc. Mis intereses son bien amplios, por lo seguramente el contenido va a ser bastante heterogéneo. Esta página principal, simplemente le muestra los últimos artículos escritos, tiene de todas formas, accesos a otras modalidades de lecturas:

* Usando el teclado, las teclas ESC o / le abrirán un cuadro de texto para escribir un busqueda completa en el sitio, hablando técnicamente de los últimos 1000 artículos o páginas, ya veremos cuando lleguemos a ese límite.
* Accediendo a [blog](/blog) podrá ver el contenido de forma histórica
* Sino, por medio de [etiquetas](/tags) se accede a una vista de las "etiquetas" que caracterizan lso distintos contenidos
* Por último [Archivo](/archive)

Suerte!!

---------------------------------------
<div class="posts">
  {% for post in site.categories.featured limit:5 %}
  <div class="post">
    <h1 class="post-title">
      <a href="{{ site.url }}{{ post.url }}">
        {{ post.title }}
      </a>
    </h1>
	{% if post.modified.size > 2 %}<span class="post-date indexpg" itemprop="dateModified" content="{{ post.modified | date: "%Y-%m-%d" }}"><i class="fa fa-edit" title="Última actualización"> {{ post.modified | date_to_string }}</i> <a href="{{ site.url }}/featured" title="Featured posts"><i class="fa fa-paperclip" title="Featured" class="social-icons"></i></a></span>{% else %}<span class="post-date indexpg" itemprop="datePublished" content="{{ post.date | date: "%Y-%m-%d" }}"><i class="fa fa-calendar" title="Publicado"> {{ post.date | date_to_string }}</i> <a href="{{ site.url }}/featured" title="Featured posts"><i class="fa fa-paperclip" title="Featured" class="social-icons"></i></a></span>{% endif %}
	{% if post.content contains '<!--break-->' %}
	  {{ post.content | split:'<!--break-->' | first }}
		<a href="{{ site.url }}{{ post.url }}" title="Read more"><strong> [Leer mas]</strong></a>
	{% else %}
    {{ post.content | strip_html | truncatewords:75 }}
		<a href="{{ site.url }}{{ post.url }}" title="Read more"><strong> [L	eer mas]</strong></a>
	{% endif %}
	
  </div>
  <hr class="transp">
  {% endfor %}
</div>

<div class="post">
  <h1 itemprop="name" class="post-title">{{ page.title }}</h1>
  <span class="post-date" itemprop="datePublished" content="{{ page.date | date: "%Y-%m-%d" }}"><i class="fa fa-calendar" title="Date published"> 
    <a class="permalink" href="{{ site.url }}{{ page.url }}" itemprop="url" title="Permanent link to this post">
      {% assign m = page.date | date: "%-m" %}
      {{ page.date | date: "%-d" }}
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
      {{ page.date | date: "%Y" }}
    </a></i>
  </span>
  {% include read_time.html %}
  {% if page.modified.size > 2 %}
    {% assign moddate = page.modified | date_to_string %}
    {% assign pgdate = page.date | date_to_string %}
  {% unless moddate == pgdate | date_to_string %}
  <span class="post-date" itemprop="dateModified" content="{{ page.modified | date: "%Y-%m-%d" }}">
    <i class="fa fa-edit" title="Last updated"> 
      {% assign m = page.modified | date: "%-m" %}
      {{ page.modified | date: "%-d" }}
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
      {{ page.modified | date: "%Y" }}
    </i>
  </span>{% endunless %}{% endif %}
  <span class="post-tags" itemprop="keywords" content="{{ page.tags | array_to_sentence_string }}">{% for tag in page.tags %}{% if forloop.first %}<i class="fa fa-tags" title="page tags"></i>{% endif %} <a href="{{ site.url }}/tags/#{{ tag | cgi_escape }}" title="Pages tagged {{ tag }}" rel="tag">{{ tag }}</a>{% unless forloop.last %} &bull; {% endunless %}{% endfor %}</span>
    {% unless page.show_meta == false %}
      {% include meta_info.html %}
    {% endunless %}
  {{ content }}
  <hr>
</div>

<h3 class="post-title">
<div class="pagination" style="margin: 0.5rem;">
    <a class="pagination-item older" href="{{ site.url }}/blog"><i class="fa fa-edit"> Blog</i></a>
    <a class="pagination-item newer" href="{{ site.url }}/tags"><i class="fa fa-tags"> Etiquetas</i></a>
</div>
</h3>
