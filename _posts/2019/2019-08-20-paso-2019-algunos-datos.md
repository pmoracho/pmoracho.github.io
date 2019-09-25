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
description: Algunos datos tal vez interesantes de las Paso 2019
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

La justicia electoral publicó una serie de tablas que representa los
datos de los telegramas con el escrutinio de cada mesa. Una buena
oportunidad para practicar un poco con **`R`** y de paso intentar
responder algunas preguntas.

## Antes de empezar

La fuente oficial dónde descargar los datos es esta:
<http://descargaresultados.s3-sa-east-1.amazonaws.com/resultados.zip>,
son varios archivos delimitados por el piepe (`|`), sin embargo, elaboré
un paquete que ya trae precargados estos datos y además incorpora una
reformulación de los mismos bajo un modelo relacional clásico, que para
el que este acostunbrado a trabajar con bases de datos, como yo,
seguramente es una foma más cómoda. El paquete se llama
[paso2019](https://github.com/pmoracho/paso2019), se puede descar e
instalar en **`R`** o bien, si ya disponemos de `devtools`, simplemente
podremos hacer:

``` r
devtools::install("pmoracho/paso2019")
```

Luego simplemente cargamos el paquete junto con los otros que vamos a
necesitar

``` r
library("paso2019")
library("tidyverse")
library("ggplot2")
library("ggelegant")
```

## Aclaraciones iniciales

Hay algunas inconsistencias en los datos que en algún momento puede
llamar la atención. Por empezar hay una inconsistencia entre las tres
tablas de mesas, descargadas del sitio oficial de los resultados.
Podemos verificarlo así:

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

    ##                         tabla registros
    ## 1               mesas_totales    100142
    ## 2         mesas_totales_lista    100148
    ## 3 mesas_totales_agrp_politica    100148

Podemos ver que `mesas_totales`tiene dos mesas menos que el resto de las
tablas. Particularmente son las siguientes:

``` r
mesas_totales_lista %>% 
  anti_join(mesas_totales, by = "CODIGO_MESA") %>% 
  distinct(CODIGO_MESA) %>% 
  kable()
```

| CODIGO\_MESA |
| :----------- |
| 0206300917X  |
| 0207900541X  |
| 0207900582X  |
| 0207900606X  |
| 0207900649X  |
| 0207900738X  |

La otra inconsistencia notable, es entre esta información y la que se
publica en la página web: <https://resultados.gob.ar/>, la mesas
escrutadas según esta página son 100,156 mesas, los datos descargados,
indican en el mejor de los casos 100,148 mesas, es decir 8 mesas menos.

## Preguntas

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

| escrutada | cantidad | porcentaje |
| :-------- | -------: | ---------: |
| Totales   |   103134 | 100.000000 |
| Si        |   100568 |  97.511975 |
| No        |     2566 |   2.488025 |

En este análisis hemos considerado las mesas no escrutadas, aquellas
dónde la cantidad de votos de todas las listas asociadas es cero. Aquí
también hay una discrepancia con la página, la cual informa un 98.6% de
mesas escrutadas y en el caso de los datos publicados, el porcentaje es
algo menor 97.54%.

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
  select(nombre_meta_agrupacion, votos, porcentaje, acumulado) %>% 
  kable()
```

| nombre\_meta\_agrupacion                       |    votos | porcentaje | acumulado |
| :--------------------------------------------- | -------: | ---------: | --------: |
| FRENTE DE TODOS                                | 11622428 |  0.4765586 | 0.4765586 |
| JUNTOS POR EL CAMBIO                           |  7825208 |  0.3208599 | 0.7974185 |
| CONSENSO FEDERAL                               |  2007035 |  0.0822952 | 0.8797137 |
| VOTOS en BLANCO                                |   758988 |  0.0311211 | 0.9108347 |
| FRENTE DE IZQUIERDA Y DE TRABAJADORES - UNIDAD |   697776 |  0.0286112 | 0.9394459 |
| FRENTE NOS                                     |   642662 |  0.0263513 | 0.9657972 |
| UNITE POR LA LIBERTAD Y LA DIGNIDAD            |   533100 |  0.0218589 | 0.9876561 |
| MOVIMIENTO AL SOCIALISMO                       |   173585 |  0.0071176 | 0.9947737 |
| FRENTE PATRIOTA                                |    58575 |  0.0024018 | 0.9971754 |
| MOVIMIENTO DE ACCION VECINAL                   |    36324 |  0.0014894 | 0.9986648 |
| PARTIDO AUTONOMISTA                            |    32562 |  0.0013352 | 1.0000000 |

Acá es dónde tenemos que plantear todas las relaciones para llegar a la
información que necesitamos, alquien que venga del mundo de las bases de
datos se sentirá cómodo, los que vienen de la estadística, lo verán
demasiado complejo tal vez. De cualquier manera, con las tablas
originales podemos hacer algo similar:

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

| NOMBRE\_AGRUPACION                             |    votos | porcentaje |
| :--------------------------------------------- | -------: | ---------: |
| FRENTE DE TODOS                                | 11622428 |  0.4918660 |
| JUNTOS POR EL CAMBIO                           |  7825208 |  0.3311661 |
| CONSENSO FEDERAL                               |  2007035 |  0.0849386 |
| FRENTE DE IZQUIERDA Y DE TRABAJADORES - UNIDAD |   697776 |  0.0295302 |
| FRENTE NOS                                     |   642662 |  0.0271977 |
| UNITE POR LA LIBERTAD Y LA DIGNIDAD            |   533100 |  0.0225610 |
| MOVIMIENTO AL SOCIALISMO                       |   173585 |  0.0073462 |
| FRENTE PATRIOTA                                |    58575 |  0.0024789 |
| MOVIMIENTO DE ACCION VECINAL                   |    36324 |  0.0015372 |
| PARTIDO AUTONOMISTA                            |    32562 |  0.0013780 |

En los datos originales, los **VOTOS en BLANCO** no se consideran al
nivel de una agrupación, por lo que no se ven en el agrupado por estas,
hay que ir a buscarlos a `mesas_totales`, en el modelo nuevo estos se
consideran como si fueran una agrupación más, lo que es más consistente
con ley que regula las Paso.

## ¿Cómo fue la distribución de votos?

Miremos la categoría de Predidente y Vice:

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

![](/images/2019/2019-08-20-paso-2019-algunos-datos_files/figure-gfm/boxplot-1.png)<!-- -->

Es interesante ver el **boxplot** y ver los “outliers” en particular los
que caen el limite máximo, son las mesas dónde solo hay votos para una
determinada agrupación. Interesante que revisar estos casos vulnera
fácilmente el secreto del voto.

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

| nombre\_meta\_agrupacion                       | mesas | votos |
| :--------------------------------------------- | ----: | ----: |
| FRENTE DE TODOS                                |    21 |  2072 |
| VOTOS en BLANCO                                |    19 |   354 |
| JUNTOS POR EL CAMBIO                           |    20 |   204 |
| FRENTE DE IZQUIERDA Y DE TRABAJADORES - UNIDAD |     1 |     3 |
| MOVIMIENTO AL SOCIALISMO                       |     1 |     2 |
| FRENTE NOS                                     |     1 |     1 |
| Total                                          |    63 |  2636 |

No lo voy a hacer, pero claramente es muy fácil llegar a estas mesas,
para luego ir al padrón y relacionar votos con personas, claro aún
faltaría saber exactamente quienes votaron y quienes no concurrieron,
pero en estos casos es claro que es muy sencillo vulnerar el secreto.

Es raro no, que todos los votos vayan a una única agrupación ¿no? sin
embargo, estamos hablando de 63 mesas sobre 100,148, y no llegan a ser
más de 2600 votos, por lo que no seamos suspicaces en esto. Podemos
claro estudiar más en detalle el tema:

``` r
mesas_100 %>% 
  select(nombre_meta_agrupacion,
         nombre_distrito,
         nombre_seccion,
         nombre_circuito,
         nombre_establecimiento,
         codigo_mesa,
         votos) %>% 
  arrange(-votos) %>% 
  kable()
```

| nombre\_meta\_agrupacion                       | nombre\_distrito    | nombre\_seccion     | nombre\_circuito                   | nombre\_establecimiento                | codigo\_mesa | votos |
| :--------------------------------------------- | :------------------ | :------------------ | :--------------------------------- | :------------------------------------- | :----------- | ----: |
| FRENTE DE TODOS                                | SANTIAGO DEL ESTERO | RÍO HONDO           | CIRCUITO POZUELOS                  | ESCUELA N° 829                         | 2202202012X  |   279 |
| VOTOS en BLANCO                                | BUENOS AIRES        | BAHÍA BLANCA        | CIRCUITO 74                        | ESCUELA EP N°70/ES N°25                | 0200700142X  |   272 |
| FRENTE DE TODOS                                | SANTIAGO DEL ESTERO | SAN MARTÍN          | CIRCUITO ATOJ POZO                 | ESCUELA N° 747                         | 2202402154X  |   260 |
| FRENTE DE TODOS                                | SANTIAGO DEL ESTERO | ATAMISQUI           | CIRCUITO MEDELLIN                  | ESCUELA N° 507                         | 2200500851X  |   246 |
| FRENTE DE TODOS                                | SANTIAGO DEL ESTERO | BANDA               | CIRCUITO CHAUPI POZO               | ESCUELA N° 66 MAURO CARRANZA           | 2200601184X  |   244 |
| FRENTE DE TODOS                                | SANTIAGO DEL ESTERO | CHOYA               | CIRCUITO VILLA LA PUNTA            | COLEGIO SECUNDARIO JUAN N. BURES       | 2200901327X  |   243 |
| FRENTE DE TODOS                                | SANTIAGO DEL ESTERO | RÍO HONDO           | CIRCUITO VINARA                    | ESCUELA N° 867                         | 2202202007X  |   239 |
| FRENTE DE TODOS                                | FORMOSA             | MATACOS             | CIRCUITO 82                        | EPEP N° 438                            | 0900801352X  |   213 |
| FRENTE DE TODOS                                | SANTIAGO DEL ESTERO | PELLEGRINI          | CIRCUITO AHI VEREMOS               | ESCUELA N° 1006                        | 2201801812X  |   171 |
| JUNTOS POR EL CAMBIO                           | SANTIAGO DEL ESTERO | CAPITAL             | CIRCUITO ESTE                      | COLEGIO SAN FRANCISCO                  | 2200100229X  |   111 |
| FRENTE DE TODOS                                | SANTIAGO DEL ESTERO | MORENO              | CIRCUITO STAYLE                    | ESCUELA N° 842                         | 2201601635X  |    63 |
| FRENTE DE TODOS                                | SANTIAGO DEL ESTERO | MITRE               | CIRCUITO ALBARDON                  | ESCUELA N° 578                         | 2201501631X  |    40 |
| JUNTOS POR EL CAMBIO                           | CATAMARCA           | BELÉN               | CIRCUITO 113A                      | ESC. PROV. NRO. 474                    | 0301300806X  |    27 |
| FRENTE DE TODOS                                | FORMOSA             | PATIÑO              | CIRCUITO 51                        | EPEP N° 13 REP. ARGENTINA              | 0900601105X  |    26 |
| FRENTE DE TODOS                                | CHACO               | TAPENAGA            | CIRCUITO 72                        | E.G.B. 15 RAUL A PIAT                  | 0600901439X  |    25 |
| VOTOS en BLANCO                                | CHACO               | MAYOR L. J. FONTANA | CIRCUITO 77                        | E.E.T. 25                              | 0601101494X  |    23 |
| JUNTOS POR EL CAMBIO                           | BUENOS AIRES        | CORONEL DORREGO     | CIRCUITO 235                       | ESCUELA EP N°6                         | 0202500035X  |    13 |
| VOTOS en BLANCO                                | LA RIOJA            | CAPITAL             | CIRCUITO LA RIOJA (CAPITAL)        | ESC. PROV. 369 MESTRO HUMBERTO PEREYRA | 1200100128X  |    13 |
| JUNTOS POR EL CAMBIO                           | SANTA FE            | VERA                | CIRCUITO GUAYCURU                  | C.E.R.N°270 GRAL.J.DE S.MARTIN         | 2102208136X  |    10 |
| JUNTOS POR EL CAMBIO                           | CATAMARCA           | BELÉN               | CIRCUITO 110                       | ESC. PROV. NRO. 468                    | 0301300797X  |     8 |
| FRENTE DE TODOS                                | FORMOSA             | PATIÑO              | CIRCUITO 065 A                     | EPEP N° 162                            | 0900601217X  |     8 |
| VOTOS en BLANCO                                | SAN LUIS            | PEDERNERA           | CIRCUITO CIUDAD V.MERCEDES (3001B) | EPA NØ10 M.E.V.LUCERO-PRIMARIA         | 1900300621X  |     6 |
| JUNTOS POR EL CAMBIO                           | CÓRDOBA             | RÍO CUARTO          | CIRCUITO 173                       | ESC JOSE DE SAN MARTIN                 | 0401305963X  |     5 |
| VOTOS en BLANCO                                | SAN LUIS            | PUEYRREDÓN          | CIRCUITO SAN LUIS (1014)           | INST.SAN LUIS REY DE FRANCIA           | 1900100279X  |     5 |
| VOTOS en BLANCO                                | SAN LUIS            | GOBERNADOR DUPUY    | CIRCUITO BUENA ESPERANZA           | ESC.EPEM NØ4 CORTES APARICIO           | 1900901188X  |     5 |
| JUNTOS POR EL CAMBIO                           | BUENOS AIRES        | OLAVARRÍA           | CIRCUITO 716                       | ESCUELA ES N°17                        | 0208400232X  |     4 |
| JUNTOS POR EL CAMBIO                           | BUENOS AIRES        | TRENQUE LAUQUEN     | CIRCUITO 961B                      | ESCUELA EP N°12                        | 0211500117X  |     4 |
| JUNTOS POR EL CAMBIO                           | CÓRDOBA             | TULUMBA             | CIRCUITO 365                       | ESC LUIS E JUNCOS(EX NAC 212)          | 0402508446X  |     4 |
| FRENTE DE TODOS                                | ENTRE RÍOS          | FEDERAL             | CIRCUITO LAS ACHIRAS               | ESC PROV N° 32 “BRIG GRAL URDINARRAIN” | 0801402861X  |     4 |
| JUNTOS POR EL CAMBIO                           | SAN LUIS            | AYACUCHO            | CIRCUITO EL CALDEN                 | ESC.NØ196 MTRA.M.DE RAMIREZ            | 1900701154X  |     4 |
| VOTOS en BLANCO                                | BUENOS AIRES        | MERLO               | CIRCUITO 652A                      | ESCUELA EP N°23                        | 0207700315X  |     4 |
| VOTOS en BLANCO                                | SAN LUIS            | PEDERNERA           | CIRCUITO CIUDAD V.MERCEDES (3016)  | ESC. NØ43 DR. TOMAS JOFRE              | 1900300808X  |     4 |
| VOTOS en BLANCO                                | SAN LUIS            | PEDERNERA           | CIRCUITO CIUDAD V.MERCEDES (3016)  | ESC. NØ43 DR. TOMAS JOFRE              | 1900300808X  |     4 |
| VOTOS en BLANCO                                | SAN LUIS            | BELGRANO            | CIRCUITO LA CALERA                 | CTRO.EDUC.NØ7 GEOL.R.GUIÑAZU           | 1900801174X  |     4 |
| JUNTOS POR EL CAMBIO                           | BUENOS AIRES        | TRES ARROYOS        | CIRCUITO 977                       | ESCUELA EP N°28                        | 0211700156X  |     3 |
| FRENTE DE IZQUIERDA Y DE TRABAJADORES - UNIDAD | CATAMARCA           | BELÉN               | CIRCUITO 111                       | ESC. PROV. NRO. 347                    | 0301300800X  |     3 |
| VOTOS en BLANCO                                | CÓRDOBA             | CRUZ DEL EJE        | CIRCUITO 42                        | ESCUELA PROV.M. MORENO                 | 0400404111X  |     3 |
| JUNTOS POR EL CAMBIO                           | BUENOS AIRES        | GENERAL VILLEGAS    | CIRCUITO 411                       | ESCUELA EP N°45                        | 0205500078X  |     2 |
| JUNTOS POR EL CAMBIO                           | BUENOS AIRES        | SAN PEDRO           | CIRCUITO 922                       | ESCUELA EP N°22                        | 0210800152X  |     2 |
| MOVIMIENTO AL SOCIALISMO                       | CATAMARCA           | VALLE VIEJO         | CIRCUITO 76                        | ESC. PROV. NRO. 272                    | 0300800654X  |     2 |
| FRENTE DE TODOS                                | CÓRDOBA             | GENERAL SAN MARTÍN  | CIRCUITO 88                        | ESC.R.DE ESCALADA                      | 0400604435X  |     2 |
| FRENTE DE TODOS                                | CHACO               | PRIMERO DE MAYO     | CIRCUITO 31                        | E.G.B. 278 FRANCISCO SOUILHE           | 0600201048X  |     2 |
| FRENTE DE TODOS                                | FORMOSA             | LAISHÍ              | CIRCUITO 022 C                     | EPES N° 18 LIB. G. SAN MARTIN          | 0900200594X  |     2 |
| FRENTE DE TODOS                                | FORMOSA             | PATIÑO              | CIRCUITO 060 A                     | EPEP N° 9 DR. N. AVELLANEDA            | 0900601189X  |     2 |
| VOTOS en BLANCO                                | BUENOS AIRES        | EZEIZA              | CIRCUITO 289                       | COLEGIO SAN IGNACIO (EP/ES)            | 0213200059X  |     2 |
| VOTOS en BLANCO                                | SAN LUIS            | PEDERNERA           | CIRCUITO NUEVA ESCOCIA             | ESC.NØ200 CAP FAUSTO GAVAZZI           | 1900300885X  |     2 |
| VOTOS en BLANCO                                | SANTIAGO DEL ESTERO | SILIPICA            | CIRCUITO MANOGASTA                 | ESCUELA N° 810 SANTA BARBARA           | 2202302135X  |     2 |
| JUNTOS POR EL CAMBIO                           | BUENOS AIRES        | CARLOS TEJEDOR      | CIRCUITO 171                       | ESCUELA EP N°5/ES N°5                  | 0202000021X  |     1 |
| JUNTOS POR EL CAMBIO                           | BUENOS AIRES        | NECOCHEA            | CIRCUITO 687                       | ESCUELA EP N°5                         | 0208200231X  |     1 |
| JUNTOS POR EL CAMBIO                           | BUENOS AIRES        | NECOCHEA            | CIRCUITO 687                       | ESCUELA EP N°5                         | 0208200231X  |     1 |
| JUNTOS POR EL CAMBIO                           | BUENOS AIRES        | TRES ARROYOS        | CIRCUITO 969                       | ESCUELA EP N°6                         | 0211700155X  |     1 |
| JUNTOS POR EL CAMBIO                           | CHACO               | LIBERTAD            | CIRCUITO 36                        | E.G.B. 113                             | 0600301083X  |     1 |
| JUNTOS POR EL CAMBIO                           | CHACO               | GENERAL BELGRANO    | CIRCUITO 116                       | E.G.B. 515 I I DE BARRERA              | 0601802235X  |     1 |
| FRENTE DE TODOS                                | FORMOSA             | PATIÑO              | CIRCUITO 47                        | EPEP N° 100                            | 0900601077X  |     1 |
| FRENTE DE TODOS                                | SAN LUIS            | SAN MARTÍN          | CIRCUITO CRUZ DE CAÑA              | ESC.NØ285 GDRO.D.ORTIZ                 | 1900500991X  |     1 |
| FRENTE DE TODOS                                | SAN LUIS            | AYACUCHO            | CIRCUITO EL RINCON                 | ESC.NØ298 MTRO.E.DARACT                | 1900701112X  |     1 |
| FRENTE NOS                                     | SAN LUIS            | BELGRANO            | CIRCUITO LOMAS BLANCAS             | EX ESC.NØ336 MTRO.DIOG.TABOADA         | 1900801171X  |     1 |
| JUNTOS POR EL CAMBIO                           | SANTA FE            | GENERAL OBLIGADO    | CIRCUITO EL RICARDITO              | C.E.R.N°322 MARTIN M.DE GUEMES         | 2100701577X  |     1 |
| VOTOS en BLANCO                                | CATAMARCA           | CAPAYÁN             | CIRCUITO 13                        | ESC. PROV. NRO. 266                    | 0300200407X  |     1 |
| VOTOS en BLANCO                                | CÓRDOBA             | SANTA MARÍA         | CIRCUITO 320                       | OLEGARIO V ANDRADE (CIRC 322)          | 0402107952X  |     1 |
| VOTOS en BLANCO                                | SANTA FE            | SAN JERÓNIMO        | CIRCUITO GALVEZ                    | ESC.NAC.SUP.DE COMERCIO N°44           | 2101807172X  |     1 |
| VOTOS en BLANCO                                | TUCUMÁN             | CAPITAL             | CIRCUITO 16                        | COLEGIO NTRA SRA DE MONSERRAT          | 2300100771X  |     1 |
| VOTOS en BLANCO                                | TUCUMÁN             | CRUZ ALTA           | CIRCUITO 167                       | ESCUELA JOSE COLOMBRES                 | 2301202913X  |     1 |

¿Que observamos? muchas mesas con pocos votos, por lo que es mucho más
probable que vayan todos a una única agrupación, ni hablar de las mesas
que tienen 1 solo voto (eso será motivo para otro análisis). Sin embargo
también hay varias mesas con una cantidad importante de votos, por
ejemplo, com ás de 100 votos hay 10 mesas, la mayoría del **FRENTE de
TODOS**, una de **VOTO en BLANCO** y otra para **JUNTOS por el CAMBIO**.
Si queremos estudiar estos casos y verificar si existió algún error de
carga, podríamos obtener las `urls` de las imagenes de los telegramas:

``` r
mesas_100 %>% 
  filter(votos >= 100) %>% 
  mutate(url = get_telegrama_url(codigo_mesa),
         cmd = paste0("view_telegrama('", codigo_mesa, "')")) %>% 
  select(codigo_mesa, nombre_meta_agrupacion, votos, url, cmd) %>% 
  kable()
```

| codigo\_mesa | nombre\_meta\_agrupacion | votos | url                                                                             | cmd                            |
| :----------- | :----------------------- | ----: | :------------------------------------------------------------------------------ | :----------------------------- |
| 0900801352X  | FRENTE DE TODOS          |   213 | <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/0900801352X/1.png> | view\_telegrama(‘0900801352X’) |
| 2200100229X  | JUNTOS POR EL CAMBIO     |   111 | <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/2200100229X/1.png> | view\_telegrama(‘2200100229X’) |
| 2200500851X  | FRENTE DE TODOS          |   246 | <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/2200500851X/1.png> | view\_telegrama(‘2200500851X’) |
| 2200601184X  | FRENTE DE TODOS          |   244 | <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/2200601184X/1.png> | view\_telegrama(‘2200601184X’) |
| 2200901327X  | FRENTE DE TODOS          |   243 | <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/2200901327X/1.png> | view\_telegrama(‘2200901327X’) |
| 2201801812X  | FRENTE DE TODOS          |   171 | <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/2201801812X/1.png> | view\_telegrama(‘2201801812X’) |
| 2202202007X  | FRENTE DE TODOS          |   239 | <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/2202202007X/1.png> | view\_telegrama(‘2202202007X’) |
| 2202202012X  | FRENTE DE TODOS          |   279 | <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/2202202012X/1.png> | view\_telegrama(‘2202202012X’) |
| 2202402154X  | FRENTE DE TODOS          |   260 | <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/2202402154X/1.png> | view\_telegrama(‘2202402154X’) |
| 0200700142X  | VOTOS en BLANCO          |   272 | <https://www.resultados2019.gob.ar/opt/jboss/rct/tally/pages/0200700142X/1.png> | view\_telegrama(‘0200700142X’) |

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

Siempre dentro de la categoría Presidente y Vice

``` r
# Calculamos los votos totales por mesa
votos_porcentaje %>%
  group_by(id_mesa) %>% 
  summarize(total_votos = sum(votos)) -> mesas_votos

# Calculamos la media de votos por mesa
media_total_votos <- round(mean(mesas_votos$total_votos),0)

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

![](/images/2019/2019-08-20-paso-2019-algunos-datos_files/figure-gfm/hist_setup_a-1.png)<!-- -->
