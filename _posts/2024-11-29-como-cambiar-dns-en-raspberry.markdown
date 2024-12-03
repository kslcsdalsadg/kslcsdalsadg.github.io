---
layout: splash
title:  Cambiar el DNS en una Raspberry PI
description: Cómo cambiar el registro de DNS en una Raspberry PI
date:   2024-11-29 6:45:15 +0100
categories: configuraciones raspberry dns
header:
  overlay_image: /assets/images/raspberrypi--banner.jpg
  overlay_filter: 0.5 
excerpt: |
  En muchas ocasiones usaremos una (o más) Raspberry Pi para configurar nuestro propio servidor de DNS. De hecho, es muy normal usar 2 de ellas para establecer el servidor DNS primario y el secundario.
---
En ese caso, es fundamental configurar el DNS de las Raspberry para que no usen los datos que les envíe el router, ya que, por ejemplo, el servicio [Pi-Hole unbound](/_posts/2023-11-06-pi-hole-unbound-vs-cloudflare-doh.markdown) falla si el servidor de DNS primario coincide con la IP del dispositivo.

Para cambiar el DNS deberemos ejecutar el comando **nmcli device show**, que nos enseñará como resultado los datos de conectividad del dispositivo. Entre esos valores nos quedaremos con el valor asociado a la clave **GENERAL.CONNECTION** 

{% highlight text %}
...
GENERAL.CONNECTION:                     Wired connection 1
...
IP4.DNS[1]:                             192.168.31.5
IP4.DNS[2]:                             192.168.31.6
...
{% endhighlight %}

Seguidamente, ejecutaremos los comandos: 

{% highlight shell %}
sudo nmcli conn modify "Wired connection 1"  ipv4.dns "192.168.31.7 192.168.31.8"
sudo nmcli conn modify "Wired connection 1"  ipv4.ignore-auto-dns yes
sudo systemctl restart NetworkManager
{% endhighlight %}

sustituyendo los valores *"Wired connection 1"* y *"192.168.31.7 192.168.31.8"* por el valor asociado a *GENERAL.CONNECTION* y el servidor o servidores de DNS que queramos establecer, respectivamente.

(Para los curiosos, los comandos anteriores realizan las siguientes funciones: 
  1. establecer el servidor DNS a utilizar, 
  2. indicar al sistema que debe usarse el valor anterior en lugar del valor que proporcione la configuración automática (que seguramente será la establecida en el router) y 
  3. reiniciar el servicio para aplicar los cambios).

A continuación, podemos volver a ejecutar el comando **nmcli device show** para comprobar que los cambios se han guardado correctamente, lo que podremos ver también en el archivo */etc/resolv.conf*.

