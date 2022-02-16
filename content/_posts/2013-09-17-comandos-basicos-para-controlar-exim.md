---
id: 73
title: 'Comandos básicos para controlar Exim'
date: '2013-09-17T10:36:13-04:00'
author: volkan68
layout: post
guid: 'http://websistente.com/blog/?p=73'
permalink: /comandos-basicos-para-controlar-exim/
categories:
    - DevOp
    - Networking
---

 <span style="font-size:16px;">[Exim](http://es.wikipedia.org/wiki/Exim) es el servidor de correo usado por defecto en Debian, la distribución que uso en la mayoría de mis proyectos. Exim no es tan popular como Postfix o Sendmail, pero desde mi punto de vista resulta mucho más robusto y escalable que los dos anteriores, pero como dice el refran en gustos y colores no han escrito los autores. Este post contiene los comando básicos que se pueden usar para gestionar la cola de correos de Exim en las operaciones, espero que les sea de utilidad en caso de que les toque lidiar con un VPS debian.</span>

 <span style="font-size:16px;"></span>

 <span style="font-size:16px;"><span style="line-height: 1.6em;">Exim utiliza el message-ID para referirse a los mensajes en su cola, éste message-ID esta formado por caracteres alfanuméricos con mayúsculas y minúsculas (reconoce la diferencia entre ellas), y su formato es como este: XXXXX-YYYYYY-ZZ. La mayoría de los comandos relacionados con la gestión de la cola y el registro de estos mensajes utiliza estos identificadores de mensaje.</span></span>

 <span style="font-size:16px;"><span style="line-height: 1.6em;">Existen tres archivos por cada mensaje en el directorio de cola. Si usted está tratando con estos archivos a mano, en lugar de utilizar los comandos exim apropiadas como se detalla a continuación, asegúrese de editra/remover todos y no dejar a Exim con restos de mensajes en la cola. Evite el manejo de la cola manualmente porque esto podría creale problemas a la larga, en lugar de ello use los comandos descritos más abajo.</span></span>

 <span style="font-size:16px;">Los archivos en el directorio <span style="font-family: 'courier new', courier, monospace;">**/var/spool/exim/msglog**</span> contienen la información de registro (log) para cada mensaje y se nombran igual que el message-ID. Los archivos en el directorio <span style="font-family: 'courier new', courier, monospace;">**/var/spool/exim/**</span> tienen el nombre del message-ID, más un sufijo que denota si es la cabecera del mensaje (-H) o el cuerpo del mensajes (-D). Estos directorios pueden contener subdirectorios más hash para hacer frente a las colas de correo realmente grandes, así que no esperes todo lo que aparezca siempre directamente en la parte superior en los directorios <span style="font-family: 'courier new', courier, monospace;">**/var/spool/exim/**</span> o <span style="font-family: 'courier new', courier, monospace;">**/var/spool/exim/msglog**</span>, cualquier búsqueda usando grep tendrá que ser recursivas. </span>

 <span style="font-size:18px;"><u>**Obtener información básica sobre la cola**</u>:</span>

 <span style="font-size:16px;">Obtener el número de mensaje en la cola:</span>

 <span style="font-size:16px;">**<span style="color: rgb(0, 0, 255);"><span style="font-family: 'courier new', courier, monospace;">root@localhost# exim -bpc</span></span>**</span>

 <span style="font-size:16px;"><span style="line-height: 1.6em;">Obtener un reporte de los mensajes en la cola (hora en que el mensaje entro a la cola, tamaño, message-ID, remitente, destinatario):</span></span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 255);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exim -bp**</span></span></span>

 <span style="font-size:16px;">Obtener un reporte resumen de los mensajes que hay en la cola (cantidad, espacio de disco usado, cuánto tiempo hace desde que llegó el más antiguo, cuánto desde que llegó el mensaje más reciente, todo desglosado por dominios y mostrando los totales):</span>

 <span style="font-size:16px;"><span style="font-family: 'courier new', courier, monospace;"><span style="color: rgb(0, 0, 255);">**root@localhost# exim -bp | exiqsumm**</span></span></span>

 <span style="font-size:16px;">Obtener exactamente que está procesando Exim en este momento:</span>

 <span style="font-size:16px;"><span style="font-family: 'courier new', courier, monospace;"><span style="color: rgb(0, 0, 255);">**root@localhost# exiwhat**</span></span></span>

 <span style="font-size:16px;">Probar como Exim está ruteando los correos a una dirección de correo específica:</span>

 <span style="font-size:16px;"><span style="font-family: 'courier new', courier, monospace;"><span style="color: rgb(0, 0, 255);">**root@localhost# exim -bt alias@localdomain.com  
 user@thishost.com  
 &lt;– alias@localdomain.com  
 router = localuser, transport = local\_delivery  
 root@localhost# exim -bt user@thishost.com  
 user@thishost.com  
 router = localuser, transport = local\_delivery  
 root@localhost# exim -bt user@remotehost.com  
 router = lookuphost, transport = remote\_smtp  
 host mail.remotehost.com \[1.2.3.4\] MX=0**</span></span></span>

 <span style="font-size:16px;">Ejecutar una transacción SMTP desde la línea de comando, como si esta fuera hecha desde el IP asignado al Exim. Este comando nos mostrará todos los checks, ACL y filtrs que son aplicados a ese host en particular. Tenga en cuenta que este mensaje no enviara un mensaje de la cola.</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 255);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exim -bh 192.168.11.22**</span></span></span>

 <span style="font-size:16px;">Display all of Exim's configuration settings:</span>

 <span style="font-size:16px;"><span style="font-family: 'courier new', courier, monospace;"><span style="color: rgb(0, 0, 255);">**root@localhost# exim -bP**</span></span></span>

 <span style="font-size:18px;"><u>**Buscando en la cola con exiqgrep**</u>:</span>

 <span style="font-size:16px;">Exim incluye una utilidad que es bastante agradable para buscar patrones a través de la cola, llamada **exiqgrep**. Es bueno aprender a utilizarla y ponerla en práctica. Si usted no la está usando y si usted no está familiarizado con las distintas banderas que utiliza, puede que probablemente esté haciendo las cosas de la manera difícil, como entubar (pipe) `exim-pb` a otros programas como awk, grep, cut, o `wc – l `. No hay que hacernos la vida más difícil de lo que ya es.</span>

 <span style="font-size:16px;">Existen varios indicadores de que el control del comando exiqgrep para buscar en los mensajes de la cola que coincidan con cierto patrón de búsqueda. Estos controls se pueden combinar para llegar a una búsqueda muy particular.</span>

 <span style="font-size:16px;"><span style="line-height: 1.6em;">Use el indicador -f para buscar en la cola por mensajes de un remitente específico:</span></span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 255);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exiqgrep -f \[luser\]@domain**</span></span></span>

 <span style="font-size:16px;"><span style="line-height: 1.6em;">Use el indicador -r para buscar en la cola por mensajes de un destinatario específico o mensajes enviados a un dominio específico:</span></span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 255);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exiqgrep -r \[luser\]@domain**</span></span></span>

 <span style="font-size:16px;">Use -o to print messages older than the specified number of seconds. For example, messages older than 1 day:</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 255);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exiqgrep -o 86400 \[…\]**</span></span></span>

 <span style="font-size:16px;">Use -y to print messages that are younger than the specified number of seconds. For example, messages less than an hour old:</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 255);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exiqgrep -y 3600 \[…\]**</span></span></span>

 <span style="font-size:16px;">Use -s to match the size of a message with a regex. For example, 700-799 bytes:</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 255);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exiqgrep -s '^7..$' \[…\]**</span></span></span>

 <span style="font-size:16px;">Use -z to match only frozen messages, or -x to match only unfrozen messages.</span>

 <span style="font-size:16px;">There are also a few flags that control the display of the output.</span>

 <span style="font-size:16px;"><span style="line-height: 1.6em;">Use -i to print just the message-id as a result of one of the above two searches:</span></span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 255);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exiqgrep -i \[ -r | -f \] …**</span></span></span>

 <span style="font-size:16px;">Use -c to print a count of messages matching one of the above searches:</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 255);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exiqgrep -c …**</span></span></span>

 <span style="font-size:16px;">Print just the message-id of the entire queue:</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 255);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exiqgrep -i**</span></span></span>

 <span style="font-size:18px;">**<u>Manejando la cola</u>:**</span>

 <span style="font-size:16px;">El binario principal de exim (/usr/sbin/exim) es usado con varios flags para hacer que las cosas pasen en la cola de mensajes.La mayoría de las veces se requiere que uno o más message-ID se especifique en la línea de comandos, es aquí donde el comando "<span style="font-family: 'courier new', courier, monospace;">**exiqgrep -i**</span>" descrito anteriormente se vuelve de gran utilidad.</span>

 <span style="font-size:16px;">Iniciar el procesamiento de la cola de mensajes:</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 205);"><span style="font-family: 'courier new', courier, monospace;">**<span style="line-height: 1.6em;">root@localhost# exim -q -v</span>**</span></span></span>

 <span style="font-size:16px;">Inicial el proecesamiento de la cola de mensajes sólo para los envios locales:</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 205);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exim -ql -v**</span></span></span>

 <span style="font-size:16px;">Borrar un mensaje de la cola:</span>

 <span style="font-size:16px;"><span style="font-family: 'courier new', courier, monospace;"><span style="color: rgb(0, 0, 205);">**root@localhost# exim -Mrm &lt;message-id&gt; \[** </span></span></span><span style="font-family: 'courier new', courier, monospace;"><span style="color: rgb(0, 0, 205);">**<message-id><span style="font-size:16px;"> … \]</span></message-id>**</span></span>

 <span style="font-size:16px;">Congelar un mensaje de la cola:</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 205);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exim -Mf &lt;message-id&gt; \[** </span></span></span><span style="color: rgb(0, 0, 205);"><span style="font-family: 'courier new', courier, monospace;">**<message-id><span style="font-size:16px;"> … \]</span></message-id>**</span></span>

 <span style="font-size:16px;">Descongelar un mensaje de la cola:</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 205);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exim -Mt &lt;message-id&gt; \[** </span></span></span><span style="color: rgb(0, 0, 205);"><span style="font-family: 'courier new', courier, monospace;">**<message-id><span style="font-size:16px;"> … \]</span></message-id>**</span></span>

 <span style="font-size:16px;">Despachar un mensaje, ya sea que este esté congelado o no, o que haya o no alcanzado el número máximo de reintentos:</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 205);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exim -M &lt;message-id&gt; \[** </span></span></span><span style="color: rgb(0, 0, 205);"><span style="font-family: 'courier new', courier, monospace;">**<message-id><span style="font-size:16px;"> … \]</span></message-id>**</span></span>

 <span style="font-size:16px;">Despachar un mensaje, pero sólo si el momento de reenvío se ha alcanzado:</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 205);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exim -Mc &lt;message-id&gt; \[** </span></span></span><span style="color: rgb(0, 0, 205);"><span style="font-family: 'courier new', courier, monospace;">**<message-id><span style="font-size:16px;"> … \]</span></message-id>**</span></span>

 <span style="font-size:16px;">Forzar un mensaje a fallar y enviar un mensaje de rebote como "cancelled by administrator":</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 205);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exim -Mg &lt;message-id&gt; \[** </span></span></span><span style="color: rgb(0, 0, 205);"><span style="font-family: 'courier new', courier, monospace;">**<message-id><span style="font-size:16px;"> … \]</span></message-id>**</span></span>

 <span style="font-size:16px;">Borra todos los mensajes congelados:</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 205);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exiqgrep -z -i | xargs exim -Mrm**</span></span></span>

 <span style="font-size:16px;">Borra todos los mensajes más antiguos que cinco dias (86400 \* 5 = 432000 seconds):</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 205);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exiqgrep -o 432000 -i | xargs exim -Mrm**</span></span></span>

 <span style="font-size:16px;">Congelar todos los mensajes en la cola para un remitente dado:</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 205);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exiqgrep -i -f luser@example.tld | xargs exim -Mf**</span></span></span>

 <span style="font-size:16px;">Ver la cabecera de un mensaje:</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 205);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exim -Mvh &lt;message-id&gt;**</span></span></span>

 <span style="font-size:16px;">Ver el cuerpo de un mensaje:</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 205);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exim -Mvb &lt;message-id&gt;**</span></span></span>

 <span style="font-size:16px;">Ver los logs de un mensaje:</span>

 <span style="font-size:16px;"><span style="font-family: 'courier new', courier, monospace;"><span style="color: rgb(0, 0, 205);">**root@localhost# exim -Mvl &lt;message-id&gt;**</span></span></span>

 <span style="font-size:16px;">Añadir un destinatario a un mensaje:</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 205);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exim -Mar &lt;message-id&gt; &lt;address&gt; \[**</span></span><span style="font-family:courier new,courier,monospace;"><span style="color:#0000CD;">**…\]**</span></span></span>

<address> <span style="font-size:16px;">Reemplazar el destinatario de un mensaje:</span>

 <span style="font-size:16px;"><span style="color: rgb(0, 0, 205);"><span style="font-family: 'courier new', courier, monospace;">**root@localhost# exim -Mes &lt;message-id&gt; &lt;address&gt;**</span></span></span>

<div> </div></address>