---
id: 92
title: '¿Es la web en tiempo real el futuro?'
date: '2013-10-28T01:24:09-04:00'
author: volkan68
layout: post
guid: 'http://websistente.com/blog/?p=92'
permalink: /es-la-web-en-tiempo-real-el-futuro/
categories:
    - Desarrollo
---

 <span style="font-size: 16px;">La web en tiempo real es algo que millones de personas ven todos los días en sus aplicaciones favoritas, pero que la mayoría no se da cuenta de dicha funcionalidad. Empresas como Facebook y Twitter usan los datos en tiempo real para actualizar los flujos de actividad sus usuario, lo que resulta en una experiencia que se siente menos estática y más como la mensajería instantánea.</span>

 <span style="font-size: 16px;">Este nuevo paradigma implica enviar datos a los usuarios en lugar de que sea sean las aplicaciones las que busquen actualizaciones periódicamente. Los datos en tiempo real se ven en web apps como chats, flujos de actividad, juegos multijugador, paneles de control, y las experiencias de segunda pantalla. Dos principales beneficios de la web en tiempo real son que se incrementa la participación de los usuarios y se reduce la carga del servidor.</span>

 <span style="font-size: 16px;"><span style="line-height: 1.6em;">Grandes empresas como Facebook, Twitter y Google han hecho de los datos en tiempo real la norma en las aplicaciones que usamos todos los días, pero ¿qué hay del resto de nosotros?. Varias startups están democratizando la tecnología de la web en tiempo real, por lo que el resto de nosotros puede ahora proporcionar experiencias en tiempo real en nuestras aplicaciones. Echemos un vistazo a algunas de estas herramientas.</span></span>

 <span style="font-size: 18px;">**MeteorJS**</span>

 <span style="font-size: 16px;">[MeteorJS](http://www.meteor.com/) es una plataforma de código abierto para construir aplicaciones web que combina plantillas en el lado del cliente con datos en tiempo real. Siempre que los datos en el servidor cambien, las actualizaciones son enviadas a la aplicación de forma automática. Diseñado para los desarrolladores de JavaScript, MeteorJS permite escribir código que se ejecutará en el cliente y el servidor. La compañía ha construido un conjunto de herramientas de línea de comandos para la creación de nuevos proyectos. Estas herramientas permiten la ejecución local durante el proceso de desarrollo y el despliegue a los servidores de MeteorJS cuando el proyecto está listo para ser presentado al mundo. MeteorJS también ofrece la opción de crear un paquete e implementar la solución de web en tiempo real en tus propios servidores.</span>

 <span style="font-size: 16px;">MeteorJS utiliza "paquetes inteligentes" para añadir funcionalidades a su aplicación. Estos van desde los componentes de interfaz de usuario hasta populares framworks CSS y JavaScript. La mayoría de las aplicaciones de MeteorJS utilizan MongoDB para el almacenamiento de datos y ya incluye un sistema de validación de cuentas de usuario para proteger los datos.</span>

 <span style="font-size: 16px;">Esta puesta en marcha tiene algunos grandes nombres que lo respaldan, pero es de código abierto y no está siquiera en su versión 1.0.</span>

 <span style="font-size: 16px;">**Firebase**</span>

 <span style="font-size: 16px;">[Firebase](https://www.firebase.com/) es una Startup que ofrece una funcionalidad similar a MeteorJS, además de un panel de control pulido que permite ver los datos y configurar la autenticación de los usuarios de tu aplicación. Oficialmente soportan algunas bibliotecas del lado del cliente y Firebase tiene un sesgo hacia el desarrollo de aplicaciones móviles para iOS y Android con JavaScript, además de muchos otros lenguajes soportados por la comunidad Firebase. Este énfasis en los desarrolladores móviles alinea Firebase con muchos backend como servicio para móviles ([mBaaS](http://en.wikipedia.org/wiki/Backend_as_a_service), por sus siglas en inglés "[**mobile Backend as a Service**](http://en.wikipedia.org/wiki/Backend_as_a_service)") jugadores como [StackMob](https://www.stackmob.com/), [Parse](https://www.parse.com/about/index), etc. Las empresas típicas en el sector mBaaS ofrece almacenamiento de datos, autenticación de usuario, envío de notificaciones y código de servidor personalizado. Firebase se diferencia al combinar la persistencia de datos con la entrega de datos en tiempo real.</span>

 <span style="font-size: 16px;">**PubNub**</span>

 <span style="font-size: 16px;">[PubNub](http://www.pubnub.com/) se centra exclusivamente en la entrega de datos en tiempo real. Esta Startup fue fundada en 2010 y proporciona una interfaz de programación de aplicaciones (o API) fácil de usar, para los desarrolladores que deseen crear aplicaciones para el envio y recepción de datos en tiempo real. PubNub puede escalar a millones de mensajes por segundo. Es compatible con más de 50 SDKs incluyendo populares ambientes de desarrollo como iOS , Java , Ruby , PHP , . NET , y JavaScript . Su enfoque en sólo los datos en tiempo real significa que no hay soporte para el almacenamiento de datos o la autenticación de uso que se encuentra presente en MeteorJS, Firebase o en las empresas mBaaS.</span>

 <span style="font-size: 16px;">**¿Qué está sucediendo con las mBaaS?**</span>

 <span style="font-size: 16px;">Las plataformas de backend- as-a -service para móviles, realmente se han apoderado de los desarrolladores móviles y siguen creciendo en popularidad. Luego de que [Facebook comprara Parse el pasado mes de abril](http://techcrunch.com/2013/04/25/facebook-parse/) y la liberación de [Microsoft de Azure Mobile Services](http://msdn.microsoft.com/en-us/library/windowsazure/jj554228.aspx) demuestran las grandes apuestas que tanto Facebook y Microsoft están haciendo en mBaaS .</span>

 <span style="font-size: 16px;">mBaaS está diseñado para ayudar a los desarrolladores con el almacenamiento de datos, la gestión de los usuarios, la integración con redes sociales y el envio de notificaciones. Pero ¿qué pasa con los datos en tiempo real? A pesar de que encaja perfectamente dentro de la misión de las empresas mBaaS, la gran mayoría no son compatibles con esta función.</span>

 <span style="font-size:16px;">A principios de este mes de Octubre, Google lanzó su [Mobile Backend Starter para AppEngine](https://developers.google.com/cloud/samples/mbs/)</span>,<span style="font-size: 16px;"> que incluye [mensajería pub/sub](http://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern) que es necesaria para la funcionalidad de datos en tiempo real. Esta evolución pone a Google firmemente en el juego de los mBaaS .</span>

 <span style="font-size: 16px;">Tendremos que esperar y ver cómo otras empresas mBaaS reaccionan a estos cambios y cómo se da forma al futuro de la industria .</span>

 <span style="font-size: 16px;">Los desarrolladores tienen muchas opciones cuando se trata de los datos en tiempo real.</span>