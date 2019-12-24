---
title: "Algunos datos (tal vez interesantes) de las paso 2019"
author: "Patricio Moracho"
date: 2019-08-20
post_date: 2019-08-20
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
description: Algunos datos (tal vez interesantes) de las Paso 2019
tags:
  - desarrollo
  - R
output:
  github_page:    
    jekyllthat::jekylldown
  pdf_document: default
  html_document:
    df_print: paged
---

La justicia electoral publicó una serie de tablas que representa los datos de
los telegramas con el escrutinio de cada mesa. Una buena oportunidad para
practicar un poco con **`R`** y de paso intentar responder algunas preguntas.

Antes de empezar
----------------

La fuente oficial dónde descargar los datos es esta:
<http://descargaresultados.s3-sa-east-1.amazonaws.com/resultados.zip>, son
varios archivos delimitados por el pipe (`|`), sin embargo, elaboré un paquete
que ya trae precargados estos datos y además incorpora una reformulación de los
mismos bajo un modelo relacional clásico, que para el que este acostunbrado a
trabajar con bases de datos, como yo, seguramente es una foma más cómoda. El
paquete se llama [paso2019](https://github.com/pmoracho/paso2019), se puede
descar e instalar en **`R`** o bien, si ya disponemos de `devtools`,
simplemente podremos hacer:

``` r
devtools::install_gitgub("pmoracho/paso2019")
```

Luego simplemente cargamos el paquete junto con los otros que vamos a
necesitar. `ggelegant` es otro paquete que armé, que contiene un tema para
**ggplot2** en el que estoy trabajando, igual que el anterior:

``` r
devtools::install_gitgub("pmoracho/ggelegant")
```

**Importante**: A la fecha, los datos, telegramas y la página, están "off-line"
a la espera de la elección definitiva del 27 de octubre. Del paquete `paso2019`
se podrán usar todos los datos, pero las rutinas que consultan y visualizan los
telegramas, lamentablemente ya no funcionan.

``` r
library("paso2019")
library("tidyverse")
library("ggplot2")
library("ggelegant")
```

Aclaraciones iniciales
----------------------

Hay algunas inconsistencias en los datos que en algún momento puede llamar la
atención. Por empezar hay una inconsistencia entre las tres tablas de mesas,
descargadas del sitio oficial de los resultados. Podemos verificarlo así:

``` r
data.frame(tabla = c("mesas_totales", 
                     "mesas_totales_lista", 
                     "mesas_totales_agrp_politica"
                     ),
           registros = c( mesas_totales %>% distinct(CODIGO_MESA) %>% nrow(),
                          mesas_totales_lista %>% distinct(CODIGO_MESA) %>% nrow(),
                          mesas_totales_agrp_politica %>% distinct(CODIGO_MESA) %>% nrow()
                          )
           ) %>% 
    kable()
```

| tabla                          |  registros|
|:-------------------------------|----------:|
| mesas\_totales                 |     100142|
| mesas\_totales\_lista          |     100148|
| mesas\_totales\_agrp\_politica |     100148|

Podemos ver que `mesas_totales`tiene seis mesas menos que el resto de las
tablas. Particularmente son las siguientes:

``` r
mesas_totales_lista %>% 
  anti_join(mesas_totales, by = "CODIGO_MESA") %>% 
  distinct(CODIGO_MESA) %>% 
  kable()
```

| CODIGO\_MESA |
|:-------------|
| 0206300917X  |
| 0207900541X  |
| 0207900582X  |
| 0207900606X  |
| 0207900649X  |
| 0207900738X  |

La otra inconsistencia notable, es entre esta información y la que se publica
en la página web: <https://resultados.gob.ar>, la mesas escrutadas según esta
página son 100,156 mesas, los datos descargados, indican en el mejor de los
casos 100,148 mesas, es decir 8 mesas menos.

Preguntas
---------

### ¿Cuantas mesas fueron escrutadas y cuantas no?

``` r
mesas %>% 
  summarise(total = n()) %>% 
  as.numeric() -> mesas_totales

mesas %>% 
  group_by(escrutada=ifelse(escrutada, "Si", "No")) %>% 
  summarise(cantidad = n()) %>% 
  mutate(porcentaje=cantidad/mesas_totales*100) %>% 
  union(mesas %>% 
    summarise(escrutada = "Totales", cantidad = n(), porcentaje=100)
  ) %>% 
  arrange(-cantidad) %>% 
  kable()
```

| escrutada |  cantidad|  porcentaje|
|:----------|---------:|-----------:|
| Totales   |    103134|  100.000000|
| Si        |    100568|   97.511975|
| No        |      2566|    2.488025|

En este análisis hemos considerado las mesas no escrutadas, aquellas dónde la
cantidad de votos de todas las listas asociadas es cero. Aquí también hay una
discrepancia con la página, la cual informa un 98.6% de mesas escrutadas y en
el caso de los datos publicados, el porcentaje es algo menor 97.54%.

### ¿Quién ganó en la categoría de Presidente y Vice?

Acá es dónde tenemos que plantear todas las relaciones para llegar a la
información que necesitamos, alquien que venga del mundo de las bases de datos
se sentirá cómodo, los que vienen de la estadística, lo verán demasiado
complejo tal vez.

``` r
votos %>% 
  left_join(listas, by = "id_lista") %>% 
  left_join(agrupaciones, by = "id_agrupacion") %>% 
  left_join(categorias, by = "id_categoria") %>% 
  left_join(meta_agrupaciones, by = "id_meta_agrupacion") %>% 
  filter(nombre_categoria == "Presidente y Vicepresidente de la República") %>% 
  group_by(nombre_meta_agrupacion, votos_totales) %>% 
  summarise(votos = sum(votos)) %>%  
  ungroup() %>% 
  arrange(-votos) %>% 
  mutate(porcentaje = votos / votos_totales,
         acumulado = cumsum(porcentaje)) %>%
  select(nombre_meta_agrupacion, votos, porcentaje, acumulado) %>% 
  kable()
```

| nombre\_meta\_agrupacion                       |     votos|  porcentaje|  acumulado|
|:-----------------------------------------------|---------:|-----------:|----------:|
| FRENTE DE TODOS                                |  11622428|   0.4765586|  0.4765586|
| JUNTOS POR EL CAMBIO                           |   7825208|   0.3208599|  0.7974185|
| CONSENSO FEDERAL                               |   2007035|   0.0822952|  0.8797137|
| VOTOS en BLANCO                                |    758988|   0.0311211|  0.9108347|
| FRENTE DE IZQUIERDA Y DE TRABAJADORES - UNIDAD |    697776|   0.0286112|  0.9394459|
| FRENTE NOS                                     |    642662|   0.0263513|  0.9657972|
| UNITE POR LA LIBERTAD Y LA DIGNIDAD            |    533100|   0.0218589|  0.9876561|
| MOVIMIENTO AL SOCIALISMO                       |    173585|   0.0071176|  0.9947737|
| FRENTE PATRIOTA                                |     58575|   0.0024018|  0.9971754|
| MOVIMIENTO DE ACCION VECINAL                   |     36324|   0.0014894|  0.9986648|
| PARTIDO AUTONOMISTA                            |     32562|   0.0013352|  1.0000000|

De cualquier manera, con las tablas originales podemos hacer algo parecido:

``` r
mesas_totales_lista %>% 
  filter(CODIGO_CATEGORIA=="000100000000000") %>% 
  summarise(sum(VOTOS_LISTA)) %>% 
  as.numeric() -> votos_totales 

mesas_totales_lista %>% 
  filter(CODIGO_CATEGORIA=="000100000000000") %>% 
  left_join(descripcion_postulaciones, by = c("CODIGO_CATEGORIA", "CODIGO_AGRUPACION", "CODIGO_LISTA")) %>% 
  group_by(NOMBRE_AGRUPACION) %>% 
  summarise(votos=sum(VOTOS_LISTA)) %>% 
  arrange(-votos) %>% 
  mutate(porcentaje = votos / votos_totales,
         acumulado = cumsum(porcentaje)) %>% 
  select(NOMBRE_AGRUPACION, votos, porcentaje) %>% 
  kable()
```

| NOMBRE\_AGRUPACION                             |     votos|  porcentaje|
|:-----------------------------------------------|---------:|-----------:|
| FRENTE DE TODOS                                |  11622428|   0.4918660|
| JUNTOS POR EL CAMBIO                           |   7825208|   0.3311661|
| CONSENSO FEDERAL                               |   2007035|   0.0849386|
| FRENTE DE IZQUIERDA Y DE TRABAJADORES - UNIDAD |    697776|   0.0295302|
| FRENTE NOS                                     |    642662|   0.0271977|
| UNITE POR LA LIBERTAD Y LA DIGNIDAD            |    533100|   0.0225610|
| MOVIMIENTO AL SOCIALISMO                       |    173585|   0.0073462|
| FRENTE PATRIOTA                                |     58575|   0.0024789|
| MOVIMIENTO DE ACCION VECINAL                   |     36324|   0.0015372|
| PARTIDO AUTONOMISTA                            |     32562|   0.0013780|

En los datos originales, los **VOTOS en BLANCO** no se consideran al nivel de
una agrupación, por lo que no se ven en el agrupado por estas, hay que ir a
buscarlos a `mesas_totales`, en el modelo nuevo estos se consideran como si
fueran una agrupación más, lo que es más consistente con ley que regula las
Paso.

¿Cómo fue la distribución de votos de las agrupaciones más importantes?
-----------------------------------------------------------------------

Miremos la categoría de Predidente y Vice, primero armamos los datos:

``` r
votos %>% 
    filter(id_categoria == 137) %>% 
    left_join(listas, by = "id_lista") %>% 
    left_join(agrupaciones, by = "id_agrupacion") %>% 
    left_join(meta_agrupaciones, by = "id_meta_agrupacion") %>% 
    inner_join(votos %>% 
                  group_by(id_mesa, id_categoria) %>% 
                  summarize(total_mesa = sum(votos)) %>% 
                  filter(total_mesa != 0), by = c("id_mesa", "id_categoria")) %>% 
    mutate(porcentaje = votos/total_mesa) %>% 
    select(nombre_meta_agrupacion, id_mesa, porcentaje, votos) -> votos_porcentaje

votos_porcentaje %>% 
    group_by(nombre_meta_agrupacion) %>% 
    summarise(total_votos = sum(votos)) %>% 
    arrange(-total_votos) %>% 
    filter(total_votos > quantile(total_votos, .3)) -> top

colores <- c("#26a7ed", "#fbfb00", "#b8867b", "#950000", "#996600", "#4d4d4d", "#57BFEB")    
```

Y ahora la gráfica:

``` r
votos_porcentaje %>%
    inner_join(top, by = "nombre_meta_agrupacion") %>% 
    ggplot(aes(x=fct_reorder(nombre_meta_agrupacion, porcentaje), 
               y=porcentaje, 
               color=nombre_meta_agrupacion)) +
    scale_x_discrete(labels = function(x) str_wrap(x, 10)) +
    geom_boxplot(color=rev(colores)) +
    coord_flip() +
    labs(title="Porcentajes de Votos", 
         subtitle="Distribución de los % por mesa en la categoria Presidente", 
         caption="fuente: DINE", 
         x="",
         color=NULL) +
    theme_elegante_std(base_family = "Raleway") +
    theme(legend.position="none") +
    theme(axis.text.x = element_text(size=8))
```

<img src="/images/2019/2019-08-20-paso-2019-algunos-datos_files/figure-markdown_github/boxplot-1.png" style="display: block; margin: auto;" />

Es interesante ver el **boxplot** y prestar atención a los "outliers", en
particular los que caen el limite máximo, son las mesas dónde solo hay votos
para una determinada agrupación. Interesante que al revisar estos casos
vulneramos fácilmente el secreto del voto.

``` r
# Mesas con el 100% de votos a una agrupación
votos_porcentaje %>% 
  filter(porcentaje == 1) %>% 
  left_join(mesas, by = "id_mesa") %>%
  left_join(circuitos, by = "id_circuito") %>%
  left_join(secciones, by = "id_seccion") %>%
  left_join(distritos, by = "id_distrito") %>%
  left_join(establecimientos, by = "id_establecimiento") %>% 
  select(nombre_meta_agrupacion,
         nombre_distrito,
         nombre_seccion,
         nombre_circuito,
         nombre_establecimiento,
         codigo_mesa,
         votos) -> mesas_100

mesas_100 %>% 
  group_by(nombre_meta_agrupacion) %>% 
  summarise(mesas = n(), votos = sum(votos)) %>% 
  arrange(-votos) %>% 
  union(mesas_100 %>% 
    summarise(nombre_meta_agrupacion = "Total", mesas = n(), votos = sum(votos))
  ) %>% 
  kable()
```

| nombre\_meta\_agrupacion                       |  mesas|  votos|
|:-----------------------------------------------|------:|------:|
| Total                                          |     63|   2636|
| FRENTE NOS                                     |      1|      1|
| MOVIMIENTO AL SOCIALISMO                       |      1|      2|
| FRENTE DE IZQUIERDA Y DE TRABAJADORES - UNIDAD |      1|      3|
| JUNTOS POR EL CAMBIO                           |     20|    204|
| VOTOS en BLANCO                                |     19|    354|
| FRENTE DE TODOS                                |     21|   2072|

No lo voy a hacer, pero claramente es muy fácil llegar a estas mesas, para
luego ir al padrón y relacionar votos con personas, claro aún faltaría saber
exactamente quienes votaron y quienes no concurrieron, pero en estos casos es
claro que es muy sencillo vulnerar el secreto del voto.

Es raro no, que todos los votos vayan a una única agrupación ¿no? sin embargo,
estamos hablando de 63 mesas sobre 100,148, y no llegan a ser más de 2600
votos, por lo que no seamos suspicaces en esto. Podemos claro estudiar más en
detalle el tema:

``` r
# Revisemos 10 casos
mesas_100 %>% 
  select(nombre_meta_agrupacion,
         nombre_distrito,
         nombre_seccion,
         nombre_circuito,
         nombre_establecimiento,
         codigo_mesa,
         votos) %>% 
  arrange(-votos) %>% 
  head(10) %>% 
  kable()
```

| nombre\_meta\_agrupacion | nombre\_distrito    | nombre\_seccion | nombre\_circuito        | nombre\_establecimiento          | codigo\_mesa |  votos|
|:-------------------------|:--------------------|:----------------|:------------------------|:---------------------------------|:-------------|------:|
| FRENTE DE TODOS          | SANTIAGO DEL ESTERO | RÍO HONDO       | CIRCUITO POZUELOS       | ESCUELA N° 829                   | 2202202012X  |    279|
| VOTOS en BLANCO          | BUENOS AIRES        | BAHÍA BLANCA    | CIRCUITO 74             | ESCUELA EP N°70/ES N°25          | 0200700142X  |    272|
| FRENTE DE TODOS          | SANTIAGO DEL ESTERO | SAN MARTÍN      | CIRCUITO ATOJ POZO      | ESCUELA N° 747                   | 2202402154X  |    260|
| FRENTE DE TODOS          | SANTIAGO DEL ESTERO | ATAMISQUI       | CIRCUITO MEDELLIN       | ESCUELA N° 507                   | 2200500851X  |    246|
| FRENTE DE TODOS          | SANTIAGO DEL ESTERO | BANDA           | CIRCUITO CHAUPI POZO    | ESCUELA N° 66 MAURO CARRANZA     | 2200601184X  |    244|
| FRENTE DE TODOS          | SANTIAGO DEL ESTERO | CHOYA           | CIRCUITO VILLA LA PUNTA | COLEGIO SECUNDARIO JUAN N. BURES | 2200901327X  |    243|
| FRENTE DE TODOS          | SANTIAGO DEL ESTERO | RÍO HONDO       | CIRCUITO VINARA         | ESCUELA N° 867                   | 2202202007X  |    239|
| FRENTE DE TODOS          | FORMOSA             | MATACOS         | CIRCUITO 82             | EPEP N° 438                      | 0900801352X  |    213|
| FRENTE DE TODOS          | SANTIAGO DEL ESTERO | PELLEGRINI      | CIRCUITO AHI VEREMOS    | ESCUELA N° 1006                  | 2201801812X  |    171|
| JUNTOS POR EL CAMBIO     | SANTIAGO DEL ESTERO | CAPITAL         | CIRCUITO ESTE           | COLEGIO SAN FRANCISCO            | 2200100229X  |    111|

¿Que observamos? muchas mesas con pocos votos, por lo que es mucho más probable
que vayan todos a una única agrupación, ni hablar de las mesas que tienen 1
solo voto (eso podría ser motivo para otro análisis). Sin embargo también hay
varias mesas con una cantidad importante de votos, por ejemplo, con más de 100
votos hay 10 mesas, la mayoría de los votos se los lleva el **FRENTE de
TODOS**, una de **VOTO en BLANCO** y otra para **JUNTOS por el CAMBIO**. Si
queremos estudiar estos casos y verificar si existió algún error de carga,
podríamos obtener las `urls` de las imagenes de los telegramas:

``` r
mesas_100 %>% 
  filter(votos >= 100) %>% 
  mutate(url = get_telegrama_url(codigo_mesa),
         cmd = paste0("view_telegrama('", codigo_mesa, "')")) %>% 
  select(codigo_mesa, nombre_meta_agrupacion, votos, url) %>% 
  kable()
```

| codigo\_mesa | nombre\_meta\_agrupacion |  votos| url                                                                             |
|:-------------|:-------------------------|------:|:--------------------------------------------------------------------------------|
| 0900801352X  | FRENTE DE TODOS          |    213| <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/0900801352X/1.png> |
| 2200100229X  | JUNTOS POR EL CAMBIO     |    111| <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/2200100229X/1.png> |
| 2200500851X  | FRENTE DE TODOS          |    246| <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/2200500851X/1.png> |
| 2200601184X  | FRENTE DE TODOS          |    244| <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/2200601184X/1.png> |
| 2200901327X  | FRENTE DE TODOS          |    243| <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/2200901327X/1.png> |
| 2201801812X  | FRENTE DE TODOS          |    171| <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/2201801812X/1.png> |
| 2202202007X  | FRENTE DE TODOS          |    239| <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/2202202007X/1.png> |
| 2202202012X  | FRENTE DE TODOS          |    279| <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/2202202012X/1.png> |
| 2202402154X  | FRENTE DE TODOS          |    260| <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/2202402154X/1.png> |
| 0200700142X  | VOTOS en BLANCO          |    272| <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/0200700142X/1.png> |

O incluso, ver directamente la imagen, mediante:

``` r
view_telegrama('0900801352X')
view_telegrama('2200100229X')
view_telegrama('2200500851X')
view_telegrama('2200601184X')
view_telegrama('2200901327X')
view_telegrama('2201801812X')
view_telegrama('2202202007X')
view_telegrama('2202202012X')
view_telegrama('2202402154X')
view_telegrama('0200700142X')
```

La otra curiosidad que me surgió es:

### ¿Cual es la distribución de votos por mesa?

``` r
# Calculamos los votos totales por mesa
votos_porcentaje %>%
  group_by(id_mesa) %>% 
  summarize(total_votos = sum(votos)) -> mesas_votos
```

    ## Warning: The `printer` argument is deprecated as of rlang 0.3.0.
    ## This warning is displayed once per session.

``` r
summary(mesas_votos$total_votos)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##     1.0   244.0   261.0   252.9   274.0   351.0

Siempre dentro de la categoría Presidente y Vice, la media de votos por mesa es
de 253, con una curva centrada alrededor de los 300 votos por mesas y con un
claro sesgo hacía la derecha. La mesa con mayor cantidad de votos tuvo 351.

``` r # Calculamos la media de votos por mesa media_total_votos <-
round(mean(mesas_votos$total_votos),0)

# Para evitar un geom_histogram que es muy lento es que
# generamos esta función que hace lo mismo dibujando las
# barras mediante geom_rect
# + info: https://es.stackoverflow.com/questions/295767/c%c3%b3mo-mejorar-performance-de-un-geom-histogram

res = hist(mesas_votos$total_votos, plot=FALSE, breaks=1000)
dat = data.frame(xmin=head(res$breaks, -1L),
                 xmax=tail(res$breaks, -1L),
                 ymin=0.0,
                 ymax=res$counts)
  
ggplot(dat, aes(xmin=xmin, xmax=xmax, ymin=ymin, ymax=ymax)) +
  geom_rect(size=0.5, colour="#718FD7", fill="#718FD7") +
  geom_vline(aes(xintercept = media_total_votos),
             col = '#BC3204', size=1) +
  geom_text(aes(label = paste("media:", media_total_votos), y = 1400, x = media_total_votos+1), 
            vjust = -1, col='#BC3204',size = 4, angle = 90) +
  labs(title="Cantidad de votos por mesa", 
       subtitle="Distribución de cantidades de votos por mesa en la categoria Presidente", 
       caption="fuente: DINE", 
       x="Cantidad de mesas",
       y="Cantidad de votos",
       color=NULL) +
  theme_elegante_std(base_family = "Raleway") 
```

<img src="/images/2019/2019-08-20-paso-2019-algunos-datos_files/figure-markdown_github/hist-1.png" style="display: block; margin: auto;" />

### ¿Y.. cuales fueron los distritos que más votos aportaron a las principales agrupaciones?

En esta elección hubo dos grandes fuerzas, el **FRENTE de TODOS** y **JUNTOS
por el CAMBIO**, veamos dónde unos sacaron mayor diferencia sobre los otros.
Primero preparamos los datos de los 10 distritos más importantes para cada una
de estas agrupaciones:

``` r
votos_porcentaje %>% 
  filter(nombre_meta_agrupacion %in% c('FRENTE DE TODOS','JUNTOS POR EL CAMBIO')) %>% 
  left_join(mesas, by = "id_mesa") %>%
  left_join(secciones, by = "id_seccion") %>%
  left_join(distritos, by = "id_distrito") %>%
  mutate(fdt = ifelse(nombre_meta_agrupacion == 'FRENTE DE TODOS',votos, 0),
         jpc = ifelse(nombre_meta_agrupacion == 'JUNTOS POR EL CAMBIO',votos, 0)
         ) %>% 
  group_by(nombre_distrito, nombre_seccion) %>% 
  summarise(dif = sum(fdt-jpc)) %>% 
  ungroup() %>% 
  arrange(dif) -> diferencias_distritos

colores <- c("#26a7ed", "#fbfb00")    

diferencias_distritos %>% 
  head(10) %>% 
  union(diferencias_distritos %>% 
          tail(10)) %>% 
  mutate(nombre_distrito = case_when( nombre_distrito == 'CIUDAD AUTÓNOMA DE BUENOS AIRES' ~ 'CABA',  
                                      nombre_distrito == 'BUENOS AIRES' ~ 'BS.AS.',  
                                      TRUE ~ nombre_distrito)
            ) %>% 
  mutate(area = paste(nombre_distrito, nombre_seccion, sep = ' / '),
         agrupacion = ifelse(dif > 0, 'FRENTE DE TODOS', 'JUNTOS POR EL CAMBIO')) %>% 
  select(area,agrupacion,diferencia = dif) -> top_10_distritos
```

Y ahora sí, veamos los datos:

``` r
top_10_distritos %>% 
  ggplot(aes(x=fct_reorder(area,diferencia),y=diferencia)) +
  geom_bar(stat='identity', aes(fill=agrupacion), width=.7) +
  geom_text(aes(label=format(abs(diferencia), nsmall=0, big.mark=",")),  vjust=0.38) +
  
  scale_fill_manual(labels = function(x) str_wrap(x, 20), values = colores) + 
  scale_y_continuous(labels = scales::comma) +
  coord_flip() +
  labs(title='Top 10 de Distritos por agrupación', 
         subtitle="Diferencias en Votos entre las dos principales fuerzas", 
         caption="fuente: DINE", 
         x="",
         color=NULL) +
    theme(axis.text.y = element_text(size=8)) +
    theme_elegante_std(base_family = "Raleway")
```

<img src="/images/2019/2019-08-20-paso-2019-algunos-datos_files/figure-markdown_github/distritos_plot-1.png" style="display: block; margin: auto;" />

¿Y en cuantas mesas ganó cada agrupación?
-----------------------------------------

``` r
votos_porcentaje %>% 
  select(nombre_meta_agrupacion, id_mesa, votos) %>% 
  arrange(id_mesa, -votos) %>% 
  group_by(id_mesa) %>% 
  mutate(nr = row_number()) %>% 
  filter(nr == 1) %>% 
  group_by(nombre_meta_agrupacion) %>% 
  summarize(mesas = n()) %>% 
  arrange(-mesas) %>% 
  kable()
```

| nombre\_meta\_agrupacion                       |  mesas|
|:-----------------------------------------------|------:|
| FRENTE DE TODOS                                |  65656|
| JUNTOS POR EL CAMBIO                           |  30512|
| CONSENSO FEDERAL                               |    122|
| VOTOS en BLANCO                                |     77|
| FRENTE DE IZQUIERDA Y DE TRABAJADORES - UNIDAD |     23|
| FRENTE NOS                                     |     16|
| MOVIMIENTO AL SOCIALISMO                       |      5|
| UNITE POR LA LIBERTAD Y LA DIGNIDAD            |      4|
| FRENTE PATRIOTA                                |      3|
| MOVIMIENTO DE ACCION VECINAL                   |      1|
