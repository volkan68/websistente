---
id: 266
title: 'Docker y la revolución de los containers'
date: '2014-10-10T12:29:09-04:00'
author: volkan68
layout: post
guid: 'http://websistente.com/blog/?p=266'
permalink: /docker-y-la-revolucion-de-los-containers/
categories:
    - CloudComputing
    - DevOp
    - Virtualización
---

 Docker es una plataforma open source para desarrolladores y sysadmin que permite crear, distriubuir y correr aplicaciones de forma consistente independiente de la configuración del OS y el harware que lo contenga. El proyecto Docker consiste de dos partes "**Docker Engine**" que es un runtime ligero y portable (lo que instalamos en nuestro servidor) con un conjunto de herramientas que nos permiten crear, iniciar y detener los contenedores. La otra parte del proyecto es "[Docker Hub](https://hub.docker.com/account/signup/)", que es un repositorio en la nube a través del cual podemos compartir los contenedores que hemos creado, si es que así lo queremos. Docker se ha planteado como objetivo eliminar la fricción entre el proceso de desarrollo, QA ([Quality Assurance](http://es.wikipedia.org/wiki/Aseguramiento_de_la_calidad)) y producción a través del uso de contenedores que son una envoltura para nuestra aplicación y todas las librerias que esta necesite. Cómo resultado de esta contenerización, el departamento TIC puede desplegar la aplicación sin ningun cambio ya sea en una latop, en un servidor físico, en un servidor virtual o en la nube, siempre y cuando tenga instalado el **Docker Engine**.

 <span style="font-size:16px;">**¿Por qué los desarrolladores deberían usarlo?**</span>

 Con Docker un desarrollador podría usar cualquier lenguaje de programación, con cualquier framework que prefiera para producir una aplicación y luego de que "Dockerize" su aplicación esta es completamente independiente y esta podría correr en cualquier lugar, el laptop de un colega con OS X o Windows, el servidor Ubuntu en la nube usado por el departamente de QA para probar la aplicación o el servidor físico con RedHat usado para producción.

 Los desarrolladores pueden rápidamente inciar un nuevo proyecto con las más de 13000 aplicaciones disponibles en el Docker Hub. Docker administra y registra todos los cambios y dependencias, haciendo más fácil a los sysadmin entender como la aplicación funciona y fue desarrollada.

 <span style="font-size:16px;">**¿Por qué los sysadmin deberían usarlo?**</span>

 Los sysadmin deberían usarlo porque Docker provee un entorno de desarrollo estandarizado que ayuda a reducir los problemas de "Pero si funciona en mi máquina" y el juego del gran bonetón entre los departamentos de desarrollo, QA y producción. Al producir una aplicación "dockerizada", la plataforma y las dependencias son abstraidas de la máquina física donde se efectuo ya sea el proceso de desarrollo, QA o proudcción y por lo tanto pueden ser usadas universalmente.

 Adicionalmente la estandarización con el Docker Engine como unidad para el proceso de implementación le da a los sysadmin flexibilidad en la distribución de la carga. No importa donde la aplicación será ejecutada, dentro de la empresa o en un data center sobre una máquina física, o como una máquina virtual en la nube, el balanceo de la carga está menos restringido por las limitaciones de la infraestructura física que corre la aplicación y por el contrario la asignación de la carga está más al servicio de las políticas del negocio. Aún más lo ligero del runtime del Docker Engine garantiza un rápido proceso de escalabilidad ya sea hacia arriba o abajo en función de la demanda.

 Docker ayuda a los sysadmin a desplegar y correr una aplicación en cualquier tipo de insfraestructura de manera rápida y confiable.

 <span style="font-size:16px;">**¿Cuál es la diferencia entre un contenedor y una máquina virtual?**</span>

 Una máquina virtual (VM) requiere la instalación de un sistema operativo, con todas las librerías y dependencias de nuestra aplicación. Pero además el rendimiento es penalizado con la existencia del hypervisor que se encarga de distribuir el uso de los recursos físicos entre las máquinas virtuales hosteadas en el servidor físico. Podemos ver nuestra aplicación dentro de una máquina virtual más o menos de esta manera:

<center>| \| Aplicación A \| Aplicación B \| \|---\|---\| \| Binarios/Librerías \| Binarios/Librerías \| \| Sistema Operativo Virtualizado \| Sistema Operativo Virtualizado \| |
|---|
| Hypervisor |
| Sistema Operativo Anfitrion |
| Servidor Físico |

</center> En el caso de un Docker Engine, sólo es necesario el servidor físico y el sistema operativo anfitrion. Sobre este stack es que nuestras apliaciones se ejecutarán, razón por la cual el uso de recursos es mucho más eficiente. Veamoslo en el siguiente cuadro:

<center>| \| Aplicación A \| Aplicación B \| \|---\|---\| \| Binarios/Librerias \| Binarios/Librerias \| |
|---|
| Docker Engine |
| Sistema Operativo Anfitrion |
| Servidor Físico |

</center> Aquí les dejo un video del último Google I/O 2014, en donde explican como se puede utilizar Docker dentro de la red de Google, este video está en inglés pero explica muy claramente cómo usar Docker:

 <iframe allowfullscreen="" frameborder="0" height="360" loading="lazy" src="//www.youtube.com/embed/tsk0pWf4ipw" width="640"></iframe>