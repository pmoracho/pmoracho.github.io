---
layout: post
published: true
date: '2019-05-06'
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
title: Trucos para Github
description: Lista de trucos y "tips" para trabajar con github
tags:
  - desarrollo
  - github
---

## 1. Copiar `README.md` a el branch `gh-pages`

El escenario es: tenemos una página del proyecto mantenida en `github` con el
branch `gh-pages`, queremos que la página de inicio, es decir `index.md`, sea
una copia del `README.md` del proyecto. Por ahora, la forma más barata de
hacerlo es:

```sh

# Nos vamos al branch de la página del proyecto
git checkout gh-pages
# Actualizamos lo que tengamos en el remoto
git pull

# Copiamos de master el README en index
git checkout master -- README.md
cp README.md index.md
rm README.md

# Actualizamos los cambios
git add -u
git commit -m "cambios en el index.md"
git push

git checkout master
```

O en una sola linea:

```sh
git checkout gh-pages;git pull;git checkout master -- README.md;cp README.md index.md;rm README.md;git add -u;git commit -m "cambios en el index.md";git push;git checkout master
```


