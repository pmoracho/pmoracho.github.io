---
layout: post
title: Un reemplazo mejor al cd de Linux.
date: 2014-08-06
tag:
  - bash
  - desarrollo
  - terminal
show_meta: true
comments: true
---

Cada vez estoy pasando más horas delante de una terminal *Nix, ya sea en para
acceder remotamente a sistemas, como también por el uso cada vez mayor que hago
del entorno **Cygwin** en Windows. Por lo que ya estoy customizando y armando
herramientas para mi uso diario. La idea, como siempre, es compartir. Esta vez
le toca a un reemplazo del viejo y conocido comando `cd` o "change directory".
La necesidad puntual era poder mantener un historial de carpetas visitadas, de
modo de poder acceder de forma rápida a las mismas. Es habitual acceder a
directorios largos o profundos, digamos por ejemplo:
`/cydrive/c/SVN/proyecto-trunk/resource/test/input/test1`, a veces también hay
que moverse momentáneamente de una carpeta a otra, en fin, escribir esto cada
vez es engorroso.

Hay un par de comandos internos de **Bash** muy interesantes que explotan la
posibilidad de mantener una "pila" de carpetas. Hablamos de `popd`, `pushd` y
`dirs`, conviene investigarlas. Asimismo hay una función muy interesante
desarrollada por Petar Marinov, que se suele incorporar al `.bashrc` y usarla
como alias del `cd`. Sobre esta base hice una pocas modificaciones para
adaptarla a mis propias necesidades.

``` shell
##################################################################################
## A better cd replacement by Patricio Moracho {pmoracho@gmail.com}
## Based most on a work of Petar Marinov
## Improvements:
## + Could set maxitems for dir stack (set to 20)
## + Save dir stack between sessions
## + Grep for saved paths for better search cd :text
##################################################################################
mycd ()
{
	local x2 the_new_dir adir index maxitems histdirfile actual
	local -i cnt
	histdirfile="$HOME/.histdirfile"		# Set history dir file
	maxitems=20								# Items to save in dirstack
	#############################################################################
	# The first time if not exist dir stack and exist $histdirfile (load it)
	#############################################################################
	lines=`dirs -v|wc -l`
	if [ $lines -lt 2 ] && [ -f $histdirfile ]; then
	  actual=`pwd`
		while read directory ; do
			pushd "$directory" 2>/dev/null 1>/dev/null
		done < $histdirfile
		cd "$actual"
	fi 
	#############################################################################
	# List paths (cd --) for compatibility with Petar Marinov func, replace
	# with (cd :)
	#############################################################################
	if [[ $1 ==  "--" ]]; then
		dirs -v -l | gawk '{printf("\033[1;33m%2d\033[0m %s\n",$1,$2)}'
		return 0
	fi
	#############################################################################
	# List paths (cd :)
	# List paths with grep for string (cd :tmp)
	#############################################################################
	if [[ ${1:0:1} ==  ":" ]]; then
		search=${1:1}
		if [[ $search == "" ]]; then
			dirs -v -l | gawk '{printf("\033[1;33m%2d\033[0m %s\n",$1,$2)}'
		else
			dirs -v -l | grep -i -e $search | gawk '{printf("\033[1;33m%2d\033[0m %s\n",$1,$2)}'
		fi
		return 0
	fi
	the_new_dir=$1
	[[ -z $1 ]] && the_new_dir=$HOME
	#############################################################################
	# Dir in -number way
	#############################################################################
	if [[ ${the_new_dir:0:1} == '-' ]]; then
		# Extract dir N from dirs
		index=${the_new_dir:1}
		[[ -z $index ]] && index=1
		adir=$(dirs +$index)
		[[ -z $adir ]] && return 1
		the_new_dir=$adir
	fi
	#############################################################################
	# '~' has to be substituted by ${HOME}
	#############################################################################
	[[ ${the_new_dir:0:1} == '~' ]] && the_new_dir="${HOME}${the_new_dir:1}"
	#############################################################################
	# Now change to the new dir and add to the top of the stack
	#############################################################################
	pushd "${the_new_dir}" > /dev/null
	[[ $? -ne 0 ]] && return 1
	the_new_dir=$(pwd)
	#############################################################################
	# Trim down everything beyond maxitems + 1 entry and save in $histdirfile
	#############################################################################
	popd -n +$((maxitems + 1)) 2>/dev/null 1>/dev/null
	#echo "popd -n +$((maxitems + 1)) 2>/dev/null 1>/dev/null"
	#############################################################################
	# Save to $histdirfile
	#############################################################################
	dirs -l -p|sort|uniq>$histdirfile
	#############################################################################
	# Remove any other occurence of this dir, skipping the top of the stack
	#############################################################################
	for ((cnt=1; cnt <= $maxitems; cnt++)); do
		x2=$(dirs +${cnt} 2>/dev/null)
		[[ $? -ne 0 ]] && return 0
		[[ ${x2:0:1} == '~' ]] && x2="${HOME}${x2:1}"
		if [[ "${x2}" == "${the_new_dir}" ]]; then
			popd -n +$cnt 2>/dev/null 1>/dev/null
			cnt=cnt-1
		fi
	done
	return 0
}
```

Fundamental incorporar este código al `.bashrc` y por último configurarlo como
alias: `alias cd=mycd`

Que cosas podemos hacer:

* `cd --`: Lista los últimos directorios visitados con un índice para acceder al mismo
* `cd -índice`: Podremos ir directamente a esa carpeta

El stack de directorios se salva, se ordena y se quitan los repetidos en un
archivo de sesión, por lo que cuando abramos de nuevo la sesión se restaura la
última lista. Podemos configurar editando la variable `maxitems`, la cantidad de
carpetas a salvar.

Actualización del 18/09/2014: Nueva funcionalidad

* `cd : `: Ídem `cd --`
* `cd : <searchstring>`: Lista los últimos directorios que contengan `<searchstring>`
