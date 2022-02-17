---
id: 167
title: 'Asegurando tu server Ubuntu'
date: '2013-11-06T09:50:51-05:00'
author: volkan68
layout: post
guid: 'http://websistente.com/blog/?p=167'
permalink: /asegurando-tu-server-ubuntu/
categories:
    - DevOp
    - Networking
    - Seguridad
---

 La seguridad es uno de los puntos a los que debemos prestar mucha atención cuando ponemos un servidor en producción (es decir accesible a través de Internet), aunque no existe una receta mágina que vuelva invulnerable nuestro servidor, existen una serie de medidas básicas de seguridad que deberíamos tener en nuestro servidor para evitar los ataques más comunes y detectar algún intento de intrusión. En este post detallo una serie de medidas básicas para asegurar un servidor Ubuntu 12.04 independientemente de cual será la apliación a la cual lo destinaremos y de cual es el lugar donde lo hostearemos. Es posible tener configuraciones más seguras basados en el tipo de uso que le daremos al servidor (webapp, mail server, irc, etc.) y el data center o el servicio de nube que usemos, pero dado que las posibilidades serían infinitas, es que he decidido escribir este compendio de reglas mínimas.

 <span style="line-height: 1.6em;">Las medidas mínimas de seguridad que debemos tener en nuestro servidor son las siguientes:</span>

1. Deshabiliatar el acceso de "root" a través del protocolo SSH y cambiar el puerto por defecto.
2. Instalar y configurar un Firewall (ufw es el más simple)
3. Asegurar la memoria compartida. Restringir los accesos al shared memory en el /etc/fstab.
4. Protegernos contra ataques de fuerza bruta.
5. Permitir que el comando su sólo pueda ser usado por el grupo de administradores.
6. Asegurar los parámetros de red por defecto en el sysctl.
7. Instalar un software para búsqueda de rootkits.

 Cómo conseguir cada uno de estos siete puntos está explicado a continuación:

 <span style="font-size:16px;"><u>**Deshabiliatar el acceso de "root".**</u></span>

 Para deshabilitar el acceso de root debemos editar el archivo "<span style="font-family:courier new,courier,monospace;">**/etc/ssh/sshd\_config**</span>", buscamos la línea:

 <span style="font-family:courier new,courier,monospace;">**PermitRootLogin yes**</span>

 Y la reemplazamos por:

 <span style="font-family:courier new,courier,monospace;">**PermitRootLogin no**</span>

 Ahora para cambiar el puerto debemos buscar esta línea:

 **<span style="font-family:courier new,courier,monospace;">Port 22</span>**

 Y reemplazarla por esta otra línea:

 <span style="font-family:courier new,courier,monospace;">**Port 2233**</span>

 El nuevo puerto puede ser cualquiera en principio pero debemos asegurarnos de no elegir otro que este en uso por nuestro sistema. En este caso en particual he colocado el valor "2233", pero ya queda a elección del lector escoger un puerto.

 Una vez guardados los cambios, debemos reiniciar el daemon del ssh con este comando:

 <span style="font-family:courier new,courier,monospace;">**/etc/init.d/ssh restart**</span>

 <span style="font-size:16px;"><u>**Instalar y configurar un Firewall.**</u></span>

 El más simple y obvio de usar en Ubuntu es ufw ([Uncomplicated FireWall](https://wiki.ubuntu.com/UncomplicatedFirewall?action=show&redirect=UbuntuFirewall)), por defecto no está instalado asi que lo primero que debemos hacer es instalarlo:

```
<pre id="terminal">
	apt-get update
	apt-get instal ufw
```

 Una vez instalado debemos indicarle que puertos debe permitir acceder a las conexiones externas, para ello debemos ejecutar estos comandos:

```
<pre id="terminal">
ufw enable 
ufw allow 2233
ufw allow 80
ufw status
Status: active

To                         Action      From
--                         ------      ----
80/tcp                     ALLOW       Anywhere
2233/tcp                   ALLOW       Anywhere
80/tcp                     ALLOW       Anywhere (v6)
2233/tcp                   ALLOW       Anywhere (v6)
```

 Con nuestro Firewall habilitado y sólo permitiendo accesos a nuestro servidor web y al ssh en el puerto modificado estamos hemos concluido esta parte. Si deseamos aceptar conexiones entrantes a través de otros puestos sólo debemos hacerlo usando este comando:

 <span style="font-family:courier new,courier,monospace;">**ufw allow &lt;*port-number*&gt;**</span>

 Donde <span style="font-family:courier new,courier,monospace;">**&lt;*port-number*&gt;**</span> es el número del puerto en el cual aceptaremos conexiones.

 <u><span style="font-size:16px;">**Asegurar la memoria compartida.**</span></u>

 Básicamente lo que debemos hacer es editar el archivo "/etc/fstab" y agregar esta línea al final del archivo:

 <span style="font-family:courier new,courier,monospace;">**tmpfs /dev/shm tmpfs defaults,noexec,nosuid 0 0**</span>

 <u><span style="font-size:16px;">**Protegernos contra ataques de fuerza bruta.**</span></u>

 Para ello podemos usar ya sea [denyhosts](http://denyhosts.sourceforge.net/) que bloquea específicamente los ataques de fuerza bruta contra ssh/telnet o [fail2ban](http://www.fail2ban.org/wiki/index.php/Main_Page) que es mucho más flexible y permite bloquer ataques de fuerza bruta contra cualquier servicio ofrecido por nuestro servidor, dado que explicar todos las opciones de configuración de fail2ban tomaría muchas líneas sólo lo mencionamos, aunque recomendamos su instalación especialmente en los casos de servidores de correo con protocolos POP3 o IMAP.

 Para instalar denyhosts sólo debemos seguir estos sencillos pasos:

```
<pre id="terminal">
	apt-get install denyhosts
	/etc/init.d/denyhosts restart
```

 Todos los IPs usados para intentar loguearse en el servidor que no han provisto un password válido en tres intentos son listados en /etc/hosts.deny y son baneados permanentemente de acceder al puerto del ssh.

 <span style="font-size:16px;"><u>**Restringir el comando "su" al grupo de administradores.**</u></span>

 Ya nuestro servidor no permite accesos usando el usuario "root", pero si alguien adivina o roba la credencial de un usuario y accede al sistema en teoría podría ejecutar un "su -" para ganar privilegios de administrador. Es por ello que siempre es una buena práctica, especialmente cuando hay varios "usuarios" con acceso a un servidor el restringir el uso del comando "su" sólo para aquellos usuarios que pertenecen al grupo de administadores. Esto lo conseguimos a través de estos comandos:

```
<pre id="terminal">
groupadd admin
usermod -a -G admin <tu_usuario>
dpkg-statoverride --update --add root admin 4750 /bin/su
</tu_usuario>
```

 Reemplaza <tu_usuario> por el usuario que utiliza para loguearse al servidor. Para verificar que los cambios están hechos ejecute este comando y debería de ver una respuesta similar: </tu_usuario>

```
<pre id="terminal">
# ls -lh /bin/su

-rwsr-x--- 1 root admin 31K 2010-01-26 17:09 /bin/su
```

 <u><span style="font-size:16px;">**Asegurar los parámetros de red por defecto.**</span></u>

 Para hacer esto debemos editar el archivo "/etc/sysctl.conf" y agregar estas líneas:

 <span style="color:#0000FF;"><span style="font-family:courier new,courier,monospace;">**net.ipv4.conf.all.accept\_source\_route=0  
 net.ipv4.conf.default.accept\_source\_route=0  
 net.ipv4.icmp\_echo\_ignore\_broadcasts = 1  
 net.ipv4.icmp\_echo\_ignore\_all = 1  
 net.ipv4.conf.all.log\_martians = 1**</span></span>

 Luego de ello, debemos reiniciar los valores por defecto de las conección de red, ejecutando este comando:

 <span style="font-family:courier new,courier,monospace;">**sysctl -p**</span>

 Básicamente lo que hemos hecho es prohibir redirección de paquetes originados fuera y bloquear el protocolo ICPM (ping). Si vamos a usar este servidor como una VPN, pues entonces no debemos aplicar las dos primeras líneas que deshabilitan el source route.

 <span style="font-size:16px;"><u>**Instalar software de búsqueda de rootkits.**</u></span>

 Las dos opciones más conocidas que tenemos aquín son [rkhunter](http://rkhunter.sourceforge.net/) y [Chkrootkit](http://freecode.com/projects/chkrootkit). En lo personal recomiendo rkhunter, pero se puede usar cualquiera de los dos. Lo importante es tener al menos uno de ellos y ejecutarlo con regularidad para detectar alguna intrusión en nuestro sistema. Para intalar rkhunter en Ubuntu sólo debemos ejecutar este comando:

 <span style="font-family:courier new,courier,monospace;">**apt-get install rkhunter**</span>

 Para usar rkhunter, primero debemos actualizar su DB de vulnerabilidades conocidas con este comando:

 <span style="font-family:courier new,courier,monospace;">**rkhunter –update**</span>

 Luego ya podemos ejecutar una búsqueda en nuestro sistema con este comando:

 <span style="font-family:courier new,courier,monospace;">**rkhunter –check**</span>

 Espero que esta breve tutorial de las normas de seguridad básicas a tener en cuenta en un servidor de producción les haya servido y permita mejorar la seguridad de su red y de Internet como un todo.