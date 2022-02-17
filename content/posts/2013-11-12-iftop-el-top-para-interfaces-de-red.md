---
id: 217
title: 'iftop el «top» para interfaces de red'
date: '2013-11-12T09:26:26-05:00'
author: volkan68
layout: post
guid: 'http://websistente.com/blog/?p=217'
permalink: /iftop-el-top-para-interfaces-de-red/
categories:
    - DevOp
    - Networking
---

 [iftop](http://www.ex-parrot.com/pdw/iftop/) es una herramienta que hace con el tráfico de una interface de red, lo que "top" hace con el uso de un procesador. Esta herramienta escucha todo el tráfico de red a una interface determinada y nos muestra una tabla que resume el uso de ancho de banda usado por cada par de host o IPs connectadas (servidor/cliente), es muy útil para identificar si estamos recibiendo un ataque desde una IP en particular. En Debian y Ubuntu se instala simplemente haciendo un "apt-get install iftop", pero para Centos la historia es un poco diferente. Aquí les paso los pasos necesarios para tener iftop corriendo en un Centos 6.x.

 Primero debemos instalar el soporte para el EPEL ([Extra Package Enterprise Level](http://fedoraproject.org/wiki/EPEL/es)), si estamos usando un sistema de 64 bits debemos ejecutar este comando como root:

```
<pre id="terminal">
cd /tmp
wget http://mirror.umd.edu/fedora/epel/6/x86_64/epel-release-6-8.noarch.rpm
rpm -ivh epel-release-6-8.noarch.rpm
```

 Si estamos usando un sistema de 32 bits debemos usar este otro comando como root:

```
<pre id="terminal">
cd /tmp
wget http://mirror.umd.edu/fedora/epel/6/i386/epel-release-6-8.noarch.rpm
rpm -ivh epel-release-6-8.noarch.rpm
```

 Con el EPEL instalado ahora si ya podremos instalar iftop de la siguiente manera:

```
<pre if="terminal">
yum -y install iftop
```

 Una vez instalado iftop usarlo es tan sencillo como esto:

 iftop -i &lt;interface&gt;

 Por ejemplo para monitorear la interface "eth0", sólo debemos ejecutar este comando:

 iftop -i eth0.

 Si no deseamos resolución inversa de IP y deseamos ver las conexiones establecidas entre IP el comandos sería este:

 iftop -i eth0 -n

 Usualmente los resultados aparecen en este formato:

 [![iftop-demo](http://websistente.com/blog/wp-content/uploads/2013/11/iftop-demo-300x157.jpg)](http://websistente.com/blog/wp-content/uploads/2013/11/iftop-demo.jpg)

 Espero que este tutorial les haya sido de utilidad.