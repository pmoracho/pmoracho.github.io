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

Resulta interesante que nunca hayan dejado importar notas de crédito (que
generan percepciones o retenciones) si aplican a comprobantes de un período
distinto al informado. Esto virtualmente imposibilita emitir un crédito con
percepción a una factura del mes pasado, o recibir una nota de crédito en esta
misma situación de parte de un proveedor, o al menos si no queremos "perder" la
parte del impuesto. No sé bien en que se sustenta este criterio, cuando con el
IVA estos es absolutamente posibles de hacer.

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

