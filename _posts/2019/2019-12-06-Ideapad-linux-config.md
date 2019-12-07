---
title: "Configurando un Linux Mint en una Ideapad 540"
author: "Patricio Moracho"
date: 2019-12-05
post_date: 2019-12-05
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
description: Configurando un Linux Mint en una Ideapad 540
tags:
  - linux
output:
  github_page:    
    jekyllthat::jekylldown
  pdf_document: default
  html_document:
    df_print: paged
---

Llegó a mis manos una **Lenovo IdeaPad S540**, una belleza de maquina. Lo
primero como siempre, instalar Linux. La maquina ya viene con Windows 10 Home,
si se quiere conservar la licencia lo ideal es crear una ISO de la instalación,
para luego hacer un usb booteable, interesante, que los números de licencia se
encuentren en la BIOS del equipo por lo que no hay que ni escribirlos ni
memorizarlos. 

De cualquier forma, hoy es posible descargar una ISO válida, directamente desde
[ms][Microsot], el booteable, con [unetbootin][unetbootin], bajo linux,
en el sistema de Microsoft no tuve buenas experiencias, en este sentido.

Elegimos como SO, **[Linux Mint 19 "Tara" mate version][mint]**, hace años que
uso este sabor, variante **xfce**, así que ya estoy bastante acostumbrado y
francamente ya perdí el interés en estar probando sistemas e interfaces nuevas
todo el tiempo. El proceeso de instalación no puede ser más sencillo, lo que sí,
la IdeaPad solo tiene puertos usb por lo que habrá que construir un "pendrive"
booteable desde la [ISO][mintiso]. Muy pocas cosas para configurar, idiomoa, 
teclado,  zona horaria, usuario, nombre ddel equipo y listo. En pocos 
minutos tendremos un SO Linux totalmente funcional. ¿Totalmente?, bueno,
no exactamente, algunas cosas habrá que meter mano. Hasta hoy hay dos cosas que
el instalador de Mint no resuelve bien: el hardware de red y el escaner de 
la huella dactilar. Vamos por parte:

### Wifi

Entiendo que la placa de red no esta soportada nativamente por el instalador,
por lo que de entrada no hay wifi y teniendo en cuenta que el equipo tampoco
tiene un puerto ethernet, esto es bastante molesto. Pero a no deseperar,
buscando se encuentran soluciones. Y la solución pasa por compilar e instalar
el driver de la placa, al menos en el caso de esta placa:

    > inxi -F|grep RTL8821CE
    Network:   Device-1: Realtek RTL8821CE 802.11ac PCIe Wireless Network Adapter driver: rtl8821ce





[ms]: https://www.microsoft.com/es-es/software-download/windows10ISO
[unetbootin]: https://unetbootin.github.io/linux_download.html
[mint]: https://linuxmint.com/edition.php?id=256
[mintiso]: https://torrents.linuxmint.com/torrents/linuxmint-19.2-mate-64bit.iso.torrent
