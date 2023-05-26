---
layout: splash 
title:  "Enviar mensajes a Telegram desde Home Assistant"
date:   2023-05-24 16:01:15 +0200
categories: configuraciones domótica home-assistant
header:
  overlay_image: /assets/images/home-assistant--banner.jpg
  overlay_filter: 0.5 
excerpt: |
  Home Assistant es un software libre que permite la automatización de tareas del hogar y al que pueden añadirse literalmente miles de plugins que permiten la automatización de casi cualquier cosa que puedas imaginar.
---
Desde subir o bajar las persianas dependiendo de la luz solar hasta gestionar las diferentes cámaras de seguridad (ver post relativo a [Frigate](/configuraciones/domótica/frigate/instalacion-y-configuracion-de-frigate)), pasando por una automatización que encienda la televisión cuando llegas a casa, con [Home Assistant](https://www.home-assistant.io) puedes automatizar casi cualquiera de las cosas que ocurren en tu hogar.

Además, Home Assistant permite enviar notificaciones de forma nativa tanto a la aplicación para Android o iOS como a uno o más canales de Telegram. 

En el blog de [Manel Rodero](https://www.manelrodero.com/blog/instalacion-de-home-assistant-en-docker) encontrarás instrucciones precisas sobre cómo instalar Home Assistant en un docker, sin duda la mejor de las opciones disponibles.

Para poder enviar mensajes a un canal de Telegram primero tendremos que crear un bot de Telegram, lo que podremos realizar fácilmente siguiendo las siguientes instrucciones:

1. Abrir la app de Telegram (web, Android, iOS) y, en el buscador, teclear *@BotFather*. El primer resultado de la lista de búsqueda, será el bot oficial, que podremos distinguir también porque tendrá un check azul a la derecha del nombre, lo que nos indica que es un bot oficial de Telegram,
2. Hacer clic en el botón *Iniciar* en la ventana de chat,
3. Escribir el comando *newbot* o hacer clic sobre dicho comando en la lista de comandos que se mostrará tras haber hecho clic en el botón *iniciar*,
4. Escoger el nombre del bot y pulsar la tecla *enter*,
5. Escoger el nombre de usuario del bot (este nombre es único y debe acabar en *bot*),
6. Si el nombre de usuario del bot está libre se nos mostrará el token (también llamado *API Key*) asignado al bot, que deberemos guardar para más adelante.

Una vez creado el bot, deberemos crear el grupo o canal al que queremos que Home Assistant envíe los mensajes. Para ello simplemente haremos clic en el menú lateral de la aplicación y seleccionaremos la opción *nuevo grupo* o *nuevo canal* según corresponda.

En cualquiera de los dos casos seguiremos las siguientes instrucciones para completar la creación del grupo:

1. Seleccionaremos el nombre del grupo y haremos clic en *siguiente*,
2. Si es el caso, indicaremos que se trata de un *canal privado*,
3. Seleccionaremos a los miembros del grupo o canal, entre ellos al bot que acabamos de crear, 

Necesitaremos obtener el identificador del grupo o canal. Hay varias maneras de obtenerlo pero la más sencilla es acceder a Telegram desde la [interfaz web](https://web.telegram.org), hacer clic con el botón derecho del ratón en el nombre del grupo o canal correspondiente y seleccionar *abrir en una nueva pestaña*, lo que nos mostrará en una nueva pestaña el grupo o canal en cuestión y, en la barra de direcciones, la URL del mismo. El identificador del grupo o canal se forma concatenando *-100* y el número que aparece al final de la URL (después del signo menos).

Una vez disponemos del token del bot y del identificador del grupo o canal, accederemos al archivo de configuración de Home Assistant, añadiremos las líneas que se indican a continuación y reiniciaremos la aplicación.

{% highlight yaml %}
telegram_bot:
  - platform: broadcast
    api_key: *TOKEN*
    allowed_chat_ids: [ *ID_DEL_CANAL* ]
        
notify: 
  - platform: telegram
    name: telegram
    chat_id: *ID_DEL_CANAL*
{% endhighlight %}

Una vez hecho esto nos aparecerá, en el apartado de acciones de nuestras automatizaciones, la opción de llamar al servicio *notify.telegram*, que enviará el mensaje que le indiquemos al grupo o canal que hayamos creado. Es importante tener en cuenta que podemos crear tantos grupos o canales como queramos, si bien el bot que enviará los mensajes será siempre el mismo. Así, podemos tener un canal para cosas urgentes (por ejemplo si salta la alarma) y uno para temas varios (un sensor que marca bateria baja, por ejemplo).

