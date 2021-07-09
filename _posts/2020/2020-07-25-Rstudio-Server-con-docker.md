---
title: "Rstudio Server para múltiples usuarios con Docker"
author: "Patricio Moracho"
date: "25 de Julio del 2020"
post_date: 2020-07-25
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
description: Como configurar un Rstudio Server con Docker
tags:
- desarrollo
- R
- rstudio
- docker
output:
  github_page:
    jekyllthat::jekylldown
  pdf_document: default
  html_document:
    df_print: paged
---

# Rstudio Server con docker


Iniciamos el servicio de `docker`

    sudo systemctl start docker


**Rstudio** es uno de los mejores IAE - Integrated análisis environment, si acabo de inventar el término. Es un placer trabajar con esta herramienta, es muy liviana y muy sencilla de instalar, de hecho una vez instalado es portable, yo suelo tener en un pendrive un Rstudio + R y tenerlo a mano por cualquier cosa. Como si fuera poco es multiplataforma.

Primero que nada, hay que notar la diferencia entre `imagen` y `contenedor`, cuando haces algo como esto:

    docker run rocker/tidyverse

Lo que hace `docker` es: (1) crear un contenedor que no es más que una instancia de la imagen `rocker/tidyverse` (2) Inicia dicho contenedor. En la práctica lo que vamos a intentar hacer es crear un contenedor especial que ajustaremos a nuestras necesidades y luego simplemente iniciaremos dicho contenedor.

A partir de aquí trabajaremos por línea de comando, a modo de prueba de concepto usaré un usuario común, la maquina virtual funcionará perfectamente siempre que usemos el mismo usuario para crearla, crear las carpetas iniciales e iniciarla.


### 1. Configuración del entorno en la maquina física

Para cualquiera de lo usuarios que vayamos a crear en nuestra maquina virtual, necesitaremos una carpeta personal para cada uno, en ellos no solo se salvarán las configuraciones personales, sino que también se alojara cualquier paquete adicional que el usuario instale. Por lo que mínimamente necesitaremos una carpeta `home` general y obviamente que este fuera del ámbito del contenedor, para que los cambios sean persistentes. Podemos hacer esto:

    mkdir ~/rstudio_home


### 2. Creamos el contenedor

Ahora sí crearemos nuestra real maquina virtual, la imagen `rocker/tidyverse` requiere que definamos un usuario inicial, el puerto y asimismo, necesitaremos definir un volumen para el `home` de cualquier usuario

    docker run -d -p 8787:8787 -e USER=rstudio -e PASSWORD=rstudio --name rstudio-server -v ~/rstudio_home:/home rocker/tidyverse

Detalle:

* `-d`: Se ejecuta el contenedor en background
* `-p 8787:8787`: mapeamos el port externo 8787 con el mismo port interno
* `-e USER=rstudio -e PASSWORD=rstudio` nuestro usuario y password inicial
* `--name rstudio-server`: Queremos seguir usando el contenedor, por lo que le daremos un nombre

 * `-v ~/rstudio_home:/home`: Mapeamos la carpeta que acabamos de crear al `home` de la maquina virtual.

Si esto funcionó correctamente, se habrá impreso por terminal el `ID` de este nuevo contenedor, y se habrá iniciado el  mismo. Podemos verificarlo accediendo mediante el navegador a `http://localhost:8787/`, nos debiera aparecer la página de login, dónde podremos ingresar el hasta ahora único usuario que tenemos. También podremos verificar la existencia de este contenedor mediante `docker container ls` y a partir de ahora nos olvidamos de la imagen y trabajaremos con este contenedor, por ejemplo para detenerlo `docker container stop rstudio-server`, para iniciarlo `docker container start rstudio-server`, o incluso removerlo `docker container rm rstudio-server`.

### 3. Agregar usuarios

En la práctica, nuestra maquina o contenedor, está corriendo un `Linux` muy básico y una instancia de **Rstudio Server**, este último define sus usuarios a partir de los usuarios del sistema operativo, por lo que para conseguir más usuarios deberemos simplemente "entrar" al Linux del contenedor y darlos de alta.

Vamos a abrir una terminal pero en nuestro `rstudio-server`:


    docker exec -it rstudio-server bash

Esto abrirá un `bash` dentro de nuestro contenedor, nos daremos cuenta por que seguramente habrá cambiado nuestro prompt por algo así:

    root@38645db9730e:/#

Y ahora, para crear un usuario nuevo, simplemente haremos algo así:

    root@096cb40493d3:/# adduser usuario1 --gid 1000
    Adding user `usuario1' ...
    Adding new user `usuario1' (1001) with group `rstudio' ...
    Creating home directory `/home/usuario1' ...
    Copying files from `/etc/skel' ...
    New password:
    Retype new password:
    passwd: password updated successfully
    Changing the user information for usuario1
    Enter the new value, or press ENTER for the default
    	Full Name []: Usuario 1
    	Room Number []:
    	Work Phone []:
    	Home Phone []:
    	Other []:
    Is the information correct? [Y/n] Y
    root@096cb40493d3:/#

El comando `adduser usuario1 --gid 1000` dará de alta un nuevo usuario con dicho nombre y nos solicitará una serie de datos (El `--gid 1000` es para que pertenezcan al mismo grupo del Rstudio Server), luego de completarlos habremos creado un nuevo usuario. Con este procedimiento podremos crear los tantos como  vayamos a necesitar, francamente desconozco si Rstudio Server, en particular esta versión Open Source, impone algún limite. F3inalmente para salir: `exit`.

Podremos verificar que se hayan creado las carpetas de cada usuario, revisando nuestro `rstudio_home`:

    |---> # ls -l  ~/rstudio_home/
    total 16
    drwxr-xr-x 2 xxxxxxxx yyyyyyyy 4096 jul 24 23:26 usuario1
    drwxr-xr-x 2     1002 yyyyyyyy 4096 jul 24 23:27 usuario2
    drwxr-xr-x 2     1003 yyyyyyyy 4096 jul 24 23:34 usuario3
    drwxr-xr-x 2     1004 yyyyyyyy 4096 jul 24 23:34 usuario4

Notarás que hay una nueva carpeta por cada usuario que has creado, en ella seguramente hay un directorio R dónde van a parar todos los paquetes que cada usuario descargue. Lo otro que vas a notar, son usuarios y grupos "extraños" o "inconsistentes", lo que ocurre e que estás carpetas se han creado con `ids` de usuarios y grupos propios de la maquina virtual y que no tienen un concordancia con los usuarios y grupos reales del sistema host. Esto no debería traer dificultades a la maquina virtual, pero sí alguna molestia en el host. El tema permisos lo dejo afuera de mi respuesta pero es algo que sin duda deberías profundizar, o más bien aquel que se encargue de administrar el sistema. [Aquí][1] hay información interesante para consultar.

A partir de este momento, recuerda detener el contenedor cuando apaguen el servidor:

    docker container stop rstudio-server

O volverlo a iniciar en cualquier momento

    docker container start rstudio-server

Cada usuario debería poder ingresar a la url `ttp://ip-del-servidor:8787` y logearse con los datos que le hemos asignado a cada uno, crear y salvar archivos persistentes y descargar e instalar paquetes propios de su login.


  [1]: https://vsupalov.com/docker-shared-permissions/
