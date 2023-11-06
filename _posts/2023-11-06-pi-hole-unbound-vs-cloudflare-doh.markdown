---
layout: splash 
title:  PiHole Unbound vs Cloudflared-DOH
description: Diferencias entre los 2 principales métodos de instalación de PiHole seguro (unbound y cloudflared-DOH)
date:   2023-11-06 16:01:15 +0100
categories: configuraciones pi-hole cloudflare doh docker
header:
  overlay_image: /assets/images/pihole--banner.jpg
  overlay_filter: 0.5 
excerpt: |
  Pihole es una app que permite bloquear anuncios y rastreadores al navegar por Internet. 
---
Por defecto Pihole utiliza los servicios de [Google](https://dns.google), [Cloudflare](https://www.cloudflare.com/es-es/application-services/products/dns/), [Quad9](https://www.quad9.net/es/) y otros, según configuración, para resolver los nombres de dominio que se le preguntan.

Cada uno de estos servicios tiene su política de privacidad determinada aunque podríamos resumir en que:

a) No es bueno que una empresa tenga acceso a la lista completa de sitios de Internet a los que nos conectamos,
b) Cualquiera que tenga acceso a los datos que intercambiamos con el servidor de DNS que utiliza Pihole tiene acceso a la lista de sitios a los que nos conectamos.

No existe una solución que permita resolver estos 2 problemas, así que tendremos que optar por aquella que nos parezca más importante.

Para resolver el problema de que una empresa tenga acceso a la lista completa de sitios a los que nos conectamos podemos usar *unbound*, una aplicación que se instala en el contenedor en el que hemos instalado Pihole y que hace las veces de *servidor de DNS recursivo*, conectándose al servicio raíz y a los nodos que corresponda para resolver cada una de las partes de cada dominio.

Por su parte, para resolver el problema de que un tercero pueda obtener la lista de sitios a los que accedemos podemos usar una conexión segura contra Cloudflare, lo que se denomina *cloudflared-doh*. 

Ambas soluciones tienen sus pros y contras y aunque personalmente uso *unbound*, considero que las políticas de privacidad de Cloudflare (no almacena logs que permitan identificar a los usuarios y solo almacena los logs durante 25 horas) son suficientemente buenas como para plantearse su uso.

Elijas la opción que elijas, te dejo [aquí](/assets/bin/pi-hole.tar) la descripción de los contenedores y configuraciones de Pihole para usar tanto *unbound* como *cloudflared-doh*.

[Fuente](https://docs.pi-hole.net)
