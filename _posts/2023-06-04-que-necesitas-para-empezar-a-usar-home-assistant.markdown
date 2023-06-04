---
layout: splash 
title:  "¿Qué necesitas para empezar a usar Home Assistant?"
date:   2023-06-04 16:01:15 +0200
categories: configuraciones domótica home-assistant
header:
  overlay_image: /assets/images/home-assistant--banner.jpg
  overlay_filter: 0.5 
excerpt: |
  Home Assistant es el software de gestión de domótica open source más extendido, pero puede ser difícil de configurar para un usuario nobel. 
---
Siempre recomendamos la instalación en un ordenador que no se vaya a usar para otras tareas, o por lo menos que no se vaya a usar para tareas de usuario, ya que podríamos tener problemas de rendimiento. La mejor opción de instalación es, en nuestra opinión, la de instalarlo en un equipo con Ubuntu, preferiblemente una [versión server](https://ubuntu.com/download/server) (no necesitamos de una interfaz gráfica que ocupe parte de la memoria RAM) y que sea LTS (que son las que aportan soporte extendido).

Sobre la instalación limpia de Ubuntu procederemos a instalar el [gestor de dockers](https://docs.docker.com/engine/install/ubuntu/) y seguidamente los contenedores que indicaremos:

**MQTT**

Lo más habitual es que queramos añadir entidades zigbee a nuestra instalación de Home Assistant, para lo cual necesitaremos instalar los contenedores de [MQTT](https://www.manelrodero.com/blog/instalacion-de-mosquitto-mqtt-broker-en-docker) y [Zigbee2MQTT](https://www.manelrodero.com/blog/instalacion-de-zigbee2mqtt-en-docker).

**Cloudflare DDNS**

En ocasiones necesitaremos conectar desde fuera de nuestra red a nuestra instalación de Home Assistant, para lo que recomendamos usar una VPN, lo que nos permitirá abrir un tunel desde fuera de nuestra red al interior de la misma y acceder a todos los servicios que tengamos instalados en ella de forma segura.

Recomendamos usar [Wireguard](https://www.manelrodero.com/blog/instalacion-de-wireguard-en-docker), que es un protocolo de VPN Open Source bastante novedoso y que usa pocos recursos

Además, si nuestro operador de red no nos permite comprar una dirección IP estática, necesitaremos comprar un dominio y utilizar algún servicio que nos permita [asignar la dirección IP cambiante de nuestro router a dicho dominio](https://www.manelrodero.com/blog/dns-dinamico-gratuito-usando-cloudflare), lo que nos permitirá facilitar la conexión desde el exterior.

**Home Assistant**

Preparado el entorno y preparados los contenedores básicos, podemos plantearnos ya la instalación de [Home Assistant](https://www.manelrodero.com/blog/instalacion-de-home-assistant-en-docker).

**Addons de Home Assistant**

Una vez instalado Home Assistant, procederemos sí o sí a la instalación de [Hacs](https://www.manelrodero.com/blog/instalacion-de-hacs-en-home-assistant-docker), que nos permitirá instalar otros paquetes como por ejemplo [Alarmo](https://www.youtube.com/watch?v=hkpYFFxZ-G4) y [Frigate](https://www.youtube.com/watch?v=w0EEM9H8hBk).

Para usar Home Assistant nos puede servir prácticamente cualquier hardware, incluso una Raspberry Pi (preferiblemente de 4a. generación), pero si vamos a usar Frigate necesitaremos un hardware mínimo (un Intel i5 de 4a. generación con 8GB de RAM o superior) y muy recomendablemente una [tarjeta Coral](https://coral.ai/products/accelerator/) para el procesamiento de imágenes.

**Notificaciones**

Y claro, si has instalado Frigate querrás recibir notificaciones en Telegram, para lo cual tendrás que ver los sigueintes artículos: [enviar mensajes a Telegram](/configuraciones/domótica/home-assistant/enviar-mensajes-a-telegram-desde-home-assistant) y [enviar fotos y vídeos de Frigate a Telegram](/configuraciones/domótica/home-assistant/frigate/enviar-fotos-y-videos-de-frigate-a-telegram-desde-home-assistant).




