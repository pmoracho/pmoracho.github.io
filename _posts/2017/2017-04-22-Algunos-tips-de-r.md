---
layout: post
published: true
date: '2017-04-22'
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
title: Algunos de mis "tips" en R
description: Algunos de mis "tips" en R, cosas que fuí aprendiendo
tags:
  - desarrollo
  - R
---
## Merge de dataframes con valores no exactos

El [merge] en R permite justamente mezclar dos [dataframes] agrupando
columnas en la que haya valores coincidentes, sin embargo no permite manejar
un valor de desvío de modo tal que de tolerar ciertas diferencias. Una forma de
resolverlo es "alinear" los valores de uno de los [dataframes] con los del otro y
cuando no supere la "tolerancia" lo que hacemos es igualarlos para que entonces
el merge funcione como esatamos buscando. Algo así:

``` R
usuarios <- c(1,2,3,4)
distạncias <- c(20.005,21.000,23.000,19.009)
df1 <- data.frame(usuarios,distancias)

usuarios <- c(5,6,7,8)
distancias <- c(20.006,21.000,23.000,19.008)
df2 <- data.frame(usuarios,distancias)

df1$distancias <- sapply(df1$distancias, function(x)
{
    # El elemento de df1 coincide con tolerancia con el de df2 ?
    ifelse(min(abs(df2$distancias - x), na.rm=TRUE) < 0.001 * x,
           # Si, entonces reemplazo el elemento de df1 por el de df2 para que el merge funcione
           df2[which.min(abs(df2$distancias - x)),"distancias"], 0)
})
df3 <- merge(df1, df2, by = "distancias")
```
El resultado sería algo así:

```
 distancias usuarios.x usuarios.y
1     19.008          4          8
2     20.006          1          5
3     21.000          2          6
4     23.000          3          7
```

También es interesante la solución de este problema presentada en
[stackoverflow] mediante el uso del paquete [fuzzyjoin]

``` R
library(fuzzyjoin)

difference_join(df1, df2, by = "distancias", max_dist = 0.001)
#>   usuarios.x distancias.x usuarios.y distancias.y
#> 1          2           21          6           21
#> 2          3           23          7           23

difference_join(df1, df2, by = "distancias", max_dist = 0.1)
#>   usuarios.x distancias.x usuarios.y distancias.y
#> 1          1       20.005          5       20.006
#> 2          2       21.000          6       21.000
#> 3          3       23.000          7       23.000
#> 4          4       19.009          8       19.008


difference_join(df1, df2, by = "distancias", max_dist = Inf)
#>    usuarios.x distancias.x usuarios.y distancias.y
#> 1           1       20.005          5       20.006
#> 2           1       20.005          6       21.000
#> 3           1       20.005          7       23.000
#> 4           1       20.005          8       19.008
#> 5           2       21.000          5       20.006
#> 6           2       21.000          6       21.000
#> 7           2       21.000          7       23.000
#> 8           2       21.000          8       19.008
#> 9           3       23.000          5       20.006
#> 10          3       23.000          6       21.000
#> 11          3       23.000          7       23.000
#> 12          3       23.000          8       19.008
#> 13          4       19.009          5       20.006
#> 14          4       19.009          6       21.000
#> 15          4       19.009          7       23.000
#> 16          4       19.009          8       19.008

```

# Obtener el nombre de cualquier objeto

``` R
prueba <-0
substitute(prueba) ## Retorna "prueba"
```

# Un split inteligente

Por ejemplo, tenemos esta cadena `'Marca: Avon Marca: Imusa Marca: Falabella'`
y queremos quedarnos solo con los nombres de las marcas, en ese caso podemos
hacer lo siguiente:

``` R
s <- 'Marca: Avon Marca: Imusa Marca: Falabella'
result<-sapply(strsplit(s, 'Marca: '), function(e) {trimws(e[e!=""])})  
result
[1,] "Avon"     
[2,] "Imusa"    
[3,] "Falabella"
```

# Separar un columna en varias según un caracter en particular

``` R
list <- "id datos
        1 Juan:29384:Madrid
        2 Alba:394839:Alicante
        3 Elena:102948:Segovia"

df = read.table(textConnection(list), header=TRUE) 
df2 <- within(df, datos<-data.frame(do.call('rbind', strsplit(as.character(datos), ':', fixed=TRUE))))
df2

#   id datos.X1 datos.X2 datos.X3
# 1  1     Juan    29384   Madrid
# 2  2     Alba   394839 Alicante
# 3  3    Elena   102948  Segovia
```

# Configurar un Timeout para funciones

Si se requiere dar un tiempo máximo a la ejecución de una función el truco pasa por hacer uso de [`withTimeout`](https://www.rdocumentation.org/packages/R.utils/versions/2.5.0/topics/withTimeout)

```
require(R.utils)

mi.funcion <- function(x)
{
	Sys.sleep(1*x) 
	return(x^x)
}

mi.funcion.with.tiemout <- function(x, timeout=1)
{
	# En caso de timeout, no genero error y devuelvo -1
	ret <- withTimeout(mi.funcion(x), timeout = timeout, onTimeout = "silent")
	if (is.null(ret)) {ret = -1}
	return(ret)
}

sapply(c(1,2,3,4), mi.funcion.with.tiemout, timeout=4)
```

El retorno:

```
    [1]  1  4 27 -1
```

Como se vé, al llegar al valor 4 se supera el timeout y se aplica en este caso
un valor genérico -1 que devuelve la función propia `mi.funcion.with.tiemout`

# Actualizar R en Linux Mint

1. Agregar origen: `sudo gedit /etc/apt/sources.list`, hay que agregar el siguiente repositorio: `deb http://cran.cnr.berkeley.edu/bin/linux/ubuntu/ version/` reemplazar `version` por lo que corresponda, por ejemplo: `deb http://cran.cnr.berkeley.edu/bin/linux/ubuntu/ xenial/`

2. Agregar claves:
```
gpg --keyserver keyserver.ubuntu.com --recv-key E084DAB9 
gpg -a --export E084DAB9 | sudo apt-key add -
```

3. Update/Upgrade/Install
```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install r-base
```

4. Instalar RStudio. Simplemente descaragar el instalador correspondiente desde
   [este](http://www.rstudio.com/ide/download/) enlace, luego simplemete: `sudo gdebi <paquete.deb>`

# Comentarios multilínea

Lamentablemente R no maneja comentarios multilinea, hay un truco que no se si
es muy bueno que digamos y que pasa por escribir una cadena común multilinea,
que R evaluará como tal

``` R
" Esta es una prueba
  de un string multilinea que hace las veces
  de un comentario
"
```


[merge]:https://stat.ethz.ch/R-manual/R-devel/library/base/html/merge.html
[dataframes]:https://stat.ethz.ch/R-manual/R-devel/library/base/html/data.frame.html
[fuzzyjoin]:https://cran.r-project.org/web/packages/fuzzyjoin/fuzzyjoin.pdf
[stackoverflow]:https://es.stackoverflow.com/questions/64480/combinar-dos-data-frames-con-merge-permitiendo-un-peque%C3%B1o-error/64509#64509
