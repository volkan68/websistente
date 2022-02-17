---
id: 137
title: 'Usando MySQL con node.js'
date: '2013-11-01T12:40:14-04:00'
author: volkan68
layout: post
guid: 'http://websistente.com/blog/?p=137'
permalink: /usando-mysql-con-node-js/
categories:
    - Desarrollo
---

 Imaginémonos que deseamos usar MySQL en un proyecto con node.js, después de todo MySQL es de lejos la DB relacional más usada ¿cómo hacerlo?. Pues bien aquí les dejo un pequeño tutorial que describe como usar la base de datos MySQL en un proyecto node.js usando el framework [express](http://expressjs.com/). Este tutorial asume que ya tiene instalado node.js y MySQL ya sea localmente o en su servidor de desarrollo, no interesa el sistema operativo en teoría. Pero deseo advertir que este tutorial es el resultado de las pruebas realizadas en un Ubuntu 12.04 Server Edition y perfectamente podría correr en cualquier otra distribución de Linux.

 <span style="line-height: 1.6em;">La idea detras del presente proyecto es muy simple, permitir grabar los datos ingresados en un formulario a la DB. Despues de todo es una de las funcionalidades más comunes el de permitir que los usuarios nos hagan llegar sus preguntas o dirección de contacto. Por otro lado no pretende ser un proyecto final con todas las medidas de seguridad requeridas, la idea es mostrar los detalles de como usar el framework </span>[express](http://expressjs.com/)<span style="line-height: 1.6em;"> que es muy popular entre los desarrolladores node.js además de como instalar el </span>[módulo de MySQL para node.js](https://github.com/felixge/node-mysql)<span style="line-height: 1.6em;"> e integrarlo con express. Si desea utilizar este código en producción por favor no olvide verficicar que no hay inyección SQL antes de ejecutar los queries.</span>

 <span style="line-height: 1.6em;">Empecemos por el pincipio, creemos un nuevo proyecto llamado "</span>**node-mysql**<span style="line-height: 1.6em;">", para ello debemos ejecutar este comando:</span>

```
<pre id="terminal">
$ express --css stylus --ejs node-mysql
```

 Como resultado obtendremos este mensaje:

```
<pre id="terminal">
   create : node-mysql
   create : node-mysql/package.json
   create : node-mysql/app.js
   create : node-mysql/public
   create : node-mysql/public/javascripts
   create : node-mysql/public/images
   create : node-mysql/public/stylesheets
   create : node-mysql/public/stylesheets/style.styl
   create : node-mysql/routes
   create : node-mysql/routes/index.js
   create : node-mysql/routes/user.js
   create : node-mysql/views
   create : node-mysql/views/index.ejs

   install dependencies:
     $ cd node-mysql && npm install

   run the app:
     $ node app
```

 Seguiremos al pie de la letra las recomendaciones para completar la creación de nuestro proyecto:

```
<pre id="terminal">
cd node-mysql && npm install
```

 Ahora instalemos el módulo que integra nuestro node.js con MySQL, para ello debemos hacer esto:

```
<pre id="terminal">
npm install mysql
```

 Una vez instalado el soporte para MySQL creemos un usuario y una base de datos de prueba a la cual le asignaremos los privilegios necesarios, para ello debemos seguir estos pasos:

```
<pre id="terminal">
$ mysql -u root -p mysql
Enter password: 
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 44
Server version: 5.5.34-0ubuntu0.12.04.1 (Ubuntu)

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> CREATE USER 'node'@'localhost' IDENTIFIED BY 'UnPassword';
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE DATABASE nodesql;
Query OK, 1 row affected (0.00 sec)

mysql> GRANT ALL PRIVILEGES ON nodesql.* TO 'node'@'localhost';
Query OK, 0 rows affected (0.04 sec)

mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)

mysql> \q
Bye

$ mysql -u node -pUnPassword nodesql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 46
Server version: 5.5.34-0ubuntu0.12.04.1 (Ubuntu)

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 


mysql> CREATE TABLE comentarios (id INT NOT NULL AUTO_INCREMENT,              
    -> email VARCHAR(50),
    -> mensaje VARCHAR(200),
    -> PRIMARY KEY (ID));
Query OK, 0 rows affected (0.05 sec)

mysql>
```

 Ahora ya estamos listos para comenzar a editar los archivos de nuestro proyecto para obtener el resultado que buscamos. Primero hablemos sobre las partes del arbol de directorios que nos crea express:

 **<span style="font-family:courier new,courier,monospace;">ls -l  
 total 24  
 -rw-r–r– 1 root root 1356 Nov 1 14:19 app.js  
 drwxr-xr-x 7 root root 4096 Nov 1 11:45 <span style="color:#0000FF;">node\_modules</span>  
 -rw-r–r– 1 root root 203 Nov 1 11:43 package.json  
 drwxr-xr-x 5 root root 4096 Nov 1 11:43 <span style="color:#0000FF;">public</span>  
 drwxr-xr-x 2 root root 4096 Nov 1 12:44 <span style="color:#0000FF;">routes</span>  
 drwxr-xr-x 2 root root 4096 Nov 1 12:59 <span style="color:#0000FF;">views</span></span>**

 En color azul he listado los directorios del proyecto que contienen las partes del mismo y que describiré brevemente:

- **node\_modules**: Contiene todos los módulos que instalamos sólo para nuestro proyecto, es aquí por ejemplo donde está instalado el módulo MySQL. No lo borre ni edite los archivos dentro de él a menos que sepa lo que está haciendo.
- **public**: Contiene todos los "assets" o archivos estáticos de nuestro proyecto.
- **routes**: Contiene el código de las funciones ejecutar cuando un determinado URL es recibido, de alli viene el nombre de "routes", porque enruta la ejecución en base a la solicitud del cliente.
- **views**: Este es el lugar donde se colocan las "vistas" o "templates" que usaremos para generar nuestra páginas.

 El archivo **package.json** contiene la descripción del proyecto y puede ser editado a nuestra conveniencia. Si no está seguro del significado de los campos del archivo por favor remitase a la documentación oficial de express. El archivo **app.js** es donde nuestra aplicación reside y básicamente le indica a node.js que hacer con todas las solicitudes recibidas de los clientes.

 El archivo inicial **app.js** debería tener este contenido:

```
<pre id="terminal">
/**
 * Module dependencies.
 */

var express = require('express');
var routes = require('./routes');
var user = require('./routes/user');
var http = require('http');
var path = require('path');

var app = express();

// all environments
app.set('port', process.env.PORT || 3000);
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');
app.use(express.favicon());
app.use(express.logger('dev'));
app.use(express.json());
app.use(express.urlencoded());
app.use(express.methodOverride());
app.use(app.router);
app.use(require('stylus').middleware(path.join(__dirname, 'public')));
app.use(express.static(path.join(__dirname, 'public')));

// development only
if ('development' == app.get('env')) {
  app.use(express.errorHandler());
}

app.get('/', routes.index);
app.get('/users', user.list);

http.createServer(app).listen(app.get('port'), function(){
  console.log('Express server listening on port ' + app.get('port'));
});
```

 Nuestra modificación va a consistir en estas tres simples cosas:

1. Declarar el módulo MySQL con su respectiva configuración, a la vez de redireccionar la página inicial al formulario que deseamos presentar en cuanto el usuario llega. Todo eso lo haremos en el archivo app.js
2. Crear las rutas nuevas.
3. Crear las views nuevas.

 Una vez completados esos cambios estamos listos para ejecutar nuestra aplicación y correr algunas pruebas.

 <u>**Declarar el módulo MySQL y enrutar al usuario al formulario.**</u>

 Por favor abra el archivo apps.js y cambielo de forma que se vea como este código:

 **<span style="font-family:courier new,courier,monospace;">/\*\*  
 \* Module dependencies.  
 \*/</span>**

 **<span style="font-family:courier new,courier,monospace;">var express = require('express');  
<span style="color:#0000FF;">var mysql = require('mysql');</span>  
 var routes = require('./routes');  
<span style="color:#008000;">// var user = require('./routes/user');</span>  
<span style="color:#0000FF;">var mesg = require('./routes/mensaje');  
 var inserta = require('./routes/inserta');</span>  
 var http = require('http');  
 var path = require('path');</span>**

 **<span style="font-family:courier new,courier,monospace;">var app = express();</span>**

 <span style="color:#0000FF;">**<span style="font-family:courier new,courier,monospace;">connection = mysql.createConnection({  
 host : 'localhost',  
 user : 'node',  
 password : 'UnPassword',  
 database: 'nodesql'  
 });</span>**</span>

 **<span style="font-family:courier new,courier,monospace;">// all environments  
<span style="color:#0000FF;">app.set('ipaddr', '0.0.0.0');</span>  
 app.set('port', process.env.PORT || 3000);  
 app.set('views', path.join(\_\_dirname, 'views'));  
 app.set('view engine', 'ejs');  
 app.use(express.favicon());  
 app.use(express.logger('dev'));  
 app.use(express.json());  
 app.use(express.urlencoded());  
 app.use(express.methodOverride());  
 app.use(app.router);  
 app.use(require('stylus').middleware(path.join(\_\_dirname, 'public')));  
 app.use(express.static(path.join(\_\_dirname, 'public')));</span>**

 **<span style="font-family:courier new,courier,monospace;">// development only  
 if ('development' == app.get('env')) {  
 app.use(express.errorHandler());  
 }</span>**

 <span style="color:#008000;">**<span style="font-family:courier new,courier,monospace;">// app.get('/', routes.index); </span>**</span>

 **<span style="font-family:courier new,courier,monospace;">app.get('/', mesg.formulario);  
 app.post('/insertmesg', inserta.registro);  
<span style="color:#008000;">// app.get('/users', user.list);</span></span>**

 **<span style="font-family:courier new,courier,monospace;">http.createServer(app).listen(app.get('port'), function(){  
 console.log('Express server listening on port ' + app.get('port'));  
 });</span>**

 <span style="line-height: 1.6em;">Como podrá observar el código que hemos agregado está en azul y podrá notar que hemos creado dos rutas. Una se llama "mesg" que se encarga de mostrarnos el formulario y la otra se llama "inserta", que como su nombre lo indica es aquella que inserta los datos a la DB. Las líneas en verde han sido comentadas ya que no las usaremos y pueden ser borradas si lo desea.</span>

 <u>**Crear las rutas nuevas.**</u>

 Crearemos dos rutas una llamada "mensaje.js" y otra llamada "inserta.js", como su nombre lo indica ambos archivos deben ser puestos en el directorio "routes".

 Aquí el contenido de mensaje.js:

 **<span style="font-family:courier new,courier,monospace;">/\*  
 \* GET home page.  
 \*/</span>**

 **<span style="font-family:courier new,courier,monospace;">exports.formulario = function(req, res){  
 res.render('mensaje', { title: 'Deje su mensaje' });  
 };</span>**

 <span style="font-family:tahoma,geneva,sans-serif;">Aquí el contenido de inserta.js</span>

 <span style="font-family:courier new,courier,monospace;">**/\*  
 \* GET users listing.  
 \*/**</span>

 <span style="font-family:courier new,courier,monospace;">**exports.registro = function(req, res){  
 email = req.body.Email;  
 mesg = req.body.Mensaje;  
 console.log('insert into comentarios ( email , mensaje ) values (' + "'" + email + "'" +',' + "'" + mesg + "'" + ');');  
 connection.connect();  
 connection.query('insert into comentarios ( email , mensaje ) values (' + "'" + email + "'" +',' + "'" + mesg + "'" + ');', function (err, results, fields) {  
 if (err) {  
 console.log("Error: " + err.message);  
 throw err;  
 } else {  
 res.render('agradecimiento', { title: 'Gracias' });  
 }  
 });  
 connection.end();  
 // res.send("Su registro ha sido insertado: " + email + " " + mesg);  
 };**</span>

 <u>**Crear las vistas nuevas.**</u>

 En lo que respecta a las vistas, crearemos dos de nombres "**mensaje.ejs**" y "**agradecimiento.ejs**". Como su nombre lo indica ambos archivos deben ir dentro del directorio "**views**".

 Este es el contenido de **mensaje.ejs**:

 <span style="font-family:courier new,courier,monospace;">**&lt;!DOCTYPE html&gt;  
 &lt;html&gt;  
 &lt;head&gt;  
 &lt;title&gt;&lt;%= title %&gt;&lt;/title&gt;  
 &lt;link rel='stylesheet' href='/stylesheets/style.css' /&gt;  
 &lt;/head&gt;  
 &lt;body&gt;  
 &lt;h1&gt;&lt;%= title %&gt;&lt;/h1&gt;  
 &lt;p&gt;Bienvenidos a nuestra primera aplicaci&amp;oacute;n de prueba de node.js + MySQL.&lt;/p&gt;  
 &lt;p&gt;Por favor dejenos su mensaje y su direcci&amp;oacute;n de correo electr&amp;oacute;nico.&lt;/p&gt;  
 &lt;p&gt;  
 &lt;form name="input" action="/insertmesg" method="post"&gt;  
 Email: &lt;input type="text" name="Email"&gt;&lt;br /&gt;  
 Mensaje: &lt;br /&gt;  
 &lt;textarea rows="10" cols="40" name="Mensaje"&gt;  
 &lt;/textarea&gt;&lt;br /&gt;  
 &lt;input type="submit" value="Enviar"&gt;  
 &lt;/form&gt;  
 &lt;/p&gt;  
 &lt;/body&gt;  
 &lt;/html&gt;**</span>

 Este es el contenido de **agradecimiento.ejs**:

 <span style="font-family:courier new,courier,monospace;">**&lt;!DOCTYPE html&gt;  
 &lt;html&gt;  
 &lt;head&gt;  
 &lt;title&gt;&lt;%= title %&gt;&lt;/title&gt;  
 &lt;link rel='stylesheet' href='/stylesheets/style.css' /&gt;  
 &lt;/head&gt;  
 &lt;body&gt;  
 &lt;h1&gt;&lt;%= title %&gt;&lt;/h1&gt;  
 &lt;p&gt;Gracias por compartir su opinion con nosotros&lt;/p&gt;  
 &lt;p&gt;&lt;input type="button" value="Inicio" OnClick="location.href='/'"&gt;&lt;/p&gt;  
 &lt;/body&gt;  
 &lt;/html&gt;**</span>

 Listo ahora ya podemos iniciar nuestro proyecto y probar si realmente inserta en la DB. Para ello ejecutamos este comando que inicia la aplicación node.js:

 <span style="font-family:courier new,courier,monospace;">**node app.js**</span>

 Ahora abramos nuestro navegador e intentemos llenar el formulario. De tener éxito podremos ver los resultados en el cliente MySQL, asi por ejemplo podríamos tener algo como esto:

```
<pre id="terminal">
$ mysql -u node -pUnPassword nodesql
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 54
Server version: 5.5.34-0ubuntu0.12.04.1 (Ubuntu)

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> SELECT * FROM comentarios;
+----+--------------------+---------------------------------------------------+
| id | email              | mensaje                                           |
+----+--------------------+---------------------------------------------------+
|  1 | user@dominio.com   |     Prueba inicial                                |
|  2 | nombre@dominio.com |     Un mensaje de prueba.                         |
+----+--------------------+---------------------------------------------------+
2 rows in set (0.00 sec)

mysql> 
```

 Espero que este tutorial les haya clarificado como usar el módulo MySQL dentro de node.js lo cual es últil para aquellos que desean traducir proyectos existente de PHP a node.js.