---
layout: post
published: false
date: '2020-09-09'
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
title: "Missing Values"
description: 
tags:
  - desarrollo
---

# Valores faltantes
## Apuntes

### Tipos de valores faltantes

* Valores explicítos
    * `<NA>`
    * Otros específicos dependientes del contexto (datos) por ejemplo
      `"falta"`, `"N/D"`, o bien código previamente documentados: `-1`, `999`,
      etc.
    * Otros más ambiguos: espacios, blancos (`""`), en particular cuidado con el
      `NULL`, este valor, muy común en bases de datos, puede representar la
      inexistencia de una variable, por ejemplo cuando se producen en un `JOIN`
      a diferencia de un `NA` dónde la variable existe pero no tiene un valor
      conocido.

* Valores implícitos


### Imputación
