---
id: 34
title: 'Una introducción a la virtualización'
date: '2013-05-30T09:34:43-04:00'
author: volkan68
layout: post
guid: 'http://websistente.com/blog/?p=34'
permalink: /una-introduccion-a-la-virtualizacion/
categories:
    - Virtualización
---

 Todo lo que conocemos hoy como [Cloud Computing](http://es.wikipedia.org/wiki/Computaci%C3%B3n_en_la_nube) (la tan famosa "***nube***"), no existiría si no se hubiera creado la "virtualización". La virtualización, es la creación de una computadora a través de software, en otras palabras en vez de usar chips y ponerlos juntos para crear una aquitectura de computacional, se crean un programa que simula/emula esos chips. De alli que la virtualización comenzara con la emulación de arquitecturas antiguas a través de una solución software que nos permite correr todos los programas existentes para la arquitectura que emulamos. Aunque nos cueste creerlo la virtualización/emulación de hardware se remonta a [mediados de los años 60](http://www.everythingvm.com/content/history-virtualization), con IBM realizando investigaciones sobre el tema en el "[Cambridge Scientific Center](http://en.wikipedia.org/wiki/Cambridge_Scientific_Center)".

 En un principio el [IBM/PC](http://es.wikipedia.org/wiki/IBM_PC) fue una computadora monousuario/monotarea de 16 bits y con una RAM máxima de 1 MB (si un megabyte), que se hizo popular por su gran catálogo de software e inició una revolución a tal punto que hizo que la categoría a la que pertenecía "[microcomputadoras](http://es.wikipedia.org/wiki/Microcomputadora)" quedara olvidada y fuera renombrada como "Personal Computers", "Computadoras Personales" o PCs. El mercado creció tanto que la arquitectura PC se extendió en todas direcciones llegando a matar incluso a las famosas "[minicomputadoras](http://es.wikipedia.org/wiki/Minicomputadora)". Las PCs evolucionaron, se convirtieron en "Workstations" y "Servers", su potencia creció y su costo disminuyó. La antigua y limitada arquitectura PC de 16 bits fue reemplazada por la más poderosa de 32 bits con la introducción del [i386](http://es.wikipedia.org/wiki/Intel_80386), que luego como ya sabemos ha sido superada por la arquitectura multinúcleo de 64 bits de años recientes. Pero, la entonces nueva arquitectura de 32 bits rompió la compatibilidad de hardware con el PC original, con lo cual muchos juegos y aplicaciones dejaron de funcionar, razón por la cual aparecieron emuladores de la arquitectura del PC original entre ellos el más popular fue [Bosh](http://en.wikipedia.org/wiki/Bochs), aunque en la actulidad el emulador más popular es [Qemu](http://wiki.qemu.org/Main_Page).

 El crecimiento exponencial de la potencia del hardware, su abaratamiento y las crecientes necesidades de los departamentos de computo hizo natural que se pensara en particionar la capacidad de computo de los servidores en instancias más pequeñas que serian asignadas por departamentos. Es decir un servidor virtual o VPS (Virtual Private Server) es una máquina virtual que corre independientemente, puede ser reiniciada sin afectar el funcionamiento del host u otros virtuales sobre el mismo host, posee sus propios accesos a la red y librerías. Para todo fin práctico los VPS pueden ser vistos como una máquina real situada en un data center. Visto de este modo la virtualización se entiende mejor como el conjunto de metodologías y técnicas que permiten dividir los recursos de cómputo de una computadora lo suficientemente poderosa llamada server (o servidor) en varios entornos más pequeños llamados máquinas virtuales o VPS.

 En la actualidad son conocidas tres técnicas que permiten la virtualización:

 **Virtualización de hardware**: En esta técnica se emula una arquitectura hardware real (como la x86) o ficticia (como la máquina virtual de Java), el software usado para virtualización presenta a las máquinas virtuales una capa de abstracción de hardware que toma control de todos los requerimientos de las máquinas virtuales en lo referente al acceso a los recursos físicos de la máquina que los hospeda (el host). Esta aproximación permite ejecutar cualquier sistema operativo en las máquinas virtuales sin modificación alguna, ya que desde la perspectiva de la máquina virtual se está corriendo sobre una máquina real. Obviamente esta solución introduce una penalización en la performance de la máquina virtual si la comparamos con una máquina real, porque la capa adicional introducida para emular el hardware, conocida como hypervisor ([hipervisor](https://es.wikipedia.org/wiki/Hipervisor)) usa recursos del host tambien. Sin embargo la flexibilidad introducida de poder correr muchos sistemas operativos diferentes dentro de un mismo servidor físico han hecho a esta solución una de las más populares. Productos de virtualización que siguen esta técnica son por ejemplo: [VMWare](http://www.vmware.com/), [VirtualBox](https://www.virtualbox.org/), [QEMU](http://wiki.qemu.org/Main_Page), [XenServer](http://www.citrix.com/products/xenserver/overview.html), [KVM](http://www.linux-kvm.org/page/Main_Page), [Microsoft Virtual Server](http://www.microsoft.com/windowsserversystem/virtualserver/), entre otros.

 **Paravirtualización**: Esta técnica requiere que los kernel de los sistemas operativos de las máquinas virtuales sean modificados para que en lugar de hacer uso del hardware real, se llame al hypervisor que en este caso recibe el nombre de Virtual Machino Monitor (VMM). La mayor parte del código se corre nativo en cada máquina virtual porque la arquitectura de hardware tanto del host como de los VPS es la misma, sólo los accesos a los dispositivos físicos es interrumpido por el hypervisor y es la razón por la cual el kernel de las máquinas virtuales debe ser modificado para que sepan como tratar estas interrupciones. Esta técnica es mucho más eficiente que la una virtualización de hardware (ya que la mayor parte del código corre directamente sobre el hardware), pero tiene dos claras limitaciones. La primera es que la arquitectura de hardware del host (el servidor) y las máquinas virtuales debe ser la misma. La segunda limitación es que los kernel de las máquinas virtuales deben ser modificados para que puedan reaccionar ante las interrupciones del VMM. Una de las soluciones de paravirtualización más populares actualmente es [Xen](http://www.xen.org/products/xenhyp.html) que es usado por ejemplo por Amazon para su producto de computación en la nube [EC2](http://aws.amazon.com/ec2/) .

 **Virtualización a nivel de Sistema Operativo**: Esta técnica es mucho más restrictiva que la de paravirtualización, sólo permite ejecutar un sólo tipo de sistema operativo. Esto como consecuencia de que sólo existe un kernel para todas las máquinas virtuales, que no son más que entornos aislados a nivel del user-space. De alli que a este técnica usualmente se le llame de containers (contenedores), ya que básicamente cada máquina virtual es un chroot con esteriodes. Casi todas las plataformas Unix tienen un sistema de containers que permiten particionar una máquina física en varias más pequeñas con su propio usuario root, IP y sistema de archivos. La principal limitación de esta técnica es que sólo se puede ejecutar un sólo tipo de kernel (es decir un sólo tipo de sistema operativo), pero ofrece la mayor densisdad de virtuales por server físico. Debido a que la penalización de virtualización es casi inexistente, pueden aprovecharce mucho más los recursos del sistema. Ejemplos de este tipo de virtualización son [LXC](http://lxc.sourceforge.net/) y [OpenVZ](http://openvz.org/Main_Page) en Linux, [Jails](http://www.freebsd.org/doc/handbook/jails.html) en FreeBSD y [Zones](http://www.oracle.com/technetwork/articles/servers-storage-admin/zones-containers-virtualization-1880908.html) en Solaris.

 Expuestas ya las principales técnicas de virtualización espero que esto les sea de ayuda cuando decidan que servicio de VPS usaran, como es lógico los VPS más económicos son aquellos que usan virtualización a nivel de sistema operativo como OpenVZ, seguidos de cerca en cuanto a precio por aquellos que usan paravirtualización como Xen PV (paravirtualizado), siendo los más caros usualmente aquellos que ofrecen virtualización de hardware como Xen HVM (Hardware Virtual Machine) o KVM.