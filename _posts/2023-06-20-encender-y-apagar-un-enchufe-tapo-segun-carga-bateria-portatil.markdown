---
layout: splash 
title:  Cómo encender y apagar un enchufe TAPO según el estado de carga de un ordenador portátil
description: Cómo encender y apagar un enchufe TAPO según el estado de carga de un ordenador portátil
date:   2023-06-20 06:01:15 +0200
categories: docker python
header:
  overlay_image: /assets/images/python--banner.jpg
  overlay_filter: 0.5 
excerpt: |
  ¿Cómo encender y apagar un enchufe TAPO según el estado de carga de un ordenador portátil?
---
Muchos de nosotros tenemos Home Assistant, y otros servicios, en un ordenador portátil porque, entre otras, ofrece la ventaja de *integrar* un SAI en su interior.

El problema es que, si la batería no se carga y descarga de forma correcta, se va a degradar muy rápidamente.

Para solucionar este problema os presentamos hoy un script que permite encender o apagar un enchufe *TAPO* dependiendo del estado de la batería del ordenador, lo que os permitirá cargar de forma correcta la batería y maximizar su vida útil.

Para implementar nuestro script de control, que podremos ejecutar por ejemplo en un cron, necesitaremos usar 2 librerías: [psutil](https://github.com/giampaolo/psutil) y [TapoP100](https://github.com/fishbigger/TapoP100), que podremos importar en nuestra instalación de *python* usando el comando *pip*.

El script en cuestión es el siguiente, que podéis configurar con el porcentaje de batería mínimo y máximo deseado (lo recomendable es que la batería siempre oscile entre el 20 y el 80%). Adicionalmente, tendréis que indicar la dirección IP del enchufe (que tendréis que configurar como estática en el router) y el username y contraseña de acceso a vuestra cuenta *TP-Link* (el username y contraseña con el que hacéis login en la app de *TAPO*). 

{% highlight python %}
from PyP100 import PyP100

import psutil

BATTERY_CHARGED_PERCENT = 80
BATTERY_DISCHARGED_PERCENT = 20

PLUG_IP_ADDRESS = '192.168.1.X'
PLUG_USERNAME = 'USERNAME'
PLUG_PASSWORD = 'PASSWORD'

battery = psutil.sensors_battery()

if battery is not None:
    p100 = PyP100.P100(PLUG_IP_ADDRESS, PLUG_USERNAME, PLUG_PASSWORD)
    p100.handshake()
    p100.login()
    is_plug_on = p100.getDeviceInfo()['result']['device_on']
    toggle_plug_state = False
    if battery.power_plugged and battery.percent >= BATTERY_CHARGED_PERCENT and is_plug_on:
        toggle_plug_state = True
    elif not battery.power_plugged and battery.percent <= BATTERY_DISCHARGED_PERCENT and not is_plug_on:
        toggle_plug_state = True
    if toggle_plug_state:
        p100.toggleState()
{% endhighlight %}
