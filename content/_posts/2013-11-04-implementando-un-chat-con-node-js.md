---
id: 152
title: 'Implementando un chat con node.js'
date: '2013-11-04T11:56:29-05:00'
author: volkan68
layout: post
guid: 'http://websistente.com/blog/?p=152'
permalink: /implementando-un-chat-con-node-js/
categories:
    - CloudComputing
    - Desarrollo
---

 Una de las ventajas que ofrece node.js es su naturaleza asíncrona y por lo tanto permite escalar fácilmente sin preocuparnos por sincrónizar procesos entre sí ya que no existen procesos una aplicación node.js consiste de un único hilo de ejecución. Una de las aplicaciones que más se beneficia de esta característica es justamente un chat y ha sido uno de los selling points favoritos de los evangelistas de node.js. Pero qué tan sincillo es programar un chat en node.js, pues bien como dice [Eric Raymond](http://es.wikipedia.org/wiki/Eric_S._Raymond) en [La Catedral y el Bazar](http://www.openbiz.com.ar/La%20Catedral%20y%20El%20Bazar.pdf): "*Los buenos programadores saben qué código escribir. Los grandes programadores saben qué código reescribir*". Así que para que empezar desde cero, he encontrado un muy buen esqueleto con que comenzar una aplicación de chat en [Github](https://github.com/wmora/nodejs-express-socketio-chatroom), escrita por desarrollador llamado [William Mora](https://plus.google.com/+WilliamMora/posts).

 <span style="line-height: 1.6em;">Como es obvio vamos a necesitar un servidor donde desplegar nuestra aplicación, existen varias opciones desde las gratuitas como </span>[Heroku](https://www.heroku.com/)<span style="line-height: 1.6em;">, hasta las pagadas y super escalables como </span>[Amazon EC2](http://aws.amazon.com/ec2/)<span style="line-height: 1.6em;">, para nuestro caso usaré una instancia mini del proveedor donde trabajo </span>[OpenVirtuals.com](http://www.openvirtuals.com/)<span style="line-height: 1.6em;">. Los 256 MB que ofrece y los dos núcleos que ofrece esta opción son más que suficientes para atender decenas de usuarios concurrentes.</span>

 Este es un breve tutorial que explica como tener nuestro chat corriendo y se ofrecen las pautas generales de como modificar el look del chat. Este no es un proyecto final, no se cubren temas sobre como preparar node.js para escalar o protegerlo contra ataques.

 Primero comencemos por descargar nuestro chat y tenerlo corriendo, es algo que lograremos con los siguentes comandos:

```
<pre id="terminal">
git clone https://github.com/wmora/nodejs-express-socketio-chatroom.git

cd nodejs-express-socketio-chatroom

npm install

npm -g install forever
```

 Aunque el módulo [Forever](https://github.com/nodejitsu/forever) no es técnicamente necesario para la ejecución de nuestro chat, es importante instalarlo si tenemos pensado dejar corriendo nuestro chat incluso si nos deslogueamos de nuestra consola.

 Ahora ya estamos listos para arrancar nuestro chat con "Forever" usando este comando:

```
<pre id="terminal">
forever start server.js
```

 ¿Cómo sabemos que nuestro chat está corriendo?, bueno la forma más sencilla es abriendo nuestro navegador y apuntando al URL de nuestro chat, pero la otra opción es usar Forever para ver el status de los procesos que está corriendo:

```
<pre id="terminal">
forever list
info:    Forever processes running
data:        uid  command             script    forever pid   logfile                      uptime       
data:    [0] oFfy /usr/local/bin/node server.js 31025   31027 /home/chat/.forever/oFfy.log 0:0:12:8.220 
```

 Si deseamos detener nuestro chat, sólo debemos usar este comando:

```
<pre id="terminal">
	forever stop server.js
```

 Ahora toca hacer público nuestro chat, para ello usaremos **Nginx** como proxy inverso para nuestro proyecto. Igualmente necesitamos asignarle un nombre para que sea fácilmente accesible, en particular a esta prueba le he puesto el nombre "chat.cix.pe" o alternativamente "www.chat.cix.pe". Para lo primero siguiendo los pasos descritos en un [post anterior](http://websistente.com/blog/2013/10/como-usar-node-js-en-produccion/) debemos editar el archivo "<span style="font-family:courier new,courier,monospace;">**/etc/nginx/sites-available/default**</span>" y poner esto dentro del mismo:

 **<span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;">upstream chat {  
 # Point to our internal chat port  
 server 127.0.0.1:8080 max\_fails=0 fail\_timeout=10s weight=1;</span></span>**

 **<span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> # Send visitors back to the same server each time.  
 ip\_hash;</span></span>**

 **<span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> # Enable number of keep-alive connections.  
 keepalive 512;  
 }</span></span>**

 **<span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;">server {  
 listen 80; ## listen for ipv4; this line is default and implied  
 #listen \[::\]:80 default ipv6only=on; ## listen for ipv6</span></span>**

 **<span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> root /usr/share/nginx/www;  
 index index.html index.htm;</span></span>**

 **<span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> </span></span><span style="color:#FF0000;"><span style="font-family:courier new,courier,monospace;">server\_name chat.cix.pe www.chat.cix.pe;</span></span>**

 **<span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> # Timeout for closing keep-alive connections.  
 keepalive\_timeout 10;</span></span>**

 **<span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> # Enable gzip compression.  
 gzip on;  
 gzip\_http\_version 1.1;  
 gzip\_vary on;  
 gzip\_comp\_level 6;  
 gzip\_proxied any;  
 gzip\_buffers 16 8k;  
 gzip\_disable "MSIE \[1-6\]\\.(?!.\*SV1)";</span></span>**

 **<span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;"> location / {  
 # Set this to your upstream module.  
 proxy\_pass http://chat;  
 # Proxy headers.  
 proxy\_set\_header X-Real-IP $remote\_addr;  
 proxy\_set\_header X-Forwarded-Proto $scheme;  
 proxy\_set\_header X-Forwarded-For $proxy\_add\_x\_forwarded\_for;  
 proxy\_set\_header Host $host;  
 proxy\_set\_header X-NginX-Proxy true;  
 proxy\_set\_header Upgrade $http\_upgrade;  
 proxy\_set\_header Connection "upgrade";  
 proxy\_cache\_bypass $http\_upgrade;  
 proxy\_http\_version 1.1;  
 proxy\_redirect off;  
 # Go to next upstream after if server down.  
 proxy\_next\_upstream error timeout http\_500 http\_502 http\_503 http\_504;  
 proxy\_connect\_timeout 5s;  
 # Gateway timeout.  
 proxy\_read\_timeout 20s;  
 proxy\_send\_timeout 20s;  
 # Buffer settings.  
 proxy\_buffers 8 32k;  
 proxy\_buffer\_size 64k;  
 }</span></span>**

 <span style="color:#0000FF;">**<span style="font-family:courier new,courier,monospace;"> location /socket.io {  
 proxy\_pass http://chat;  
 }</span>**</span>

 **<span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;">}</span></span>**

 <span style="line-height: 1.6em;">Una vez editado el archivo podemos reiniciar nuestro Nginx con el siguiente comando y estamos listos para chatear.</span>

 Cómo he explicado esto es sólo una demostración y no pretende ser un proyecto 100% terminado aún hay varias cosas que optimizar y asegurar aquí, pero espero que le sirva al lector como una guía práctica de cómo tener un chat escrito en node.js corriendo en un VPS con limitados recursos. Por lo pronto los espero en el chat [chat.cix.pe](http://chat.cix.pe/).