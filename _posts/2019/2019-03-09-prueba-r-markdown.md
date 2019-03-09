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

R Markdown
----------

¿R, gráficos, markdown y `githup-pages`? ¿Se puede? ¿Desde `rstudio`?

Bueno, generarar blogs con markdown es sumamente sencillo y está [super documentado](https://bookdown.org/yihui/blogdown/), sin embargo, cuando tienes unas necesidades algo más simples y limitadas, la anterior solución es un poco mucho. El escenario: poder a partir de un código R markdown, generar el contenido markdown especifico para subir a un sitio `github` ya existente, junto con las imagenes relacionadas. No parece mucho, pero tiene su complejidad.

### 1. Instalar `jeckyllthat`

    install.packages("devtools")
    library(devtools)
    install_github("ColinFay/jekyllthat")

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

``` r
summary(cars)
```

    ##      speed           dist       
    ##  Min.   : 4.0   Min.   :  2.00  
    ##  1st Qu.:12.0   1st Qu.: 26.00  
    ##  Median :15.0   Median : 36.00  
    ##  Mean   :15.4   Mean   : 42.98  
    ##  3rd Qu.:19.0   3rd Qu.: 56.00  
    ##  Max.   :25.0   Max.   :120.00

Including Plots
---------------

You can also embed plots, for example:

![](/images/2019/2019-03-09-prueba-r-markdown_files/figure-markdown_github/pressure-1.png)

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
