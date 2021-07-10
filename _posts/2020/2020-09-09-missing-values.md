---
title: "Missing Values"
author: "Patricio Moracho"
date: "9 de sseptiembre del 2020"
post_date: 2020-09-09
layout: post
categories: cat
excerpt_separator: <!--more-->
published: false
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
description: Información útil para usar Visual Code
tags:
- desarollo
- r
- datos
output:
  github_page:
    jekyllthat::jekylldown
  pdf_document: default
  html_document:
    df_print: paged
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
