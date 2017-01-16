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

``` yaml
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

Realmente funciona muy bien y comprime significativamente cada página.

**Nota**: El "strip" de los comentarios puede generar distintos problemas en particular si el comienzo o el final del mismo no está correctamente escrito en cualquier página. Tener en cuenta el espacio final e inicial siempre. Por otro lado, si usan **disqus**, hay que editar de `disqus_comments.html` el código: `(function() { // DON'T EDIT BELOW THIS LINE` y quitar el comentario, sino es probable que deje de funcionar.

## Sitemap.xml

Primero que nada, limpiar páginas y "posts" que no queremos en el sitemap editando cada archivo y agregando el siguiente código en la cabecera:

``` yaml
---
sitemap:
  exclude: 'yes'
---  
```

Con esto se quita casi todo, pero por alguna razón extraña, a mi particularmente y entiendo que a otros usuarios, les suele incluir archivos de otra índole que no son efectivamente páginas. Esto es por que Jeckyll entiende todo archivo que nos es un "post" como una página. En el caso del alojamiento y generación dinámica en Github lo que me venía ocurriendo es que me incluía un archivo `..assets/style.css`, no era de los míos por lo que no lo podía excluir. Opté por lo más fácil que me pareció, verificar si la "página" finalizaba con el texto "css" y entonces no considerarla en el sitemap. Acá va el código:

``` liquid
---
layout: null
sitemap:
  exclude: 'yes'
---
{% raw  %}
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
{% endraw  %}
</urlset>
```

## Syntax highlighting 

Jeckyll para "iluminar" código utiliza por defecto [rouge](https://github.com/jneen/rouge). La lista de lenguajes soportados se puede consultar [aquí](https://github.com/jneen/rouge/wiki/List-of-supported-languages-and-lexers). Configurar un bloque de código en un "post" es tan simple como esto:

```
	``` js
	function getPostsFromXml(xml) {
		var json = xmlToJson(xml);
		// Atom 1.0 format
		if (json.entry && json.entry instanceof Array) {
			return json.entry;
		}
		// Atom 2.0 format
		else {
			return json.channel.item;
		}
	}
	```
```

esto terminará mostrandose así:

``` js
function getPostsFromXml(xml) {
	var json = xmlToJson(xml);
	// Atom 1.0 format
	if (json.entry && json.entry instanceof Array) {
		return json.entry;
	}
	// Atom 2.0 format
	else {
		return json.channel.item;
	}
}
```


Si hay que mostrar código "liquid", el problema que tenemos es que Jeckyll lo considerará código y lo interpretará por lo que la solución es incluir:

``` liquid
{% raw  %}
{% aca va el codigo que se quiere mostrar %}
{% endraw %}

```
Nota: el end raw se escribió con un un espacio entre end y raw intencionalmente para poder "dibujarlo" quitárselo cuando se use


## Enlaces interesantes

* [Liquid text help](http://shopify.github.io/liquid/tags/iteration/)
* [Temas](http://jekyllthemes.org/)
