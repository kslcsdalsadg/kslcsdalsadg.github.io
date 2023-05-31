---
layout: splash
title:  "Reseteo de datos de zigbee2mqtt"
date:   2023-05-31 06:45:15 +0200
categories: configuraciones domótica home-assistant zigbee2mqtt
header:
  overlay_image: /assets/images/zigbee2mqtt--banner.jpg
  overlay_filter: 0.5 
excerpt: |
  ¿Cómo reconfigurar zigbee2mqtt si no nos permite agregar dispositivos nuevos?
---
En ocasiones me ha sucedido que la instalación de zigbee2mqtt (que tengo en un docker) se queda en un estado en el que detecta correctamente los cambios en los dispositivos existentes (puertas que se abren, dispositivos que vibran, etc) pero no añade los nuevos dispositivos cuando intento emparejarlos. 

Cuando esto sucede, parar el contenedor y volverlo a iniciar no sirve de nada, y la única solución es eliminar los datos del mismo y volver a configurarlo de nuevo.

Para ello ejecutaremos los siguientes comandos en la máquina en la que se esté ejecutando el contenedor:

{% highlight bash %}
docker container stop zigbee2mqtt
docker container rm zigbee2mqtt 
docker rmi koenkk/zigbee2mqtt
{% endhighlight %}

(En rigor, el último de los comandos no es obligatorio, pero así forzaremos que se actualice la imagen del contenedor, por si hay una nueva versión).

Seguidamente procederemos a eliminar todos los archivos del volumen de datos de zigbee2mqtt (el que tuvieras mapeado sobre */app/data*, en mi caso ~/dockers-data/zigbee2mqtt), para lo que seguramente necesitarás permisos de superusuario.

{% highlight bash %}
$ sudo rm -rf ~/dockers-data/zigbee2mqtt/*
{% endhighlight %}

Hecho esto, crearemos el archivo de configuración de zigbee2mqtt en el directorio raíz del volumen de datos de zigbee2mqtt, con el siguiente contenido:

{% highlight yaml %}
mqtt:
  base_topic: zigbee2mqtt
  server: mqtt://127.0.0.1:1883
  user: admin
  password: **************************
serial:
  port: /dev/ttyACM0
advanced:
  network_key: GENERATE
  pan_id: GENERATE
frontend: true
homeassistant: true
{% endhighlight %}

Es importante que tengamos especial cuidado en añadir la clave *pan_id* en la sección *advanced*, ya que forzará que la antena zigbee se resetée. En caso de no hacerlo lo más normal es que veamos el siguiente error en el log de zigbee2mqtt y el servicio no se iniciará: *Error: network commissioning timed out - most likely network with the same panId or extendedPanId already exists nearby*  

Finalmente procederemos a iniciar el contenedor y, hecho esto, reconfiguraremos nuestros dispositivos.

{% highlight bash %}
docker-compose -f ~/dockers/zigbee2mqtt.yml up -d 
{% endhighlight %}

En el blog de [Manel Rodero](https://www.manelrodero.com/blog/instalacion-de-zigbee2mqtt-en-docker) encontrarás información interesante sobre zigbee2mqtt, incluyendo la relativa a la actualización del firmware del *SONOFF Zigbee 3.0 USB Dongle Plus ZBDongle-P*.
