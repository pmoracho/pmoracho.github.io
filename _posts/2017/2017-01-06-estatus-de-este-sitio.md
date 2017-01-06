---
layout: post
published: true
date: '2017-01-06'
modified: '2017-01-06'
imagefeature: sitelogo.png
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
title: Estatus de este sitio
description: Estatus de este sitio
---

## Estatus de este sitio

{% assign counter = 0 %}
{% for post in site.posts %}
  {% assign thisyear = post.date | date: "%B %Y" %}
  {% assign prevyear = post.previous.date | date: "%B %Y" %}
  {% assign counter = counter | plus: 1 %}
  {% if thisyear != prevyear %}
    {{ thisyear }} ({{ counter }})
    {% assign counter = 0 %}
  {% endif %}
{% endfor %}
