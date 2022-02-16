---
id: 106
title: 'Cómo usar node.js en producción'
date: '2013-10-30T11:19:20-04:00'
author: volkan68
layout: post
guid: 'http://websistente.com/blog/?p=106'
permalink: /como-usar-node-js-en-produccion/
categories:
    - Desarrollo
    - DevOp
---

 [node.js](http://nodejs.org/) es una de las muchas herramienta que trata de dar una solución al clásico problema [C10K](http://en.wikipedia.org/wiki/C10k_problem) que aqueja al popular servidor web Apache. Para ello node.js ofrece un modelo de eventos asíncronos, de un sólo hijo de ejecución. Sin embargo la parte del non-blocking que tanto promocionan en el website oficial de node.js asume que lo único que ejecutará node.js seran queries contra un servicio [RESTful](http://es.wikipedia.org/wiki/Representational_State_Transfer). Sin embargo en el mundo real una aplicación web es más que queries contra un servicio RESTful y es por ello que si dejamos que node.js se haga cargo del envio de los archivos css, html, las imágnes y todos los otros objetos que constituyen una página web la parte del non-blocking no se cumple y difícilmente el servicio pueda escalar a más de unos cuantos cientos de clientes simultáneos, de alli la razón de muchas de las críticas que ha recibido node.js. Cuál es la solución usar un servidor web que se haga cargo de servir el contenido estático de nuestra aplicación web y para no ello no hay nada mejor que el pequeño y ágil Nginx.

 En este post les explicaré como configurar node.js y Nginx en un servidor Ubuntu 12.04 para correr una aplicación desarrollada en node.js. Por favor tengan en cuenta que estos pasos también pueden ser seguidos en Debian (omitiendo el comando sudo, siempre y cuando estemos logueados como root), si desea hacer lo mismo con distribuciones basadas en RPM como Centos o Fedora entonces siga las ideas generales pero no podrá copiar y pegar directamente los comando listados en este tutorial.

 <span style="line-height: 1.6em;">Lo primero que debemos hacer es actualizar nuestro Ubuntu para asegurarnos de que estamos usando lo repositorios de paquetes más nuevos y luego procedemos a instalar todos los paquetes que vamos a requerir para poder instalar nuestro node.js que será instalado desde el código fuente:</span>

```
<pre id="terminal">
	# Para asegurarnos de que estamos usando los repositrios más nuevos.
	sudo apt-get update

	# Todos los paquetes que vamos a necesitar.
	sudo apt-get install build-essential libssl-dev mysql-server nano git curl htop links
```

 <span style="line-height: 1.6em;">Para instalar node.js debemos descargar el código fuente desde su [website oficial](http://nodejs.org/download/), desempaquetarlo y luego seguir el clásico ciclo de compilación para tenerlo instalado en nuestro servidor:</span>

```
<pre id="terminal">
# Crear un nuevo directorio y entrar en él.
sudo mkdir /opt/node/
cd /opt/node/

# Descargar el código fuente de node.js.
# Al momento de escribir este tutorial la versión
# más nueva fue la 0.10.21.
sudo wget http://nodejs.org/dist/v0.10.21/node-v0.10.21.tar.gz

# Extraer los archivos del tarball.
sudo tar xvzf node-v0.10.21.tar.gz

# Hagamos algo de limpueza antes de compilar.
sudo mv node-v0.10.21/* .
sudo rm node-v0.10.21.tar.gz
sudo rm -r node-v0.10.21/

# Ejecutar el `make` y el `make install`.
# Tener paciencia.
# La compilación puede demorar algunos minutos.
sudo make
sudo make install

# Ejecutar una batería de pruebas para verificar
# que todo está bien.
sudo make test

# Verificar que nuestra versión es la correcta y
# no tenemos interferencia de versiones anteriores.
node -v
```

 Con el node.js ya compilado debemos de crear una sencilla aplicación de prueba que sólo dira "Hola Mundo!!!" cada vez que apuntemos el browser a ella, aquí los pasos previos a creación de nuesta primera aplicación node.js:

```
<pre id="terminal">
# Crear un directorio para nuestras aplicaciones web.
sudo mkdir /var/www/
cd /var/www/

# Crear un directorio para nuestra primera aplicación.
sudo mkdir holamundo/
cd holamundo/

# Crear nuestro archivo de servidor.
pico server.js
```

 Ahora que pico esta abierto copie y pegue este código fuente en JavaScript que justamente tiene por misión responder con un "Hola Mundo!!!" a todas las solicitudes que reciba:

```
<pre id="terminal">
var http = require('http');
http.createServer(function (req, res) {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hola Mundo!!!\n');
}).listen(3000);
console.log('Server running at http://127.0.0.1:3000/');
```

 Si no tiene experiencia con pico, para grabar debe precional la tecla &lt;Control&gt; y la letra "O" al mismo tiempo, luego para salir debe presional la tecla &lt;Control&gt; y la letra "X" al mismo tiempo.

 Ahora para asegurarnos que nuestra aplicación permanezca corriendo para siempre e incluso se recupere luego de que por alguna razón el kernel termina el proceso instalaremos un módulo node.js llamado [Forever](https://github.com/nodejitsu/forever) (por siempre), un nombre muy conveniente para la funcionalidad que realiza:

```
<pre id="terminal">
# Instalar el módulo Forever globalmente.
sudo npm install -g forever

# Iniciar nuesta webapp.
forever start -l hello-world.log server.js

# Ver el log de la actividad de nuesta webapp
tail -f ~/.forever/hello-world.log
```

 Ahora abra el navegador en modo texto llamado "links" usando este comando:

```
<pre id="terminal">
	links http://127.0.0.1:3000
```

 Y debería ver el mensaje "Hola Mundo!!!" que indica que la aplicación node.js está corriendo y aceptando peticiones sólo desde el mismo servidor, esto lo hacemos por seguridad para evitar que algún usuario mal intencionado intente un ataque directo al node.js. Para salir de "links" presione las teclas &lt;ESC&gt;+F+X y al mensaje que le aparezca digale "Y" o acepte.

 Ahora intalemos Nginx, que para el caso de nuesto Ubuntu 12.04 es muy sencillo ya que los paquetes están disponibles en los repositorios de la distribución:

```
<pre id="terminal">
sudo apt-get install nginx
```

 Para ver los archivos de log (bitácora) podemos usar el comando "tail" que por defecto nos muestra las 10 últimas líneas de un archivo de texto (todos los logs son archivos de texto), de esta forma sabremos si no hay errores reportados:

```
<pre id="terminal">
# Ver el logs de los accesos.
tail -f /var/log/nginx/access.log

# Ver el log de los errores.
tail -f /var/log/nginx/error.log
```

 Ahora debemos configurar nuestro Nginx para que actue como un porxy inverso y redireccione todas las solicitudes que cumplan una regla determinada a nuestra aplicación node.js, en primer lugar debemos hacer una copia de back-up de la actual configuración y luego crearemos la nuestra:

```
<pre id="terminal">
# Backup de la configuración por defecto
mv /etc/nginx/sites-available/default /etc/nginx/sites-available/default.bak

# Crear una nueva configuración.
pico /etc/nginx/sites-available/default
```

 Si ya tiene el editor de texto abierto en su terminal, por favor copie y pegue este archivo de configuración (recuere, para grabar &lt;Control&gt;+O y para salir &lt;Control&gt;+X):

```
<pre id="terminal">
# The upstream module is the link between Node.js and Nginx.
# Upstream is used for proxying requests to other servers.
# All requests for / get distributed between any of the servers listed.
upstream helloworld {
  # Set up multiple Node.js webservers for load balancing.
  # max_fails refers to number of failed attempts
  # before server is considered inactive.
  # weight priorities traffic to server. Ex. weight=2 will recieve
  # twice as much traffic as server with weight=1
  server 127.0.0.1:3000 max_fails=0 fail_timeout=10s weight=1;
  # server 127.0.0.1:3001 max_fails=0 fail_timeout=10s weight=1;
  # server 127.0.0.1:3002 max_fails=0 fail_timeout=10s weight=1;
  # server 127.0.0.1:3003 max_fails=0 fail_timeout=10s weight=1;
 
  # Send visitors back to the same server each time.
  ip_hash;
 
  # Enable number of keep-alive connections.
  keepalive 512;
}
 
server {
  listen 80;
  listen [::]:80 default_server ipv6only=on;
 
  # Index files.
  index index.html;
 
  # Domain names.
  # Make sure to set the A Record on your domain's DNS settings to your server's IP address.
  # You can test if was set properly by using the `dig` command: dig yourdomain.com
  server_name yourdomain.com www.yourdomain.com;
 
  # Timeout for closing keep-alive connections.
  keepalive_timeout 10;
  
  # Enable gzip compression.
  gzip on;
  gzip_http_version 1.1;
  gzip_vary on;
  gzip_comp_level 6;
  gzip_proxied any;
  gzip_buffers 16 8k;
  gzip_disable "MSIE [1-6]\.(?!.*SV1)";
  
  # Max upload size.
  # client_max_body_size 16M;
 
  # Change access and error log files.
  # access_log /var/log/nginx/yourdomain.com/access.log;
  # error_log /var/log/nginx/yourdomain.com/error.log;
 
  # Custom error page.
  # error_page 404 maintenance.html;
  # error_page 500 502 503 504 maintenance.html;
  
  # location /maintenance.html {
  #  root /var/www;
  # }
 
  location / {
    # Set this to your upstream module.
    proxy_pass http://helloworld;
    # Proxy headers.
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $host;
    proxy_set_header X-NginX-Proxy true;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_cache_bypass $http_upgrade;
    proxy_http_version 1.1;
    proxy_redirect off;
    # Go to next upstream after if server down.
    proxy_next_upstream error timeout http_500 http_502 http_503 http_504;
    proxy_connect_timeout 5s;
    # Gateway timeout.
    proxy_read_timeout 20s;
    proxy_send_timeout 20s;
    # Buffer settings.
    proxy_buffers 8 32k;
    proxy_buffer_size 64k;
  }
 
  # Enable caching of static files.
  # location ~* \.(css|js|gif|jpe?g|png)$ {
  #  expires 168h;
  #  add_header Pragma public;
  #  add_header Cache-Control "public, must-revalidate, proxy-revalidate";
  # }
  
  # Don't cache html files.
  # location ~* \.html$ {
  #  expires -1;
  # }
}
```

 Despues de que ya haga grabado y salido del archivo de configuración del Nginx, por favor recargue la configuración del servidor web usando este comando:

```
<pre id="terminal">
sudo service nginx reload
```

 Si ha seguido los pasos correctamente, sería posible en este momento que apuntando su navegador a la IP pública de su servidor web vea el "<span style="font-family:times new roman,times,serif;">Hola Mundo!!!</span>". Esta configuración puede ser modificada para permitir distribuir la carga entre "X" instancias de node.js, recuerde que aunque su servidor físico o virtual tenga múltiples "cores" (núcleos), node.js sólo usara uno y en caso desear usar todo ese poder de computo, Ud. debería tener varias instancias de la aplicación corriendo en direfentes puertos, por regla general nunca corra más instancias de node.js que el número de núcleos en su servidor menos uno, de esa forma evitará que haya un conflicto entre ellos y compitan por el mismo recurso (CPU).