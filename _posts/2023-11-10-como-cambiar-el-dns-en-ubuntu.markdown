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
Para poder acceder a una máquina en una red necesitamos conocer la dirección IP de la misma, que es el número que la identifica de manera única, como el DNI de las personas, la matrícula de los coches, etc.

Los nombres de dominio son nombres fáciles de recordar que se asocian a direcciones IP, y el servidor de DNS es el que se encarga de realizar la traducción de estos nombres a sus direcciones IP correspondientes.

Los nombres de dominio se conforman con diferentes palabras separadas por puntos de forma que la más general es la que se coloca más a la derecha, mientras que la más específica es, por el contrario, la que se coloca más a la izquierda.

Existe toda una infrastructura a nivel mundial para gestionar estos nombres de dominio, de forma que cada parte del nombre del mismo (de derecha a izquierda) es gestionado por una entidad registradora, que es la que se encarga de su gestión y, finalmente, la que conoce la dirección IP que corresponde.

Los operadores de red suelen implementar servidores de DNS que son los que se encargan de conectar con los registradores de forma recursiva y realizar la traducción, pero también podemos instalar nuestros propios servicios, como Pi-Hole, que incorporan además bloqueo de propaganda y otras funcionalidades, amén de incorporar un plus de privacidad, que no está nada mal en estos días.

Si instalas tu propio servidor de DNS es posible que necesites cambiar la configuración de DNS que incorpora tu ordenador. En el caso de Ubuntu, esta configuración se almacena en el archivo */etc/systemd/resolved.conf* y concretamente en la clave *DNS*, en la que tendremos que incluir (separados por espacios) las direcciones IP de las máquinas en las que se ubican tus servidores (es bueno tener más de uno). [Nótese que hemos de incorporar las direcciones IP de los servidores, no sus nombres de dominio.]

Si es posible, deberemos incorporar estas direcciones IP al router que nos da acceso a Internet.

Puedes encontrar más información sobre este tema en los siguientes posts: [Instalación Pi-Hole y Cloudflared-DOH](/instalacion-de-pihole-y-cloudflared-doh/) y [Pi-Hole Unbound vs Cloudflared-DOH](/pi-hole-unbound-vs-cloudflare-doh/).
