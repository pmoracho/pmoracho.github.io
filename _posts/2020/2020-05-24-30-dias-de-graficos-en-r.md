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

## Introducción

A raíz de que el 12 de mayo se conmemora el nacimiento de Florence
Nightingale, la enfermera creadora del diagrama de área polar y
referente femenina de la visualización de datos, la comunidad
[@R4DS\_es](https://twitter.com/R4DS_es) lanzó un lindo desafío,
[proyecto](https://github.com/cienciadedatos/datos-de-miercoles/blob/master/30-dias-de-graficos-2020.md),
uno distinto por día. Esta es la lista
completa:

| día | fecha       | desafío                                           | Completado? |
| --- | ----------- | ------------------------------------------------- | ----------- |
| 1   | 12 de mayo  | barras / columnas                                 | ✓           |
| 2   | 13 de mayo  | líneas                                            | ✓           |
| 3   | 14 de mayo  | puntos / burbujas                                 | ✓           |
| 4   | 15 de mayo  | gráficos con facetas                              | ✓           |
| 5   | 16 de mayo  | diagramas de arco                                 | ✓           |
| 6   | 17 de mayo  | gráficos de donut                                 | ✓           |
| 7   | 18 de mayo  | gráficos ridgeline                                | ✓           |
| 8   | 19 de mayo  | gráficos de contorno                              | ✓           |
| 9   | 20 de mayo  | gráficos de áreas apiladas                        | ✓           |
| 10  | 21 de mayo  | ¡explorar paletas de colores\!                    | ✓           |
| 11  | 22 de mayo  | mapas de calor (*heatmap*)                        | ✓           |
| 12  | 23 de mayo  | gráficos de paleta (*lollipop*)                   |             |
| 13  | 24 de mayo  | visualizar datos temporales                       |             |
| 14  | 25 de mayo  | gráficos de rectángulos/árbol (*treemap*)v        |             |
| 15  | 26 de mayo  | dendorgamas                                       |             |
| 16  | 27 de mayo  | gráficos de waffle                                |             |
| 17  | 28 de mayo  | diagramas de sankey                               |             |
| 18  | 29 de mayo  | visualizar datos espaciales                       |             |
| 19  | 30 de mayo  | gráficos de flujo (*stream graph*)                |             |
| 20  | 31 de mayo  | redes                                             |             |
| 21  | 1 de junio  | gráficos con anotaciones                          |             |
| 22  | 2 de junio  | visualizar datos textuales                        |             |
| 23  | 3 de junio  | gráficos de proyección solar (*sunburst*)         |             |
| 24  | 4 de junio  | coropletas                                        |             |
| 25  | 5 de junio  | gráficos de violín                                |             |
| 26  | 6 de junio  | diagramas de marimekko                            |             |
| 27  | 7 de junio  | ¡gráficos animados\!                              |             |
| 28  | 8 de junio  | diagramas de cuerdas                              |             |
| 29  | 9 de junio  | gráficos de coordenadas paralelas                 |             |
| 30  | 10 de junio | diagramas de área polar o de Florence Nightingale |             |

## Objetivos

En lo personal, mi idea es aprovechar este desafío para:

  - Profundizar el conocimiento de **Ggplot2**, ya que intentaremos
    resolver todos usando este paquete
  - Probar y eventualmente ajustar, mi tema personalizado
    [`ggelegant`](https://github.com/pmoracho/ggelegant)
  - Tratar de usar datos actuales y no apoyarme en gráficas de ejemplo
    ya resueltas

## Día 1: Gráfico de barras / Columnas

Una gráfica de barras o columnas, tal vez una de las más clásicas,
muestran comparaciones numéricas entre una variable discreta y una serie
de los valores continuos que toma cada una de estas variables discretas
o “categorías”. A diferencia de un histograma, los gráficos de barra no
muetran un desarrollo continuo entre las categorías.

Este ejemplo muestra las diferencia de situación con respecto al
**COVID-19** en Argentina y los países vecinos, en cuanto a cantidad de
casos y fallecidos. Usamos una escala logarítmica en el caso del eje `y`
para que los enormes números de Brasil no nos oculten los datos del
resto.

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
       x = ""
    ) +
    scale_fill_discrete(palette = function(x) c("#67a9cf", "#ef8a62")) +
    theme_elegante_std(base_family = "Ralleway") 
```

<img src="/images/2020/2020-05-24-30-dias-de-graficos-en-r_files/figure-gfm/dia1-1.png" style="display: block; margin: auto;" />

## Día 2: Gráfico de líneas

Son gráficos que muestras la evolución de una o más variables continuas
en un determinado intervalo, generalmente de tiempo. Lo habitual es que
el ejex Y tenga un valor cuantitativo y el eje X tiene la seceuncia que
representa el intervalo, En el plano cartesiano, se establecen los
puntos X, Y y luego se van conectando estos mediante líneaas rectas.

En este ejemplo, la idea es mostrar la evolución de los casos de
**COVID-19** en las dos provincias más importantes en cantidad de casos
de la Argentina, desde el primer infectado, el eje `x` muestra el número
de días y el `y` la cantidad de casos detectados dicho día. Dibujamos
puntos en cada `x,y` y luego unímos cada uno con un segemento,
adicionalmente agregamos una curva que refuerza la visión de la
tendencia.

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

## Día 3: Una gráfica de puntos

También se los conoce como gráficos de dispersión o “Scatterplot”.
Suelen intentar mostrar la relación entre dos variables continuas por
medio de los patrones que se forman.

En este ejemplo, me pregunto ¿Hay relación entre el desarrollo humano y
la cantidad de infecciones?, para esto armamos un gráfico de dispersión
dónde cada país tiene un punto que correlaciona las variabled números de
infectados (`x`) y el índice de desarrollo humano (`y`), además
agregamos unas etiquetas que marcan los paíse en el extremo de cada
variable y la Argentina.

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

## Día 4: Facetas

Las “Facetas” no es un tipo de gráfico sino más bien una forma de
mostrar uno de estos. Cuando se tiene múltiples variables, muchas veces
resulta confuso verlas todas en un mismo gráfico, el facetado permite
dividir cada variable o grupo en múltiples graficos similares.

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
  mutate(fecha = as.Date(fecha, "%d/%m/%Y")) %>% 
  select(dia=dia_inicio, distrito=osm_admin_level_4, cantidad=nue_casosconf_diff) -> data

data %>% 
  inner_join(data %>% 
               group_by(distrito) %>% 
               summarize(cantidad = sum(cantidad)) %>% 
               arrange(-cantidad) %>% 
               top_n(9), by = c("distrito"), suffix=c("",".y")) %>% 
  ggplot(mapping=aes(x=dia, y=cantidad)) +
  geom_line(color="#67a9cf") +
  geom_point(color="#67a9cf") +
  geom_smooth(method = 'loess',
              formula = 'y ~ x', alpha = 0.2, size = 1, span = .3, se=FALSE, color="#ef8a62") + 
  labs(title = paste("COVID-19 en Argentina"), 
       subtitle = paste0("Variación de casos diarios en los distritos con más casos (al: ", last_date, ")") , 
       caption = "Fuente: https://github.com/SistemasMapache/Covid19arData", 
       y = "Número de casos", 
       x = "Número de días desde el 1er caso"
  ) +
  facet_wrap(~distrito,scales="free") +
  theme_elegante_std(base_family = "Ralleway") 
```

<img src="/images/2020/2020-05-24-30-dias-de-graficos-en-r_files/figure-gfm/dia4-1.png" style="display: block; margin: auto;" />

Una linda visualización es la que se legro mediante
[geofaceteAR](https://github.com/electorArg/geofaceteAR):

``` r
library("geofaceteAR")

argentina_grid <-  data.frame(
  col = c(1, 3, 5, 1, 2, 1, 3, 4, 2, 2, 4, 1, 3, 3, 4, 1, 2, 2, 1, 1, 2, 1, 1, 1),
  row = c(1, 2, 2, 2, 2, 3, 3, 3, 3, 4, 4, 4, 4, 5, 5, 5, 5, 6, 6, 7, 7, 8, 9, 10),
  code = c("AR-Y", "AR-P", "AR-N", "AR-A", "AR-T", "AR-K", "AR-H", "AR-W", "AR-G", "AR-X", "AR-E", "AR-F", "AR-S", "AR-B", "AR-C", "AR-J", "AR-D", "AR-L", "AR-M", "AR-Q", "AR-R", "AR-U", "AR-Z", "AR-V"),
  name_es = c("Jujuy", "Formosa", "Misiones", "Salta", "Tucumán", "Catamarca", "Chaco", "Corrientes", "Santiago del Estero", "Córdoba", 
              "Entre Ríos", "La Rioja", "Santa Fe", "Buenos Aires", "CABA", "San Juan", "San Luis", "La Pampa", "Mendoza", "Neuquén", "Río Negro", 
              "Chubut", "Santa Cruz", "Tierra del Fuego"),
  stringsAsFactors = FALSE
)

data %>% 
  filter(distrito!='Indeterminado') %>% 
  ggplot(mapping=aes(x=dia, y=cantidad)) +
  geom_line(color="#67a9cf") +
  geom_point(color="#67a9cf") +
  geom_smooth(method = 'loess',
              formula = 'y ~ x', alpha = 0.2, size = 1, span = .3, se=FALSE, color="#ef8a62") + 
  labs(title = paste("COVID-19 en Argentina"), 
       subtitle = paste0("Variación de casos diarios en los distritos con más casos (al: ", last_date, ")") , 
       caption = "Fuente: https://github.com/SistemasMapache/Covid19arData", 
       y = "Número de casos", 
       x = "Número de días desde el 1er caso"
  ) +
  facet_geo(~ distrito, grid = argentina_grid, scales = "free_y") +
  # facet_wrap(~ distrito, scales = "free_y") +
  theme_elegante_std(base_family = "Ralleway") 
```

<img src="/images/2020/2020-05-24-30-dias-de-graficos-en-r_files/figure-gfm/dia4b-1.png" style="display: block; margin: auto;" />

## Día 5: Un gráfico de Arcos

Los gráficos de arco forman parte de los diagramas de red, en este caso
los nodos se colocan sobre un eje horizontal y la líneas o arcos señalan
las relaciones entre cada nodo. Eventualmente se puede ajustar el grosor
de la líne para incluir una nueva dimensión. En lo personal me resulta
difíciles de leer pero entiendo que para algunas aplicaciones
específicas pueden ser útiles. En este ejemplo, trato de mostrar las
relaciones musicales interpresonales de **Luis Alberto Spinetta** en su
primera etapa como músico.

``` r
library("tidyverse")
library("tidygraph")
library("ggraph")
library("igraph")

if ("ggelegant" %in% rownames(installed.packages())) {
  library("ggelegant")
} else {
  # devtools::install_github("pmoracho/ggelegant")
  theme_elegante_std <- function(base_family) {}
}

data <- structure(list(persona = structure(c(10L, 10L, 10L, 10L, 10L, 
                                             10L, 6L, 6L, 12L, 12L, 12L, 12L, 8L, 9L, 2L, 7L, 7L, 3L, 3L, 
                                             1L, 11L, 5L, 4L, 4L, 13L), .Label = c("Angel del Guercio", "Carlos Xartuch", 
                                                                                   "Daniel Albertelli", "Edelmiro Molinari", "Eduardo Miró", "Emilio del Guercio", 
                                                                                   "Guido Meda", "Hector Nuñez", "Horacio Soria", "Luis Alberto Spinetta", 
                                                                                   "Ricardo Miró", "Rodolfo Garcia", "Santiago Novoa"), class = "factor"), 
                       grupo = c(" Bundleman", " Los Larkings", " Los Masters", 
                                 " Los Mods", " Los Sbirros", " Almendra (1967-1969)", " Bundleman", 
                                 " Almendra (1967-1969)", " Los Larkings", " Los Masters", 
                                 " Los Mods", " Almendra (1967-1969)", " Los Larkings", " Los Larkings", 
                                 " Los Larkings", " Los Masters", " Los Mods", " Los Masters", 
                                 " Los Mods", " Los Sbirros", " Los Sbirros", " Los Sbirros", 
                                 " Los Sbirros", " Almendra (1967-1969)", " Los Sbirros")), class = "data.frame", row.names = c(NA, 
                                                                                                                                -25L))

data %>% 
  select(from = persona, to = grupo, grupo=grupo, name=persona) -> prepared.data

tbl_graph(edges=prepared.data, directed = TRUE) %>% 
  ggraph(layout = "linear") +
  geom_edge_arc(aes(color=grupo),  edge_width=1.5, edge_alpha = 0.5, fold = TRUE,) +
  geom_node_point(size = 2, color="#67a9cf") +
  geom_node_text(aes(label = str_wrap(name,13)), size = 3, nudge_y =-.7, angle = 90, fontface = "bold",  hjust=.5) +
  coord_cartesian(clip = "off") + 
  theme_elegante_std(base_family = "Ralleway") +
  theme(axis.title.x=element_blank(),
        axis.text.x=element_blank(),
        axis.ticks.x=element_blank(),
        axis.title.y=element_blank(),
        axis.text.y=element_blank(),
        axis.ticks.y=element_blank(),
        legend.position = "none") +
  labs(title = "Historia musical de Luis Albero Spinetta", 
       subtitle = "Los comienzos"
  ) 
```

<img src="/images/2020/2020-05-24-30-dias-de-graficos-en-r_files/figure-gfm/dia5-1.png" style="display: block; margin: auto;" />

## Día 6: Un gráfico de aros de cebolla

Odio las donas, pero bueno, en realidad se llaman “donuts plots”, una
variante de un clásico gráfico de torta, solo sin el centro, a
diferencia de los últimos, los gráficos de donas, al no mostrar el área
completa, logran que el usuario se enfoque más en la longitud de cada
sector, lo cual mejora la percepción de los cambios. Además,
eventualmente permiten usar el área del centro para agregar más
información.

``` r
library("tidyverse")
library("ggrepel")

if ("ggelegant" %in% rownames(installed.packages())) {
  library("ggelegant")
} else {
  # devtools::install_github("pmoracho/ggelegant")
  theme_elegante_std <- function(base_family) {}
}

covid.data <- read_csv('https://docs.google.com/spreadsheets/d/16-bnsDdmmgtSxdWbVMboIHo5FRuz76DBxsz_BbsEVWA/export?format=csv&id=16-bnsDdmmgtSxdWbVMboIHo5FRuz76DBxsz_BbsEVWA&gid=0')

last_date <- max(as.Date(covid.data$fecha,"%d/%m/%Y"))
break_porc <- .95
covid.data %>% 
  select(distrito=osm_admin_level_4, casos=nue_casosconf_diff) %>% 
  group_by(distrito) %>% 
  summarise(casos=sum(casos)) %>% 
  mutate(porc = casos / sum(casos)) %>%
  ungroup() %>% 
  arrange(-porc) %>% 
  mutate(cporc = cumsum(porc),
         distrito = ifelse(cporc < break_porc, distrito, 'Resto')) %>% 
  group_by(distrito) %>% 
  summarise(casos = sum(casos),
            porc = sum(round(porc*100,2))) -> data

data$porc[data$distrito == 'Resto'] <- 100 - sum(data$porc[data$distrito != 'Resto'])
data %>% 
  mutate( ymax = cumsum(porc),
          ymin = lag(ymax, default=0)
  ) -> data

mac_perc <- sum(data$porc[data$distrito != 'Resto'])
data$distrito <- with(data, reorder(distrito, porc))
data %>% 
  ggplot(aes(ymax=ymax, ymin=ymin, xmax=4, xmin=3, fill=distrito)) +
  geom_rect(color="gray90", size=1.2) +
  geom_label_repel(mapping = aes(x=3.5, y=ymin + (ymax - ymin)/2,
                                 colour =  ifelse(porc > 10, 2, 0),
                                 label = paste0(distrito, ": ", format(porc, digits=2, trim=FALSE), "%\nCasos:", 
                                                format(casos, big.mark = ",", trim=FALSE))),
                   family = "Ralleway", 
                   nudge_y = 1,
                   nudge_x = 1) +
  coord_polar(theta="y") + # Try to remove that to understand how the chart is built initially
  xlim(c(2, 4)) +
  labs(title = paste("COVID-19 en Argentina"), 
       subtitle = paste0("Distribución del ", mac_perc , "% de los casos por distrito\n (Datos al: ", last_date, ")") , 
       caption = "Fuente: https://github.com/SistemasMapache/Covid19arData"
  ) +
  theme_elegante_std(base_family = "Ralleway") +
  theme(axis.title.x=element_blank(),
        axis.text.x=element_blank(),
        axis.ticks.x=element_blank(),
        axis.title.y=element_blank(),
        axis.text.y=element_blank(),
        axis.ticks.y=element_blank(),
        legend.position = "none") +
  scale_fill_brewer(palette = "Blues")
```

<img src="/images/2020/2020-05-24-30-dias-de-graficos-en-r_files/figure-gfm/dia6-1.png" style="display: block; margin: auto;" />

## Día 7: Gráficos “Ridgeline”

Los graficos “ridgeline” mapean la distribución de múltiples variables
continuas con un conjunto de variables categoricas en la forma de curvas
“suaves” que permite comparar cada categoría:

``` r
library("tidyverse")
library("ggridges")

if ("ggelegant" %in% rownames(installed.packages())) {
  library("ggelegant")
} else {
  # devtools::install_github("pmoracho/ggelegant")
  theme_elegante_std <- function(base_family) {}
}

covid.data <- read_csv('https://docs.google.com/spreadsheets/d/16-bnsDdmmgtSxdWbVMboIHo5FRuz76DBxsz_BbsEVWA/export?format=csv&id=16-bnsDdmmgtSxdWbVMboIHo5FRuz76DBxsz_BbsEVWA&gid=0')

last_date <- max(as.Date(covid.data$fecha,"%d/%m/%Y"))
break_porc <- .95

covid.data %>% 
  mutate(distrito = osm_admin_level_4, 
         casos = nue_casosconf_diff) %>% 
  select(distrito, casos) -> data

data %>% 
  group_by(distrito) %>% 
  summarise(casos=sum(casos)) %>% 
  mutate(porc = casos / sum(casos)) %>%
  arrange(-porc) %>% 
  mutate(cporc = cumsum(porc),
         distrito = ifelse(cporc < break_porc, distrito, 'Resto')) %>% 
  group_by(distrito) %>% 
  summarise(casos = sum(casos),
            porc = sum(round(porc*100,2))) -> principales

perc <- sum(principales$porc[principales$distrito != 'Resto'])

data %>% 
  left_join(principales, by="distrito") %>% 
  mutate(distrito = ifelse(is.na(casos.y), 'Resto', distrito)) %>% 
  select(distrito, casos=casos.x) %>% 
  ggplot(aes(x = casos, y = distrito, fill = distrito)) +
    geom_density_ridges() +
    theme(legend.position = "none") +
    labs(title = paste("COVID-19 en Argentina"), 
       subtitle = paste0("Distribución de casos diarios (al: ", last_date, ")\nDetalle en los distritos que suman el ", perc, "% de los casos totales del país") , 
       caption = "Fuente: https://github.com/SistemasMapache/Covid19arData", 
       y = "Distritos", 
       x = "Cantidades de casos diarios"
    ) +
    scale_x_continuous(breaks = c(c(0,5, 10, 20, 50), seq(from=75, to=max(data$casos)+25, by = 25))) +
    theme_elegante_std(base_family = "Ralleway") +
    theme(legend.position = "none")  
```

<img src="/images/2020/2020-05-24-30-dias-de-graficos-en-r_files/figure-gfm/dia7-1.png" style="display: block; margin: auto;" />

## Día 8: Gráfico de contornos

Este tipo de gráficos básicamente une puntos x e y que comparte un mismo
vlor de una tercer variable z, el caso típico, son los mapa
cartográficos , cuyas lineas unen puntos de semejante altura.

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
  geom_density2d(contour = TRUE, n = 1000) +
  geom_label_repel(mapping = aes(label = pais_etiquetado),
                   color="#67a9cf",family = "Ralleway", vjust = -1.2, hjust = 1.1, fontface="bold") +

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

<img src="/images/2020/2020-05-24-30-dias-de-graficos-en-r_files/figure-gfm/dia8-1.png" style="display: block; margin: auto;" />

## Día 9: Areas apiladas

Es una variante del gráfico de areas, que a su vez es una variante del
de lineas, solo que en este caso se “apilan” más de una variable, lo que
es útil para compararlas.

``` r
library("tidyverse")
library("ggrepel")

if ("ggelegant" %in% rownames(installed.packages())) {
  library("ggelegant")
} else {
  # devtools::install_github("pmoracho/ggelegant")
  theme_elegante_std <- function(base_family) {}
}

covid.data <- read_csv('https://docs.google.com/spreadsheets/d/16-bnsDdmmgtSxdWbVMboIHo5FRuz76DBxsz_BbsEVWA/export?format=csv&id=16-bnsDdmmgtSxdWbVMboIHo5FRuz76DBxsz_BbsEVWA&gid=0')
covid.data %>% 
  mutate(fecha=as.Date(fecha,"%d/%m/%Y")) %>% 
  group_by(dia_inicio,fecha) %>% 
  summarize(casos = sum(nue_casosconf_diff),
         fallecidos = sum(nue_fallecidos_diff)) %>% 
  select(dia = dia_inicio, fecha, casos, fallecidos) %>% 
  pivot_longer(-c("dia", "fecha"), names_to = 'metrica', values_to='cantidades') -> data

data %>% 
  left_join(data %>% 
              group_by(metrica) %>%
              summarise(cantidades = max(cantidades), maximo = TRUE),
            by = c("metrica", "cantidades")
  ) %>% 
  mutate(maximo = ifelse(maximo, paste0("Pico de ", metrica, " de ", cantidades, "\nel ", fecha), NA)) -> data

last_date <- max(as.Date(covid.data$fecha,"%d/%m/%Y"))

ggplot(data, aes(x=dia, y=cantidades, fill=metrica, color=metrica)) + 
  geom_area(alpha=0.6 , size=1) +
  labs(title = paste("COVID-19 en Argentina"), 
       subtitle = paste0("Variación de casos y fallecimientos por día (al: ", last_date, ")") , 
       caption = "Fuente: https://github.com/SistemasMapache/Covid19arData", 
       y = "Cantidades diarias", 
       x = "Número de días desde el 1er caso"
  ) +
  geom_label_repel(mapping = aes(label = maximo),
                   color = "white",
                   segment.color="gray90",
                   family = "Ralleway", 
                   vjust = -1,
                   hjust = 1.5,
                   box.padding = 1,
                   show.legend = FALSE) +
  scale_fill_discrete(palette = function(x) c("#67a9cf", "#ef8a62")) +
  scale_color_discrete(palette = function(x) c("#67a9cf", "#ef8a62")) +
  guides(color = FALSE, label=FALSE) +
  theme_elegante_std(base_family = "Ralleway")
```

<img src="/images/2020/2020-05-24-30-dias-de-graficos-en-r_files/figure-gfm/dia9-1.png" style="display: block; margin: auto;" />

## Día 10: Exploración de paletas de colores

Nada del otro mundo, un script para generar una gráfica de ejemplo de
una paleta determinada de colores, en este ejemplo explorando la del
paquete `[wesanderson]`

``` r
library("tidyverse")
library("wesanderson")
library("gridExtra")

if ("ggelegant" %in% rownames(installed.packages())) {
  library("ggelegant")
} else {
  # devtools::install_github("pmoracho/ggelegant")
  theme_elegante_std <- function(base_family) {}
}

colores <- 20
paletas <- names(wes_palettes)

data.frame(x=factor(1:colores), y=1) %>% 
  ggplot(aes(x = x, y = y, fill = x)) + 
  geom_col() +
  theme_elegante_std(base_family = "Ralleway") +
  theme(axis.text.x=element_blank(),
        axis.ticks.x=element_blank(),
        axis.title.y=element_blank(),
        axis.text.y=element_blank(),
        axis.ticks.y=element_blank(),
        legend.position = "none")  -> g

plots <- list()
for (p in paletas) {
  plots[[p]] <- g + scale_fill_manual(values = wes_palette(colores, name = p, type = "continuous"), name = "") +
    labs(x = p)
}
grid.arrange(grobs = plots, ncol = 2)
```

<img src="/images/2020/2020-05-24-30-dias-de-graficos-en-r_files/figure-gfm/dia10-1.png" style="display: block; margin: auto;" />

## Día 11: Mapa de Calor

Un mapa de calor muestra la variación en una determinada variable (por
lo general continua) mediante el uso del color. En este ejemplo mapeamos
tres variables, el distrito o provincia (categórica), la cantidad de
días desde el primer contagio (categórica) y la variable continua de la
cantidad de casos que es la que mapeamos a la dimensión del color.

``` r
library("tidyverse")
library("zoo")
library("forcats")
library("scales")
  if ("ggelegant" %in% rownames(installed.packages())) {
  library("ggelegant")
} else {
  # devtools::install_github("pmoracho/ggelegant")
  theme_elegante_std <- function(base_family) {}
}

covid.data <- read_csv('https://docs.google.com/spreadsheets/d/16-bnsDdmmgtSxdWbVMboIHo5FRuz76DBxsz_BbsEVWA/export?format=csv&id=16-bnsDdmmgtSxdWbVMboIHo5FRuz76DBxsz_BbsEVWA&gid=0')
# Dependiendo del horario, la última fila puede ser un placeholder si datos
# covid.data <- covid.data[covid.data$dia_inicio != max(covid.data$dia_inicio),]

last_date <- max(as.Date(covid.data$fecha,"%d/%m/%Y"))
first_date <- min(as.Date(covid.data$fecha,"%d/%m/%Y"))
ndias <- max(covid.data$dia_inicio)


dias <- expand.grid(distrito = unique(covid.data$osm_admin_level_4),
                    dia = 1:ndias,
                    stringsAsFactors = FALSE)
dias$fecha = first_date + dias$dia - 1
dias$casos = 0

dias_rolling <- 3
dias_ventana <- 14

dias %>% 
  left_join(covid.data, by=c("distrito" = "osm_admin_level_4", "dia" = "dia_inicio")) %>% 
  mutate(casos = replace_na(nue_casosconf_diff, 0)) %>% 
  select(dia, distrito, fecha=fecha.x, casos)  %>% 
  group_by(dia, distrito, fecha) %>% 
  summarise(casos = sum(casos)) %>%
  arrange(distrito, dia) %>% 
  group_by(distrito) %>% 
  filter(dia >= ndias - dias_ventana - dias_rolling,
         distrito != 'Indeterminado') %>% 
  mutate(roll_casos_3 = rollmean(casos, dias_rolling, align='right', fill=0),
         s_roll_casos_3 = replace_na((roll_casos_3-min(roll_casos_3))/(max(roll_casos_3)-min(roll_casos_3)),0),
         label_casos = casos
  ) %>% 
  filter(dia >= ndias - dias_ventana ) %>% 
  ggplot(aes(x = dia, y =   fct_reorder(distrito, casos), fill = s_roll_casos_3)) + 
    geom_tile(colour="gray80", size=0.2) +
    geom_text(aes(label=label_casos, color = s_roll_casos_3 > .7)) +
    scale_color_manual(guide = FALSE, values = c("gray30", "gray90")) +
    scale_fill_distiller(palette = "YlGnBu", direction = 1, na.value = "white") +
    labs(title = paste("COVID-19 en Argentina"), 
       subtitle = paste0("Evolución diaria de los casos por distrito (últimos ", dias_ventana, " días al: ", last_date, ")\n",
                         "Los números son casos diarios, la escala de color en función del promedio de casos de los últimos ", dias_rolling, " días") , 
       caption = "Fuente: https://github.com/SistemasMapache/Covid19arData", 
       x = "Últimos días"
    ) +
    theme_elegante_std(base_family = "Ralleway") + 
    scale_x_continuous(breaks = seq(ndias - dias_ventana, ndias, by = 1)) +
    theme(axis.title.y=element_blank(),
        legend.position = "none") 
```

<img src="/images/2020/2020-05-24-30-dias-de-graficos-en-r_files/figure-gfm/dia11-1.png" style="display: block; margin: auto;" />

## Día 12: Un gráfico lolipop

``` r
library("tidyverse")
library("forcats")
library("countrycode")

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
  group_by(countryterritoryCode) %>% 
  summarize(casos = sum(cases), fallecidos = sum(deaths)) %>% 
  arrange(-casos) %>% 
  mutate(nr = row_number()) %>% 
  select(code_pais = countryterritoryCode, casos, fallecidos, nr) -> data

media_casos <- mean(data$casos)
media_fallecidos <- mean(data$fallecidos)

data %>% 
  filter(nr <= 50) %>% 
  mutate(sobre_media = casos > media_casos,
         pais = countrycode(code_pais, origin = 'iso3c', destination = 'un.name.es')) %>% 
  ggplot(aes(x=fct_reorder(pais, -nr), y=casos, color = sobre_media)) +
  geom_segment(aes( y=0 , xend = pais, yend = casos)) + 
  geom_point() +
  coord_flip() +
  geom_text(aes(label = format(abs(casos), digits=0, big.mark = ','),
                vjust = .5,
                hjust = -.1),
            position = position_dodge(width=1)) +
  labs(title = paste("COVID-19 en el mundo"), 
       subtitle = paste("Casos por pais al: ", last_date, '\nLos primeros 50 países') , 
       caption = "Fuente: https://opendata.ecdc.europa.eu/covid19/casedistribution/csv", 
       y = "Casos", 
       x = "País"
  ) +
  scale_y_continuous(breaks = scales::pretty_breaks(n = 5), limits = c(0, max(data$casos) * 1.01),
                     labels = scales::comma) +

  scale_color_manual(labels = c("Sobre la media", "Debajo de la media"), values = c("#67a9cf", "#ef8a62")) +
  theme_elegante_std(base_family = "Ralleway") 
```

<img src="/images/2020/2020-05-24-30-dias-de-graficos-en-r_files/figure-gfm/dia12-1.png" style="display: block; margin: auto;" />
