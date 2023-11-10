---
layout: splash 
title:  ¿Cómo tener varios Pi-hole en la misma máquina física?
description: En este artículo explicaremos como poder tener 2 o más servidores DNS (Pi-Hole en nuestro caso) en una misma máquina física
date:   2023-11-10 06:01:15 +0100
categories: configuraciones pi-hole docker
header:
  overlay_image: /assets/images/pihole--banner.jpg
  overlay_filter: 0.5 
excerpt: |
  Pihole es una app que permite bloquear anuncios y rastreadores al navegar por Internet. 
---
Si te planteas poner tu propio servidor de DNS, es muy recomendable tener más de una instancia y, a poder ser, que se ejecuten en máquinas diferentes. 

Si solo tienes un servidor de DNS, o si tienes varios pero se ejecutan en la misma máquina, y la apagas, te quedarás sin servicio de traducción de nombres en tu 
red lo que impedirá que las personas que la usan accedan normalmente a Internet.

En cualquier caso, en ocasiones puede ser beneficioso tener 2 servidores de DNS en la misma máquina, por ejemplo para no perder el acceso a Internet mientras uno de ellos se actualiza.

Como no podemos conectar más de un proceso al mismo puerto, el 53 en el caso del servidor de DNS, tendremos que añadir otra (u otras) interfaces de red, de forma que cada servidor de DNS escuchará en el puerto 53 de una de estas interfaces. 

Esto podremos hacerlo añadiendo más tarjetas de red a nuestro equipo o bien añadiendo interfaces virtuales. En Ubuntu Desktop es posible hacerlo accediendo a la configuración de red o editando la configuración de *netplan*, tal como se indica [aquí](https://upcloud.com/resources/tutorials/configure-floating-ip-ubuntu).

En caso de que las interfaces que hayamos añadido sean virtuales deberemos acceder a la configuración del router para indicar que nos debe reservar las direcciones IP que hayamos seleccionado. Si no lo hacemos corremos el riesgo de que el servidor DHCP del router las asigne a otra máquina, lo que causaría problemas.

Una vez hecho esto deberemos indicarle a cada uno de los servidores DNS que deben atender peticiones en el puerto 53 pero en una IP concreta. En el caso de que estemos ejecutando 
Pi-Hole en un docker esto se hace añadiendo la dirección IP en el apartado *ports*, de la siguiente manera:

{% highlight yaml %}
    ports:
      - direccion_ip:53:53/tcp
      - direccion_ip:53:53/udp
      - direccion_ip:8080:80/tcp
{% endhighlight %}

Finalmente, tendremos que modificar la configuración de DNS de nuestra máquina, y posiblemente del router, para que use los nuevos servidores (y las nuevas direcciones)
que acabamos de añadir, tal como se indica en este otro [post](/como-cambiar-el-dns-en-ubuntu/).