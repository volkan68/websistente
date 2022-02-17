---
id: 123
title: 'MongoDB es fácil'
date: '2013-10-31T10:42:01-04:00'
author: volkan68
layout: post
guid: 'http://websistente.com/blog/?p=123'
permalink: /mongodb-es-facil/
categories:
    - CloudComputing
    - DevOp
---

 MongoDB es una base de datos [NoSQL](http://es.wikipedia.org/wiki/NoSQL) orientada a documentos que son guardados en una estructura JSON dinámica que es denominada [BSON](http://bsonspec.org/) (Binary JSON). Debido a que JSON es un formato de datos familiar para los programodores JavaScript, es que muchos desarrolladores node.js usan esta base de datos para sus proyectos. Otra gran ventaja de MongoDB es que es un proyecto Open Source y por lo tanto es posible de ser necesario modificar el códio. El nombre MongoDB proviene de la palabra inglesa "humongous" (que significa enorme) y justamente este sistema de base de datos está diseñado para ser enorme permitiendo escalar horizontalmente la base de datos, es decir podemos agregar "X" número de nodos a nuestra base de datos expandiendo de forma casi ilimitada la capacidad de almacenamiento de la misma.

 En este breve tutorial explicaremos como instalar MongoDB en un servidor Ubuntu 12.04, aunque los pasos de la instalación son los mismos para Debian los comandos para administrar el daemon del MongoDB son diferentes y a menos que indique lo contrario este tutorial debe ser usado para intalar, configurar y administrar MongoDB en un servidor Ubuntu 12.04.

 <span style="line-height: 1.6em;">Primero debemos actualizar nuestros respositorios para estar seguros de que tenemos la versión más reciente de los paquetes disponibles en el repositorio:</span>

```
<pre id="terminal">
sudo apt-get update
```

 Luego agregaremos el repositorio de donde podemos obtener los paquetes (.deb) oficiales de MongoDB:

```
<pre id="terminal">
# Agregamos la clave pública PGP del repositorio de MongoDB
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10

# Creamos un archivo que contenga el repositorio oficial
echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' | sudo tee /etc/apt/sources.list.d/mongodb.list

# Actualizamos nuevamente nuestra lista de repositorios
sudo apt-get update
```

 Finalmente ya estamos listos para intalar MongoDB con un simple apt-get:

```
<pre id="terminal">
sudo apt-get install mongodb-10gen
```

 Ahora podemos configuremos nuestro MongoDB si lo deseamos, para ello debemos editar el archivo **<span style="font-family:courier new,courier,monospace;">/etc/mongodb.conf</span>**. En nuestro caso usaremos la configuración por defecto ya que sólo deseamos hacer unas pruebas simples, pero es posible configurar sistemas con replicación y varios esclavos que permiten escalar el sistema horizontalmente.

 Para verificar que nuestro servicio de MongoDB está corriendo debemos ejecutar este comando y obtener un resultado similar:

```
<pre id="terminal">
netstat -nap | grep 27017
tcp        0      0 0.0.0.0:27017           0.0.0.0:*               LISTEN      2613/mongod     
unix  2      [ ACC ]     STREAM     LISTENING     133233390 2613/mongod         /tmp/mongodb-27017.sock
```

 En caso de que no esté corriendo verifique la configuración e intente reiniciarlo con este comando:

```
<pre id="terminal">
sudo service mongodb start
```

 Los comandos para admnistrar nuestro MongoDB son estos:

```
<pre id="terminal">
# Para iniciar el servicio MongoDB:
sudo service mongodb start

# Para detener el servicio MongoDB:
sudo service mongodb stop

# Para reiniciar el sercicio MongoDB:
sudo service mongodb restart
```

 Ahora hagamos unas pequeñas pruebas para verificar que está funcionando correctamente, para ello usaremos el cliente "mongo", y lo primero que veremos será algo equivalente a esto:

 **<span style="font-family:courier new,courier,monospace;">$ mongo  
 MongoDB shell version: 2.4.7  
 connecting to: test  
 Server has startup warnings:   
 Thu Oct 31 17:54:23.900 \[initandlisten\]   
 Thu Oct 31 17:54:23.900 \[initandlisten\]  
 Thu Oct 31 17:54:23.900 \[initandlisten\]   
 &gt;</span>**

 Ahora, lo primero que debemos hacer es crear nuestra DB, lo cual es tan sencillo como elegir un nombre (en nuestro caso "<span style="color:#FF0000;"><span style="font-family:courier new,courier,monospace;">**nuevadb**</span></span>") y comenzar a agregar registros (ojo, no es obligatorio que todos los registros deban tener la misma estructura), empecemos añadiendo estos registros:

 <span style="font-family:courier new,courier,monospace;">**&gt; db.<span style="color:#FF0000;">nuevadb</span>.save( { city: "Lima", pop: "8000000" } )  
 &gt; db.<span style="color:#FF0000;">nuevadb</span>.save( { city: "Chiclayo", pop: "1000000" } )  
 &gt; db.<span style="color:#FF0000;">nuevadb</span>.save( { city: "Trujillo", pop: "1200000" } )  
 &gt; db.<span style="color:#FF0000;">nuevadb</span>.save( { city: "Piura", pop: "1100000" } )**</span>

 No pretendo que esos sean los valores reales, pero nos servirá para tener una estrutura sencilla tabular sobre la cual corres queries sencillas, la primera de todas es obtener un listado total para lo cual usaremos este comando:

 **<span style="font-family:courier new,courier,monospace;">&gt; db.nuevadb.find()  
 { "\_id" : ObjectId("5272659986090431f21bc2bd"), "city" : "Lima", "pop" : "8000000" }  
 { "\_id" : ObjectId("527265a386090431f21bc2be"), "city" : "Chiclayo", "pop" : "1000000" }  
 { "\_id" : ObjectId("527265ad86090431f21bc2bf"), "city" : "Trujillo", "pop" : "1200000" }  
 { "\_id" : ObjectId("527265b786090431f21bc2c0"), "city" : "Piura", "pop" : "1100000" }</span>**

 Observe como MongoDB le agrega un índice a todos los registros que hemos agregado, ya que en el fondo todos esos registros son documentos de formato libre, aunque en nuestro caso por simplicidad, mi cerebro ha estado acostumbrado durante casi 25 años a pensar en términos de tablas,asi que lo hemos organizado así. Pero tenga en mente que el formato de cada entrada de la DB es libre.

 Ahora hagamos simples queries, despues de todo nuestros programas siempre haran una búsqueda sobre el total de los datos almacenados para proveer un reporte resumido que es justamente lo que nuestro usuario necesita, nuestra primera query busca todas los registros cuyo campo "pop" (population) es mayor a 1200000 y es justamente lo que nos muetra el resultado:

 <span style="font-family:courier new,courier,monospace;">**&gt; db.nuevadb.find( { pop: { $gt: "1200000" } } )  
 { "\_id" : ObjectId("5272659986090431f21bc2bd"), "city" : "Lima", "pop" : "8000000" }**</span>

 Ahora busquemos aquellas ciudades con poblaciones menores a 1200000:

 **<span style="font-family:courier new,courier,monospace;">&gt; db.nuevadb.find( { pop: { $lt: "1200000" } } )  
 { "\_id" : ObjectId("527265a386090431f21bc2be"), "city" : "Chiclayo", "pop" : "1000000" }  
 { "\_id" : ObjectId("527265b786090431f21bc2c0"), "city" : "Piura", "pop" : "1100000" }</span>**

 Finalmente busquemos todas las ciudades cuyo campo "city" sea igual a "Chiclayo":

 <span style="font-family:courier new,courier,monospace;">**&gt; db.nuevadb.find( { city: "Chiclayo" } )  
 { "\_id" : ObjectId("527265a386090431f21bc2be"), "city" : "Chiclayo", "pop" : "1000000" }**</span>

 Para salir del cliente "mongo" sencillamente tipeamos "exit":

 <span style="font-family:courier new,courier,monospace;">**&gt; exit  
 bye**</span>

 Espero que este tutorial les haya sido de utilidad, recuerden que la información divulgada aquí es un resumen de lo mínimo necesario para poder empezar a usar MongoDB, el tema es amplio y una vez teniendo el sistema mínimo operando les recomiendo remitirse a la [documentación oficial del proyecto MongoDB](http://docs.mongodb.org/manual/).