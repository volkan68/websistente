---
id: 199
title: 'node-twitter un clone de twitter en node.js'
date: '2013-11-08T12:22:37-05:00'
author: volkan68
layout: post
guid: 'http://websistente.com/blog/?p=199'
permalink: /node-twitter-un-clone-de-twitter-en-node-js/
categories:
    - CloudComputing
    - Desarrollo
---

 Revisando los repositorios de Github por proyectos interesantes escritos en node.js, he encontrado uno llamado node-twitter de [Vinit Kumar](http://vinitkumar.me/). El código se ve limpio y sólo requiere la intalación de MongoDB como motor de base de datos, que afortunadamente ya hemos descrito en este blog como instalarlo localmente. Una version operativa del proyecto se puede visitar en este URL: <http://ntwitter.nodejitsu.com/login>. En este post voy a describir brevemente como hacer para tener una versión similar corriendo en tu VPS o servidor físico si es que deseas iniciar un proyecto similar.

 Asumo en este tutorial que empiezas con una instalación fresca de Ubuntu 12.04, entonces lo primero que haremos será instalar todas las dependencias que necesitamos de la siguiente manera (recuerde ejecutar todos estos comandos como root):

```
<pre id="terminal">
# Actualizando repositorios
apt-get update
apt-get -y upgrade

# Instalando node.js
apt-get -y install python-software-properties
apt-add-repository -y ppa:chris-lea/node.js
apt-get update
apt-get -y install nodejs

# Instalando mongo.js
apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10
echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' | sudo tee /etc/apt/sources.list.d/mongodb.list
apt-get update
apt-get install mongodb-10gen

# Inicia el servicio de MongoDB
service mongodb start

# Instala un entorno de desarrollo y utilitarios
apt-get -y install build-essential git zip unzip nano
```

 Una vez completada la instalación de los prerquisitos, estamos listos para instalar nuestro clone de twitter de la siguiente manera:

```
<pre id="terminal">
cd /var/www
git clone https://github.com/volkan68/node-twitter.git
cd node-twitter
npm install
npm -g install forever
```

 Con el código instalado para iniciar la ejecución sólo necesitamos crear una configuración inicial que la haremos copiando una configuración de prueba ya existente y luego lanzando la aplicación usando forever de la siguiente manera:

```
<pre id="terminal">
cp ./config/config.local.js ./config/config.js

PORT=80 forever start server.js
```

 Esto iniciará el proyecto en el puerto 80 donde podremos verlo accesando el IP de nuestra VPS a través de nuestro navegador, pero no es la forma de tenerlo en producción. Para ello recomiendo seguir las instrucciones descritas en el post "[Asegurando tu server Ubuntu](http://websistente.com/blog/2013/11/asegurando-tu-server-ubuntu/)" y usar un nginx como proxy inverso para proteger nuestro node.js de ataques de denegación de servicio. Además no se olviden editar el archivo "./config/config.js" con sus propios IDs y Keys de Facebook &amp; Github.

 Si deseas ver la versión que he instalado localmente en uno de los VPS mini provistos por [OpenVirtuals.com](https://www.openvirtuals.com/), puedes ir a este dominio: [http://www.ntwitter.cix.pe](http://www.ntwitter.cix.pe/)

 Espero que este breve tutorial les haya sido útil y quedo a la espera de sus comentarios.