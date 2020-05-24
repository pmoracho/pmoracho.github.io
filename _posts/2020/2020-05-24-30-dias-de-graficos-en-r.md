---
title: "30 días de gráficos en R"
author: "Patricio Moracho"
date: "24 de mayo del 2020"
post_date: 2020-05-24
layout: post
categories: cat
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
description: 30 días de gráficos en R
tags: 
- desarrollo
- R
- ggplot2
output:
  github_page:    
    jekyllthat::jekylldown
  pdf_document: default
  html_document:
    df_print: paged
---

### Introducción

A raíz de que el 12 de mayo se conmemora el nacimiento de Florence
Nightingale, la enfermera creadora del diagrama de área polar y
referente femenina de la visualización de datos, la comunidad
<https://twitter.com/R4DS_es> lanzó un lindo desafío, [30 gráficos
30](proyeccto), uno distinto por día. Esta es la lista
completa:

| día | fecha       | desafío                                                       |
| --- | ----------- | ------------------------------------------------------------- |
| 1   | 12 de mayo  | barras / columnas                                             |
| 2   | 13 de mayo  | líneas                                                        |
| 3   | 14 de mayo  | puntos / burbujas                                             |
| 4   | 15 de mayo  | gráficos con facetas                                          |
| 5   | 16 de mayo  | diagramas de arco                                             |
| 6   | 17 de mayo  | gráficos de donut :doughnut:                                  |
| 7   | 18 de mayo  | gráficos ridgeline                                            |
| 8   | 19 de mayo  | gráficos de contorno                                          |
| 9   | 20 de mayo  | gráficos de áreas apiladas                                    |
| 10  | 21 de mayo  | ¡explorar paletas de colores\!                                |
| 11  | 22 de mayo  | mapas de calor (*heatmap*)                                    |
| 12  | 23 de mayo  | gráficos de paleta (*lollipop*) :lollipop:                    |
| 13  | 24 de mayo  | visualizar datos temporales                                   |
| 14  | 25 de mayo  | gráficos de rectángulos/árbol (*treemap*)                     |
| 15  | 26 de mayo  | dendorgamas                                                   |
| 16  | 27 de mayo  | gráficos de waffle :waffle:                                   |
| 17  | 28 de mayo  | diagramas de sankey                                           |
| 18  | 29 de mayo  | visualizar datos espaciales :world\_map:                      |
| 19  | 30 de mayo  | gráficos de flujo (*stream graph*)                            |
| 20  | 31 de mayo  | redes                                                         |
| 21  | 1 de junio  | gráficos con anotaciones                                      |
| 22  | 2 de junio  | visualizar datos textuales                                    |
| 23  | 3 de junio  | gráficos de proyección solar (*sunburst*)                     |
| 24  | 4 de junio  | coropletas                                                    |
| 25  | 5 de junio  | gráficos de violín :violin:                                   |
| 26  | 6 de junio  | diagramas de marimekko                                        |
| 27  | 7 de junio  | ¡gráficos animados\!                                          |
| 28  | 8 de junio  | diagramas de cuerdas                                          |
| 29  | 9 de junio  | gráficos de coordenadas paralelas                             |
| 30  | 10 de junio | diagramas de área polar o de :star:Florence Nightingale:star: |

### Objetivos

En lo personal, mi idea es aprovechar este desafío para:

  - Profundizar el conocimiento de **Ggplot2**, ya que intentaremos
    resolver todos usando este paquete
  - Porbar y eventualmente ajustar, mi tema personalizado
