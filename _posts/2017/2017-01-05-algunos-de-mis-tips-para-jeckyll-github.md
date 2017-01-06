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
title: Algunos de mis "tips" para Jeckyll & Github
description: Algunos de mis "tips" para Jeckyll & Github
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

