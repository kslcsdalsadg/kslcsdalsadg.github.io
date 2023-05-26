---
layout: splash
title:  "Instalación y configuración de Frigate"
date:   2023-05-23 18:45:15 +0200
categories: configuraciones domótica frigate
header:
  overlay_image: /assets/images/frigate--banner.jpg
  overlay_filter: 0.5 
excerpt: |
  Frigate es un NVR de código abierto que permite la detección de objetos por IA en tiempo real y en local.
---
La mayoría de las cámaras para seguridad privada ofrecen detección de objetos, bien sea usando detección de movimiento (las más) o algún tipo de Inteligencia Artificial (las menos), y la práctica totalidad almacenan los vídeos e imágenes en su cloud, lo que pone el interior de tu casa a disposición de casi cualquiera. 

[Frigate](https://frigate.video) es un NVR de código abierto que permite la detección de objetos por IA en tiempo real y es interesante por varios motivos, entre los que destaca que el procesamiento de las imágenes se realiza en local, aunque también es interesante que permite combinar cámaras de diferentes fabricantes, soportando la práctica totalidad de protocolos de acceso.

En conjunción con una o más tarjetas [Coral](https://coral.ai) es capaz de procesar en tiempo real múltiples cámaras (media docena o más) en ordenadores con un hardware no demasiado puntero.

En mi caso particular uso como centro de domótica un ordenador portátil **HP Elitebook 820 G1** con las siguientes características:

{% highlight plaintext %}
CPU Intel Core i5 4300U a 1.9 GHz (hasta 2.9 GHz en modo turbo)
8 GB DDR3L SDRAM 
Disco SSD de 128 GB
Disco externo de 500 GB 7200 RPM
Intel HD Graphics 4400
{% endhighlight %}

Además, dispongo de una tarjeta Coral conectada a uno de los puertos USB 3.0, con lo que consigo liberar a la CPU del portátil de parte del trabajo que realiza Frigate, si bien existe también una versión más barata que se conecta a un puerto Mini PCIe, aunque no es muy recomendable si el ordenador no dispone de un buen disipador de calor y nada recomendable en un ordenador portátil, ya que la tarjeta se calienta bastante cuando se activa la detección de objetos.

Como Sistema Operativo uso [Ubuntu 22.04-LTS](https://ubuntu.com) y Frigate corre en un contenedor definido de la siguiente manera:

{% highlight yaml %}
services:
  frigate:
    container_name: frigate
    image: ghcr.io/blakeblackshear/frigate:stable
    privileged: true 
    restart: unless-stopped
    shm_size: "192mb" 
    devices:
      - /dev/bus/usb:/dev/bus/usb
      - /dev/dri/renderD128 
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ../dockers-data/frigate/config.yml:/config/config.yml
      - ../dockers-data/frigate/frigate.db:/config/frigate.db
      - /media/sdb1/frigate:/media/frigate
      - type: tmpfs
        target: /tmp/cache
        tmpfs:
          size: 1000000000
    ports:
      - "5000:5000"
      - "8554:8554"
    environment:
      LIBVA_DRIVER_NAME: "i965"
{% endhighlight %}

Te recomiendo que consultes la [documentación](https://docs.frigate.video) de Frigate para conocer el valor adecuado para los parámetros *shm_size* y *devices*, así como el motivo por el que el container debe ejecutarse en modo privilegiado.

A continuación indico el contenido de mi archivo de configuración:

{% highlight yaml %}
mqtt:
  enabled: true
  host: 127.0.0.1
  port: 1883
  user: "{FRIGATE_MQTT_USER}"
  password: "{FRIGATE_MQTT_PASSWORD}"

database: 
  path: /config/frigate.db

ui:
  live_mode: mse

rtmp:
  enabled: false

birdseye:
  enabled: false

ffmpeg:
  hwaccel_args: preset-vaapi
  input_args: preset-rtsp-generic
  output_args:
    record: preset-record-generic

snapshots:
  enabled: true
  timestamp: true
  bounding_box: true
      
record:
  enabled: true
  retain:
    days: 7

motion:
  improve_contrast: true

detect:
  enabled: false
  width: 1920
  height: 1080

detectors:
  coral:
    type: edgetpu
    device: usb

objects:
  track:
    - person
    - dog
    - cat 
  filters:
    person:
      min_area: 25000

go2rtc:
  streams:
    camera_1:
      - "ffmpeg:rtsp://{FRIGATE_RTSP_USER}:{FRIGATE_RTSP_PASSWORD}@192.168.31.231/stream1"
 
cameras:
  camera_1:
    enabled: true
    ui: 
      order: 1
    ffmpeg:
      inputs:
        - path: rtsp://127.0.0.1:8554/camera_1
          roles:
            - record
            - detect
{% endhighlight %}

En esta configuración cabe destacar el uso de *go2rtc* para acceder a las cámaras, mientras que el resto de procesos (entre ellos el de detección) se conectan  al stream de *go2rtc*, lo que permitirá minimizar el número de conexiones a las cámaras, que está limitado en determinadas marcas y/o modelos. 

Finalmente indicar que la instalación de Frigate se integra en Home Assistant a través de HACS, que es una tienda de aplicaciones no oficial para Home Assistant y para cuya instalación seguí las instrucciones publicadas en el blog de [Manel Rodero](https://www.manelrodero.com/blog/instalacion-de-hacs-en-home-assistant-docker).
