---
layout: splash 
title:  "Rastreador GPS para coche prácticamente gratuito"
date:   2023-05-28 15:11:15 +0200
categories: gps
header:
  overlay_image: /assets/images/maps--banner.jpg
  overlay_filter: 0.5 
excerpt: |
  ¿Cómo tener localizado tu coche por muy poco dinero?
---
Antes de nada indicar que las instrucciones contenidas en este post me han funcionado perfectamente en el momento de escribirlo, pero podrían en alguna actualización del firmware del dispositivo o, en general, por cualquier otra causa, por lo que no puedo hacerme responsable de su contenido. 

Realizadas las consideraciones previas correspondientes, cabe destacar que estas instrucciones se refieren a este dispositivo concreto: 
[rastreador GPS WanWayTech](https://www.amazon.es/dp/B0B4VQY6ZS), que se vende en Amazon por un precio que oscula entre los 5 y los 12 euros y, aunque la empresa lo vende con una suscripción opcional a un servicio de rastreo a través de una aplicación móvil, es muy sencillo de reprogramar, lo que nos permite ahorrarnos el importe de la suscripción.

Para la reprogramación del dispositivo lo primero que deberemos es hacernos con una tarjeta SIM funcional, preferiblemente de prepago. En concreto [Simyo](https://www.simyo.es) ofrece una tarifa de unos 3 céntimos por MB en prepago lo que, teniendo en cuenta que el rastreador viene gastando unas 20-25 MB al mes, nos generará un gasto aproximado de unos 60 o 70 céntimos se euro.

Una vez dispongamos de la tarjeta SIM, y antes de instalarla en el localizador, desactivaremos la necesidad de introducir el PIN para acceder a la tarjeta, ya que una vez instalada no tendremos manera de introducirlo, puesto que el localizador no dispone de teclado.

Seguidamente levantaremos la tapita del rastreador para acceder al compartimento de la SIM y sustituiremos la SIM que trae el equipo por la nuestra, tal como se indica en el siguiente vídeo.

<iframe width="560" height="315" src="https://www.youtube.com/embed/lczrr8aBCS4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe><br>

Una vez insertada nuestra tarjeta SIM, procederemos a encender el dispositivo, para lo que pulsaremos unos 2 segundos el botón de encendido, que se encuentra a la derecha del puerto de carga.

![image puerto de carga y botón de inicio](/assets/images/boton-de-inicio-y-puerto-carga-rastreador-gps.jpg)

Veremos que el dispositivo está encendido porque parparearán las luces azul y verde en la parte superior del dispositivo.

A continuación enviaremos 3 SMS al número de teléfono asociado al rastreador con el siguiente contenido (enviaremos los mensajes en ese orden, esperando a que el anterior se reciba para enviar el siguiente, ya que tienen que recibirse en el orden correcto):

1. FACTORYALL#
2. APN,orangeworld#
3. server,0,46.101.24.212,5023,0#

(El segundo SMS es para la configuración del APN de datos, y cambiará dependiendo del operador de tu tarjeta, mientras que el tercero es para configurar la IP del servidor al que vamos a enviar nuestra posición y puede ser cualquiera de las que se listan [aquí](https://www.traccar.org/demo-server/)).
 
Hecho esto nos conectaremos mediante el navegador a [demo.traccar.org](https://demo.traccar.org) y procederemos a crearnos una cuenta gratuíta y a dar de alta el dispositivo de localización, para lo que usaremos el IMEI que figura en la etiqueta del mismo, la que hemos levantado para acceder a la tarjeta SIM.

![image captura de pantalla de traccar](/assets/images/traccar-screenshoot.jpg)

La batería del dispositivo viene durando unas 2 semanas, pudiendo hacer el seguimiento desde la página web. 

**Si vas a usar este dispositivo con Simyo y no tienes cuenta aún con ellos, puedes usar enviarme un [email](mailto:dwsko24k@duck.com) y te facilitaré mi número de teléfono para que lo uses en el momento del alta y ambos nos llevemos un pequeños regalo de bienvenida...**

[Aquí](https://www.chollometro.com/comments/permalink/9006492) podéis encontrar algunos comandos adicionales para la configuración del rastreador, como por ejemplo la posibilidad de establecer perímetros de seguridad o incluso la opción de poder llamar al dispositivo (que también dispone de un micrófono) y escuchar lo que sucede en tu vehículo, si fuera el caso.
