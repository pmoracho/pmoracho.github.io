---
layout: default
permalink: index.html
title: Blog de pmoracho
description: El blog de Patricio Moracho en github
published: true
---
![img006.xcf.bz2.jpg]({{site.baseurl}}/images/media/img006.xcf.bz2.jpg)

# Bienvenido

Esto no es más que otro blog personal. No esperen demasiado de él, es simplemente un lugar para dejar asentado aquello que necesito transcribir, datos, ideas, trabajos, proyectos, etc. Mis intereses son bien amplios, por lo seguramente el contenido va a ser bastante heterogeneo. 

Suerte!!



##Recuerde que Puede presionar <ESC> o '/' para hacer una búsqueda en el sitio

<div class="super-search" id="js-super-search">
	<a href="javascript:void(0)" onclick="superSearch.toggle()" class="super-search__close-btn">X</a>
	<input type="text" placeholder="Ingrese el texto a buscar" class="super-search__input" id="js-super-search__input">
	<ul class="super-search__results" id="js-super-search__results"></ul>
</div>

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
		<a href="{{ site.url }}{{ post.url }}" title="Read more"><strong> [Leer mas]</strong></a>
	{% endif %}
	
  </div>
  <hr class="transp">
  {% endfor %}
</div>

<div class="posts">
  {% for post in site.posts limit:5 %}
  {% unless post.category contains "featured" %}
  <div class="post">
    <h1 class="post-title">
      <a href="{{ site.url }}{{ post.url }}">
        {{ post.title }}
      </a>
    </h1>
	{% if post.modified.size > 2 %}
		<span class="post-date indexpg" itemprop="dateModified" content="{{ post.modified | date: "%Y-%m-%d" }}">
		<i class="fa fa-edit" title="Última actualización"> 
		{% assign m = post.modified | date: "%-m" %}
		{{ post.modified | date: "%-d" }}
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
		{{ post.modified | date: "%Y" }}
		</i>
		</span>
	{% else %}
		<span class="post-date indexpg" itemprop="datePublished" content="{{ post.date | date: "%Y-%m-%d" }}">
		<i class="fa fa-calendar" title="Publicado"> 
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
		</i>
		</span>
	{% endif %}
	{% if post.content contains '<!--break-->' %}
	  	{{ post.content | split:'<!--break-->' | first }}
		<a href="{{ site.url }}{{ post.url }}" title="Read more"><strong> [Leer mas]</strong></a>
	{% else %}
    	{{ post.content }}
	{% endif %}
  </div>
  {% unless forloop.last %}<hr class="transp">{% endunless %}
  {% endunless %}
  {% endfor %}
</div>
<h3 class="post-title">
<div class="pagination" style="margin: 0.5rem;">
    <a class="pagination-item older" href="{{ site.url }}/blog"><i class="fa fa-edit"> Blog</i></a>
    <a class="pagination-item newer" href="{{ site.url }}/tags"><i class="fa fa-tags"> Etiquetas</i></a>
</div>
</h3>
