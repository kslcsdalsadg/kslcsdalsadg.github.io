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

Cada uno de estos servicios tiene diferentes políticas de privacidad, siendo más beneficiosas para el usuario las de Cloudflare o Quad9 que las de Google, aunque el tiempo de respuesta de los servidores de Google es, por otro lado, inferior al del resto.

En cualquier caso, toda la información sobre los sitios a los que accedemos va a pasar por los servicios de Cloudflare, Quad9 o Google, que podrían utilizarla según su propia conveniencia.

Para evitar esto podemos instalar **unbound**, que es un servicio recursivo de resolución de nombres que accede directamente a la fuente de los datos, los servidores de dominio raíz, para realizar la traducción de nombres a direcciones IP, de la misma forma en que lo hacen Cloudflare, Quad9 o Google. Pero entonces, ¿qué conseguimos con esto? Pues básicamente diversificar y que ningún servidor tenga la información completa de los sitios por los que nos movemos. 

Así, cuando Pihole necesite conocer la dirección IP asociada a un nombre de dominio, éste preguntará al servicio **unbound**, que se aloja en su mismo contenedor y, solo si **unbound** desconoce esta dirección, preguntará al servicio raíz y a los servicios oficiales relacionados, dependiendo del nombre que necesitemos resolver.

Todas las peticiones de resolución de nombres se enrutan a través de un puerto determinado, lo que permite a agentes externos, como por ejemplo el operador con el que tenemos contratada la fibra, acceder muy fácilmente a esta información.

Podemos evitar esto usando el *servicio de traducción de nombres bajo https que ofrece Cloudflare desde hace ya bastante tiempo*. Así, todas nuestras peticiones viajarían hasta los servidores de Cloudflare por un canal seguro lo que evitaría que fueran observadas por algún tercero a costa de entregárselas en bandeja a Cloudflare, si bien la política de privacidad de este servicio garantiza, a día de hoy, que no almacenan registros que nos puedan identificar y que no guardan datos más que por 25 horas.

[Contenedores y archivos de configuración](/assets/bin/pi-hole.tar)

[Fuente](https://docs.pi-hole.net)
