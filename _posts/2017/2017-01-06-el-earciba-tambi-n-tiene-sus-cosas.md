---
layout: post
published: true
date: '2017-01-06'
modified: '2017-01-06'
imagefeature: sitelogo.png
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
title: El eARCIBA también tiene sus cosas
tags:
  - eArciba
description: El eARCIBA también tiene sus cosas

---
## Notas de crédito aplicadas a comprobantes de otros períodos

Después de muchisíma idas y vueltas e investigación, puedo confirmar lo siguiente:

* Si se informan los créditos del período que aplican a facturas del mismo,
  tendremos problemas para ingresar los de otros períodos si el día
  del crédito entre ambos es el mismo. Es muy loco, pero e más o menos así:
    * Estamos en el período 9/17
	* informamos un crédito del 13/9/17
	* Podremos agregar un crédito del 12/7/17 del 14/2/17 pero jámas uno del día 13 de cualquier otro período

* Al final llegamos a un procedimiento más o menos como esto:
  * Créditos parciales ya sabemos que no pasan (no van)
  * Créditos totales de facturas aplicadas del mismo período, las neteamos o sea no informamos ni créditos ni facturas (conversado con personal del Agip)
  * Créditos aplicados a facturas de otros períodos ahora sí los informamos todos

Tipo        Aplicado a facturas	 Detalle
----------- -------------------- -------------------------------
Parcial		Cualquiera           No se informan 
Total       Del mismo período    Se netean con las facturas, no informamos ni débitos ni créditos (conversado con personal del Agip)
Total       De otros períodos    Se informan sin problemas, 

## No más créditos parciales

Hace uno o dos meses, creo que a partir de la implementación del régimen
general, el **eArciba** no permite más importar créditos parciales. O sea si se
quiere anular parte de una factura, si ésta tiene percepción, hay que anularla
completamente y refacturar. A los proveedores habrá que pedirles lo mismo, la
anulación completa y la refacturación.

En el caso de las retenciones está ocurriendo algo similar. Hablamos con la
gente del **Agip** y esto hay que tomarlo con "pinzas", nos sugirieron "netear"
los comprobantes. Es decir, eliminar el crédito y ajustar el débito para que se
corresponda con la retención fina informada, obviamente nuestra pregunta fue:
¿Y si tenemos una inspección? la respuesta: si conservan los dos comprobantes y
la orden de pago con los certificados de retención se puede demostrar que lo
informado se corresponde con la realidad. No sé... no me hago cargo..

## Retenciones por facturas de letra "B"

Estos comprobantes informados como factura de letra "B" no pasan, nuevamente la
sugerencia del **Agip**: informarlos como "09 - Otros comprobantes" y sin
letra. Efectivamente así pasan.


## Otras cosas a tener en cuenta

* Los archivos tienen que estar ordenados por fecha de retención / percepción, sino el proceso va a fallar
* El Monto Sujeto a Retención/Percepción debe ser igual al Monto Total del Comprobante menos el IVA menos Otros Conceptos
* Si se trata de una percepción los únicos comprobantes válidos son 01-Factura y 09-Otro Comprobante
* Los sujetos que no son de capital deben informarse como 1 - No inscriptos

## Enlaces interesantes

* [Parseit](https://pmoracho.github.io/parseit/) es fundamental para analizar
  los archivos generados para el **eArciba**.

