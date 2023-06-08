---
layout: splash 
title:  Instalación de Pihole y Cloudflared DoH
description: Instalación de Pihole y Cloudflared DoH (docker)
date:   2023-06-04 16:01:15 +0200
categories: configuraciones pi-hole cloudflare doh docker
header:
  overlay_image: /assets/images/pihole--banner.jpg
  overlay_filter: 0.5 
excerpt: |
  Pihole es una app que permite bloquear anuncios y rastreadores al navegar por Internet. 
---
Pihole es un servidor de DNS (y opcionalmente también un servidor DHCP) que permite bloquear parte de los anuncios y rastreadores que contienen las diversas páginas web que visitamos al navegar por Internet.

La instalación de Pihole se puede hacer de diversas maneras y aquí explicaremos cómo hacerlo usando dockers.

Si solo queremos instalar Pihole, podemos hacerlo fácil y rápidamente usando la receta que indicamos a continuación.

{% highlight yaml %}
version: "3"
services:
  pihole:
    image: pihole/pihole:latest
    container_name: pihole
    environment:
      - TZ=Europe/Madrid
    volumes:
      - ../dockers-data/pi-hole/etc:/etc/pihole
      - ../dockers-data/pi-hole/dnsmasq.d:/etc/dnsmasq.d
    ports:
      - 53:53/tcp
      - 53:53/udp
      - 8080:80/tcp 
    restart: unless-stopped
{% endhighlight %}

Sin embargo, resulta mucho más interesante enlazar nuestra instalación de Pihole con cloudflare porque permite conexiones a sus servidores DNS usando conexiones seguras, lo que impedirá al operador de red que estemos utilizando rastrear los sitios a los que nos conectamos. A esta combinación se la conoce como *Pihole + Cloudflared DoH* y su instalación es muy sencilla siguiendo los siguientes pasos.

1. Crearemos un directorio 'pihole' en el directorio en el que vayamos a ubicar la receta para el *docker-compose*.

2. Creamos un archivo *dockerfile* en dicho directorio con el contenido que indicamos a continuación.

{% highlight text %}
FROM pihole/pihole:latest

MAINTAINER ali azam <ali@azam.email>

EXPOSE 53:53/tcp 53:53/udp 67:67/udp 80:80/tcp

RUN apt-get update \
    && apt-get -y install wget \
    && wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb \
    && apt-get -y purge wget \
    && apt-get install ./cloudflared-linux-amd64.deb \
    && mkdir -p /etc/cloudflared/ \
    && mkdir -p /etc/s6-overlay/s6-rc.d/cloudflared/dependencies.d \
    && touch /etc/s6-overlay/s6-rc.d/cloudflared/type \
    && touch /etc/s6-overlay/s6-rc.d/cloudflared/run \
    && touch /etc/s6-overlay/s6-rc.d/cloudflared/dependencies.d/base \
    && touch /etc/s6-overlay/s6-rc.d/user/contents.d/cloudflared \
    && echo 'longrun' >> /etc/s6-overlay/s6-rc.d/cloudflared/type \
    && echo -e '#!/command/with-contenv bash\ncloudflared' >> /etc/s6-overlay/s6-rc.d/cloudflared/run

COPY ./config.yml /etc/cloudflared/config.yml

ENTRYPOINT /s6-init
{% endhighlight %}

(observad que este archivo dockerfile se basa en el docker estándar de pihole pero con unos pequeños cambios).

3. Crearemos un archivo *config.yml* en dicho directorio con el contenido que indicamos a continuación.

{% highlight yaml %}
proxy-dns: true
proxy-dns-port: 5053
proxy-dns-upstream:
  - https://1.1.1.1/dns-query
  - https://1.0.0.1/dns-query
{% endhighlight %}

4. Creamos la receta docker fuera del directorio que hemos creado anteriormente.

{% highlight yaml %}
version: "3"

services:
    pihole:
      build:
        context: pihole
        dockerfile: dockerfile
      container_name: pi-hole
      environment:
        TZ: 'Europe/Madrid'
      volumes:
        - ../dockers-data/pi-hole/etc:/etc/pihole
        - ../dockers-data/pi-hole/dnsmasq.d:/etc/dnsmasq.d
      ports:
        - 53:53/tcp
        - 53:53/udp
        - 8080:80/tcp        
      restart: unless-stopped

networks:
  default:
    external: true
    name: nginxpm_network
{% endhighlight %}

(Darse cuenta que en esta receta hacemos referencia al archivo *pihole/dockerfile* que hemos creado en el paso 1)

Tanto si usamos la versión normal de *Pihole* como la versión *Pihole + cloudflared DoH*, levantaremos el docker con el comando 

{% highlight bash %}
docker-compose -f pihole.yml up -d
{% endhighlight %}

Lo más probable es que nuestra instalación de Linux incluyera ya un servidor de DNS, en cuyo caso recibiremos un error al iniciar el docker: *Error starting userland proxy: listen tcp4 0.0.0.0:53: bind: address already in use.* 

Eso se debe a que ya hay una aplicación escuchando (y sirviendo peticiones) en el puerto 53 (que es el puerto estándar de DNS). Procederemos a parar dicha app, con los siguientes comandos (que ejecutaremos con permisos de superusuario).

{% highlight bash %}
systemctl disable systemd-resolved.service
systemctl stop systemd-resolved
{% endhighlight %}

Hecho esto podremos reiniciar el contenedor de Pihole, esta vez ya sin ningún error...

{% highlight bash %}
docker container stop pihole
docker-compose -f pihole.yml up -d
{% endhighlight %}

Una vez iniciado el contenedor, podemos acceder a los logs con el comando *docker logs pihole -f*. Es posible que observemos en los logs un mensaje indicando que 
la resolución DNS no está disponible ([✗] DNS resolution is currently unavailable* o [✗] DNS resolution is not available*). Esto se debe a que el contenido del archivo */etc/resolv.conf* no es el correcto, por lo que procederemos a editarlo y dejarlo como indicamos a continuación.

{% highlight text %}
search home
nameserver 127.0.0.1
{% endhighlight %}

En este punto volveremos a reiniciar el contenedor de Pihole, si fuera el caso, procediendo a comprobar que la resolución DNS está ahora activa...

Antes de acceder a la configuración web de Pihole, procederemos a crear un password de administración, que será el que utilicemos para autenticarnos en la interfaz de gestión.

{% highlight bash %}
docker exec -it pihole bash
pihole -a -p
exit
{% endhighlight %}

Ahora sí, podremos ya acceder a la interfaz de Pihole para la configuración: http://[ip]:8080/admin (la IP será la de la máquina en la que estamos ejecutando el docker).

Lo primero que haremos es apuntar nuestro Pihole a la instalación de Cloudflare DoH, para lo que accederemos a *Settings* -> *DNS*, desmarcaremos todas las opciones que se hayen activas en la columna de la izquierda, introduciremos *127.0.0.1#5053* en el campo valor de *Custom 1 (IPv4)* (columna de la derecha) y guardaremos los cambios haciendo clic en el botón *save* abajo a la derecha.

Adicionalmente, accederemos a la configuración de nuestro router y, si es un router neutro, procederemos a cambiar la configuración DNS para que el servidor primario sea nuestro Pihole, y el secundario el que prefiramos (1.1.1.1 para Cloudflare, que sería el recomendado, 8.8.8.8 para el caso de Google o cualquier otro de nuestra elección) procediendo a continuación a reiniciar el router para que los cambios sean efectivos.

En caso que nuestro router sea de operador, lo más probable es que no nos permita cambiar el servidor de DNS, por lo que tendremos que cambiarlo manualmente en cada uno de los equipos que queramos.

[Fuente](https://github.com/AzamServer/pihole-doh)