---
id: 66
title: 'Usando PHP en Heroku'
date: '2013-07-19T10:56:44-04:00'
author: volkan68
layout: post
guid: 'http://websistente.com/blog/?p=66'
permalink: /usando-php-en-heroku/
categories:
    - CloudComputing
    - Desarrollo
    - DevOp
---

 Aunque muchos no lo saben y [Heroku](http://heroku.com) no lo publicita es posible desplegar aplicaciones **PHP** o **Perl** en el servicio [PaaS](http://es.wikipedia.org/wiki/Computaci%C3%B3n_en_la_nube#Plataforma_como_servicio) de Heroku. Claro que hay algunas limitaciones, la primera es que MySQL no es la base de datos relacional que ofrece la plataforma, sino que los es PostgreSQL. Esto hace que algunas muy populares webapps como WordPress por ejemplo no puedan ser deplegadas directamente en Heroku y tal vez es la razón por la cual no se publicita mucho el soporte a PHP. En este muy breve tutorial explicaremos como tener una aplicación PHP con base de datos PostgreSQL corriendo en Heroku.

 Primero, estamos asumiendo que Ud. ya tiene una cuenta en Heroku (son gratuitas, puede [registrarse aquí](https://id.heroku.com/signup)) y que tiene el [Toolbelt](https://toolbelt.heroku.com/) instalado en su máquina de desarrollo que asumimos es un entorno Unix/Linux. Si estos dos requerimientos ya están satisfechos comencemos.

 Creemos un directorio en donde podremos el código de nuestra aplicación:

 **<span style="font-family:courier new,courier,monospace;">mkdir TestHeroku</span>**

 **<span style="font-family:courier new,courier,monospace;">cd TestHeroku</span>**

 Una vez dentro de dicho directorio usando su editor de texto favorito (pico, nano, vi, vim, emacs) cree un archivo <span style="font-family:courier new,courier,monospace;">**index.php**</span> con este código:

 **<span style="font-family:courier new,courier,monospace;">&lt;?php</span>**

 **<span style="font-family:courier new,courier,monospace;">$conn\_string = "";</span>**

 **<span style="font-family:courier new,courier,monospace;">$dbconn = pg\_connect($conn\_string)   
 or die('No puedo conectarme: ' . pg\_last\_error());</span>**

 **<span style="font-family:courier new,courier,monospace;">echo "Conectado a la Base de Datos!";</span>**

 **<span style="font-family:courier new,courier,monospace;">?&gt;</span>**

 Se habran dado cuenta de que la cadena de conexión está en blanco, esto es debido a que debemos obtener esta información desde Heroku.

 Ahora que ya tenemos creado el código de nuestra aplicación mínima, procedemos a crear el repositorio git, agregar el código que hemos creado a él, para ello son necesarios los siguientes pasos:

 **<span style="font-family:courier new,courier,monospace;">git init</span>**

 **<span style="font-family:courier new,courier,monospace;">git add .</span>**

 **<span style="font-family:courier new,courier,monospace;">git commit -m "Aplicación básica PHP con acceso a DB"</span>**

 Hasta aquí está listo todo lo que necesitamos para poder comenzar a desplegar nuestro código en Heroku, ahora vienen dos pasos cruciales, el primero es crear nuestra aplicación en Heroku y el segundo es conseguir las credenciales para poder acceder nuestra DB. Para crear nuestra aplicación sólo debemos ejecutar este comando:

 <span style="font-family:courier new,courier,monospace;">**heroku create**</span>

 Para obtener las credenciales de nuestra DB, primero hay que añadir el addon de soporte de PostgreSQL gratuito (la versión para desarrolladores) y luego preguntar por las credenciales, para ello ejecutamos estos dos comandos:

 <span style="font-family:courier new,courier,monospace;">**user@desktop:~/PHP/TestHeroku$ <span style="color:#0000FF;">heroku addons:add heroku-postgresql:dev</span>  
 Adding heroku-postgresql:dev on shielded-lake-3567… done, v4 (free)  
 Attached as HEROKU\_POSTGRESQL\_NAVY\_URL  
 Database has been created and is available  
 ! This database is empty. If upgrading, you can transfer  
 ! data from another database with pgbackups:restore.  
 Use `heroku addons:docs heroku-postgresql:dev` to view documentation.**</span>

 <span style="font-family:courier new,courier,monospace;">**user@desktop:~/PHP/TestHeroku$ <span style="color:#0000FF;">heroku pg:credentials NAVY</span>  
 Connection info string:  
 "<span style="color:#FF0000;">dbname=d49jgofa4ihhd1 host=ec2-45-152-86-231.compute-1.amazonaws.com port=5432 user=tbejgilawfitui password=43212UU2sdffEEW0dfQcYh8tP8fS sslmode=require</span>"  
 Connection URL:  
 postgres://tbejgilawfitui:0QH82ZO2MWIuk0dfQcYh8tP8fS@ec2-54-225-68-241.compute-1.amazonaws.com:5432/d49jgofa4ihhd1**</span>

 Observer que en estas capturas de pantalla, la información que Ud. debe tipear está en **<span style="color:#0000FF;">azul</span>** y la información que necesitamos para completar nuestro código está en <span style="color:#FF0000;">**rojo**</span>. Copie la información que Ud. obtiene en su terminal y edite el código del archivo index.php para que quede de esta manera:

 <span style="font-family:courier new,courier,monospace;">**&lt;?php**</span>

 <span style="font-family:courier new,courier,monospace;">**$conn\_string = "**</span>**<span style="color: rgb(255, 0, 0);">dbname=d49jgofa4ihhd1 host=ec2-45-152-86-231.compute-1.amazonaws.com port=5432 user=tbejgilawfitui password=43212UU2sdffEEW0dfQcYh8tP8fS sslmode=require</span>**<span style="font-family:courier new,courier,monospace;">**";**</span>

 <span style="font-family:courier new,courier,monospace;">**$dbconn = pg\_connect($conn\_string)   
 or die('Could not connect: ' . pg\_last\_error());**</span>

 <span style="font-family:courier new,courier,monospace;">**echo "Connected to the DB";**</span>

 <span style="font-family:courier new,courier,monospace;">**?&gt;**</span>

 Ahora estamos listos para hacer un último commit y subir nuestra aplicación a Heroku haciendo esto:

 <span style="font-family:courier new,courier,monospace;">**git add .**</span>

 <span style="font-family:courier new,courier,monospace;">**git commit -m "Credenciales de DB incluídas"**</span>

 <span style="font-family:courier new,courier,monospace;">**git push heroku master**</span>

 Ahora ya podemos ver el resultado de nuestra aplicación visitando el URL de la misma en Heroku. Por lo general los URL de las applicaciones de Heroku se parecen a este:

 <http://shielded-lake-3567.herokuapp.com/>

 Si no saben cual es el nombre de su aplicación, ni el URL para accesarlo y no desean loguearse a la interface web para averiguarlo, sólo ejecuten este comando dentro del directorio que contiene su aplicación para obtener dicha información:

 <span style="font-family:courier new,courier,monospace;">**user@desktop:~/PHP/TestHeroku$ <span style="color:#0000FF;">heroku apps:info</span>  
 === shielded-lake-3567  
 Addons: heroku-postgresql:dev  
 Git URL: git@heroku.com:shielded-lake-3567.git  
 Owner Email: user@domain.com  
 Region: us  
 Repo Size: 192k  
 Slug Size: 9M  
 Stack: cedar  
 Tier: Legacy  
 Web URL: <span style="color:#FF0000;">http://shielded-lake-3567.herokuapp.com/</span>**</span>

 Espero que este breve tutorial les haya sido de utilidad y quedo a la espera de sus comentarios.