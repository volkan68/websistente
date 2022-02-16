---
id: 100
title: 'Single Page Application (SPA) una tendencia creciente'
date: '2013-10-29T09:22:45-04:00'
author: volkan68
layout: post
guid: 'http://websistente.com/blog/?p=100'
permalink: /single-page-application-spa-una-tendencia-creciente/
categories:
    - CloudComputing
    - Desarrollo
---

 <span style="font-size:16px;">Singla Page Application (SPA) o Single Page Interface (SPI) es una aplicación web (web app) o sitio web que sólo carga la página web una sóla vez y desde alli renderiza los objetos que contituyen la página basado en las acciones del usuario, es decir el único flujo entre el servidor y la página web son datos. La llegada de tecnologías que permiten enviar datos directamente al navegador como [AJAX](http://es.wikipedia.org/wiki/AJAX) hicieron posible el nacimiento de las SPA, pero ha sido la llegada de [node.js](http://nodejs.org/) el que ha llevado las SPA a otro nivel.</span>

 <span style="font-size:16px;">Pero la creación de SPA con JavaScript en el servidor usando node.js requiere de algún framework que nos permite mantener nuestro código ordenado y facilite su mantenimiento, de preferencia que siga el paradigma [MVC](http://es.wikipedia.org/wiki/Modelo_Vista_Controlador) (Model View Controller) que es tan popular en otros framworks como Rails o Django. En este post daremos un rápido vistazo a todas nuestras opciones de MVC para desarrollo de SPA en node.js.</span>

 <span style="font-size:16px;"></span>

 <span style="font-size:16px;">**[Ember](http://emberjs.com/)**<span style="line-height: 1.6em;">, </span><span style="line-height: 1.6em;">es una librería JavaScript MVC del lado del cliente que ha estado en desarrollo desde hace bastante tiempo. </span><span style="line-height: 1.6em;">En pocas palabras, Ember ayuda a crear aplicaciones web en las que gran parte de la lógica se ejecuta en el navegador y no requiere necesariamente peticiones al servidor para funcionar. Cuando el usuario interactúa con objetos como botones y cuadros de texto en la página del navegador, Ember actualiza el DOM directamente y (casi) instantánea, para mostrar los cambios directamente al usuario sin actualizar la página. Si se requiere algún dato del servidor las solicitudes pueden ser hechas de forma asíncrona en segundo plano. Ember toma el control del lado de "aplicación" (o front end), dejando el servidor para que actúe como un motor de API que sirve y guardar los datos </span><span style="line-height: 1.6em;">(es aquí donde node.js entra en juego).</span></span>

 <span style="font-size:16px;">**[Backbone.js](http://documentcloud.github.io/backbone/)**, es otra librería JavaScript MVC del lado del cliente que facilita la organización del código. Una de las principales ventajas de Backbone es que posee una gran comunidad de usuarios (en parte por ser uno de los primeros framworks MVC para JavaScript) y posee una fácil integración con MongoDB la base de datos NoSQL más popular entre las Starups que desarrollan sobre node.js.</span>

 <span style="font-size:16px;">[**Knockout.js**](http://knockoutjs.com/), es una librería JavaScript del lado del cliente que se basa en el modelo [MVVM](http://en.wikipedia.org/wiki/Model_View_ViewModel) que te ayuda a crear interfaces de usuario enriquecidas y ser altamente interactivo con el usuario, con un modelo de datos subyacente limpio. Una de las ventajas que ofrece Knockout.js es una librería llamada [Knockback.js](http://kmalakoff.github.com/knockback/) que nos permite integrarlo con Backbone.js y tener ambos paradigmas en caso de ser necesario MVVM provisto por Knockout.js y MVC provisto por Backbone.js.</span>

 <span style="font-size:16px;">[**Spine.js**](http://kmalakoff.github.com/knockback/), es una librería JavaScript del lado del cliente basada en el modelo MVC y trata de ser una versión liviana de Backbone.js, para lograr ello no posee ningun sistema de plantillas o widgets incluído y nosotros podemos crear el nuestro o usar otro como [Angular.js](http://angularjs.org/), [Mustache](http://mustache.github.com/) o [Google Closure](https://developers.google.com/closure/library/).</span>

 <span style="font-size:16px;">[**SproutCore**](http://sproutcore.com/), es una libería JavaScript del lado del cliente basada en el modelo MVC que pretende ser a la vez veloz y fácil de usar por parte del usuario. Cuenta con una excelente documentación, aunque su comunidad de usuarios no está entre las más grandes, la calidad de la documentación es uno de sus puntos fuertes.</span>

 <span style="font-size:16px;">Estos no son todos los frameworks MVC que existen para JavaScript, sin embargo he tratado de listar aquellos que son open source, basados 100% en JavaScript y son un proyecto dinámico que aún están en activo desarrollo.</span>