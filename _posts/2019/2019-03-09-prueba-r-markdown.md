---
title: "Prueba R Markdown Post"
author: Patricio Moracho
post_date: 2019-03-09
layout: post
permalink: /prueba-r-markdown/
categories: cat
output: 
  github_page:    
    jekyllthat::jekylldown
  pdf_document: default
  html_document:
    df_print: paged
excerpt_separator: <!--more-->
published: true
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
description: Ejemplos de gráficos con Ggplot2 y el theme_elegante()
tags:
  - desarrollo
  - R
---

¿R, gráficos, markdown y `githup-pages`? ¿Se puede? ¿Desde `rstudio`?

Bueno, generarar blogs con markdown es sumamente sencillo y está [super documentado](https://bookdown.org/yihui/blogdown/), sin embargo, cuando tienes unas necesidades algo más simples y limitadas, la anterior solución es un poco mucho. El escenario: poder a partir de un código R markdown, generar el contenido markdown especifico para subir a un sitio `github` ya existente, junto con las imagenes relacionadas. No parece mucho, pero tiene su complejidad.

### 1. Instalar [`jeckyllthat`](https://github.com/ColinFay/jekyllthat)

    install.packages("devtools")
    library(devtools)
    install_github("ColinFay/jekyllthat")

### 2. Crear un nuevo documento `.Rmd`

Modificar la cabecera del mismo por un modelo como el siguiente:

    ---
    title: "Prueba R Markdown Post"
    author: Patricio Moracho
    post_date: 2019-03-09
    layout: post
    permalink: /prueba-r-markdown/
    categories: cat
    output: 
      github_page:    
        jekyllthat::jekylldown
      pdf_document: default
      html_document:
        df_print: paged
    excerpt_separator: <!--more-->
    published: true
    show_meta: true
    comments: true
    mathjax: false
    gistembed: false
    noindex: false
    hide_printmsg: false
    sitemap: true
    summaryfeed: false
    description: Ejemplos de gráficos con Ggplot2 y el theme_elegante()
    tags:
      - desarrollo
      - R
    ---

### 3. Configurar la sección `setup` del documento

Es muy importante configurar `knitr::opts_knit$set(base.url='/images/2019/')`, `base_url` debería apuntar a la carpeta dónde se lojan las imagenes. En mi caso, las administro en una estructura como: `<root>/images/<year>`.

### 4. Ejecutar `knitr`, con la opción de salida de `jekylldown`

Esto generará estos documentos

    <titulo-del-post>.md
    <titulo-del-post>/*.png

Lo que resta es:

-   Copiar `<titulo-del-post>.md` a la carpeta de los posts, por ejemplo `_posts/2019`
-   Copiar la carpeta completa y sus archivos `<titulo-del-post>/*.png` a la carpeta de las imagenes que habíamos definido antes, por ejemplo `images/2019`

Y ahora sí, ¿podemos incluir un plot?

``` r
plot(pressure)
```

<img src="/images/2019/2019-03-09-prueba-r-markdown_files/figure-markdown_github/pressure-1.png" width="50%" style=display: block; margin-left: auto; margin-right: auto; width: 50%;" />
