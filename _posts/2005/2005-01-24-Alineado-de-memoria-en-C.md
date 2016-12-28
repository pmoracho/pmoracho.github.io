---
layout: post
title: Alineado de memoria en C
description: Como trabajar correctamente en la lectura binaria de archivos y las estructuras en memoria en C 
date: 2016-10-11
tag:
  - c
  - desarrollo
show_meta: true
comments: true
---

Bueno, hay un tema que hace tiempo lo había estudiado y resulta que ahora me estaba pasando lo mismo y no me acordaba exactamente que era. Para ubicarnos, un código C, uso de una estructura y un sizeof que devuelve cualquier “verdura”. Veamos, este es el ejemplo:

``` c
struct page_idx
{
    char app[4];   — 4 bytes
    char nose[2];  — 2 bytes
    long page_no;  — 4 bytes
    long page_pos; — 4 bytes
};
```

Se supone que esto debería dar 14, sin embargo compilándolo con el Gcc, el sizeof me da 16 bytes???. El tema es la alineación de las estructuras que usa el compilador, básicamente la idea es mejorar la performance de acceso a la memoria, para hacer esto, las estructuras se almacenan en bloques de memoria que sean múltiplos del elemento de mayor tamaño, en este ejemplo 4 bytes, por lo que la estructura final ocuparía 16 bytes. Esto no debería ser ningún problema si la estructura la creamos y la usamos en memoria, pero en el momento que necesitamos usarla para acceder a un registro en un archivo, la cosa se complica. La solución es “avisarle” al compilador que no haga ninguna alineación, a mi gusto la mejor forma es declarar la estructura así:

``` c
struct page_idx
{
    char app[4];   — 4 bytes
    char nose[2];  — 2 bytes
    long page_no;  — 4 bytes
    long page_pos; — 4 bytes
}__attribute__ ((packed));
```

Y eso es todo..//
