---
layout: post
title: Mis tips de Vim
description: Los atajos de teclado de Vim que no quiero olvidar
date: 2016-12-23
tag:
  - vim
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

Mi particular lista de comandos de Vim. Los que en particular me resultan
interesantes de usar. Trucos y keymaps interesantes y configuraciones
particulares
<!-- break -->

### Basicos

Teclas	|Descripción
--------|-----------
e!		| Volver a cargar el archivo actual descartando cambios
`Esc`	| Volver al modo normal
i 		| Modo insert
o 		| Inserta una nueva linea y entra en modo inserción
:q		| Salir
:q! 	| Salir sin grabar
:w 		| Grabar
ZZ 		| Grabar el archivo y salir
R		| Modo remplazo
J		| Join de la lineas seleccionadas
q:      | commandline history Window (puts you in full edit mode)
`Nop`"q/| Search history Window (puts you in full edit mode)
:`C-F`	| History Window

### Movimiento 

Teclas	|Descripción
--------|-----------
h 		| Mover cursor a Izquierda ([arrow left])
j		| Mover cursor hacia abajo ([arrow down])
k		| Mover cursor hacia arriba ([arrow up])
l		| Mover cursor a derecha ([arrow right])
0		| Ir al primer caracter de la línea
^		| Ir al primer caracter no blanco de la línea (en algunos teclados ^^)
$		| Ir al final de la línea desde el cursor
w		| Moverse de a palabras con el cursor a derecha
b		| Moverse de a palabras con el cursor a izquierda
`n`gg	| Ir a la línea `n`. ej 34gg
`n`f`c`	| Moverse a la primer `n` ocurrencias del caracter `c` a la izquierda, por ej, 5fx i a la quinta ocurrencia de 'x'
`n`t`c`	| idem f, se posiciona un caracter antes
C-o		| Undo de movimientos

### Borrar

Teclas	|Descripción
--------|-----------
`n`dd 	| Borrar `n` líneas. Por ej. 5dd
`n`dw	| Borrar `n` palabras
`n`cw 	| Borrar `n` palabras y habilitar el modo insert
d$ 		| Borrar desde el cursor hasta el fin de línea (también D)
`n`x 	| Borrar `n` caracteres después del cursor
`n`X 	| Borrar `n` caracteres antes del cursor
`n`d`m`	| Borrar `n` caracteres según el movimiento del curso `m`. Ej. 4dh borra 4 caracteres a izquierda
`n`d`m`	| Borrar `n` caracteres según el movimiento del curso `m`. Ej. 4dh borra 4 caracteres a izquierda
D		| Borra hasta el final de la linea
`<C-h>`	| En modo insert: borra un carácter hacia atrás (backspace)
`<C-w>`	| En modo insert: borra una palabra hacia atrás
`<C-u>`	| En modo insert: borra la línea entera hacia atrás


### Edición

Teclas		|Descripción
------------|-----------
.			| Repite el último comando de cambio
`n`o`<esc>`	| Inserta 1 línea en blanco
`n`J		| Junta líneas, funciona con selección
u			| Deshacer, también C-Z en windows
`n`Y 		| Copia `<n>` Líneas a partir del cursor a la memoria
`n`p		| Pega `n` veces lo último copiado luego del cursor (los comandos que borran también copian)
`n`P		| Pega `n` veces lo último copiado después del cursor
`n`r`c`		| Remplaza desde el cursor `n` veces por el caracter `c`
`n`i`c`		| Inserta desde el cursor `n` veces el caracter `c`
ct`c`		| Cut til. Cortar hasta el caracter `c` (foward)
cT`c`		| Cut til. Cortar hasta el caracter `c` (backward)
dt`c`		| Delete til. Borrar hasta el caracter `c` (foward)
dT`c`		| Delete til. Borrar hasta el caracter `c` (backward)
YP			| Duplica la linea actual

### CASE

Teclas			|Descripción
----------------|-----------
Vu				| Lowercase line
VU				| Uppercase line
vEU				| Switch word to uppercase
ggguG			| Set all text to lowercase
:set ignorecase	| Ignore case in searches
:set smartcase	| Ignore case in searches excepted if an uppercase letter is used
`:%s/\<./\u&/g`	| Sets first letter of each word to uppercase
`:%s/\<./\l&/g`	| Sets first letter of each word to lowercase
`:%s/.*/\u&`	| Sets first letter of each line to uppercase
`:%s/.*/\l&`	| Sets first letter of each line to lowercase

### Selección

Teclas	|Descripción
--------|-----------
vi`c`	| "Visual inside `c`haracter". Selecciona entre caracteres. Por ej vi" o vi' o vi(
vib		| "Visual inside block". Selecciona entre parentesis/bloques
di`c`	| "Delete inside `c`haracter". Borra entre caracteres. Por ej di" o di' o di(
dib		| "Delete inside block". Borra entre parentesis/bloques
ci`c`	| "Cut inside `c`haracter". Corta entre caracteres. Por ej ci" o ci' o ci(
cib		| "Cut inside block". Corta entre parentesis/bloques
`C-Q`	| Selección visual en columnas

### Marcas y navegación

Teclas	|Descripción
--------|-----------
m`c`	| Establece una marca `c` (de la a a la z)
'`c`	| Posiciona el cursor en la marca `c`
'.		| Ir a la última linea modificada del archivo
``.``	| Ir a la posición exacta del último cambio
g;		| cycle thru recent changes (oldest first) (new in vim6.3)
g,  	| Reverse direction (new in vim6.3)
`C-O`   | Retroceder según lo movimientos realizados en el archivo
`C-I`   | Retroceder según lo movimientos realizados en el archivo (dirección inversa)

### Macros

Teclas             | Descripción
-------------------| -----------
q{r}               | Graba una macro en el registro {r} (finaliza grabación con q).
Q{r}               | Agrega comandos a la macro del registro {r}.
{n}@{r}            | Ejecuta {n} veces la macro del registro {r} (Por ej. 5@{r})
@@                 | Ejecuta la última macro ejecutada
:reg {r}           | Examina la macro del registro {r}
:normal @{r}       | Aplica la macro a todas las selecciones visuales
:argdo normal @{r} | Aplica la macro a todas las selecciones visuales de todos los buffers abiertos

### Búsquedas

Teclas			|Descripción
----------------|-----------
`n`/`s` `enter`	| Busca `n` veces la cadena `s` hacia adelante
`n`?`s`	`enter`	| Busca `n` veces la cadena hacia atrás `s`
n 				| Busca la siguiente ocurrencia
N 				| Busca la ocurrencia anterior
/^`s`	`enter`	| Busca `s` al comienzo de las líneas
/`s`$	`enter`	| Busca `s` al final de las líneas
/`s`.`t` `enter`| Busca `s` Cualquier caracter y `t` (Ej: Pe.o encontraría Pelo y Peso)

### Folding

Comando	|Descripción
--------|-----------
zc		| Cierra el "folding" actual
zo		| Abre el "folding" actual
za		| Cambia el estado del "folding"
zC		| Cierra todos los "foldings"
zO		| Abre todos los "foldings"
zA		| Cambia el estado de todos los "foldings"


### Modos

Commands				|Mode
------------------------|-----------
nmap, nnoremap, nunmap	| Normal mode
imap, inoremap, iunmap  | Insert and Replace mode
vmap, vnoremap, vunmap  | Visual and Select mode
xmap, xnoremap, xunmap  | Visual mode
smap, snoremap, sunmap  | Select mode
cmap, cnoremap, cunmap  | Command-line mode
omap, onoremap, ounmap  | Operator pending mode


### Plugins y configuraciones propias

Modo|Commands				| Descripción
----|-----------------------|-----------
n,i	|`<A-k>` 				| **vim-signature**: Muestra lista de marcadores
n	|`<leader>e`			| **scalpel**: Reemplazo de la palabra activa
n	|`gc<motion>`			| **Commentary** para comentar, porej gcap para un parrafo
n	|`gcc`					| **Commentary** para comentar una línea

### Plugins: vim-textobj

Plugin					|Mode|
------------------------|----|-------
vim-textobj-python		|norm|`<action>if` inner function, por ej  `vif`, `dif`
vim-textobj-python		|norm|`<action>ac` a class, por ej  `vac`, `dac`
vim-textobj-python		|norm|`<action>ic` inner class, por ej  `vic`, `dic`
vim-textobj-comment		|norm|`<action>ac` a comment, por ej  `vac`, `dac`
vim-textobj-comment		|norm|`<action>ic` inner comment, por ej  `vic`, `dic`

### Comandos y Trucos

Comando					|Descripción
------------------------|-----------
!`n`Gsort`Enter`   		| Ordena mediante sort las `n` líneas a partir del cursor
:marks					| Muestra las marcas activas
:view `<f>`   			| Abre en modo Read only el archivo `<f>`
:ab		 				| Abreviaciones (por ej. :abreviate pm Patricio Moracho)
:g/^\s*$/d     			| Elimina todas la líneas en blanco
:% s /`b`/`r`  			| Remplaza primera ocurencia de `<b>` por `<r>`
:% s /`b`/`r`/g			| Remplaza todas las ocurencias de `<b>` por `<r>`
:%s/ \s*$/				| Remplaza espacios y tabs finales de cada línea de un archivo
:%retab!           		| Retabular el archivo (convierte espacios en tabs)
:changes				| Lista los cambios e el archivo
:ju(mps)         		| Lista movimientos por los archivos
:his c           		| Historia de comandos
:his s           		| Historia de búsquedas
Ctrl-w Ctrl-f			| open file under cursor in new window
`<esc>``n`i`c``<esc>`	| Inserta `n`veces el caracter `c`
:`<Ctr-f>`          	| Historia de comandos
gqap         	 		| Reformatea parrafo actual


### Plugin: vim-signature

Commands       		    |Mode
------------------------|-----------
mx           			| Toggle mark 'x' and display it in the leftmost column
dmx          			| Remove mark 'x' where x is a-zA-Z
m,           			| Place the next available mark
m.           			| If no mark on line, place the next available mark. Otherwise, remove (first) existing mark.
m-          	 		| Delete all marks from the current line
m`<Space>`   			| Delete all marks from the current buffer
]           			| Jump to next mark
[\'          			| Jump to prev mark
]'           			| Jump to start of next line containing a mark
['           			| Jump to start of prev line containing a mark
\']           			| Jump by alphabetical order to next mark
']           			| Jump by alphabetical order to start of next line having a mark
'[           			| Jump by alphabetical order to start of prev line having a mark
m/           			| Open location list and display marks from current buffer
m[0-9]       			| Toggle the corresponding marker !@#$%^&*()
m`<S-[0-9]>` 			| Remove all markers of the same type
]-           			| Jump to next line having a marker of the same type
[-           			| Jump to prev line having a marker of the same type
]=           			| Jump to next line having a marker of any type
[=           			| Jump to prev line having a marker of any type
m?           			| Open location list and display markers from current buffer
m`<BS>`      			| Remove all markers

### Mapeos particulares

Modo 	|Commando			|Descripción
--------|-------------------|-----------
n,i		| `A-n`    			| Enciende o apaga el modo de numeración relativo
n		| `<Leader>cd`		| Configura el path de trabajo al del archivo abierto
n, i	| `A-d`				| Duplica la línea actual
v		| `(,[,{,",<,'`		| Cierra la selección visual con los caracteres mencionados
n, i	| `F2`				| Salvar buffer
n, i	| `F5`				| Maximizar ventana
n, i	| `F6`				| Arranca un explorer de windows en el directorio actual
n, i	| `F8`				| Enciende o apaga el navegador de "Tags" a la izquierda (requiere pluggin)
n, i	| `F10`				| Enciende o apaga el exporador NerdTree (requiere pluggin)
n, i	| `F11`				| Enciende o apaga el modo "Full Screen" en Windows
n, i	| `F12`				| Abre el .vimrc
n, i	| `A-w`				| Selecciona la palabra
n, i	| `A-<end>`			| Abre el Buffer exlorer (requiere pluggin)
n, i	| `A-X` o `A-x`		| Salir de Vim
n, i	| `A-<home>`		| Muestra las sesiones de edición disponibles (requiere pluggin)
n, i	| `C-n`				| Abrir un nuevo buffer en blanco
n, i	| `A-<Up>`			| Mueve la línea actual hacia arriba
n, i	| `A-<Down>`		| Mueve la línea actual hacia abajo
n, i, v	| `A-<Right>`		| Identado de la línea o selección a derecha
n, i, v	| `A-<Left>`		| Identado de la línea o selección a izquierda
v		| `j,`, `j;`, `j `	| Join de líneas con separador `,`, `;` o ` `

