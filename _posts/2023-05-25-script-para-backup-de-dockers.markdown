---
layout: splash 
title:  "Script para backup de datos de un docker"
date:   2023-05-25 15:11:15 +0200
categories: configuraciones domótica dockers
header:
  overlay_image: /assets/images/docker--banner.jpg
  overlay_filter: 0.5 
excerpt: |
  ¿Cómo hacer una copia de seguridad de los datos de tus dockers?
---
Tan importante como montar tus dockers y tenerlos en funcionamiento es tener una copia de seguridad de los datos (volúmenes) de los mismos por si la máquina en la que se alojan *desaparece*, el disco se corrompe, etc.

Habitualmente el desarrollador del aplicativo o del contenedor nos indicará qué directorios y ficheros son persistentes, lo que facilitará la tarea de decidir  qué archivos o directorios tenemos que incluir en la copia de seguridad.

Existen diferentes alternativas para realizar estos backups, pero personalmente prefiero la más sencilla: un script que se ejecuta en un cron y que empaqueta los diferentes volúmenes del contenedor en un archivo *tar*.

El script que os muestro a continuación permite empaquetar los volúmenes de un contenedor en caso de que éste no use bases de datos o de que éstas sean de tipo *sqlite3*, pero puede fácilmente modificarse para soportar otros tipos de bases de datos.

Es importante notar que el script asume que todos los volúmenes del contenedor comparten el mismo directorio raíz lo que, por otro lado, no es difícil de conseguir...

Puedes descargar el script que realiza el backup de un contenedor [aquí](https://gist.github.com/robertoprubio/fc8abe1fb776315b4e4fcf9af6984e0d) y para ejecutarlo simplemente debes pasarle como parámetro un archivo que contenga la configuración de backup de ese contenedor.

{% highlight shell %}
$ sudo backup-container-data.sh path-archivo-de-configuracion-del-backup
{% endhighlight %}

Asimismo, si en algún momento necesitas restaurar un backup puedes usar este [script](https://gist.github.com/robertoprubio/1bf5b965dc5d6107ed7e026060f793b2), aunque en este caso los parámetros a enviar al script son 2: el path al archivo de configuración de backup de ese contenedor y el path del archivo de backup que se desea restaurar.

{% highlight shell %}
$ sudo restore-container-data.sh path-archivo-de-configuracion-del-backup path-archivo-de-backup.tar.xs
{% endhighlight %}

En cuanto al archivo de configuración al que hacemos referencia, tendrá al siguiente formato.

{% highlight bash %}
#!/bin/bash

# Nombre de la app que estamos procesando

APP_NAME="frigate"

# Directorio raíz de datos (volúmenes del container en cuestión)

DATA_ROOT="/home/roberto/dockers-data/${APP_NAME}"

# Archivos de base de datos

DATABASE_LOCAL_PATHNAME=("frigate.db")

####### Datos del backup primario

# Directorio raíz del backup

BACKUPS_ROOT="/home/roberto/backups/dockers-data/${APP_NAME}"

# Propietario y grupo del archivo de backup (opcional)

BACKUPS_OWNER="roberto.roberto"

# Tiempo (en días) tras los cuales se eliminará automáticamente un backup (opcional)

BACKUPS_PURGE_DAYS="60"

####### Datos del backup secundario (opcionalmente permite guardar el backup en más de una ubicación)

ADDITIONAL_BACKUPS_ROOT=("/media/sdb1/backups/dockers-data/${APP_NAME}")
ADDITIONAL_BACKUPS_OWNER=("")
ADDITIONAL_BACKUPS_PURGE_DAYS=("60")
{% endhighlight %}

Como habrás visto, tanto los scripts como la propia configuración es de lo más sencillo y es que, a veces, no hace falta complicarse la vida...
