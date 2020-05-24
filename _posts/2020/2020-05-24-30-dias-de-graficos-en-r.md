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
<https://twitter.com/R4DS_es> lanzó un lindo desafío, \[30 gráficos
30\]\[proyeccto\], uno distinto por día. Esta es la lista
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
  - Probar y eventualmente ajustar, mi tema personalizado
    [`ggelegant`](https://github.com/pmoracho/ggelegant)
  - Tratar de usar datos actuales y no apoyarme en gráficas de ejemplo
    ya resueltas

### Día 1: Gráfico de barras / Columnas

Una gráfica de barras o columnas, tal vez una de las más clásicas,
muestran comparaciones numéricas entre una variable discreta y una serie
de los valores continuos que toma cada una de estas variables discretas
o “categorías”. A diferencia de un histograma, los gráficos de barra no
muetran un desarrollo continuo entre las categorías.

``` r
library("tidyverse")

if ("ggelegant" %in% rownames(installed.packages())) {
  library("ggelegant")
} else {
  # devtools::install_github("pmoracho/ggelegant")
  theme_elegante_std <- function(base_family) {}
}

covid.data <- read.csv("https://opendata.ecdc.europa.eu/covid19/casedistribution/csv", na.strings = "", fileEncoding = "UTF-8-BOM",
                       stringsAsFactors = FALSE)

last_date <- max(as.Date(covid.data$dateRep,"%d/%m/%Y"))
covid.data %>% 
  filter(countriesAndTerritories %in% c('Argentina','Brazil', 'Chile', 'Bolivia', 'Paraguay', 'Uruguay')) %>% 
  group_by(countriesAndTerritories) %>% 
  summarize(casos = sum(cases), fallecidos = sum(deaths)) %>% 
  ungroup() %>% 
  select(pais = countriesAndTerritories, casos, fallecidos) %>% 
  gather(referencia, cantidad, -pais) %>% 
  ggplot(aes(x=pais, fill=referencia, y=cantidad)) +
    geom_col(position=position_dodge(width=1)) +
    geom_text(aes(label = format(cantidad, digits=0, big.mark = ',')),  vjust = .6, hjust=1.1,
              position = position_dodge(width=1)) +
    coord_flip() +
    scale_y_log10(
      breaks = scales::trans_breaks("log10", function(x) 10^x),
      labels = scales::trans_format("log10", scales::math_format(10^.x))
    ) +
    labs(title = paste("COVID-19"), 
       subtitle = paste("Relación Casos / fallecidos Argentina y vecinos al: ", last_date) , 
       caption = "Fuente: https://opendata.ecdc.europa.eu/covid19/casedistribution/csv", 
       y = "log10(Cantidad)", 
       x = "País"
    ) +
    scale_fill_discrete(palette = function(x) c("#67a9cf", "#ef8a62")) +
    theme_elegante_std(base_family = "Ralleway") 
```

<img src="/images/2020/2020-05-24-30-dias-de-graficos-en-r_files/figure-gfm/dia1-1.png" style="display: block; margin: auto;" />
