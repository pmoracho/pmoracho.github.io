---
title: "Algunos datos de las paso 2019"
author: "Patricio Moracho"
date: "19 de agosto de 2019"
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
description: Algunos datos talvez interesantes de las Paso 2019
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

La justicia electoral publicó una serie de tablas que representa los datos de los telegramas con el escrutinio de cada mesa. Una buena oportunidad para practicar un poco con **`R`** y de paso intentar responder algunas preguntas.

Antes de empezar
----------------

La fuente oficial dónde descargar los datos es esta: <http://descargaresultados.s3-sa-east-1.amazonaws.com/resultados.zip>, son varios archivos delimitados por el piepe (`|`), sin embargo, elaboré un paquete que ya trae precargados estos datos y además incorpora una reformulación de los mismos bajo un modelo relacional clásico, que para el que este acostunbrado a trabajar con bases de datos, como yo, seguramente es una foma más cómoda. El paquete se llama [paso2019](https://github.com/pmoracho/paso2019), se puede descar e instalar en **`R`** o bien, si ya disponemos de `devtools`, simplemente podremos hacer:

``` r
devtools::install("pmoracho/paso2019")
```

Luego simplemente cargamos el paquete junto con los otros que vamos a necesitar

``` r
library("paso2019")
library("tidyverse")
library("ggplot2")
library("ggelegant")
```

Aclaraciones iniciales
----------------------

Hay algunas inconsistencias en los datos que en algún momento puede llamar la atención. Por empezar hay una inconsistencia entre las tres tablas de mesas, descargadas del sitio oficial de los resultados. Podemos verificarlo así:

``` r
data.frame(tabla = c("mesas_totales", 
                     "mesas_totales_lista", 
                     "mesas_totales_agrp_politica"
                     ),
           registros = c( mesas_totales %>% distinct(CODIGO_MESA) %>% nrow(),
                          mesas_totales_lista %>% distinct(CODIGO_MESA) %>% nrow(),
                          mesas_totales_agrp_politica %>% distinct(CODIGO_MESA) %>% nrow()
                          )
           )
```

    ## Warning: The `printer` argument is deprecated as of rlang 0.3.0.
    ## This warning is displayed once per session.

    ##                         tabla registros
    ## 1               mesas_totales    100142
    ## 2         mesas_totales_lista    100148
    ## 3 mesas_totales_agrp_politica    100148

Podemos ver que `mesas_totales`tiene dos mesas menos que el resto de las tablas. Particularmente son las siguientes:

``` r
mesas_totales_lista %>% 
  anti_join(mesas_totales, by = "CODIGO_MESA") %>% 
  distinct(CODIGO_MESA)
```

    ##   CODIGO_MESA
    ## 1 0206300917X
    ## 2 0207900541X
    ## 3 0207900582X
    ## 4 0207900606X
    ## 5 0207900649X
    ## 6 0207900738X

La otra inconsistencia notable, es entre esta información y la que se publica en la página web: <https://resultados.gob.ar/>, la mesas escrutadas según esta página son 100,156 mesas, los datos descargados, indican en el mejor de los casos 100,148 mesas, es decir 8 mesas menos.

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
  arrange(-cantidad) 
```

    ## # A tibble: 3 x 3
    ##   escrutada cantidad porcentaje
    ##   <chr>        <int>      <dbl>
    ## 1 Totales     103134     100   
    ## 2 Si          100568      97.5 
    ## 3 No            2566       2.49

En este análisis hemos considerado las mesas no escrutadas, aquellas dónde la cantidad de votos de todas las listas asociadas es cero. Aquí también hay una discrepancia con la página, la cual informa un 98.6% de mesas escrutadas y en el caso de los datos publicados, el porcentaje es algo menor 97.54%.

### ¿Quién ganó en la categoría de Presidente y Vice?

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
  select(nombre_meta_agrupacion, votos, porcentaje, acumulado)
```

    ## # A tibble: 11 x 4
    ##    nombre_meta_agrupacion                        votos porcentaje acumulado
    ##    <chr>                                         <dbl>      <dbl>     <dbl>
    ##  1 FRENTE DE TODOS                              1.16e7    0.477       0.477
    ##  2 JUNTOS POR EL CAMBIO                         7.83e6    0.321       0.797
    ##  3 CONSENSO FEDERAL                             2.01e6    0.0823      0.880
    ##  4 VOTOS en BLANCO                              7.59e5    0.0311      0.911
    ##  5 FRENTE DE IZQUIERDA Y DE TRABAJADORES - UN…  6.98e5    0.0286      0.939
    ##  6 FRENTE NOS                                   6.43e5    0.0264      0.966
    ##  7 UNITE POR LA LIBERTAD Y LA DIGNIDAD          5.33e5    0.0219      0.988
    ##  8 MOVIMIENTO AL SOCIALISMO                     1.74e5    0.00712     0.995
    ##  9 FRENTE PATRIOTA                              5.86e4    0.00240     0.997
    ## 10 MOVIMIENTO DE ACCION VECINAL                 3.63e4    0.00149     0.999
    ## 11 PARTIDO AUTONOMISTA                          3.26e4    0.00134     1

Acá es dónde tenemos que plantear todas las relaciones para llegar a la información que necesitamos, alquien que venga del mundo de las bases de datos se sentirá cómodo, los que vienen de la estadística, lo verán demasiado complejo tal vez. De cualquier manera, con las tablas originales podemos hacer algo similar:

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
  select(NOMBRE_AGRUPACION, votos, porcentaje)
```

    ## # A tibble: 10 x 3
    ##    NOMBRE_AGRUPACION                                 votos porcentaje
    ##    <chr>                                             <dbl>      <dbl>
    ##  1 FRENTE DE TODOS                                11622428    0.492  
    ##  2 JUNTOS POR EL CAMBIO                            7825208    0.331  
    ##  3 CONSENSO FEDERAL                                2007035    0.0849 
    ##  4 FRENTE DE IZQUIERDA Y DE TRABAJADORES - UNIDAD   697776    0.0295 
    ##  5 FRENTE NOS                                       642662    0.0272 
    ##  6 UNITE POR LA LIBERTAD Y LA DIGNIDAD              533100    0.0226 
    ##  7 MOVIMIENTO AL SOCIALISMO                         173585    0.00735
    ##  8 FRENTE PATRIOTA                                   58575    0.00248
    ##  9 MOVIMIENTO DE ACCION VECINAL                      36324    0.00154
    ## 10 PARTIDO AUTONOMISTA                               32562    0.00138

En los datos originales, los **VOTOS en BLANCO** no se consideran al nivel de una agrupación, por lo que no se ven en el agrupado por estas, hay que ir a buscarlos a `mesas_totales`, en el modelo nuevo estos se consideran como si fueran una agrupación más, lo que es más consistente con ley que regula las Paso.

¿Cómo fue la distribución de votos?
-----------------------------------

``` r
votos %>% 
    left_join(categorias, by = "id_categoria") %>% 
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

![](/images/2019/2019-08-20-paso-2019-algunos-datos_files/figure-markdown_github/boxplot-1.png)

Es interesante ver el **boxplot** y ver los "outliers" en particular los que caen el limite máximo, son las mesas dónde solo hay votos para una determinada agrupación. Interesante que en estos casos se vulnera fácilmente el secreto del voto.

``` r
# Mesas con el 100% de votos a una agrupación
votos_porcentaje %>% 
    filter(porcentaje == 1) %>% 
    left_join(mesas, by = "id_mesa") %>%
    left_join(circuitos, by = "id_circuito") %>%
    left_join(secciones, by = "id_seccion") %>%
    left_join(distritos, by = "id_distrito") %>%
    left_join(establecimientos, by = "id_establecimiento") %>% 
    group_by(nombre_meta_agrupacion) %>% 
    summarise(mesas=n(), votos=sum(votos)) %>% 
    arrange(-votos)
```

    ## # A tibble: 6 x 3
    ##   nombre_meta_agrupacion                         mesas votos
    ##   <chr>                                          <int> <dbl>
    ## 1 FRENTE DE TODOS                                   21  2072
    ## 2 VOTOS en BLANCO                                   19   354
    ## 3 JUNTOS POR EL CAMBIO                              20   204
    ## 4 FRENTE DE IZQUIERDA Y DE TRABAJADORES - UNIDAD     1     3
    ## 5 MOVIMIENTO AL SOCIALISMO                           1     2
    ## 6 FRENTE NOS                                         1     1

No lo voy a hacer, pero claramente es muy fácil llegar a estas mesas, para luego ir al padrón y relacionar votos con personas, claro aún faltaría saber exactamente quienes votaron y quienes no concurrieron, pero en estos casos es claro que es muy sencillo vulnerar el secreto.
