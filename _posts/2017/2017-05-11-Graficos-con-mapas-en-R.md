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

## Los datos a graficar

En [datos.jus.gob.ar](http://datos.jus.gob.ar/dataset/registro-sistematizacion-y-seguimiento-de-femicidios-y-homicidios-agravados-por-el-genero) publican una muy completa lista de casos por lo que lo primero que vamos a ser es importarla a nuestro entorno **R**.

``` R
tmp <- tempdir()

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

Lo que me interesa hacer es "mapear" estos datos con la provincia/estado en
dónde ocurrió el crimen, para esto lo primer qu hay que hacer es normalizar los
nombres de las provincias ya que pareciera ser que no son muy rigurosos con
estos datos, a veces cargan como `Santa Fé` y otras como `Santa Fe` por dar un
ejemplo. Bueno esta "normalización" la haremos así:

``` R
# Estandarización de los nombres de provincias
femicidios$lugar_hecho <- gsub('CABA', 'Ciudad de Buenos Aires', femicidios$lugar_hecho)
femicidios$lugar_hecho <- gsub('Entre Rios', 'Entre Ríos', femicidios$lugar_hecho)
femicidios$lugar_hecho <- gsub('Santa Fé', 'Santa Fe', femicidios$lugar_hecho)
femicidios$lugar_hecho <- gsub('Tucuman', 'Tucumán', femicidios$lugar_hecho)
femicidios_x_provincia <- as.data.frame(table(femicidios$lugar_hecho))
```

Nota: Esta normalización, ya veremos, tiene también que ver con unificar los
nombres de provincia con los que figuran en la información geográfica.

## El mapa geográfico

Como ya comenté vamos a usar [Leaflet](http://leafletjs.com/) para armar el
mapa y colorearlo luego. Para poder usar esta librería, si no la tenemos
instalada, simplemente con un `install.packages("leaflet")` alcanza. Además
necesitaremos algún que otro paquete, detallando:

* [leaflet](http://leafletjs.com/): Es la librería que generara el mapa para incorporar en una vista HTM.  `install.packages("leaflet")`
* [rgdal](https://cran.r-project.org/web/packages/rgdal/index.html/): Unos "bindings" a la librería de abstracción de datos geográficos de Frank Warmerdam. `install.packages("leaflet")`
* [plyr](https://cran.r-project.org/web/packages/plyr/index.html): La usaremos para manipular. `install.packages("plyr")`

Bien, para empezar necesitamos obtener un "Shapefile" esto es básicamente
(entre otras cosas) una definición de polígonos que nos va permitir establecer
las áreas de un mapa que corresponden a cada estado/provincia. Esta información
la podemos obtener por ejemplo de
[biogeo](http://biogeo.ucdavis.edu/projects.html), vamos a usar los "Global
Administrative Boundaries (GADM)", que justamente definen los contornos de los
estados en cada país. Vamos a buscar puntualmente el de **Argentina**.


``` R
tmp <- tempdir()

# Descarga del Shapefile de Argentina
url <- "http://biogeo.ucdavis.edu/data/diva/adm/ARG_adm.zip"
file <- basename(url)
download.file(url, file)
unzip(file, exdir = tmp)
argentina <- readOGR(dsn = tmp, layer = "ARG_adm1", use_iconv=TRUE, encoding='UTF-8')

```

Básicamente descargamos el archivo a una carpeta temporal y lo descomprimimos.
Hay que analizar el contenido para ubicar el "layer", que no es más que un
clásico archivo **DBF**, analizándolo pude detectar que se trataba de
"ARG_adm1" el que contenía las definiciones de los 24 estados del país. Lo
siguiente es usar `readOGR` una función del paquete `rgdal` que simplemente
transforma el archivo **DBF** en un vector de datos geo-espaciales.

## Combinando datos y geografía

En esta instancia tenemos datos estadísticos en nuestro `data.farme`
`femicidios` y tenemos la información geográfica en un vector del tipo
`SpatialPolygonsDataFrame` llamado  `argentina`. Ambos vectores comparten una
dato común que es el nombre de la provincia, que aprovecharemos entonces para
combinar los mismos.

Para combinar las dos fuentes de información primero debemos normalizar el
nombre del campo de provincia, para que luego un "merge" nos funcione
fácilmente, para esto simplemente a `femcidios` modificamos la columna
"lugar_hecho" por "NAME_1", con este simple cambio ya podemos combinar la
información. Importante: el merge común de **R** no me funciono del todo bien
ya que el resultado final modificaba el orden de los datos, lo que producía que
al graficar las provincias salieran mal identificadas. Por lo que optamos por
la función `join` de `plyr` que es bastante más poderosa.


``` R

# Renombrara columnas para hacer merge
colnames(femicidios_x_provincia) <- c("NAME_1", "Femicidios")

# Merge de los datos
argentina@data <- join(argentina@data,poblacion)

> head(argentina@data[c(1,2,3,4,5,11)])
  ID_0 ISO    NAME_0 ID_1                 NAME_1 Femicidios
1   12 ARG Argentina    1           Buenos Aires        268
2   12 ARG Argentina    2                Córdoba         89
3   12 ARG Argentina    3              Catamarca          7
4   12 ARG Argentina    4                  Chaco         37
5   12 ARG Argentina    5                 Chubut         16
6   12 ARG Argentina    6 Ciudad de Buenos Aires         31
```

Por último, la parte linda y divertida de todo esto:

``` R

pal <- colorQuantile("YlGn", NULL, n = 5)
state_popup <- paste0("<strong>Estado: </strong>", 
                      argentina$NAME_1, 
                      "<br><strong>Femicidios: </strong>", 
                      argentina$Femicidios)

leaflet(data = argentina) %>%
    addProviderTiles("CartoDB.Positron") %>%
    addPolygons(fillColor = ~pal(Femicidios), 
                fillOpacity = 0.8, 
                color = "#BDBDC3", 
                weight = 1, 
                popup = state_popup)

```

Y obtenemos esto:
![ejemplo][ejemplo]


[ejemplo]: {{site.baseurl}}/_posts/2017/images/rplot-01.jpg
