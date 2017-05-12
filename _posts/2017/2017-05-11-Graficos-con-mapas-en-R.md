---
layout: post
published: true
date: '2017-05-11'
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
title: Gráficos en mapas con R
description: Un ejemplo de como crear un mapa de calor sobre un mapa geografico
tags:
  - desarrollo
  - R
image:
  feature: 2017/img-feature-002.jpeg
---


A raíz de
[esta](https://es.stackoverflow.com/questions/69171/c%C3%B3mo-colorear-los-municipios-de-un-estado-de-la-rep%C3%BAblica-mexicana-usando-el-paq)
pregunta en **StackOverflow** se me dió por investigar las posibilidades que
brinda [Leaflet](http://leafletjs.com/) en **R**. La idea es jugar con alguna
estadística y hacer un "mapa de calor" sobre un mapa geográfico. Decidí
arrancar con una muy triste y lamentablemente  instalada en los medios y en la
realidad que es la de los femicidios. 

En [datos.jus.gob.ar](http://datos.jus.gob.ar/dataset/registro-sistematizacion-y-seguimiento-de-femicidios-y-homicidios-agravados-por-el-genero) publican una muy completa lista de casos por lo que lo primero que vamos a ser es importarla a nuestro entorno **R**.

``` R
# Descarga del registro de femicidio
url <- "http://datos.jus.gob.ar/dataset/27bb9b2c-521b-406c-bdf9-98110ef73f34/resource/9a06c428-8552-42fe-86e1-487bca9b712c/download/registro-de-femicidios.csv"
file <- basename(url)
download.file(url, file)

# Carga el Csv a un data.frame
femicidios <- read.table(file = file, header = TRUE, sep = ',')

> head(femicidios)
  numero edad identidad_genero tipo_victima  lugar_hecho         modalidad_comisiva fecha_hecho
1    831   42            MUJER    PRINCIPAL Buenos Aires ACUCHILLAMIENTO - GOLPES -  2017/02/11
2    830   16            MUJER    VINCULADO Buenos Aires          DISPARO DE BALA -  2017/02/12
3    830   17            MUJER    PRINCIPAL Buenos Aires          DISPARO DE BALA -  2017/02/12
4    829   42            MUJER    PRINCIPAL Buenos Aires                   GOLPES -  2017/02/12
5    828   40            MUJER    PRINCIPAL   Corrientes               QUEMADURAS -  2017/02/01
6    827   26            MUJER    PRINCIPAL Buenos Aires                   GOLPES -  2016/03/21
```


