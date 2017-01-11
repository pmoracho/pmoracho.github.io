---
layout: post
published: true
date: '2017-01-05'
modified: '2017-01-05'
imagefeature: sitelogo.png
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
title: Algunos de mis "tips" para Jeckyll y Github
description: Algunos de mis "tips" para Jeckyll y Github
tags:
  - desarrollo
  - jeckyll
  - github
---
## Comprimir HTML

Sin plugins ni cosas raras. Desde [aquí](http://jch.penibelst.de/) podremos bajar un "template", compress.html que deberemos incluir en la página default.html de la siguiente forma

```
---
layout: compress
---
```

Luego, simplemente agregar las opcion de configuración en `_config.yml`

```
compress_html:
  clippings: all
  comments: ["<!-- ", " -->"]
  endings: all
  ignore:
    envs: [local]
  blanklines: false
  profile: false
  startings: [html, head, body]
```
**Nota**: El "strip" de los comentarios puede generar distintos problemas en particular si el comienzo o el final del mismo no está correctamente escrito. Tener en cuenta el espacio final e inicial siempre. Por otro lado, si usan **disqus**, hay que editar de `disqus_comments.html` el dósigo: `(function() { // DON'T EDIT BELOW THIS LINE` y quitar el comentario, sino es probable que deje de funcionar.

## sitemap.xml

Primero que nada, limpiar páginas y "posts" que no queremos en el sitemap de la siguiente forma:

```
sitemap:
  exclude: 'yes'
```

Con esto se quta casi todo, pero por alguna razón extraña a mi particularmente y entiendo que a otros usuarios les suele incluir archivos de otra índole que no son efectivamente páginas, eso es por que jeckyll entiende todo archivo que nos es un "post" como una página. En el caso de el alojamiento y generación dinámica en Github lo que me ven´ñia ocurriendo es que me incluía un arachivo `..assets/style.css`, no era de los míos por lo que no lo podía excluir opté por lo más fácil que me pareció verificar si la "página" finalizaba con el texto "css" y entonces no considerarla en el sitemap. Acá va el código:

{% raw  %}
```markdown
---
layout: null
sitemap:
  exclude: 'yes'
---
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  {% for post in site.posts %}
    {% unless post.published == false %}
    <url>
      <loc>{{ site.url }}{{ post.url }}</loc>
      {% if post.sitemap.lastmod %}
        <lastmod>{{ post.sitemap.lastmod | date: "%Y-%m-%d" }}</lastmod>
      {% elsif post.date %}
        <lastmod>{{ post.date | date_to_xmlschema }}</lastmod>
      {% else %}
        <lastmod>{{ site.time | date_to_xmlschema }}</lastmod>
      {% endif %}
      {% if post.sitemap.changefreq %}
        <changefreq>{{ post.sitemap.changefreq }}</changefreq>
      {% else %}
        <changefreq>monthly</changefreq>
      {% endif %}
      {% if post.sitemap.priority %}
        <priority>{{ post.sitemap.priority }}</priority>
      {% else %}
        <priority>0.5</priority>
      {% endif %}
    </url>
    {% endunless %}
  {% endfor %}
  {% for page in site.pages %}
    {% assign txt = page.url | slice: -3, 3 %}
    {% unless page.sitemap.exclude == "yes" or txt == "css"  %}
    <url>
      <loc>{{ site.url }}{{ page.url | remove: "index.html" }}</loc>
      {% if page.sitemap.lastmod %}
        <lastmod>{{ page.sitemap.lastmod | date: "%Y-%m-%d" }}</lastmod>
      {% elsif page.date %}
        <lastmod>{{ page.date | date_to_xmlschema }}</lastmod>
      {% else %}
        <lastmod>{{ site.time | date_to_xmlschema }}</lastmod>
      {% endif %}
      {% if page.sitemap.changefreq %}
        <changefreq>{{ page.sitemap.changefreq }}</changefreq>
      {% else %}
        <changefreq>monthly</changefreq>
      {% endif %}
      {% if page.sitemap.priority %}
        <priority>{{ page.sitemap.priority }}</priority>
      {% else %}
        <priority>0.3</priority>
      {% endif %}
    </url>
    {% endunless %}
  {% endfor %}
</urlset>
```
{% endraw  %}


