---
layout: splash 
title:  Cómo cambiar el DNS en Ubuntu (con resolv)
description: Cómo cambiar el DNS en Ubuntu (con resolv)
date:   2023-11-10 05:51:15 +0100
categories: dns ubuntu resolv
header:
  overlay_image: /assets/images/dns--banner.jpg
  overlay_filter: 0.5 
excerpt: |
  El DNS es el servicio que traduce los nombres de dominio (*www.google.es*, por ejemplo) a la dirección IP de la máquina en la que se ubica el servicio.
---
Para poder acceder a una máquina en una red necesitamos conocer su dirección IP, que es el número que la identifica de manera única, como el DNI de las personas, la matrícula de los coches, etc.

A diferencia de las direcciones IP, que están formadas por números (IPv4) o números y letras (IPv6) ordenadas de cualquier manera, los nombres de dominio están formados por palabras fácilmente reconocibles por las personas y, por tanto, fáciles de recordar. Los nombres de dominio se asocian a las direcciones IP y es el servidor de DNS es el que se encarga de realizar la traducción de estos nombres a sus correspondientes direcciones IP.

Los nombres de dominio se conforman generalmente con 2 o más palabras separadas por puntos de forma que la más general es la que se coloca más a la derecha, mientras que la más específica es, por el contrario, la que se coloca más a la izquierda. Así, para resolver la dirección IP de un dominio hay que hacer sucesivas consultas a los gestores de cada una de las *palabras* que conforman el nombre, de derecha a izquierda.

Aunque no siempre es así, los nombres de dominio están formados por 3 palabras, que identifican (de derecha a izquierda) el tipo de dominio (por ejemplo *com*), la empresa propietaria del mismo (por ejemplo *google*) y la app o protocolo asociada a ese dominio (por ejemplo *www* para la web, *mail* para el correo electrónico, etc).

Los operadores de red suelen implementar servidores de DNS que son los que se encargan de conectar con los registradores de forma recursiva y realizar la traducción, pero también podemos instalar nuestros propios servicios de traducción de nombres, como Pi-Hole, que incorpora además bloqueo de propaganda y otras funcionalidades, amén de un plus de privacidad que no está nada mal en estos días.

Si instalas tu propio servidor de DNS es posible que necesites cambiar la configuración de tu ordenador. En el caso de Ubuntu, esta configuración se almacena en el archivo */etc/systemd/resolved.conf* y concretamente en la clave *DNS*, en la que tendremos que incluir (separados por espacios) las direcciones IP de las máquinas en las que se ubican tus servidores (es bueno tener más de uno). [Nótese que hemos de incorporar las direcciones IP de los servidores, no sus nombres de dominio.]

Si es posible, deberemos incorporar estas direcciones IP al router que nos da acceso a Internet.

Puedes encontrar más información sobre este tema en los siguientes posts: [Instalación Pi-Hole y Cloudflared-DOH](/instalacion-de-pihole-y-cloudflared-doh/) y [Pi-Hole Unbound vs Cloudflared-DOH](/pi-hole-unbound-vs-cloudflare-doh/).
