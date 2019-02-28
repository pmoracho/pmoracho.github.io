---
layout: post
published: true
date: '2019-02-04'
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
title: Ejemplos de gráficos con Ggplot2
description: Ejemplos de gráficos con Ggplot2 y el theme_elegante()
tags:
  - desarrollo
  - R
---

Ejemplo de graficas ggplot2 combinadas con theme\_elegante()
================
Patricio Moracho
2019-02-28

Hace un tiempo que estoy robando ideas de todos lados como para armar un tema de **ggplot2** que sea elegante, simple y visualmente atractivo. Lo llame `theme_elegante()`, muy original lo mio. El código es el siguiente:

``` r
#######################
# CREATING PLOT THEMES
#######################
library(extrafont)

# Create Base Theme
#------------------
theme_elegante <- function(base_size = 10,
                           base_family = "Raleway"
                           )
    {
    color.background = "#FFFFFF" # Chart Background
    color.grid.major = "#D9D9D9" # Chart Gridlines
    color.axis.text = "#666666" # 
    color.axis.title = "#666666" # 
    color.title = "#666666"
    color.subtitle = "#666666"
    strip.background.color = '#9999CC'
    
    ret <-
        theme_bw(base_size=base_size) +
        
        # Set the entire chart region to a light gray color
        theme(panel.background=element_rect(fill=color.background, color=color.background)) +
        theme(plot.background=element_rect(fill=color.background, color=color.background)) +
        theme(panel.border=element_rect(color=color.background)) +
        
        # Format the grid
        theme(panel.grid.major=element_line(color=color.grid.major,size=.55, linetype="dotted")) +
        theme(panel.grid.minor=element_line(color=color.grid.major,size=.55, linetype="dotted")) +
        theme(axis.ticks=element_blank()) +
        
        # Format the legend, but hide by default
        theme(legend.position="none") +
        theme(legend.background = element_rect(fill=color.background)) +
        theme(legend.text = element_text(size=base_size-3,color=color.axis.title, family = base_family)) +
        
        theme(strip.text.x = element_text(size=base_size,color=color.background, family = base_family)) +
        theme(strip.text.y = element_text(size=base_size,color=color.background, family = base_family)) +
        #theme(strip.background = element_rect(fill=strip.background.color, linetype="blank")) +
        theme(strip.background = element_rect(fill = "grey70", colour = NA)) +
        # theme(panel.border= element_rect(fill = NA, colour = "grey70", size = rel(1)))+
        # Set title and axis labels, and format these and tick marks
        theme(plot.title=element_text(color=color.title, 
                                      size=20, 
                                      vjust=1.25, 
                                      family=base_family, 
                                      hjust = 0.5
                                      )) +
        
        theme(plot.subtitle=element_text(color=color.subtitle, size=base_size+2, family = base_family,  hjust = 0.5))  +
        
        theme(axis.text.x=element_text(size=base_size,color=color.axis.text, family = base_family)) +
        theme(axis.text.y=element_text(size=base_size,color=color.axis.text, family = base_family)) +
        theme(text=element_text(size=base_size, color=color.axis.text, family = base_family)) +
        
        theme(axis.title.x=element_text(size=base_size+2,color=color.axis.title, vjust=0, family = base_family)) +
        theme(axis.title.y=element_text(size=base_size+2,color=color.axis.title, vjust=1.25, family = base_family)) +
        theme(plot.caption=element_text(size=base_size-2,color=color.axis.title, vjust=1.25, family = base_family)) +
        
        # Legend  
        theme(legend.text=element_text(size=base_size,color=color.axis.text, family = base_family)) +
        theme(legend.title=element_text(size=base_size,color=color.axis.text, family = base_family)) +
        theme(legend.key=element_rect(colour = color.background, fill = color.background)) +
        theme(legend.position="bottom", 
              legend.box = "horizontal", 
              legend.title = element_blank(),
              legend.key.width = unit(.75, "cm"),
              legend.key.height = unit(.75, "cm"),
              legend.spacing.x = unit(.25, 'cm'),
              legend.spacing.y = unit(.25, 'cm'),
              legend.margin = margin(t=0, r=0, b=0, l=0, unit="cm")) +

        # Plot margins
        theme(plot.margin = unit(c(.5, .5, .5, .5), "cm"))
    
    ret
}
```

Y a continuación algunos ejemplos de gráficas tradicionales de **ggplot2**.

Box plot
--------

El Boxplot es probablemente uno de los tipos de gráficos más comunes. Da un buen resumen de una o varias variables numéricas. La línea que divide la caja en 2 partes representa la mediana de los datos. El final de la caja muestra los cuartiles superior e inferior. Las líneas extremas muestran el valor más alto y el más bajo excluyendo los valores atípicos. Tenga en cuenta que Boxplot oculta el número de valores detrás de la variable. Por lo tanto, es muy recomendable imprimir el número de observaciones, añadir una observación única con fluctuaciones o usar un Violinplot si tiene muchas observaciones.

``` r
ggplot(ToothGrowth, aes(x=factor(dose), y=len, fill=factor(dose))) + 
    geom_boxplot() +
    labs(title="El efecto de la vitamina C", 
         subtitle="en el crecimiento de los dientes de los cerdos de Guinea", 
         caption="fuente: C. I. Bliss (1952) The Statistics of Bioassay. Academic Press.", 
         y="Longitud %", 
         x="Dosis",
         color=NULL) +  # title and caption
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/box_plots-1.png)

``` r
ggplot(mpg, aes(x=class, y=hwy)) + 
    geom_boxplot(
        
        # custom boxes
        color="blue",
        fill="blue",
        alpha=0.2,
        
        # Notch?
        notch=TRUE,
        notchwidth = 0.8,
        
        # custom outliers
        outlier.colour="red",
        outlier.fill="red",
        outlier.size=3
    
    ) +
    labs(title="Fuel economy data", 
         subtitle="Distribución de las millas por galón según la clase", 
         caption="fuente: http://fueleconomy.gov", 
         y="Millas por galon (autopista)", 
         x="Clase",
         color=NULL) +  # title and caption
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/box_plots-2.png)

Violin plot
-----------

Las gráficas de violín permiten visualizar la distribución de una variable numérica para uno o varios grupos. Está muy cerca de una boxplot, pero permite una comprensión más profunda de la densidad. Los violines se adaptan especialmente cuando la cantidad de datos es enorme y resulta imposible mostrar observaciones individuales. Los gráficos de violín son una forma muy conveniente de mostrar los datos y probablemente merezcan más atención en comparación con los gráficos de caja que a veces pueden ocultar características de los datos.

``` r
# reorder is close to order, but is made to change the order of the factor levels.
iris$Species = with(iris, reorder(Species, Sepal.Width, mean))
 
# Now you can plot
ggplot(iris, aes(x=Species, y=Sepal.Width, fill=Species)) +
    geom_violin(alpha=0.6) +
    labs(title="Iris", 
         subtitle="Distribución del ancho del sépalo por especie", 
         caption="Fuente: Edgar Anderson's Iris Data", 
         y="Ancho del sépalo", 
         x="Especie",
         color=NULL) +
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/violin_plot-1.png)

``` r
# Second type
ggplot(mtcars, aes(factor(cyl), mpg)) +
    geom_violin(aes(fill = factor(cyl))) +
    labs(title="Motor Trend Car Road Tests", 
         subtitle="Distribución de las millas por galón según # de cilindros", 
         caption="fuente: Motor Trend US magazine", 
         y="millas por galon", 
         x="# de cilindros",
         color=NULL) +
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/violin_plot-2.png)

Scatter plot
------------

Una gráfica de dispersión (scatterplot) muestra el valor de 2 conjuntos de datos en 2 dimensiones. Cada punto representa una observación. La posición en los ejes X (horizontal) e Y (vertical) representa los valores del valor de 2 variables. Es realmente útil estudiar la relación entre ambas variables. Es común proporcionar aún más información usando colores o formas (para mostrar grupos, o una tercera variable). También es posible mapear otra variable al tamaño de cada punto, lo que hace un gráfico de burbujas. Si tiene muchos puntos y lucha con el sobretrazado, considere la posibilidad de usar un gráfico de densidad en 2D.

``` r
options(scipen=999)  # turn-off scientific notation like 1e+48
data("midwest", package = "ggplot2")

# Scatterplot
ggplot(midwest, aes(x=area, y=poptotal)) + 
    geom_point(aes(col=state, size=popdensity)) + 
    geom_smooth(method="loess", se=F) + 
    xlim(c(0, 0.1)) + 
    ylim(c(0, 500000)) + 
    labs(subtitle="Area Vs Población", 
         y="Poblacíon", 
         x="Area", 
         title="Ejemplo de un Scatterplot", 
         caption = "Fuente: midwest") +
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/scatter_plot-1.png)

``` r
ggplot(mpg, aes(cty, hwy)) +
    geom_point() + 
    geom_smooth(method="lm", se=F) +
    labs(subtitle="Consumo en la ciudad versus autopista", 
         y="Autopista", 
         x="Ciudad", 
         title="Millas por galón", 
         caption="Fuente: midwest") +
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/scatter_plot-2.png)

``` r
data("diamonds")
levels(diamonds$cut) <- c("Pobre", "Bueno", "Muy bueno", "Premium", "Ideal")
ggplot(data=diamonds, aes(x = carat, y = price, color = cut)) + 
    geom_point(alpha = 1, size = .01, aes(color = cut)) + 
    labs(title = "Precio vs. Quilates", 
       subtitle = "¿Cual es la corelación entre el precio y los quilates?",
       x = "Quilates", 
       y = "Precio",
       caption = "pmoracho.github.io" ) + 
    scale_color_manual(values=c('#25AAE2','#F2B53A', '#8BC540', '#DC5D42', '#666666', '9FAFBE')) +
    guides(colour = guide_legend(override.aes = list(size=10))) +
    theme(legend.key = element_rect(colour = "transparent", fill = "white")) +
    scale_y_continuous(labels=dollar_format(prefix="$")) +
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/scatter_plot-3.png)

Y también veamos como se ve un "facetado""

``` r
ggplot(mpg, aes(displ, cty)) + 
    geom_point() +
    # Use vars() to supply variables from the dataset:
    facet_grid(vars(drv), vars(cyl)) +
    theme_elegante() +
    theme(panel.border= element_rect(fill = NA, colour = "grey70", size = rel(1)))
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/scatter_plot_facet-1.png)

Connected scatter plot
----------------------

Una gráfica de dispersión conectada está realmente cerca de una gráfica de línea, excepto que cada una de las rupturas en las líneas se muestran usando un punto. También está bastante cerca de la gráfica de dispersión, pero tiene una especificidad: su eje X debe ser ordenado para hacer este tipo de representación. Por lo tanto, las gráficas de dispersión conectadas se utilizan a menudo para series temporales en las que el eje X representa el tiempo. Si desea rellenar el área debajo de la línea, obtendrá un gráfico de área.

``` r
library(tidyverse)
 
# Build a Time serie data set for last year
day=as.Date("2017-06-14") - 0:364
value=runif(365) + seq(-140, 224)^2 / 10000
data=data.frame(day, value)
 
# Calculate mean value per month
data %>% mutate(month = as.Date(cut(day, breaks = "month"))) %>%
    group_by(month) %>% 
    summarise(average = mean(value)) -> don
 
# And make the plot
ggplot(don, aes(x=month, y=average)) +
    geom_line() + 
    geom_point() +
    scale_x_date(date_labels = "%b-%Y", date_breaks="1 month") +
    theme_elegante() +
    theme(axis.text.x=element_text(angle=60, hjust=1))
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/con_scatter-1.png)

``` r
# Calculate agregated data per week
don=data %>% mutate(week = as.Date(cut(day, breaks = "week"))) %>%
    group_by(week) %>% 
    summarise(average = mean(value)) 
 
# And make the plot
ggplot(don, aes(x=week, y=average)) +
    geom_line() + 
    geom_point() +
    geom_area(fill=alpha('slateblue',0.2)) +
    scale_x_date(date_labels = "%W-%b", date_breaks="1 week") +
    theme_elegante() +
    theme(axis.text.x=element_text(angle=60, hjust=1))
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/con_scatter-2.png)

Density plot
------------

Un gráfico de densidad muestra la distribución de una variable numérica. Sólo toma como entrada un conjunto de valores numéricos. Está muy cerca de un histograma. Tenga en cuenta que es muy recomendable jugar con el argumento de ancho de banda para no perder un patrón específico en los datos. Tenga en cuenta que puede comparar la distribución de varias variables graficándolas en el mismo eje, usando facetas o a través de un gráfico de gozo.

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-1-1.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-1-2.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-1-3.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-1-4.png)

Stacked area chart
------------------

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-2-1.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-2-2.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-2-3.png)

Streamgraphs
------------

Un gráfico Stream está muy cerca de un gráfico de área apilada. Muestra la evolución de un valor numérico (eje Y) en función de otro valor numérico (eje X). Esta evolución está representada por varios grupos, todos con un color distinto. A diferencia de un área apilada, no hay ninguna esquina: los bordes son redondeados, lo que da esta agradable impresión de flujo. Los gráficos de streaming son muy útiles cuando se muestran en modo interactivo: resaltar un grupo le da directamente una idea de su evolución. R permite crear fácilmente gráficos streaming gracias a la biblioteca de gráficos streaming.

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-3-1.png)

2D density plot
---------------

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-4-1.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-4-2.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-4-3.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-4-4.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-4-5.png)

Bubble plot
-----------

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-5-1.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-5-2.png)

Ridgeline plot
--------------

Un gráfico Ridgeline o Joyplot muestra la distribución de un valor numérico para varios grupos. Pueden ser muy útiles para visualizar los cambios en las distribuciones a lo largo del tiempo o del espacio. La distribución puede representarse mediante histogramas o gráficos de densidad, todos alineados a la misma escala horizontal y presentados con un ligero solapamiento. Las gráficas son especialmente interesantes cuando el número de grupos a representar es alto y, por lo tanto, una separación clásica de ventanas requeriría de mucho más espacio. Sin embargo, hay que tener cuidado, ya que está gráfica suele ocultar una parte de los datos, donde se produce el solapamiento. En R, los gráficos de Ridgeline se pueden hacer fácilmente gracias a la librería [ggridges](https://github.com/clauswilke/ggridges) de [Claus Wilke](http://wilkelab.org/), que es una extensión de ggplot2.

source: <https://www.r-graph-gallery.com/ridgeline-plot/>

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-6-1.png)

Correlograma
------------

Un correlograma o matriz de correlación permite analizar la relación entre cada par de variables numéricas de una matriz. La correlación entre cada par de variables se visualiza a través de una gráfica de dispersión, o un símbolo que representa la correlación (burbuja, línea, número...). La diagonal representa la distribución de cada variable, utilizando un histograma o un gráfico de densidad. Esta técnica es ampliamente utilizada para el análisis exploratorio ya que evita hacer cientos de gráficos para observar una matriz.

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-7-1.png)

Line plot
---------

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-8-1.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-8-2.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-8-3.png)

Dendograma
----------

Un dendrograma es un tipo de representación gráfica o diagrama de datos en forma de árbol que organiza los datos en subcategorías que se van dividiendo en otros hasta llegar al nivel de detalle deseado (asemejándose a las ramas de un árbol que se van dividiendo en otras sucesivamente). Este tipo de representación permite apreciar claramente las relaciones de agrupación entre los datos e incluso entre grupos de ellos aunque no las relaciones de similitud o cercanía entre categorías. Observando las sucesivas subdivisiones podemos hacernos una idea sobre los criterios de agrupación de los mismos, la distancia entre los datos según las relaciones establecidas, etc. También podríamos referirnos al dendrograma como la ilustración de las agrupaciones derivadas de la aplicación de un algoritmo de clustering jerárquico.

``` r
library(ggplot2)
library(ggdendro)
df       <- USArrests                         # really bad idea to muck up internal datasets
hc       <- hclust(dist(df), "ave")           # heirarchal clustering
dendr    <- dendro_data(hc, type="rectangle") # convert for ggplot
clust    <- cutree(hc,k=2)                    # find 2 clusters
clust.df <- data.frame(label=names(clust), cluster=factor(clust))
# dendr[["labels"]] has the labels, merge with clust.df based on label column
dendr[["labels"]] <- merge(dendr[["labels"]],clust.df, by="label")
# plot the dendrogram; note use of color=cluster in geom_text(...)
ggplot() + 
  geom_segment(data=segment(dendr), aes(x=x, y=y, xend=xend, yend=yend)) + 
  geom_text(data=label(dendr), aes(x, y, label=label, hjust=0, color=cluster), 
           size=3) +
  coord_flip() + scale_y_reverse(expand=c(0.2, 0)) + 
  theme(axis.line.y=element_blank(),
        axis.ticks.y=element_blank(),
        axis.text.y=element_blank(),
        axis.title.y=element_blank(),
        panel.background=element_rect(fill="white"),
        panel.grid=element_blank()) +
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/dendogram-1.png)

Line plot
---------

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-9-1.png)

Circular bar plot
-----------------

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-10-1.png)

Mosaic Plot
-----------

Para instalarlo: `devtools::install_github("haleyjeppson/ggmosaic")`

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-11-1.png) ![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-12-1.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-12-2.png)

ggTimeSeries
------------

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-13-1.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/unnamed-chunk-13-2.png)

fuentes:

-   <https://www.r-graph-gallery.com>

