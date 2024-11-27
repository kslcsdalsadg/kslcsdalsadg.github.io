---
layout: splash
title:  Configuración de un contenedor con NordVPN para usarlo de punto de acceso a Internet de otros
description: Configuración de un contenedor con NordVPN para usarlo de punto de acceso a Internet de otros
date:   2024-11-27 6:45:15 +0100
categories: configuraciones docker vpn nordvpn proxy soulseek qbittorrent
header:
  overlay_image: /assets/images/nordvpn--banner.jpg
  overlay_filter: 0.5 
excerpt: |
  NordVPN no es, seguramente, la más privada de las VPN que puedas encontrar en el mercado. Mantiene oficinas en Estados Unidos y otros países del núcleo de los llamados "14 ojos", lo que no es bueno, pero tiene muchos servidores, bastantes más que otras empresas más **privacy-friendly** y, además, tiene unos precios muy competitivos.
---
En este post enseñaremos cómo crear un contenedor que se conecte a *NordVPN* y nos permita definir otros que se conecten a Internet usando dicha conexión.

En [GitHub](https://github.com/bubuntux/nordvpn) el usuario *bubuntux* ya se ha encargado de realizar el trabajo difícil y se encarga del mantenimiento de la imagen del contenedor, por lo que simplemente necesitaremos crear un archivo con la receta, tal como se especifica en las siguientes líneas:

{% highlight yaml %}
version: "3"
services:
  nordvpn-service:
    image: ghcr.io/bubuntux/nordvpn
    container_name: nordvpn-service
    cap_add:
      - NET_ADMIN               
      - NET_RAW                 
    environment:                
      - TOKEN=f6f2bb45...     
      - CONNECT=es
      - TECHNOLOGY=NordLynx
      - NETWORK=192.168.1.0/24  
    sysctls:
      - net.ipv6.conf.all.disable_ipv6=1  # Recomended if using ipv4 only
{% endhighlight %}

Las variables de entorno *CONNECT* y *TECHNOLOGY* nos permiten decidir a qué país nos conectaremos y con qué tecnología (recomendamos *NordLynx*, que es la versión de NordVPN del protocolo Wireguard).

En cuanto al *TOKEN*, hay que generarlo desde la cuenta de [NordVPN](https://my.nordaccount.com/es/dashboard/nordvpn/access-tokens/) y es esencial mantenerlo en secreto, como es lógico.

Siempre que sea posible es una buena práctica generarse uno mismo las imágenes y no usar las que otro ha generado y que podrían ser algún caballo de Troya más o menos importante.

En este caso, podríamos hacerlo fácilmente usando la receta a continuación:

{% highlight yaml %}
services:
  nordvpn-service:
    build:
      context: nordvpn/nordvpn-service
      dockerfile: dockerfile
    container_name: nordvpn-service
    cap_add:
      - NET_ADMIN
      - NET_RAW
    environment:
      - TOKEN=f6f2bb45...     
      - CONNECT=es
      - TECHNOLOGY=NordLynx
      - TOKEN=f6f2bb45...     
    sysctls:
      - net.ipv6.conf.all.disable_ipv6=1  # Recomended if using ipv4 only
{% endhighlight %}

Y descomprimiendo [los archivos necesarios](/assets/bin/nordvpn-service.tar) en el directorio *nordvpn/nordvpn-service*.

Recuerda que para generar la imagen tendrás que ejecutar el comando *docker compose -f nordvpn.yaml build* y para *encender* el contenedor el comando es *docker compose -f nordvpn.yaml up -d --force-recreate* (asumiendo que *nordvpn.yaml* es el archivo en el que has guardado la receta docker).

Finalmente indicar que el servicio está configurado para comprobar la conexión cada cierto tiempo, procediendo a la reconexión en caso de detectarse algún error, así como para bloquear la conexión a Internet fuera de la VPN (*killswitch*).

***Aplicaciones prácticas***

A modo de ejemplo, indicaremos varias aplicaciones prácticas y cómo configurarlas para que se conecten a Internet usando la conexión VPN descrita anteriormente.

Para que todo funcione correctamente es necesario tener en cuenta 2 consideraciones:

a) Debido a las referencias que realizaremos, las definiciones de los contenedores correspondientes tendrán que estar forzosamente en el mismo archivo yaml en el que definamos la receta del servicio *nordvpn*.

b) La descripción de puertos de los diferentes contenedores deberá realizarse en el contenedor *nordvpn*, ya que es éste el que define la conectividad.

**Proxy**

Un proxy es un puente o pasarela que nos permite conectarnos a Internet de forma indirecta y que, en nuestro caso, nos permitirá usar la conexión VPN establecida anteriormente para conectar cualquiera de nuestros dispositivos, lo que puede ser útil para evitar la limitación de conexiones simultáneas que establece *NordVPN* y, en general, cualquier servicio VPN.

La parte de la receta docker correspondiente a este servicio es la siguiente:

{% highlight yaml %}
  nordvpn-proxy:
    build:
      context: nordvpn/nordvpn-proxy
      dockerfile: dockerfile
    container_name: nordvpn-proxy
    network_mode: service:nordvpn-service
    depends_on:
      - nordvpn-service
    restart: unless-stopped
{% endhighlight %}

Y [aquí](/assets/bin/nordvpn-proxy.tar) los archivos que permiten la generación de la imagen.

Notad que, en la configuración del servicio, referenciamos al contenedor *nordvpn-service*, indicando que este nuevo contenedor depende de aquel y, sobretodo, que debe usarse para establecer la conexión a Internet.

El proxy está configurado para dar servicio en el puerto 3128, así que habrá que añadir ese puerto en la lista de puertos del contenedor *nordvpn-service*, como habíamos indicado antes, que quedaría de la siguiente manera:

{% highlight yaml %}
services:
  nordvpn-service:
    build:
      context: nordvpn/nordvpn-service
      dockerfile: dockerfile
    container_name: nordvpn-service
    cap_add:
      - NET_ADMIN
      - NET_RAW
    environment:
      - TOKEN=f6f2bb45...     
      - CONNECT=es
      - TECHNOLOGY=NordLynx
      - TOKEN=f6f2bb45...   
    ports:
      - 3128:3128
    sysctls:
      - net.ipv6.conf.all.disable_ipv6=1  # Recomended if using ipv4 only
{% endhighlight %}

**qbittorrent**

Modificaremos ligeramente la receta docker descrita en [Github](https://github.com/linuxserver/docker-qbittorrent) para definir un servicio que nos permita descargar archivos *torrent* a través de la VPN.

Notad que también podríamos hacerlo usando la conexión proxy descrita en el apartado anterior.

{% highlight yaml %}
  qbittorrent:
    image: ghcr.io/linuxserver/qbittorrent
    container_name: qbittorrent
    network_mode: service:nordvpn-service
    volumes:
      - ../dockers-data-extra/qbittorrent/downloads:/downloads
    environment:
      - WEBUI_PORT=8099
    depends_on:
      - nordvpn-service
    restart: unless-stopped
{% endhighlight %}

Recordad añadir el puerto 8099 a la lista de puertos del servicio *nordvpn-service*, ya que será el que nos permita acceder a la UI de qbittorrent (http://localhost:8099) y el puerto del protocolo torrent (51413).

**soulseek**

Soulseek es un servicio *p2p* que permite compartir archivos, sobretodo archivos de música, entre los usuarios, y fue bastante popular hace algunos años, al abrigo de *Napster* y similares.

Igual que con *qbittorrent*, simplemente usaremos el servicio descrito en [Github](https://github.com/slskd/slskd) modificándolo para que use la conexión VPN.

{% highlight yaml %}
  soulseek:
    image: slskd/slskd
    container_name: soulseek
    network_mode: service:nordvpn-service
    volumes:
      - ../dockers-data-extra/soulseek:/app
    environment:
      - SLSKD_REMOTE_CONFIGURATION=true
    restart: unless-stopped
{% endhighlight %}
