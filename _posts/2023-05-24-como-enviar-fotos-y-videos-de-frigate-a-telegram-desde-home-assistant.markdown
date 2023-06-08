---
layout: splash
title:  "Cómo enviar fotos y vídeos de Frigate a Telegram desde Home Assistant"
date:   2023-05-24 17:01:15 +0200
categories: configuraciones domótica home-assistant frigate
header:
  overlay_image: /assets/images/home-assistant--banner.jpg
  overlay_filter: 0.5 
excerpt: |
  Ejemplo práctico de cómo enviar mensajes a un canal de Telegram usando una automatización de Home Assistant.
---
En los dos posts anteriores aprendimos cómo configurar [Frigate](/configuraciones/domótica/frigate/instalacion-y-configuracion-de-frigate) y [cómo crear un bot en Telegram](/configuraciones/domótica/home-assistant/enviar-mensajes-a-telegram-desde-home-assistant) y usarlo en Home Assistant para enviar mensajes a un grupo o canal.

En este post simplemente mostraré dos automatizaciones en las que uso estos dos conceptos para enviar una foto y un vídeo de la última detección de Frigate a un canal de Telegram.

**Envío de foto a un canal de Telegram al realizar una detección.**

{% highlight yaml %}
{% raw %}
alias: Notificar con foto al detectar una persona en una cámara
description: ""
trigger:
  - platform: mqtt
    topic: frigate/events
condition:
  - condition: template
    alias: Is a new event?
    value_template: "{{ event_type == 'new' }}"
  - condition: template
    alias: Is a person related event?
    value_template: "{{ object_type == 'person' }}"
  - condition: template
    alias: Is a camera event?
    value_template: "{{ camera is defined }}"
action:
  - service: notify.telegram
    data:
      message: Foto {{ camera_name }}
      data:
        parse_mode: html
        photo:
          - url: >-
              {{ base_location }}/api/frigate/notifications/{{ id }}/snapshot.jpg?format=android
            caption: Foto {{ camera_name }}
variables:
  base_location: http://127.0.0.1:8123
  id: "{{ trigger.payload_json['after']['id'] }}"
  event_type: "{{ trigger.payload_json['type'] }}"
  object_type: "{{ trigger.payload_json['after']['label'] }}"
  camera: "{{ trigger.payload_json['after']['camera'] }}"
  camera_name: "{{ camera | replace('_', ' ') }}"
  start_time: "{{ trigger.payload_json['after']['start_time'] }}"
mode: single
{% endraw %}
{% endhighlight %}

Ten en cuenta que el nombre del servicio, *notify.telegram_urgente* en mi caso, tendrá que ser el que hayas indicado en la configuración de Home Assistant (ver post [cómo crear un bot en Telegram](/configuraciones/domótica/home-assistant/enviar-mensajes-a-telegram-desde-home-assistant)).

Es interesante notar que para que la automatización se inicie usamos el topic *frigate/events* que es el que usa Frigate para el envío de mensajes a través de *mqtt*. Además, como Frigate envía varios mensajes cada vez que realiza una detección, tendremos especial cuidado de enviar notificación sólo de las nuevas detecciones y, como Frigate nos permite detectar muchos objetos diferentes, notificaremos sólo las detecciones de personas (desgraciadamente la detección de Frigate no es perfecta y un cierto número de detecciones son falsos positivos, [que podemos mitigar en mayor o menor medida](https://docs.frigate.video/guides/false_positives)).

**Envío de vídeos a un canal de Telegram al realizar una detección.**

{% highlight yaml %}
{% raw %}
alias: Notificar con vídeo al acabar de detectar una persona en una cámara
description: ""
trigger:
  - platform: mqtt
    topic: frigate/events
condition:
  - condition: template
    alias: Is a end event?
    value_template: "{{ event_type == 'end' }}"
  - condition: template
    alias: Is a person related event?
    value_template: "{{ object_type == 'person' }}"
  - condition: template
    alias: Is a camera event?
    value_template: "{{ camera is defined }}"
action:
  - delay:
      seconds: 5
  - service: notify.telegram
    data:
      message: Vídeo {{ camera_name }}
      data:
        parse_mode: html
        video:
          - url: >-
              {{ base_location }}/api/frigate/notifications/{{ id }}/{{ camera }}/clip.mp4
            caption: Vídeo {{ camera_name }}
variables:
  base_location: http://127.0.0.1:8123
  id: "{{ trigger.payload_json['after']['id'] }}"
  event_type: "{{ trigger.payload_json['type'] }}"
  object_type: "{{ trigger.payload_json['after']['label'] }}"
  camera: "{{ trigger.payload_json['after']['camera'] }}"
  camera_name: "{{ camera | replace('_', ' ') }}"
  start_time: "{{ trigger.payload_json['after']['start_time'] }}"
mode: single
{% endraw %}
{% endhighlight %}

Al igual que con las fotografías, usaremos el evento *frigate/events* sólo que, en este caso, esperaremos a que la detección finalice, procediendo en este momento (de hecho 5 segundos después) a enviar el clip de vídeo completo.

Hay que tener en cuenta que esta automatización no funcionará para vídeos muy grandes (de más de 50 MB) ya que el API de Telegram limita el tamaño de los archivos que se pueden enviar, pero la experiencia me dice que los clips de detección raramente alcanzan ese tamaño.




