---
layout: post
title: Pdmenu - una herramienta poco conocida
date: 2012-10-12
tag:
  - desarrollo
  - bash
show_meta: true
comments: true  
---

**Pdmenu** es una herramienta con poca "prensa", simple casi escasa en su
funcionalidad: solo se encarga de presentar unos menúes clásicos solo texto en
terminales mediante la librería ncurses, sin embargo combinada con otras
herramientas se vuelve sumamente útil y cómoda para ciertas cosas que hacemos
por línea de comando. Veamos algunos ejemplos:

## History

![pdmenu1][pdmenu]{: style="display: table; margin: 10px auto;"}

Una de las cosas que más me gustaban del viejo y querido **4DOS** era la lista
de últimos comandos ingresados. En bash tenemos el comando "history" que nos
mostrará la lista de los últimos (la cantidad puede variar en función de cada
entorno), bien, presentar esta lista mediante bash + pdmenu es simple

### `h` el Script (chmod +x).

¿Que hace?  Veamos el paso a paso:

* Define dos archivos temporales, uno para el menú dinámico a crear y otro de trabajo
* Define atributos de colores del menú
* Recupera los últimos 100 comandos del histórico, los ordena alfabéticamente y deja lo ocurrencias únicas.
* Termina de crear el script para Pdmenu  agregando un ítem de menú para cada comando, la acción sobre cada ítem al hacer un <ENTER> será ejecutar el comando

``` shell
#!/bin/sh
#
# $Copyright (C) 2006 Patricio Moracho
# 
# h is free software; you can redistribute it and/or
# modify it under the terms of the GNU General Public
# License as published by the Free Software Foundation; either
# version 2 of the License, or (at your option) any later version.
# 
# Filename  : h
# Description : History List viewer
# Require  : tail, sort, uniq, pdmenu
#

menufile="/tmp/$(basename $0).pdmenu"
tmpfile="/tmp/$(basename $0).tmp"

echo "# temporary menu for pdmenu" > $menufile
echo "menu:main:History Menu" >> $menufile
echo "color:desktop:black:gray" >> $menufile
echo "color:base:black:black" >> $menufile
echo "color:title:black:black" >> $menufile
echo "color:menu:lightgray:blue" >> $menufile
echo "color:selbarhot:yellow:black" >> $menufile
echo "color:menuhot:yellow:blue" >> $menufile

tail -n 100 ~/.bash_history |sort|uniq>$tmpfile
while read item
do
  cmd="exec $item"
  echo "   exec:_ $item:pause:$cmd" >>$menufile    
done < $tmpfile

rm $tmpfile

echo "   exit:_Exit" >>$menufile
pdmenu -r -c  $menufile

rm $menufile
```

## Less pero en una ventana

El comando `less` es muy habitual usarlo para mostrar una salida paginada por
ejemplo de otro comando usando la clásica función de tubería `|`. Por ejemplo,
supongamos que queremos listar el contenido de un paquete instalado en el
sistema:

Lo habitual sería: `dpkg -L pdmenu | less`

Sin embargo, mediante este script (de nuevo chmod + x):

``` shell
#!/bin/sh
#
# $Copyright (C) 2006 Patricio Moracho
# 
# lw is free software; you can redistribute it and/or
# modify it under the terms of the GNU General Public
# License as published by the Free Software Foundation; either
# version 2 of the License, or (at your option) any later version.
# 
# Filename    : lw
# Description : less in a window
# Require     : cat, sed, pdmenu
#
 
menufile="/tmp/$(basename $0).pdmenu"
 
echo "# temporary menu for pdmenu" >$menufile
echo "menu:main:Less Window Menu" >>$menufile
echo "color:desktop:black:gray" >>$menufile
echo "color:base:black:black" >>$menufile
echo "color:title:black:black" >>$menufile
echo "color:menu:lightgray:blue" >>$menufile
echo "color:selbarhot:yellow:black" >>$menufile
echo "color:menuhot:yellow:blue" >>$menufile
 
sed -e 's/:/./g' | 
 
while read item 
do
  echo "exec:$item ::$1 cat " $item  " | lw " >>$menufile
done
 
echo "exit:_Exit" >>$menufile
 
cat $menufile | pdmenu -rc -
```

Y ejecutando el mismo comando pero así: `dpkg -L pdmenu | lw`  obtendremos una
salida tipo menú de terminal, con la ventaja que asociado al tenemos asociado
el comando Cat que nos permitirá visualizar el archivo (siempre obviamente que
el ítem sea un path válido)

![pdmenu][pdmenu2]{: style="display: table; margin: 10px auto;"}

[pdmenu1]: {{site.baseurl}}/images/2012/pdmenu1.png
[pdmenu2]: {{site.baseurl}}/images/2012/pdmenu2.png

