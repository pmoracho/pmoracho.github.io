---
layout: post
published: true
date: 2017-01-13
imagefeature: sitelogo.png
show_meta: true
comments: true
mathjax: false
gistembed: false
noindex: false
hide_printmsg: false
sitemap: true
summaryfeed: false
title: Mis "Tips" de Git
tags:
  - desarrollo
  - github
---

## Configurar la seguridad

[Fuente](http://stackoverflow.com/questions/8588768/git-push-username-password-how-to-avoid)

## 1. Generate an SSH key

### Linux/Mac

``` bash
cd ~                 #Your home directory
ssh-keygen -t rsa    #Press enter for all values
```

### For Windows

(Only works if the commit program is capable of using certificates/private & public ssh keys)

Use Putty Gen to generate a key
Export the key as an open SSH key
Here is a [walkthrough](http://ask-leo.com/how_do_i_create_and_use_public_keys_with_ssh.html) on putty gen for the above steps

## 2. Associate the SSH key with the remote repository

This step varies, depending on how your remote is set up.

If it is a GitHub repository and you have administrative privileges, go to settings and click 'add SSH key'. Copy the contents of your ~/.ssh/id_rsa.pub into the field labeled 'Key'.
If your repository is administered by somebody else, give the administrator your id_rsa.pub.

## 3. Set your remote URL to a form that supports SSH 1

If you have done the steps above and are still getting the password prompt, make sure your repo URL is in the form

git+ssh://git@github.com/username/reponame.git

as opposed to

https://github.com/username/reponame.git

To see your repo URL, run:


``` bash
git remote show origin
```

You can change the URL with:

``` bash
git remote set-url origin git@github.com/username/reponame.git
```

## 4. Configuración básica de un repositorio en [github.com](github.com)

1. Crear repositorio nuevo en [github.com](github.com)
2. Usar `https:`, seguramente es mejor `ssh:` pero me resulto más simple y cómodo por ahora el primero
3. Clonar el directorio remoto, por ejemplo: `git clone https://github.com/pmoracho/code.snippets.git`
4. Configurar datos básicos por repositorio:
	```
	git config user.email "zandanga@gmail.com"
	git config user.name "Patricio Moracho"
	```
5. Configurar la url del remoto
	```	
	git remote add origin https://github.com/pmoracho/code.snippets.git
	git push --set-upstream origin master
	```	
6. Configurar el guardado temporal de las credenciales
	```
	git config --global credential.helper cache
	git config --global credential.helper "cache --timeout=3600"
	```

## Recursos escenciales

* [Pro Git, el libro oficial de Git](https://git-scm.com/book/es/v2)
* [Pro Git, el libro oficial de Git (esta traducción me gusta un poco más)](http://librosweb.es/libro/pro_git/)
