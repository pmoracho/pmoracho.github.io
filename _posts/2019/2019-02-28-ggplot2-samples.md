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
2019-03-06

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
    theme(panel.border= element_rect(fill = NA, colour = "grey70", size = rel(1))) +
    labs(title = "Cilindrda vs consumo en ciudad", 
       subtitle = "¿Cual es la relación entre la cilindarada y el consumo por cilindros/transmisión?",
       x = "Cilindrada", 
       y = "Consumo",
       caption = "pmoracho.github.io" ) + 
    scale_color_manual(values=c('#25AAE2','#F2B53A', '#8BC540', '#DC5D42', '#666666', '9FAFBE')) 
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/scatter_plot_facet-1.png)

Density plot
------------

Un gráfico de densidad muestra la distribución de una variable numérica. Sólo toma como entrada un conjunto de valores numéricos. Está muy cerca de un histograma. Tenga en cuenta que es muy recomendable jugar con el argumento de ancho de banda para no perder un patrón específico en los datos. Tenga en cuenta que puede comparar la distribución de varias variables graficándolas en el mismo eje, usando facetas o a través de un gráfico de gozo.

``` r
data("diamonds")
levels(diamonds$cut) <- c("Pobre", "Bueno", "Muy bueno", "Premium", "Ideal")

# plot 1: Density of price for each type of cut of the diamond:
ggplot(data=diamonds,aes(x=price, group=cut, fill=cut)) + 
    geom_density(adjust=1.5) +
    labs(title = "Precio vs. Densidad", 
       subtitle = "Distribución del precio en relación a la densidad",
       x = "Precio", 
       y = "Densidad",
       caption = "Fuente: pmoracho.github.io" ) +
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/density-1.png)

``` r
# plot 2: Density plot with transparency (using the alpha argument):
ggplot(data=diamonds,aes(x=price, group=cut, fill=cut)) + 
    geom_density(adjust=1.5 , alpha=0.2) +
    labs(title = "Precio vs. Densidad", 
       subtitle = "Distribución del precio en relación a la densidad",
       x = "Precio", 
       y = "Densidad",
       caption = "Fuente: pmoracho.github.io" ) +
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/density-2.png)

``` r
# plot 3: Stacked density plot:
ggplot(data=diamonds,aes(x=price, group=cut, fill=cut)) + 
    geom_density(adjust=1.5, position="fill") +
    labs(title = "Precio vs. Densidad", 
       subtitle = "Distribución del precio en relación a la densidad",
       x = "Precio", 
       y = "Densidad",
       caption = "Fuente: pmoracho.github.io" ) +
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/density-3.png)

``` r
# plot 4
ggplot(diamonds, aes(x=depth, y=..density..)) + 
    geom_density(aes(fill=cut), position="stack") +
    xlim(50,75) + 
    labs(title = "Precio vs. Densidad", 
       subtitle = "Distribución del precio en relación a la densidad",
       x = "Precio", 
       y = "Densidad",
       caption = "Fuente: pmoracho.github.io" ) +
    theme(legend.position="none") +
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/density-4.png)

Stacked area chart
------------------

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/stacked_areas-1.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/stacked_areas-2.png)![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/stacked_areas-3.png)

Streamgraphs
------------

Un gráfico Stream está muy cerca de un gráfico de área apilada. Muestra la evolución de un valor numérico (eje Y) en función de otro valor numérico (eje X). Esta evolución está representada por varios grupos, todos con un color distinto. A diferencia de un área apilada, no hay ninguna esquina: los bordes son redondeados, lo que da esta agradable impresión de flujo. Los gráficos de streaming son muy útiles cuando se muestran en modo interactivo: resaltar un grupo le da directamente una idea de su evolución. R permite crear fácilmente gráficos streaming gracias a la biblioteca de gráficos streaming.

``` r
library(ggTimeSeries)
library(ggplot2)

set.seed(10)
dfData = data.frame(
   Time = 1:1000,
   Signal = abs(
      c(
         cumsum(rnorm(1000, 0, 3)), 
         cumsum(rnorm(1000, 0, 4)), 
         cumsum(rnorm(1000, 0, 1)),
         cumsum(rnorm(1000, 0, 2))
      )
   ),
   VariableLabel = c(rep('Class A', 1000),
                     rep('Class B', 1000),
                     rep('Class C', 1000),
                     rep('Class D', 1000))
)

# base plot
ggplot(dfData,
       aes(x = Time,
           y = Signal,
           group = VariableLabel,
           fill = VariableLabel)) +
    stat_steamgraph() +
    labs(title="Titulo del gráfico", 
         subtitle="Subtitulo del gráfico", 
         caption="Fuente: pmoracho.github.io", 
         y="Señal", 
         x="Tiempo") +
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/streamgraphs-1.png)

2D density plot
---------------

``` r
# Data
a <- data.frame( x=rnorm(20000, 10, 1.9), y=rnorm(20000, 10, 1.2) )
b <- data.frame( x=rnorm(20000, 14.5, 1.9), y=rnorm(20000, 14.5, 1.9) )
c <- data.frame( x=rnorm(20000, 9.5, 1.9), y=rnorm(20000, 15.5, 1.9) )
data <- rbind(a,b,c)

# Basic scatterplot
ggplot(data, aes(x=x, y=y) ) + 
    geom_point() + 
    labs(title="Titulo del gráfico", 
         subtitle="Subtitulo del gráfico", 
         caption="Fuente: pmoracho.github.io", 
         y="x", 
         x="y") +
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/2D_density-1.png)

``` r
ggplot(data, aes(x=x, y=y) ) +
    geom_hex() +
    labs(title="Titulo del gráfico", 
         subtitle="Subtitulo del gráfico", 
         caption="Fuente: pmoracho.github.io", 
         y="x", 
         x="y") +
  theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/2D_density-2.png)

``` r
# Show the contour only
ggplot(data, aes(x=x, y=y) ) +
    geom_density_2d() +
    labs(title="Titulo del gráfico", 
         subtitle="Subtitulo del gráfico", 
         caption="Fuente: pmoracho.github.io", 
         y="x", 
         x="y") +
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/2D_density-3.png)

``` r
# Show the area only
ggplot(data, aes(x=x, y=y) ) +
    stat_density_2d(aes(fill = ..level..), geom = "polygon") +
    labs(title="Titulo del gráfico", 
         subtitle="Subtitulo del gráfico", 
         caption="Fuente: pmoracho.github.io", 
         y="x", 
         x="y") +
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/2D_density-4.png)

Bubble plot
-----------

``` r
# Let's use the diamonds data set (available in base R)
data = diamonds %>% sample_n(200)
 
ggplot(data, aes(x=carat, y=price, size=depth, color=carat)) + 
    geom_point(alpha=0.4) +
    scale_size_continuous( trans="exp", range=c(1, 25)) +
    scale_colour_continuous(guide = FALSE) +
        labs(title="Relación precio / quilates", 
         subtitle="Tamaño de las burbujas por profundidad", 
         caption="Fuente: pmoracho.github.io", 
         y="precio", 
         x="Quilates") +
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/bubble_plots-1.png)

Ridgeline plot
--------------

Un gráfico Ridgeline o Joyplot muestra la distribución de un valor numérico para varios grupos. Pueden ser muy útiles para visualizar los cambios en las distribuciones a lo largo del tiempo o del espacio. La distribución puede representarse mediante histogramas o gráficos de densidad, todos alineados a la misma escala horizontal y presentados con un ligero solapamiento. Las gráficas son especialmente interesantes cuando el número de grupos a representar es alto y, por lo tanto, una separación clásica de ventanas requeriría de mucho más espacio. Sin embargo, hay que tener cuidado, ya que está gráfica suele ocultar una parte de los datos, donde se produce el solapamiento. En R, los gráficos de Ridgeline se pueden hacer fácilmente gracias a la librería [ggridges](https://github.com/clauswilke/ggridges) de [Claus Wilke](http://wilkelab.org/), que es una extensión de ggplot2.

source: <https://www.r-graph-gallery.com/ridgeline-plot/>

``` r
library(ggridges)
library(ggplot2)
 
data("diamonds")
levels(diamonds$cut) <- c("Pobre", "Bueno", "Muy bueno", "Premium", "Ideal")
 
# basic example
ggplot(diamonds, aes(x = price, y = cut, fill = cut)) +
  geom_density_ridges() +
    labs(title = "Precio vs. Corte", 
       subtitle = "¿Cual es la distribución del precio con relación al corte?",
       x = "Precio", 
       y = "Corte",
       caption = "source: pmoracho.github.io" ) + 
    scale_x_continuous(labels=dollar_format(prefix="$")) +
    scale_color_manual(values=c('#25AAE2','#F2B53A', '#8BC540', '#DC5D42', '#666666',
                                '9FAFBE')) +
  theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/ridgeline_plot-1.png)

Correlograma
------------

Un correlograma o matriz de correlación permite analizar la relación entre cada par de variables numéricas de una matriz. La correlación entre cada par de variables se visualiza a través de una gráfica de dispersión, o un símbolo que representa la correlación (burbuja, línea, número...). La diagonal representa la distribución de cada variable, utilizando un histograma o un gráfico de densidad. Esta técnica es ampliamente utilizada para el análisis exploratorio ya que evita hacer cientos de gráficos para observar una matriz.

``` r
library(ggplot2)
library(ggcorrplot)

# Correlation matrix
data(mtcars)
corr <- round(cor(mtcars), 1)

ggcorrplot(corr, hc.order = TRUE, type = "lower",
   outline.col = "white",
   ggtheme = theme_elegante,
   colors = c("#6D9EC1", "white", "#E46726")) +
    labs(title="Motor Trend Car Road Tests", 
         subtitle="Correlación de las variables", 
         caption="fuente: Motor Trend US magazine", 
         color=NULL) +
    theme(legend.position="right")
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/correlogram-1.png)

Line plot
---------

``` r
library(ggplot2)
library(lubridate)

ggplot(data = diamonds, aes(x = carat, y = price, color = cut)) + 
    labs(title = "Precio vs. Quilatest", 
       subtitle = "What is the correlation between the price of a diamond its carat? ",
       x = "Carat", 
       y = "Price",
       caption = "www.datatoinsight.io" ) + 
    scale_color_manual(values=c('#25AAE2','#F2B53A', '#8BC540', '#DC5D42', '#666666', '9FAFBE')) +
    guides(colour = guide_legend(override.aes = list(size=10))) +
    geom_smooth(alpha = 0.2, size = 1.5, span = 4, se=FALSE) + 
    theme(legend.key = element_rect(fill = "white")) +
    scale_y_continuous(labels=dollar_format(prefix="$")) +
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/line_plot-1.png)

``` r
df <- economics_long[economics_long$variable %in% c("psavert", "uempmed"), ]
df <- df[lubridate::year(df$date) %in% c(1967:1981), ]

# labels and breaks for X axis text
brks <- df$date[seq(1, length(df$date), 12)]
lbls <- lubridate::year(brks)

# plot
ggplot(df, aes(x=date)) + 
    geom_line(aes(y=value, col=variable)) + 
    theme_elegante() +
    labs(title="Time Series of Returns Percentage", 
       subtitle="Drawn from Long Data format", 
       caption="Source: Economics", 
       y="Returns %", 
       color=NULL) +  # title and caption
    scale_x_date(labels = lbls, breaks = brks) +  # change to monthly ticks and labels
    scale_color_manual(labels = c("psavert", "uempmed"), 
                     values = c("psavert"="#00ba38", "uempmed"="#f8766d")) +  # line color
    theme(axis.text.x = element_text(angle = 90, vjust=0.5, size = 8),  # rotate x axis text 
          panel.grid.minor = element_blank())  # turn off minor grid
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/line_plot-2.png)

Connected scatter plot
----------------------

Una gráfica de dispersión conectada está realmente cerca de una gráfica de línea, excepto que cada una de las rupturas en las líneas se muestran usando un punto. También está bastante cerca de la gráfica de dispersión, pero tiene una especificidad: su eje X debe ser ordenado para hacer este tipo de representación. Por lo tanto, las gráficas de dispersión conectadas se utilizan a menudo para series temporales en las que el eje X representa el tiempo. Si desea rellenar el área debajo de la línea, obtendrá un gráfico de área.

``` r
tu <- expand.grid(Land       = gl(2, 1, labels = c("DE", "BB")),
                  Altersgr   = gl(5, 1, labels = letters[1:5]),
                  Geschlecht = gl(2, 1, labels = c('m', 'w')),
                  Jahr       = 2000:2009)

set.seed(42)
tu$Wert <- unclass(tu$Altersgr) * 200 + rnorm(200, 0, 10)

ggplot(tu, aes(x = Jahr, y = Wert, color = Altersgr, group = Altersgr)) + 
    geom_point() + 
    geom_line() + 
    facet_grid(Geschlecht ~ Land) +
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/con_scatter-1.png)

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
     labs(title = "USArrests", 
       subtitle = "Dendograma",
       x = "", 
       y = "",
       caption = "www.datatoinsight.io" ) + 
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/dendogram-1.png)

Circular bar plot
-----------------

``` r
# Create dataset
data=data.frame(
  individual=paste( "Mister ", seq(1,60), sep=""),
  value=sample( seq(10,100), 60, replace=T)
)
 
# Set a number of 'empty bar'
empty_bar=10
 
# Add lines to the initial dataset
to_add = matrix(NA, empty_bar, ncol(data))
colnames(to_add) = colnames(data)
data=rbind(data, to_add)
data$id=seq(1, nrow(data))
 
# Get the name and the y position of each label
label_data=data
number_of_bar=nrow(label_data)
angle= 90 - 360 * (label_data$id-0.5) /number_of_bar     # I substract 0.5 because the letter must have the angle of the center of the bars. Not extreme right(1) or extreme left (0)
label_data$hjust<-ifelse( angle < -90, 1, 0)
label_data$angle<-ifelse(angle < -90, angle+180, angle)
 
# Make the plot
ggplot(data, aes(x=as.factor(id), y=value)) +       # Note that id is a factor. If x is numeric, there is some space between the first bar
  geom_bar(stat="identity", fill=alpha("green", 0.3)) +
  ylim(-100,120) +
  theme_elegante() +
  theme(
    axis.text = element_blank(),
    axis.title = element_blank(),
    panel.grid = element_blank(),
    plot.margin = unit(rep(-1,4), "cm") 
  ) +
  coord_polar(start = 0) + 
  geom_text(data=label_data, aes(x=id, y=value+10, label=individual, hjust=hjust), color="black", fontface="bold",alpha=0.6, size=2.5, angle= label_data$angle, inherit.aes = FALSE ) 
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/circular-1.png)

Mosaic Plot
-----------

Para instalarlo: `devtools::install_github("haleyjeppson/ggmosaic")`

``` r
library(ggmosaic)
#> Loading required package: ggplot2

ggplot(data = fly) +
    geom_mosaic(aes(x = product(RudeToRecline), fill=DoYouRecline)) +
    theme_elegante() +
    labs(title="Time Series of Returns Percentage", 
       subtitle="Drawn from Long Data format", 
       caption="Source: Economics", 
       y="", 
       x="")
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/mosaic-1.png)

Mapas de calor
--------------

``` r
library(ggplot2)
library(reshape2)
library(plyr)
nba <- read.csv("http://datasets.flowingdata.com/ppg2008.csv")
nba$Name <- with(nba, reorder(Name, PTS))
nba.m <- melt(nba)

nba.m <- ddply(nba.m, .(variable), transform, rescale = rescale(value))
(p <- ggplot(nba.m, aes(variable, Name)) + 
        geom_tile(aes(fill = rescale), colour = "white") + 
        scale_fill_gradient(low = "white", high = "steelblue")+
        theme_elegante())
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/Heatmap-1.png)

``` r
# ======================================================== 
# Data: example with mtcars dataset 
# ======================================================== 
# load RColorBrewer 
library(ggplot2) 
library(reshape) 
 
# load data 'mtcars' 
data(mtcars) 
 
# scale data by "columns" 
carsdf = data.frame(scale(mtcars)) 
carsdf$model = rownames(mtcars) 
cars_melt = melt(carsdf, id.vars = "model") 

# ======================================================== 
# Plot 
# ======================================================== 
ggplot(data = cars_melt, aes(x = variable, y = model)) + 
  geom_tile(aes(fill = value), color = "white", size = 1) + 
  scale_fill_gradient(low = "gray95", high = "tomato") + 
  xlab("characteristics") + 
  theme_grey(base_size = 10) + 
  ggtitle("Heatmap (ggplot)") + 
  theme(axis.ticks = element_blank(), 
        panel.background = element_blank(), 
        plot.title = element_text(size = 12, colour = "gray50")) +
      theme_elegante(base_size=6)
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/Heatmap-2.png)

ggTimeSeries
------------

``` r
library(ggTimeSeries)
library(data.table)
# creating some data
set.seed(1)
dtData = data.table(
      DateCol = seq(
         as.Date("1/01/2014", "%d/%m/%Y"),
         as.Date("31/12/2015", "%d/%m/%Y"),
         "days"
      ),
      ValueCol = runif(730)
   )
dtData[, ValueCol := ValueCol + (strftime(DateCol,"%u") %in% c(6,7) * runif(1) * 0.75), .I]
dtData[, ValueCol := ValueCol + (abs(as.numeric(strftime(DateCol,"%m")) - 6.5)) * runif(1) * 0.75, .I]

# base plot
p1 = ggplot_calendar_heatmap(
   dtData,
   'DateCol',
   'ValueCol'
)

# adding some formatting
p1 +
   xlab(NULL) +
   ylab(NULL) +
   scale_fill_continuous(low = 'green', high = 'red') +
   facet_wrap(~Year, ncol = 1) +
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/ggtimeseries-1.png)

``` r
# creating some categorical data
dtData[, CategCol := letters[1 + round(ValueCol * 7)]]

# base plot
p2 = ggplot_calendar_heatmap(
   dtData,
   'DateCol',
   'CategCol'
)

# adding some formatting
p2 +
   xlab(NULL) +
   ylab(NULL) +
   facet_wrap(~Year, ncol = 1) +
    theme_elegante()
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/ggtimeseries-2.png)

Treemaps
--------

``` r
library(tidyverse)
library(ggfittext)
library(treemapify)

cars <- mtcars
cars$carname <- rownames(cars)
cars <- mutate(cars, cyl = factor(cyl))

ggplot(cars, aes(area = disp, fill = cyl, label = carname)) +
    geom_treemap() +
    scale_fill_brewer(palette="Greens", breaks=mtcars$cyl) +
    theme_elegante() +
    geom_treemap_text(
        place = "centre",
        family = 'Raleway',
        grow = TRUE
    ) +
    labs(title="Cilindradas según modelos", 
         subtitle="Abierto por cantidad de cilindros", 
         caption="Fuente: pmoracho.github.io", 
         y="", 
         x="Cilindros") 
```

![]({{site.baseurl}}/images/2019/ggplot_samples_files/figure-markdown_github/treemap-1.png)

fuentes:

-   <https://www.r-graph-gallery.com>
