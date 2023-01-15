---
title: "Visual Code"
author: "Patricio Moracho"
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
description: Información útil para usar Visual Code
tags:
- desarrollo
- vcode
output:
  github_page:
    jekyllthat::jekylldown
  pdf_document: default
  html_document:
    df_print: paged
---

# Visual Code

Creo que se está convirtiendo en mi editor favorito. Hay cosas que viniendo de Vim se extrañanan, pero:

* Es multiplataforma
* Con una interfaz muy consistente simple y clara
* Reconoce automáticamente los entornos virtuales de Python
* Previsualización de documentos de markup
* Super extensible
* Muy buena integración con Git

## Atajos de teclado funadamentales

| Detalle                  | Atajo                                                      |
|--------------------------|----------------------------------------------------------- |
| Paleta de comandos       | <kbd>ctrl + shift + p</kbd>                                |
| Quick open               | <kbd>ctrl + p</kbd>                                        |
| Confguración             | <kbd>ctrl + ,</kbd>                                        |
| Cerrar pestaña           | <kbd>ctrl + w</kbd>                                        |
| Ocultar sidebar          | <kbd>ctrl + b</kbd>                                        |
| Ocultar Panel            | <kbd>ctrl + j</kbd>                                        |
| Abrir previsualización   | <kbd>ctrl + k</kbd> <kbd>v</kbd>                           |
| Multiples cursores Up    | <kbd>ctrl + shift + Up</kbd>  (<kbd>esc</kbd> para salir)  |
| Multiples cursores Dwn   | <kbd>ctrl + shift + Dwn</kbd> (<kbd>esc</kbd> para salir)  |
| Salvar archivo           | <kbd>ctrl + s</kbd>                                        |
| Salvar archivo como      | <kbd>ctrl + shift + s</kbd>                                |
| Seleccionar Linea        | <kbd>ctrl + l</kbd>                                        |
| Comentar línea           | <kbd>ctrl + k</kbd> <kbd>ctrl + c</kbd>                    |
| DesComentar línea        | <kbd>ctrl + k</kbd> <kbd>ctrl + u</kbd>                    |
| Toggle coment            | <kbd>ctrl + shif + 7</kbd>                                 |
| Toggle Errores           | <kbd>ctrl + shif + m</kbd>                                 |
| Modo Zen                 | <kbd>ctrl + k</kbd><kbd>z</kbd>                            |


## Atajos de teclado customizados


| Detalle                  | Atajo                       | Comando                                        |
|--------------------------|-----------------------------|------------------------------------------------|
| Salir                    | <kbd>alt + x</kbd>          | workbench.action.quit                          |
| Nueva terminal integrada | <kbd>alt + t</kbd>          | workbench.action.terminal.newInActiveWorkspace |
| Duplicar línea           | <kbd>ctrl + Shift + d</kbd> | editor.action.copyLinesDownAction              |
| Selecionar palabra       | <kbd>alt + w</kbd>          | editor.action.addSelectionToNextFindMatch      |
| Comentar código          | <kbd>alt + w</kbd>          | editor.action.addSelectionToNextFindMatch      |
| Reformatear parrafo      | <kbd>alt + q</kbd>          |                                                |

## Latex Workshop (extenión)

* [home][workshop]
* [Sinippets y atajos de teclado][Snippets]


| Detalle                  | Alias | Atajo                                      |
|--------------------------|-------|--------------------------------------------|
| Font: Texto              | `FIT` | <kbd>ctrl + l</kbd><kbd>kbd>ctrl + t</kbd> |
| Font: Italica            | `FII` | <kbd>ctrl + l</kbd><kbd>kbd>ctrl + i</kbd> |
| Font: Emfasis            | `FBF` | <kbd>ctrl + l</kbd><kbd>kbd>ctrl + b</kbd> |




## Trucos

## Extensiones

| Extensión        | Detalle                                                |                                             |
|------------------|--------------------------------------------------------|---------------------------------------------|
| Reflow paragraph | Reajustar los parráfos a un ancho de línea determinado | ext install TroelsDamgaard.reflow-paragraph |
| Latex Workshop | Para editar Latex, imprescindible  | ext install James-Yu.latex-workshop |



[Snippets]: https://github.com/James-Yu/LaTeX-Workshop/wiki/Snippets#Handy-mathematical-snippets
[workshop]: https://github.com/James-Yu/LaTeX-Workshop/wiki