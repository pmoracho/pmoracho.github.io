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
[@R4DS\_es](https://twitter.com/R4DS_es) lanzó un lindo desafío,
[proyecto](https://github.com/cienciadedatos/datos-de-miercoles/blob/master/30-dias-de-graficos-2020.md),
uno distinto por día. Esta es la lista
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

### Día 2: Gráfico de líneas

Son gráficos que muestras la evolución de una o más variables continuas
en un determinado intervalo, generalmente de tiempo. Lo habitual es que
el ejex Y tenga un valor cuantitativo y el eje X tiene la seceuncia que
representa el intervalo, En el plano cartesiano, se establecen los
puntos X, Y y luego se van conectando estos mediante líneaas rectas.

``` r
library("tidyverse")

if ("ggelegant" %in% rownames(installed.packages())) {
  library("ggelegant")
} else {
  # devtools::install_github("pmoracho/ggelegant")
  theme_elegante_std <- function(base_family) {}
}
  
covid.data <- read_csv('https://docs.google.com/spreadsheets/d/16-bnsDdmmgtSxdWbVMboIHo5FRuz76DBxsz_BbsEVWA/export?format=csv&id=16-bnsDdmmgtSxdWbVMboIHo5FRuz76DBxsz_BbsEVWA&gid=0')

last_date <- max(as.Date(covid.data$fecha,"%d/%m/%Y"))

covid.data %>% 
  filter(osm_admin_level_4 %in% c('CABA', 'Buenos Aires')) %>% 
  mutate(fecha = as.Date(fecha, "%d/%m/%Y")) %>% 
  select(dia=dia_inicio, distrito=osm_admin_level_4, cantidad=nue_casosconf_diff) %>% 
  ggplot(mapping=aes(x=dia, color=distrito, y=cantidad)) +
    geom_line() +
    geom_point() +
    geom_smooth(method = 'loess',
                formula = 'y ~ x', alpha = 0.2, size = 1, span = .3, se=FALSE) + 
    labs(title = paste("COVID-19 en Argentina"), 
       subtitle = paste0("Variación de los casos diarios en CABA y Buenos Aires por día (al: ", last_date, ")") , 
       caption = "Fuente: https://github.com/SistemasMapache/Covid19arData", 
       y = "Casos", 
       x = "Número de días desde el 1er caso"
  ) +
  scale_color_discrete(palette = function(x) c("#67a9cf", "#ef8a62")) +
  theme_elegante_std(base_family = "Ralleway") 
```

<img src="/images/2020/2020-05-24-30-dias-de-graficos-en-r_files/figure-gfm/dia2-1.png" style="display: block; margin: auto;" />

### Día 3: Una gráfica de puntos

También se los conoce como gráficos de dispersión o “Scatterplot”.
Suelen intentar mostrar la relación entre dos variables continuas por
medio de los patrones que se forman.

``` r
library("tidyverse")
library("ggrepel")

if ("ggelegant" %in% rownames(installed.packages())) {
  library("ggelegant")
} else {
  # devtools::install_github("pmoracho/ggelegant")
  theme_elegante_std <- function(base_family) {}
}

covid.data <- read.csv("https://opendata.ecdc.europa.eu/covid19/casedistribution/csv", na.strings = "", fileEncoding = "UTF-8-BOM",
                       stringsAsFactors = FALSE)

hdi <- read.csv("https://data.humdata.org/dataset/05b5d8f1-9e7f-4379-9958-125c203d12ac/resource/4a7fd374-7e35-4c04-b7c8-25e5943aa476/download/hdi_human_development_index_hdig_value.csv", stringsAsFactors = FALSE)
hdi %>% 
  group_by(country_code) %>% 
  arrange(year) %>% 
  slice(n()) %>% 
  select(country_code, country, year, value) -> last_hdi


last_date <- max(as.Date(covid.data$dateRep,"%d/%m/%Y"))
paises_de_interes <- c( 'Argentina',
                       "Niger", "Norway", "EEUU", "Mauritania")
covid.data %>% 
  group_by(countriesAndTerritories, countryterritoryCode) %>% 
  summarize(casos = sum(cases), fallecidos = sum(deaths)) %>% 
  ungroup() %>% 
  inner_join(last_hdi,
            by = c("countryterritoryCode" = "country_code")
            ) %>% 
  mutate(pais = ifelse(countriesAndTerritories == 'United_States_of_America', 'EEUU', countriesAndTerritories)) %>% 
  select(pais, casos, fallecidos, HDI = value) %>% 
  mutate(pais_etiquetado = ifelse(pais %in% paises_de_interes, paste0(pais, " (casos: ", format(casos, digits=0, big.mark = ',', trim=TRUE), " hdi: ", HDI, ")"), NA)) %>% 
  ggplot(aes(x=HDI, y=casos)) +
    geom_point(color = "#67a9cf", alpha=.5, size=3) +
    geom_smooth(method = 'lm',formula='y ~ x', se=FALSE, color="#ef8a62") +
    geom_label_repel(mapping = aes(label = pais_etiquetado),
                     color="#67a9cf",family = "Ralleway", vjust = -1.2, hjust = 1.1) +
                     # nudge_x = 1, nudge_y = 5, color="#67a9cf",
                     # vjust = -2, family = "Ralleway",  
                     # direction  = "y",
                     # hjust = 2) +
    scale_y_log10(
      breaks = scales::trans_breaks("log10", function(x) 10^x),
      labels = scales::trans_format("log10", scales::math_format(10^.x))
    ) +
    labs(title = paste("COVID-19"), 
         subtitle = paste0("¿Hay relación entre el desarrollo humano y la cantidad de infecciones?\n (Datos al: ", last_date, ")") , 
         caption = "Fuente: https://opendata.ecdc.europa.eu/covid19/casedistribution/csv", 
         y = "log10(Cantidad de infectados)", 
         x = "Human development Index (2013)"
    ) +
    theme_elegante_std(base_family = "Ralleway") 
```

<img src="/images/2020/2020-05-24-30-dias-de-graficos-en-r_files/figure-gfm/dia3-1.png" style="display: block; margin: auto;" />
