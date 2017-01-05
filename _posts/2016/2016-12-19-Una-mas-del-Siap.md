---
layout: post
title: "Una más del Siap (y van...)"
date: 2016-12-19
description: Como resolvimos el problema del las CUITS inválidas de países al infor el régimen de Compras y Ventas
tag:
  - siap
show_meta: true
comments: true
---

Estamos con la DDJJ de Iva, usando el aplicativo de Compras y Ventas, la situación: varios servicios contratados a un proveedor de Granada. En el caso de operaciones al exterior lo que se estila es informar una CUIT genérica por País, de hecho el AFIP ofrece una lista de las mismas (ver) que si nos guíamos por el nombre, es una lista del 2010, lo cual ya me hace sospechar problemas, pero más allá de eso, en el caso de Granada las CUITs disponibles son estás:

![alt text](https://2.bp.blogspot.com/-3GR6MHZ-gkA/WFhC64QJPVI/AAAAAAAAHis/_bR7OGrqLKgsSMLINYXwTWmQjncO9z-gQCLcB/s640/Captura.PNG "Logo Title Text 1")

Hay tres tipos, 0 - Jurídico, 1 - Físico y 2 - Otro tipo de entidad, al ser el proveedor una empresa informamos con el jurídico y ohhh, sorpresa! el SIAP nos dá error al importar, más concrectamente **"EL CAMPO NUMERO DE IDENTIFICACION DEL VENDEDOR ES INVALIDO"**. Que nos ocurre, amor de mi vida? nada, simplemente que la CUIT es errónea, no valida el dígito verificador!!!.  Hay varios países en esta situación, en el caso de Granada, tanto el físico como el jurídico no validan, por lo que la única solución posible, fue informar las operación con el único CUIT  valido, el tipo 2. 
Me gustaría realmente saber por qué alguién del propio Afip, en su momento, cuando definió las CUITS de los países, no las validó o si las hizo y vió que no eran válidas, por que siguió adelante. La máxima de siempre: "Por que hacer algo bien, si lo podemos hacer mal?"


.. Continuara..//
