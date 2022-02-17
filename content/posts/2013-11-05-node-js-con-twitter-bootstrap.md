---
id: 161
title: 'node.js con Twitter Bootstrap'
date: '2013-11-05T11:35:49-05:00'
author: volkan68
layout: post
guid: 'http://websistente.com/blog/?p=161'
permalink: /node-js-con-twitter-bootstrap/
categories:
    - Desarrollo
---

 No hay duda que uno de los frameworks/templates para diseño de websites HTML5/CSS3 más usados en la actualidad es [Bootstrap](http://getbootstrap.com/) (un proyecto Open Source inciado por Twitter), una de las razones de su popularidad es que este template por defecto nos ofrece un diseño [responsive](http://es.wikipedia.org/wiki/Dise%C3%B1o_web_adaptativo) y [mobile first](http://www.lukew.com/resources/mobile_first.asp), lo cual convierte a esta herramienta en ideal para el desarrollo de websites que se vean bien no importa si se accede a ellos desde un smartphone, una tablet o un PC. El framework por defecto de node.js es [Express](http://expressjs.com/) y aunque es flexible gracias a su sistema de plantillas [Jade](http://jade-lang.com/), muchas frontend prefieren el familiar Bootstrap, razón por la cual aquellos que deseen desarrollar un backend en node.js y un frontend en Bootstrap podrían encontrar útil este breve tutorial.

 Primero debemos estar seguros que estamos usando la última copia estable de node.js que en el momento de escribir este tutorial fue la 0.10.21, para saber la versión que tiene instalada use este comando:

```
<pre id="terminal">
$ node --version
v0.10.21
```

 Una de las razones de tener siempre actualizado nuestro node.js es por seguridad, para evitar ataques de denegación de servicio que han sido muy comúnes a lo largo del presente año.

 Cumplido el requisito anterior, instalemos pues Bootstrap en node.js con estos comando:

```
<pre id="terminal">
$ git clone https://github.com/takimo/twitter-bootstrap-node.git
$ npm install -g ./twitter-bootstrap-node
$ twitter-bootstrap prueba
$ cd prueba && npm install
$ node apps.js
```

 Ahora ya tenemos corriendo nuestra aplicación y si deseamos ver el resultado final sólo debemos apuntar nuestro navegador a la dirección http://127.0.0.1:3000 y obtendríamos algo similar a esto:

 [![Node+Express+Bootstrap](http://websistente.com/blog/wp-content/uploads/2013/11/Node+Express+Bootstrap-293x300.jpg)](http://websistente.com/blog/wp-content/uploads/2013/11/Node+Express+Bootstrap.jpg)

 Como siempre mencionamos este no es el proyecto final, sino sólo una prueba para tener esto en producción debemos de protegerlo usando un proxy inverso como Nginx o Apache y las apropiadas cuotas de memoria y CPU a nuestro node.js para evitar que corra desvocado.

 Si deseamos editar el template y contendido mostrado arriba sólo debemos editar el archivo <span style="font-family:courier new,courier,monospace;">**./views/index.jade**</span>. Un detalle a tener en cuenta es que este módulo de node.js sólo instala la parte de estilo y diseño de Bootstrap, los efectos como drop-down menus o animaciones deben ser implementados porque las librerías javascript no son instaladas.

 Espero que este pequeño tutorial les haya servido para tener un Express con tema de Bootstrap listo para comenzar a desarrollar.