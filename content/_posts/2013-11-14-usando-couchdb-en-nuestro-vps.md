---
id: 226
title: 'Usando CouchDB en nuestro VPS'
date: '2013-11-14T10:23:17-05:00'
author: volkan68
layout: post
guid: 'http://websistente.com/blog/?p=226'
permalink: /usando-couchdb-en-nuestro-vps/
categories:
    - CloudComputing
    - DevOp
---

 [CouchDB](http://couchdb.apache.org/)<span style="line-height: 1.6em;"> es un sistema gestor de bases de datos orientado a documentos, similar a MongoDB y es una de las muchas alternativas </span>[NoSQL](http://es.wikipedia.org/wiki/NoSQL)<span style="line-height: 1.6em;"> exitentes en el mercado. Es decir, los datos no están guardados en tablas y tuplas. Pongamos un ejemplo para entenderlo, imaginen que quieres tener un registro de usuarios, donde cada usuario tiene un nombre, un correo y su edad, cada "registro" (conjunto de nombre + correo + edad) pasaría a ser un documento diferente en una gran repositorio de documentos, independiente de todos los demás. No existe una sección en donde puedas separar unos documentos de otros, todos están en el mismo repositorio. </span>***¿Qué no es CouchDB?***<span style="line-height: 1.6em;">, no es un sustituto a las bases de datos relacionales tradicionales, debes tomar en cuenta cuando vas a desarrollar que no siempre lo que parece mas nuevo es la mejor solución, CouchDB no es la solución a todos tus problemas ni una evolución de las tablas relacionales, tengamos presente eso. Este tutorial describe como instalar CouchDB en Ubuntu 12.04 y como usar Futon (el sistema de administración de CouchDB).</span>

 <span style="line-height: 1.6em;">Cómo siempre antes de instalar cualquier paquete debemos actualizar nuestros repositorios, para luego despues instalar CouchDB:</span>

```
<pre id="terminal">
apt-get update
apt-get install couchdb -y
```

 En caso de que tenga problemas ejecute este comando:

```
<pre id="terminal">
chown -R couchdb /var/run/couchdb
```

 Aunque Ud. no lo crea CouchDB ya está instaldo, como sabemos que está funcionando, pues ejecute este comando:

 <span style="color:#0000FF;">**<span style="font-family:courier new,courier,monospace;">root@couchdb:/# netstat -tl  
 Active Internet connections (only servers)  
 Proto Recv-Q Send-Q Local Address Foreign Address State </span>**</span>  
<span style="color:#FF0000;">**<span style="font-family:courier new,courier,monospace;">tcp 0 0 localhost.localdom:5984 \*:\* LISTEN</span>**</span><span style="color:#0000FF;">**<span style="font-family:courier new,courier,monospace;"> </span>**</span>

 <span style="line-height: 1.6em;">Por defecto CouchDB escucha el puerto 5984 sólo en la interface localhost, esto es por razones de seguridad por defecto, si queremos configurar el accesso a través de todas las interfaces de red debemos editar los archivo "<span style="font-family:courier new,courier,monospace;">**/etc/couchdb/default.ini**</span>" y "<span style="font-family:courier new,courier,monospace;">**/etc/couchdb/local.ini**</span>", cambiando el valor del parámetro "</span><span style="font-family:courier new,courier,monospace;">**bind\_address**</span><span style="line-height: 1.6em;">" de esta manera:</span>

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;">**bind\_address=0.0.0.0**</span></span>

 Luego de eso sólo debemos reiniciar CouchDB de la siguiente manera:

 <span style="font-family:courier new,courier,monospace;">**/etc/init.d/couchdb restart**</span>

 Para ver si ahora estamos escuchando en todas las interfaces, debemos ejecutar este comando y obtener el resultado mostrado:

 **<span style="font-family:courier new,courier,monospace;"><span style="color:#0000FF;">root@couchdb:/etc/couchdb# netstat -tl  
 Active Internet connections (only servers)  
 Proto Recv-Q Send-Q Local Address Foreign Address State </span>  
<span style="color:#FF0000;">tcp 0 0 \*:5984 \*:\* LISTEN</span></span>**

 El "<span style="font-family:courier new,courier,monospace;">**\***</span>" nos indica que ahora nuestra DB está escuchando en todas las interfaces de red, esto lo hariamos si deseamos tener nuestra DB en un VPS y nuestra aplicación web en otro VPS, de estar ambos en el mismo servidor no es necesario hacer ningún cambio.

 <span style="font-size:16px;">**<u>Usando CouchDB desde la línea de comando</u>.**</span>

 Para hacer eso necesitamos una herramienta que sea capaz de manejar el protocolo HTTP desde la línea de comandos, la más adecuada es "curl", la cual si no la tenemos instalada ya, podremos instalarla usando el siguiente comando:

 <span style="font-family:courier new,courier,monospace;">**apt-get install curl**</span>

 Con "curl" verificaremos la versión de nuestro CouchDB de la siguiente manera:

 <span style="font-family:courier new,courier,monospace;">**curl http://localhost:5984**</span>

 Y el resultado que debemos obtener debe ser algo similar a esto:

 <span style="font-family:courier new,courier,monospace;">**{"couchdb":"Welcome","version":"1.0.1"}**</span>

 Como es evidente hasta aquí CouchDB acepta queries a través del protocolo HTTP y las retorna a través del mismo y el formato usado es JSON. Por ejemplo para saber que DB tenemos en nuestro sistema debemos ejecutar este comando:

 <span style="font-family:courier new,courier,monospace;">**curl -X GET http://localhost:5984/\_all\_dbs**</span>

 En nuestro caso, sólo nos mostrará una sóla DB que usa CouchDB para manejar usuarios:

 <span style="font-family:courier new,courier,monospace;">**\[«\_users»\]**</span>

 Si queremos crear una base de datos, por ejemplo deseamos crear una base de datos de "contactos", sólo debemos ejecuatar este comando:

 <span style="font-family:courier new,courier,monospace;">**curl -X PUT http://localhost:5984/contactos**</span>

 Si todo va bien debemos obtener este mensaje:

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;">**{"ok":true}**</span></span>

 De no ser así es posible que obtengamos este mensaje de error:

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;">**{"error":"unauthorized","reason":"You are not a server admin."}**</span></span>

 En el segundo caso lo que pasa es que por defecto nuestro sistema sólo acepta comandos de escritura de usuarios que son administradores, para ello sólo debemos editar el archivo "<span style="font-family:courier new,courier,monospace;">**/etc/couchdb/local.ini**</span>", ir al final del archivo y agregar una línea como esta:

 <span style="font-family:courier new,courier,monospace;">**admin = UnP@ssW0rd**</span>

 Cómo es obvio esto es un ejemplo, reemplace la palabra "UnP@ssW0rd" por un password lo suficientemente fuerte y luego reinicie CouchDB de la siguiente manera:

 <span style="font-family:courier new,courier,monospace;">**/etc/init.d/couchdb restart**</span>

 Luego de eso, probemos este comando para crear la DB "contactos:

 **<span style="font-family:courier new,courier,monospace;">curl -X PUT http://admin:UnP@ssWord@localhost:5984/contactos</span>**

 Cómo podrán ver en el URL estamos incluyendo la combinación "usuario:password" antes de la dirección del servidor.

 Para ver las bases de datos ejecutamos otra vez el comando:

 <span style="font-family:courier new,courier,monospace;">**curl -X GET http://localhost:5984/\_all\_dbs**</span>

 Y ahora nos debería mostrar:

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;">**\[«contactos»,»\_users»\]**</span></span>

 Donde "contactos" es la DB que acabamos de crear. Cómo es evidente usar esta forma para hacer un despligue inicial en CouchDB no es la más cómoda y libre de errores, para administrar nuestro gestor de base de datos CouchDB provee una interface web llamada Futon, al cual podemos acceder a través de este URL:

 <span style="font-family:courier new,courier,monospace;">**http://localhost:5984/\_utils**</span>

 En caso de que hayamos seteado nuestro CouchDB en un VPS remoto, no olvide que debe activar el acceso a través de todas las interfaces y asignar un password de administrador antes de poder accesar a la interface web usando el IP público del VPS de la siguiente manera:

 <span style="font-family:courier new,courier,monospace;">**http://WW.XX.YY.ZZ:5984**</span>

 Una vez alli en la esquina inferior izquierda busque esta parte:

 [![CouchDB_Login](http://websistente.com/blog/wp-content/uploads/2013/11/CouchDB_Login.jpg)](http://websistente.com/blog/wp-content/uploads/2013/11/CouchDB_Login.jpg)

 Cómo es obvio haga click en "**Login**" y use las credenciales de administrador que configuró previamente. Por defecto los usuarios que se crean usando Signup no son administradores y no pueden crear nuevas DBs o cambiar la configuración de CouchDB.

 Desde esta interface gráfica podemos crear nuevas DBs, borrar DBs existentes, replicar alguna base de datos de nuestro servidor en otro servidor remoto o hacer lo inverso, con el propósito de mejorar la disponibilidad de nuestro sistema de base de datos.

 Un libro con licencia Creative Commons en español sobre cómo usar CouchDB puede ser encontrado [aquí](http://guide.couchdb.org/editions/1/es/), recomiendo su lectura para aquellos que deseen usar este sistema de base de datos para sus proyectos, especialmente la parte de [Clustering](http://guide.couchdb.org/editions/1/es/clustering.html) en donde se comenta sobre las posibilidades de proxeo/replicación/particionamiento de CouchDB a través del uso de [Lounge](http://tilgovi.github.io/couchdb-lounge/), un proyecto opensource que permite crear un sistema de base de datos de una capacidad de almacenamiento casi "infinita".

 Espero que este tutorial les haya sido de utilidad y espero sus comentarios.