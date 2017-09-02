---
layout: post
published: true
date: '2017-09-01'
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
title: Overlay de imagenes sobre areas en ggplot
description: Un posible forma de sobreimponer una imágen sobre áreas de un gráfico ggplot en R
tags:
  - desarrollo
  - R
  - ggplot
image:
  feature: 2017/img-feature-002.jpeg
---
## _¿Cómo aplicar una imágen a ciertas áreas de un `plot`?_

A raíz de [esta pregunta][pregunta] estuve varios días pensando que solución
encontrarle. Hasta dónde pude investigar [ggplot][ggplot] no ofrece ningún
mecanismo para superponer imágenes sobre areas de un gráfico cualquiera. Existe
sí, la posibilidad de incorporar una imagen como fondo general de un gráfico,
podríamos hacer coincidir una imagen que tenga solo  "dibujo" sobre el área del
`plot` y el resto sea un fondo en blanco, pero esto es muy complejo ya que este
fondo solo aplica al sector de los ejes y no a todo el "canvas". Tampoco
encontré algún paquete que ofrezca alguna solución al respecto, así que en
definitiva tuve que pensar alguna idea para resolverlo.

Tanto tiempo invertido en tratamiento de imagenes (como hobby) dieron sus
frutos: recordé las opciones de "enmascaramiento" para componer distintas
imágenes en capas. El concepto para resolver este tema es sencillo: tenemos una
imagen principal (`PLOT`), una "máscara" (`MASK`) que es otra imagen
de igual dimensión que tiene un area blanca y otra negra y por último la imagen
a sobreimponer (`AREA`). Haciendo una especie de "sandwich": `PLOT + MASK +
AREA` obtenemos la imágen final, la imagen `MASK`, dependiendo si un pixel es
banco o negro, determina si el pixel final será el de `PLOT` o el de `AREA`.

El problema es siempre: como construir nuestra `MASK`, hacerlo con una
herramienta tipo [gimp][gimp] es relativamente sencillo, pero trabajoso, ya que
debemos ir seleccionado con alguna de las opciones, el area del gráfico orginal
a sobreimponer. 

### ¿Se puede automatizar esto? ¿Cómo?

Claro que se puede, por empezar vamos a usar [ImageMagick][im] para todas las
tareas. En **R** hay un paquete que encapsula las principales funcionalidades
de [ImageMagick][im], se trata de [magick][magick], no tuve tiempo de
profundizar mucho en él, en principio encontré algunas limitaciones, así que
opté por trabajar directamente con la línea de comandos haciendo llamadas a
[system][system], veamos como:

Imaginemos que tenemos un gráfico de área como este:

![img1][img1]

Y tenemos otra imágen para el área que es esta:

![img2][img2]

## Generar la máscara

Lo más complejo es generar la máscara. La forma de hacerlo de forma automática
es terriblemene simple. Si tenemos un pixel de un determinado color y los
"sumamos" con otro pixel que tiene el valor exacto pero en "negativo",
obtendremos el negro máximo. Pensando en una paleta simple de 256 colores, si
un pixel vale 37 la versión "negativa" valdrá (256-37) es decir 219 y si
sumamos ambos el valor final sería 256. Por el contrario si un pixel lo sumamos
con otro cuyo valor no es exactamente el negativo, obtendremos un color
determinado distinto al negro. Entonces, si generamos dos versiones del `plot`
cuya única diferencia sea el color del área y sumamos ambas imágenes nos
quedaremos únicamente con el área, la cual con algún tratamiento más, lograremos
convertirla en nuestra máscara. Tal vez parezca un poco confuso, pero
gráficamente es más clara la idea.

Veamos, primero generamos un gráfico (la imágen anterior):

``` R
set.seed(1234)
df <- data.frame(
    sex=factor(rep(c("F", "M"), each=200)),
    weight=round(c(rnorm(200, mean=55, sd=5),
                   rnorm(200, mean=65, sd=5)))
)

plot1 <- ggplot(df, aes(x=weight)) +
    geom_area(stat = "bin", fill = "lightblue", color="darkblue", bins=30)+
    geom_vline(aes(xintercept=mean(weight)),
               color="blue", linetype="dashed", size=1)
```

y generamos una segunda versión, con un color de área distinto

``` R
plot2 <- ggplot(df, aes(x=weight)) +
    geom_area(stat = "bin", fill = "red", color="darkblue")+
    geom_vline(aes(xintercept=mean(weight)),
               color="blue", linetype="dashed", size=1)
```

![img5][img5]

A esta úmtima, la pasamos a "negativo:

![img3][img3]

Y si combinamos ambas imágenes, y con algo de magia de [ImageMagick][im],
convertimos el resultado a blanco y negro y nuevamente a "negativo" para que
nos quede algo así:

![img4][img4]

## Combinando las capas

Ahora, sí, tenemos las tres partes que mencionamos antes, el `plot` original,
la máscara y la imagen para el área. La combinación es una operación trivial
para [ImageMagick][im], y el resultado final es bastante satisfactorio:

![img6][img6]

Por supuesto este método tiene una cierta limitación, que es que terminamos
trabajamos sobre las versiones "bitmap" de los `plots` y no sobre la versión
real o vectorial, pero tampoco es algo tan terrible, en definitiva por lo
general terminamos publicando imágenes. Lo interesante es que esta técnica no
sirve para aplicar a cualquier área en la que podamos controlar el color, y así
de forma rápida generar gráficas como estás:

![img7][img7]

![img8][img8]

La rutina completa:
``` R

add_image_to_color <- function(imgmagick_path = NA, 
                               area_img_file, 
                               out_file, 
                               plot1, 
                               plot2, 
                               width=20, 
                               height=10, 
                               units=c("cm"),
                               dpi=300){
    
    plot_file <- tempfile(pattern="plot_", fileext=".png")
    plot_file_negate <- tempfile(pattern="plot_negate_", fileext=".png")
    mask_file <- tempfile(pattern="mask_", fileext=".png")
    area_file <- tempfile(pattern="area_", fileext=".png")
    
    convert <- ifelse(!is.na(imgmagick_path),file.path(imgmagick_path, "convert"), "convert")

    # Salvamos las dos versiones de los plots    
    ggsave(plot_file, plot = plot1, scale = 1, width = width, height = height, units = units, dpi = dpi)
    ggsave(plot_file_negate, plot = plot2, scale = 1, width = width, height = height, units = units, dpi = dpi)
    
    # Negar imagen
    cmd <- paste(convert , plot_file_negate, "-negate", plot_file_negate)
    system(cmd)

    # Flat: me quedo solo con el area
    cmd <- paste(convert, plot_file, plot_file_negate, "-compose LinearBurn -composite -colorspace gray -auto-level -negate", mask_file)
    system(cmd)
    
    # Largo y ancho del plot
    cmd <- paste0(convert, " ", plot_file, ' -format "%wx%h" info: ')
    pplot_dim <- unlist(lapply(strsplit(system(cmd, intern = TRUE), "x"), as.integer))
    
    # Redimensiono la imagen del area
    cmd <- paste(convert, area_img_file, "-resize", paste0(pplot_dim[1],"x",pplot_dim[2], "!"), area_file )
    system(cmd)
    
    # Combinar capas para armar el area con la imagen y el fondo transparente
    if (Sys.info()[['sysname']] == "Linux") {
        cmd <- paste(convert, "-composite", area_file, plot_file, "\\( -blur 1x65000 \\)", mask_file, out_file)
    } else {
        cmd <- paste(convert, "-composite", area_file, plot_file, "( -blur 1x65000 )", mask_file, out_file)
    }       
    system(cmd)
    
}

# Para ejecutar necesitamos dos versiones del mismo plot
imgmagick_path <- NA # Si lo tenemos en el path no es necesario
area_img_file <- "dolar.jpg"
out_file <- "final.jpg"

add_image_to_color(imgmagick_path, area_img_file, out_file, plot1, plot2, width = 15, height = 7)
```

[pregunta]:https://es.stackoverflow.com/questions/95753/composici%C3%B3n-de-imagen-y-gr%C3%A1fico-en-r
[ggplot]:http://ggplot2.org
[gimp]:https://www.gimp.org
[im]:https://www.imagemagick.org
[magick]:https://cran.r-project.org/web/packages/magick/vignettes/intro.html
[system]:https://stat.ethz.ch/R-manual/R-devel/library/base/html/system.html
[img1]:{{site.baseurl}}/images/2017/plot_11345999580f.png
[img2]:{{site.baseurl}}/images/2017/area_11344e74a54.png
[img3]:{{site.baseurl}}/images/2017/plot_negate_11346db71bfd.png
[img4]:{{site.baseurl}}/images/2017/mask_11345c282402.png
[img5]:{{site.baseurl}}/images/2017/plot_2_113416ed5574.png
[img6]:{{site.baseurl}}/images/2017/final_11344e74a54.png
[img7]:{{site.baseurl}}/images/2017/final_a1dfg1kl.png
[img8]:{{site.baseurl}}/images/2017/final_x4cfg1kl.png
