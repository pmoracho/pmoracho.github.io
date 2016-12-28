---
layout: default
permalink: index.html
title: Blog de pmoracho
description: "El blog de Patricio Moracho en github"
---
![Palier I](http://3.bp.blogspot.com/-OmhFAG6cKMI/UBgQEQHGnUI/AAAAAAAAAy8/DqaXCMY3Cj4/s1600/img006.xcf.bz2.jpg "Palier I")

# Bienvenido

Esto no es más que otro blog personal. No esperen demasiado de él, es simplemente un lugar para dejar asentado aquello que necesito transcribir, datos, ideas, trabajos, proyectos, etc. Mis intereses son bien amplios, por lo seguramente el contenido va a ser bastante heterogeneo. 

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
	{% if post.modified.size > 2 %}<span class="post-date indexpg" itemprop="dateModified" content="{{ post.modified | date: "%Y-%m-%d" }}"><i class="fa fa-edit" title="Última actualización"> {{ post.modified | date_to_string }}</i></span>{% else %}<span class="post-date indexpg" itemprop="datePublished" content="{{ post.date | date: "%Y-%m-%d" }}"><i class="fa fa-calendar" title="Publicado"> {{ post.date | date_to_string }}</i></span>  {% endif %}
	{% if post.content contains '<!--break-->' %}
	  {{ post.content | split:'<!--break-->' | first }}
		<a href="{{ site.url }}{{ post.url }}" title="Read more"><strong> [Leer mas]</strong></a>
	{% else %}
    {{ post.content | strip_html | truncatewords:75 }}
		<a href="{{ site.url }}{{ post.url }}" title="Read more"><strong> [Leer mas]</strong></a>
	{% endif %}
  </div>
  {% unless forloop.last %}<hr class="transp">{% endunless %}
  {% endunless %}
  {% endfor %}
</div>
<h3 class="post-title">
<div class="pagination" style="margin: 0.5rem;">
    <a class="pagination-item older" href="{{ site.url }}/blog"><i class="fa fa-edit"> Blog</i></a>
    <a class="pagination-item newer" href="{{ site.url }}/tags"><i class="fa fa-tags"> Tags</i></a>
</div>
</h3>
