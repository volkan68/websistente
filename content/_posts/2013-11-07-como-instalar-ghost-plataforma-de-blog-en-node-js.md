---
id: 178
title: 'Cómo instalar Ghost (plataforma de blog en node.js)'
date: '2013-11-07T09:59:42-05:00'
author: volkan68
layout: post
guid: 'http://websistente.com/blog/?p=178'
permalink: /como-instalar-ghost-plataforma-de-blog-en-node-js/
categories:
    - Desarrollo
    - Networking
---

 Aunque WordPress es la plataforma de blog dominante en nuestros días, John O'Nolan inició un proyecto kickstarted para crear [un sistema de blogging en node.js](http://www.kickstarter.com/projects/johnonolan/ghost-just-a-blogging-platform). Ghost se puso como meta recaudar £ 25,000 (U.S.$ 40,000) para financiar el desarrollo del proyecto y en 29 días recibió £ 196,362 (U.S.$ 314,000), debido a esta gran acogida es que decidió extender el proyecto y no sólo desarrollar un software para blogear escrito en node.js, sino que está construyendo el servicio para ofrecerlo de la misma manera a como lo hace [WordPress.com](http://wordpress.com/). El servicio aún no está disponible, pero si se desea usar el software que es Open Source en un servidor propio, sólo debemos registrarnos en [Ghost.org](https://ghost.org/) para poder descargarlo.

 Este tutorial detalla los pasos de cómo tener Ghost corriendo en nuestro VPS, este tutorial asume que Ud. usa Ubuntu 12.04 (o más nuevo), node.js 0.10.21, MySQL y Nginx. De no estar familiarizado con alguno de estos componentes, no se preocupe aquí explicaremos detalladamente como instalarlos y configurarlos.

 <span style="line-height: 1.6em;">Lo primero que le recomiendo es que si usará Ghost en producción y dada la naturaleza aún en beta de node.js siga los pasos descritos en el tutorial anterior "</span>[Asegurando tu server Ubuntu](http://websistente.com/blog/2013/11/asegurando-tu-server-ubuntu/)<span style="line-height: 1.6em;">" antes de continuar con este tutorial, si lo único que desea es probarlo puede continuar.</span>

 <span style="line-height: 1.6em;">Comencemos por instalar node.js (la versión estable) usando el repositorio semi oficial de [Chris Lea](https://launchpad.net/~chris-lea/+archive/node.js/), ejecutando estos comandos como usuario root:</span>

```
<pre id="terminal">
apt-get install python-software-properties
apt-add-repository ppa:chris-lea/node.js
apt-get update
apt-get install nodejs
node --version
v0.10.21
```

 Completada la instalación de node.js (al momento de escribir este tutorial la versión estable de node.js era la 0.10.21), podemos ahora proceder a la instalación de MySQL de la siguiente manera:

```
<pre id="terminal">
apt-get install mysql-client mysql-server
```

 Durante el proceso de intalación será interrogado sobre una clave por defecto para el usuario "root", por favor use un password que no sea fácil de adivinar y tampoco lo deje en blanco.

 Una vez instalado MySQL debemos crear el usuario (jamas use "root" por razones de seguridad) y las bases de datos que usaremos para implementar nuestro blog. Esto lo consigueremos logueandonos a la DB usando el cliente "mysql" y tipeando los comandos listados. Para loguearse a la DB debe usar este comando:

```
<pre id="terminal">
mysql -u root -p
```

 Una vez logueado exitosamente debería ver algo como esto, donde (<span style="font-family:courier new,courier,monospace;">**mysql&gt;**</span>) es el prompt por defecto usado por el cliente para aceptar comandos:

```
<pre id="terminal">
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 42
Server version: 5.5.34-0ubuntu0.12.04.1 (Ubuntu)

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

 Los comandos que debe ejecutar son estos, pero edite el texto en mayúsculas "<span style="font-family:courier new,courier,monospace;">**TU\_PASSWORD**</span>" y reemplazalo por el password que deseas asignarle al usuario "**<span style="font-family:courier new,courier,monospace;">ghost</span>**":

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;">**create database ghostdev;  
 create database ghost;  
 create user 'ghost'@'localhost' identified by 'TU\_PASSWORD';  
 grant all privileges on ghost.\* to 'ghost'@'localhost';  
 grant all privileges on ghostdev.\* to 'ghost'@'localhost';  
 flush privileges;  
 quit**</span></span>

 Con esto hemos completado nuestra instalación y configuración de MySQL procederemos a instalar Nginx usando el siguiente comando:

```
<pre id="terminal">
apt-get install nginx
```

 Creemos ahora un directorio de cacheo, para ello ejecutaremos los siguientes comandos:

```
<pre id="terminal">
mkdir /var/cache/nginx
chown www-data:www-data /var/cache/nginx
```

 Seguidamente cremos el directorio para Ghost (si el directorio existe es porque Ud. ha estado usando Apache, asegurese de que lo ha desinstalado antes de comenzar a usar Nginx):

```
<pre id="terminal">
mkdir /var/www
chown www-data:www-data /var/www
```

 Hagamos una copia de respaldo de la configuración inicial de Nginx de la siguiente manera:/etc/nginx/nginx.conf

```
<pre id="terminal">
mv /etc/nginx/nginx.conf /etc/nginx/orig_nginx.conf
```

 Ahora creemos una nueva configuración creando el archivo "/etc/nginx/nginx.conf" y colocando esto dentro del mismo:

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;">**user www-data;  
 worker\_processes 2;  
 pid /run/nginx.pid;**</span></span>

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;">**events {  
 worker\_connections 768;  
 # multi\_accept on;  
 }**</span></span>

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;">**http {**</span></span>

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> **proxy\_cache\_path /var/cache/nginx levels=1:2 keys\_zone=one:8m max\_size=3000m inactive=600m;  
 proxy\_temp\_path /var/tmp;  
 include mime.types;  
 default\_type application/octet-stream;  
 sendfile on;  
 keepalive\_timeout 65;**</span></span>

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> **gzip on;  
 gzip\_comp\_level 6;  
 gzip\_vary on;  
 gzip\_min\_length 1000;  
 gzip\_proxied any;  
 gzip\_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;**</span></span>

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> **gzip\_buffers 16 8k;**</span></span>

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> **upstream ghost\_upstream {  
 server 127.0.0.1:2368;  
 keepalive 64;  
 }**</span></span>

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> **server {  
 listen 80;**</span></span>

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> **server\_name YOUR\_DOMAIN www.YOUR\_DOMAIN;**</span></span>

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> **if ($host = 'YOUR\_DOMAIN' ) {  
 rewrite ^/(.\*)$ http://www.YOUR\_DOMAIN/$1 permanent;  
 }**</span></span>

  **location ~ ^/(img/|css/|lib/|vendor/|fonts/|robots.txt|humans.txt) {**

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> **root /var/www/core/client/assets;  
 access\_log off;  
 expires max;  
 }**</span></span>

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> **location ~ ^/(shared/|built/) {  
 root /var/www/core;  
 access\_log off;  
 expires max;  
 }**</span></span>

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> **location ~ ^/(favicon.ico) {  
 root /var/www/core/shared;  
 access\_log off;  
 expires max;  
 }**</span></span>

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> **location ~ ^/(content/images/) {  
 root /var/www;  
 access\_log off;  
 expires max;  
 }**</span></span>

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> **location / {  
 proxy\_redirect off;  
 proxy\_set\_header X-Real-IP $remote\_addr;  
 proxy\_set\_header X-Forwarded-For $proxy\_add\_x\_forwarded\_for;  
 proxy\_set\_header X-Forwarded-Proto $scheme;  
 proxy\_set\_header Host $http\_host;  
 proxy\_set\_header X-NginX-Proxy true;  
 proxy\_set\_header Connection "";  
 proxy\_http\_version 1.1;  
 proxy\_cache one;  
 proxy\_cache\_key ghost$request\_uri$scheme;  
 proxy\_pass http://ghost\_upstream;  
 }  
 }**</span></span>

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> **access\_log /var/log/nginx/access.log;  
 error\_log /var/log/nginx/error.log;**</span></span>

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> **include /etc/nginx/conf.d/\*.conf;  
 include /etc/nginx/sites-enabled/\*;  
 }**</span></span>

 En donde vea "<span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;">**YOUR\_DOMAIN.COM**</span></span>", reemplacelo por los valores del dominio o subdominio que usará para el blog. Estoy asumiendo además de que su VPS tiene 2 núcleos de tener más o menos edite el valor de "**worker\_processes**" y hágalo coincidir con el número de núcleos de su VPS. Para que la configuración anterior tenga efecto debemos reiniciar Nginx de la siguiente manera:

```
<pre id="terminal">
/etc/init.d/nginx restart
```

 Ahora ya tenemos todo listo para comenzar la instalación de Ghost en nuestro sistema, para ello lo primero que hay que hacer es desgarlo y eso lo hacemos de la siguiente manera:

```
<pre id="terminal">
cd /tpm
wget https://ghost.org/zip/ghost-0.3.3.zip
```

 Con el código descargado, descomprimamoslo y movamos todo el contenido del directorio "<span style="font-family:courier new,courier,monospace;">**/tmp/ghost**</span>" al directorio "<span style="font-family:courier new,courier,monospace;">**/var/www**</span>" de la siguiente manera:

```
<pre id="terminal">
mkdir ghost
cd ghost
unzip ../ghost-0.3.3.zip
cp -r ./* /var/www
chown -R www-data:www-data /var/www
```

 Completado los pasos anteriores instalemos las dependencias y todo lo necesario para poder arrancar nuestro blog, para ello debemos usar los siguientes comandos:

```
<pre id="terminal">
npm install --production
npm install mysql
npm install forever -g
```

 Ya estamos casi listos para iniciar nuestro blog, sólo debemos terminar la configuración editando el archivo /var/www/config.js, para ello creamos el archivo con nuestro editor favorito y pegamos esta configuración alli:

 <span style="color:#0000FF;">**<span style="font-family:courier new,courier,monospace;">// # Ghost Configuration  
 // Setup your Ghost install for various environments</span>**</span>

 <span style="color:#0000FF;">**<span style="font-family:courier new,courier,monospace;">var path = require('path'),  
 config;</span>**</span>

 <span style="color:#0000FF;">**<span style="font-family:courier new,courier,monospace;">config = {  
 // ### Development \*\*(default)\*\*  
 development: {  
 // The url to use when providing links to the site, E.g. in RSS and email.  
 url: 'http://</span>**</span><span style="color:#FF0000;">**<span style="font-family:courier new,courier,monospace;">TU\_DOMINIO.COM</span>**</span><span style="color:#0000FF;">**<span style="font-family:courier new,courier,monospace;">',</span>**</span>

 <span style="color:#0000FF;">**<span style="font-family:courier new,courier,monospace;"> // Example mail config  
 // Visit http://docs.ghost.org/mail for instructions  
 // «`  
 // mail: {  
 // transport: 'SMTP',  
 // options: {  
 // service: 'Mailgun',  
 // auth: {  
 // user: '', // mailgun username  
 // pass: '' // mailgun password  
 // }  
 // }  
 // },  
 // «`</span>**</span>

 <span style="color:#0000FF;">**<span style="font-family:courier new,courier,monospace;"> database: {  
 client: 'mysql',  
 connection: {  
 host: 'localhost',  
 user: 'ghost',  
 password: '</span>**</span><span style="color:#FF0000;">**<span style="font-family:courier new,courier,monospace;">TU\_PASSWORD</span>**</span><span style="color:#0000FF;">**<span style="font-family:courier new,courier,monospace;">',  
 database: 'ghostdev',  
 charset: 'utf8'  
 }  
 },  
 server: {  
 // Host to be passed to node's `net.Server#listen()`  
 host: '127.0.0.1',  
 // Port to be passed to node's `net.Server#listen()`, for iisnode set this to `process.env.PORT`  
 port: '2368'  
 }  
 },</span>**</span>

 <span style="color:#0000FF;">**<span style="font-family:courier new,courier,monospace;"> // ### Production  
 // When running Ghost in the wild, use the production environment  
 // Configure your URL and mail settings here  
 production: {  
 url: 'http://</span>**</span><span style="color:#FF0000;">**<span style="font-family:courier new,courier,monospace;">TU\_DOMINIO.COM</span>**</span><span style="color:#0000FF;">**<span style="font-family:courier new,courier,monospace;">',  
 mail: {},  
 database: {  
 client: 'mysql',  
 connection: {  
 host: 'localhost',  
 user: 'ghost',  
 password: '</span>**</span><span style="color:#FF0000;">**<span style="font-family:courier new,courier,monospace;">TU\_PASSWORD</span>**</span><span style="color:#0000FF;">**<span style="font-family:courier new,courier,monospace;">',  
 database: 'ghost',  
 charset: 'utf8'  
 }  
 },  
 server: {  
 // Host to be passed to node's `net.Server#listen()`  
 host: '127.0.0.1',  
 // Port to be passed to node's `net.Server#listen()`, for iisnode set this to `process.env.PORT`  
 port: '2368'  
 }  
 },</span>**</span>

 <span style="color:#0000FF;">**<span style="font-family:courier new,courier,monospace;"> // \*\*Developers only need to edit below here\*\*</span>**</span>

 <span style="color:#0000FF;">**<span style="font-family:courier new,courier,monospace;"> // ### Testing  
 // Used when developing Ghost to run tests and check the health of Ghost  
 // Uses a different port number  
 testing: {  
 url: 'http://127.0.0.1:2369',  
 database: {  
 client: 'sqlite3',  
 connection: {  
 filename: path.join(\_\_dirname, '/content/data/ghost-test.db')  
 }  
 },  
 server: {  
 host: '127.0.0.1',  
 port: '2369'  
 }  
 },</span>**</span>

 <span style="color:#0000FF;">**<span style="font-family:courier new,courier,monospace;"> // ### Travis  
 // Automated testing run through Github  
 travis: {  
 url: 'http://127.0.0.1:2368',  
 database: {  
 client: 'sqlite3',  
 connection: {  
 filename: path.join(\_\_dirname, '/content/data/ghost-travis.db')  
 }  
 },  
 server: {  
 host: '127.0.0.1',  
 port: '2368'  
 }  
 }  
 };</span>**</span>

 <span style="color:#0000FF;">**<span style="font-family:courier new,courier,monospace;">// Export config  
 module.exports = config;</span>**</span>

 <span style="line-height: 1.6em;">Luego de grabada la configuración, ya podemos inciar nuestro blog. Asegurese de editar los textos "TU\_DOMINIO.COM" y "TU\_PASSWORD", para facilitar que encuentres donde debes editar lo he marcado con el color rojo. Los valores que debes reemplazar son aquellos que hayas establecido anteriormente cuando configuraste Nginx y MySQL.</span>

 <span style="line-height: 1.6em;">Ahora sólo debemos inciar nuestro blog con el siguiente comando:</span>

 <span style="font-family:courier new,courier,monospace;">**<span style="color: rgb(66, 71, 74); font-size: 15px; line-height: 21px;">NODE\_ENV=production forever start –sourceDir /var/www index.js</span>**</span>

 <span style="line-height: 1.6em;">Listo, el resultado final puede ser observado en "[**http://ghost.cix.pe**](http://ghost.cix.pe)".</span>

 Una vez configurado todo esto estamos listos para crear nuestra cuenta de administración y podemos seguir el [turorial oficial](http://docs.ghost.org/usage/) sobre como usar el blog.