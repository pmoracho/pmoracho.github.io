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
[Microsoft][ms], el booteable, con [unetbootin][unetbootin], bajo linux,
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

Podemos usar el repositorio de los driver de Realtek RTL8821CE Driver para
ArchLinux.  Claro que al no tener red, habrá que clonar el respoitorio en un
medio extraible ("pendrive"):

    git clone https://github.com/tomaspinho/rtl8821ce.git

En el caso de Mint, ya tenemos todas las herramientas de compilación
instaladas, lo cual es postivo, por que no tenemos red.

    cd rtl8821ce
    sudo ./dkms-install.sh

Y listo..

### Escanner de huella

Esto lamentablemente, al menos a la fecha, no tiene solución. Si bien ya existe
el soporte de este tipo de dispositivos, básicamente el [proyecto][fprint]. El
problema es que particularmente el hardware todavía no está soportado, la lista
completa [aquí][listdev], para saber que dispositivo tenemos:

    |---> # lsusb
    Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
    Bus 001 Device 005: ID 0458:6001 KYE Systems Corp. (Mouse Systems) GF3000F Ethernet Adapter
    Bus 001 Device 003: ID 13d3:56b2 IMC Networks 
    Bus 001 Device 002: ID 27c6:55b4  
    Bus 001 Device 004: ID 0bda:c024 Realtek Semiconductor Corp. 

El id del hardware es `27c6:55b4`, todavía no esta en la lista del hardware
soportado, no hay otra cosa que esperar, una lástima, por que lo he llegado a
probar en Windows y es sumamente práctico.

## Postinstalación

A continuación la lista de cosas que hacemos luego de instalar el equipo.

1. Habilitar el modo Legacy para las teclas de función.

El comportamiento por defecto de las teclas F1..F12 unicamente se activa si
combinamos con la tecla Fn. Esto es posible modificarlo desde la BIOS, que de
paso, se entra presionando F2, luego simplemente buscar el ítem "Hot Key"

2. Actualizar el sistema

        sudo apt update && sudo apt upgrade -y

3. Instalar codecs multimedia

        sudo apt-get install mint-meta-codecs

4. Crear un snapshot del sistema con `timeshift`
5. Deshabilitar aplicaciones que se crgan al inicio del sistema
6. Habilitar y configurara el firewall
7. Limpiar el sistema

        sudo apt-get autoclean
        sudo apt-get clean
        sudo apt-get autoremove

## Algunas soluciones a problemas varios

1. `Guake` se "cuelga" al salir.

        sudo apt install --reinstall libutempter0

2. Error: `lightdm: PAM adding faulty module: pam_kwallet5.so`, según consta en
   `journalctl --no-pager -b -p3`. [Este post][pam] explica bastante bien el
   problema y la solución. Simplemente editar estos archivos

        sudo xed /etc/pam.d/lightdm 
        sudo xed /etc/pam.d/lightdm-greeter
        
   y hay que editar, y comentar estas líneas

        #auth optional pam_kwallet.so
        #auth optional pam_kwallet5.so

        #session optional pam_kwallet.so auto_start
        #session optional pam_kwallet5.so auto_start 

[ms]: https://www.microsoft.com/es-es/software-download/windows10ISO
[unetbootin]: https://unetbootin.github.io/linux_download.html
[mint]: https://linuxmint.com/edition.php?id=256
[mintiso]: https://torrents.linuxmint.com/torrents/linuxmint-19.2-mate-64bit.iso.torrent
[wifi]: https://github.com/tomaspinho/rtl8821ce
[fprint]: https://launchpad.net/~fingerprint/+archive/ubuntu/fingerprint-gui
[listdev]: https://fprint.freedesktop.org/supported-devices.html
[pam]: https://forums.linuxmint.com/viewtopic.php?t=286950
