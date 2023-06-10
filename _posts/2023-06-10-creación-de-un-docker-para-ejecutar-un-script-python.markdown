---
layout: splash 
title:  Creación de un docker para ejecutar un script python
description: Creación de un docker para ejecutar un script python
date:   2023-06-10 06:01:15 +0200
categories: docker python
header:
  overlay_image: /assets/images/python--banner.jpg
  overlay_filter: 0.5 
excerpt: |
  ¿Cómo ejecutar un script python en el entorno controlado de un docker? 
---
En ocasiones nos puede interesar, porque por ejemplo no tenemos permisos de superusuario en la máquina host que nos permitan instalar el intérprete de un determinado lenguaje, crear un contenedor y ejecutar en él un determinado script.

A continuación explicaremos como hacerlo para el caso de *python*, si bien el ejemplo es estrapolable a multitud de otros lenguajes.

Lo primero que haremos es describir la receta docker que nos permitirá crear el contenedor.

{% highlight yaml %}
version: "3"

services:
    myscript-python:
      build:
        context: myscript-python
        dockerfile: dockerfile
      container_name: myscript-python
      restart: never
{% endhighlight %}

Como véis, la receta es de lo más sencillo; si bien la *chicha* se encuentra en el archivo *myscript-python/dockerfile* que se referencia y cuyo contenido es el que sigue:

{% highlight text %}
FROM python:3.10

RUN mkdir /app 
WORKDIR /app

COPY my-script.py /app/ 

COPY dependencias.txt /app/ 
RUN pip install --no-cache-dir -r dependencias.txt

ENTRYPOINT [ "python", "./my-script.py" ]
{% endhighlight %}

Fijaos que lo que hacemos es, básicamente, bajarnos un docker con python 3.10, copiar el script que queremos ejecutar (y que debemos haber copiado previamente al directorio *myscript-python*) en */app* e instalar las librerías adicionales que requiera el script, si es que hay alguna. Finalmente indicamos que, cuando se ejecute el comando *docker run* sobre este contenedor, se ejecutará nuestra app.

Procederemos a continuación a generar la imagen del contenedor con el comando ***docker-compose -f myscript-python.yml build***. La imagen por defecto tendrá el nombre *dockers_myscript-python* y la podremos ejecutar tantas veces como queramos con el comando ***docker run dockers_myscript-python***.

***Pero, ¿qué pasa si nuestra app necesita ejecutarse con parámetros?***

No hay problema, ya que podemos añadir los parámetros al comando *docker run* de esta manera:

{% highlight text %}
docker run dockers_myscript-python "argumento 1" "argumento 2" ... "argumento n"
{% endhighlight %}

A modo de recapitulación, indicaremos cuál será la estructura de ficheros que necesitamos:

{% highlight text %}
myscript-python.yml
myscript-python
  |- dockerfile
  |- dependencias.txt
  |- my-script.py
{% endhighlight %}

