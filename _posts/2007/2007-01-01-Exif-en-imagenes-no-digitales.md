---
layout: post
title: Exif en imágenes no digitales.
date: 2017-01-01
description: Incorporar datos exif a imagenes escaneadas
tag:
  - bash
  - foto
show_meta: true
comments: true
published: true
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
image:
  feature: 2017/img-feature-004.jpeg
  credit: Patricio Moracho
---

## Exif en imágenes no digitales

A raiz de una consulta acerca de si la información Exif era "editable", me
surgió la idea de buscar una solución a algo que me molesta hace tiempo. Todos
estamos acostumbrados y damos por hecho el contar con los metadatos exif de las
imágenes digitales. Obviando el tema de la privacidad, es una información sin
duda muy útil para compartir, además muy necesaria para los softwares de
catalogación como Picasa o similares, sin embargo en las imágenes de origen no
digital, me refiero a las digitalizaciones de película, esta información no
existe, a lo sumo algunos programas, salvan algún que otro dato de la captura.
Yo particularmente estoy de a poco llevando todo mi archivo de negativos y
"diapos" a digital con un Epson 4490 y me sería muy útil etiquetar los
archivos, colocando al menos el modelo de cámara, la fecha de toma, película y
datos en general que recuerde. No sé si tienen alguna opción al respecto, en el
mundillo Linux, dónde me muevo, hay varias herramientas para la manipulación de
datos Exif. La que me pareció más interesante es [Exiv2](http://www.exiv2.org/)
que tiene versiones para Linux y Windows, se trata de una librería para
programación y de una herramienta por línea de comandos que permite realizar
todo tipo de manipulación sobre los metadatos, agregar, editar, borrar listar,
etc.

Básicamente armando un archivo con la configuración de los datos exif, que
queremos incorporar a uno o más archivos de imágenes podemos correr este
sencillo comando: `exiv2 -m<archivo de configuración> *.jpg` y etiquetamos todo
un conjunto de imágenes.


Les muestro un ejemplo de la configuración que usé para catalogar una serie de
fotos tomadas por una canonet 28

```
# Exif data
# Datos generales #########################################################################################################
set   Exif.Image.Copyright			Ascii		"Copyright Patricio Moracho. All rights reserved."
set   Exif.Image.Artist				Ascii		"Patricio Moracho <pmoracho@gmail.com>"
set   Exif.Image.HostComputer		Ascii		"Linux patagon 2.6.31-19-generic #56-Ubuntu SMP Thu Jan 28 02:39:34 UTC 2010 x86_64 GNU/Linux"
set   Exif.Image.Software			Ascii		"Epson Scan v3.24a"
set   Exif.Image.Artist				Ascii		"Patricio Moracho <pmoracho@gmail.com>"
set   Exif.Photo.UserComment		Ascii		"Photo taken with  by Patricio Moracho <pmoracho@gmail.com>, scanned with Epson 4490"

# Datos de la cámara ######################################################################################################
set   Exif.Image.Make					Ascii		"Canon"
set   Exif.Image.Model					Ascii		"Canonet 28"
set   Exif.Photo.FocalLength			Rational	"28/1"
set   Exif.Photo.FocalLengthIn35mmFilm	Short		"28"
set   Exif.Photo.SubjectDistanceRange   Short		"0"
set   Exif.Photo.WhiteBalance			Short		"1"
set   Exif.Photo.Contrast               Short      	"0"
set   Exif.Photo.Saturation             Short      	"0"
set   Exif.Photo.Sharpness              Short      	"0"
set   Exif.Photo.GainControl            Short      	"0"
set   Exif.Photo.ExposureProgram        Short      	"3"
set   Exif.Photo.ExposureMode         	Short      	"0"
set   Exif.Photo.MeteringMode         	Short      	"2"

# Datos de la foto ########################################################################################################
set   Exif.Photo.ExposureTime		Rational	"1/30"
set   Exif.Photo.FNumber			Rational	"28/10"
set   Exif.Image.DateTime			Ascii		"2010-02-21 01:36:42"
set   Exif.Photo.DateTimeOriginal	Ascii		"2010-02-21 01:36:42"
set   Exif.Photo.Flash				Short		"0"
set   Exif.Photo.LightSource		Short		"1"
set   Exif.Photo.DateTimeDigitized	Ascii		"2010-02-21"
set   Exif.Photo.ISOSpeedRatings	short		"100"
```
