# iaw-practica-18

<div class="sect1">
<h2 id="_diagrama">2. Diagrama</h2>
<div class="sectionbody">
<div class="imageblock">
<div class="content">
<img src="http://josejuansanchez.org/iot-dashboard/images/diagram.png" alt="diagram">
</div>
<h2 id="_mqtt_broker">4. MQTT Broker</h2>
<div class="sectionbody">
<div class="paragraph">
<p><strong>MQTT</strong> (<em>MQ Telemetry Transport</em>) es un protocolo de mensajería estándar utilizado en las aplicaciones de <strong>Internet de las cosas (IoT)</strong>. Se trata de un protocolo de mensajería muy ligero basado en el patrón <em>publish/subscribe</em>, donde los mensajes son publicados en un <em>topic</em> de un <strong>MQTT Broker</strong> que se encargará de distribuirlos a todos los suscriptores que se hayan suscrito a dicho <em>topic</em>.</p>
</div>
<div class="paragraph">
<p><a href="https://mosquitto.org"><strong>Eclipse Mosquitto</strong></a> será el <strong>MQTT Broker</strong> que vamos a utilizar en este proyecto.</p>
</div>
<div class="sect2">
<h3 id="_descripción_del_servicio_en_docker_compose_yml">4.1. Descripción del servicio en <code>docker-compose.yml</code></h3>
<div class="paragraph">
<p><strong><code>docker-compose.yml</code></strong></p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>  mosquitto: <i class="conum" data-value="1"></i><b>(1)</b>
    image: eclipse-mosquitto:2 <i class="conum" data-value="2"></i><b>(2)</b>
    ports:
      - 1883:1883 <i class="conum" data-value="3"></i><b>(3)</b>
    volumes:
      - ./mosquitto/mosquitto.conf:/mosquitto/config/mosquitto.conf <i class="conum" data-value="4"></i><b>(4)</b>
      - mosquitto_data:/mosquitto/data <i class="conum" data-value="5"></i><b>(5)</b>
      - mosquitto_log:/mosquitto/log <i class="conum" data-value="6"></i><b>(6)</b></code></pre>
</div>
</div>
<div class="colist arabic">
<table>
<tr>
<td><i class="conum" data-value="1"></i><b>1</b></td>
<td>Nombre del servicio dentro del archivo <code>docker-compose.yml</code>.</td>
</tr>
<tr>
<td><i class="conum" data-value="2"></i><b>2</b></td>
<td>Vamos a utilizar la versión <strong>2</strong> de la imagen oficial <a href="https://hub.docker.com/_/eclipse-mosquitto">eclipse-mosquitto</a>.</td>
</tr>
<tr>
<td><i class="conum" data-value="3"></i><b>3</b></td>
<td>El broker MQTT estará aceptando peticiones en el puerto 1883.</td>
</tr>
<tr>
<td><i class="conum" data-value="4"></i><b>4</b></td>
<td>Creamos un volumen de tipo <em>bind mount</em> para enlazar el archivo de configuración <code>mosquitto.conf</code> que tenemos en nuestro directorio local <code>mosquitto</code> con el directorio <code>/mosquitto/config/mosquitto.conf</code> del broker MQTT.</td>
</tr>
<tr>
<td><i class="conum" data-value="5"></i><b>5</b></td>
<td>Creamos un volumen para almacenar los mensajes que se reciben.</td>
</tr>
<tr>
<td><i class="conum" data-value="6"></i><b>6</b></td>
<td>Creamos un volumen para almacenar los mensajes de log.</td>
</tr>
</table>
</div>
</div>
<div class="sect2">
<h3 id="_archivo_de_configuración_mosquitto_conf">4.2. Archivo de configuración <code>mosquitto.conf</code></h3>
<div class="paragraph">
<p>Este archivo estará almacenado en el directorio <code>./mosquitto/mosquitto.conf</code> de nuestra máquina local. Como vamos a utilizar la <strong>versión 2</strong> del broker MQTT <a href="https://hub.docker.com/_/eclipse-mosquitto">eclipse-mosquitto</a> vamos a necesitar crear un archivo de configuración llamado <code>mosquitto.conf</code> que incluya las siguientes opciones de configuración.</p>
</div>
<div class="paragraph">
<p><strong><code>mosquitto.conf</code></strong></p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>listener 1883 <i class="conum" data-value="1"></i><b>(1)</b>
allow_anonymous true <i class="conum" data-value="2"></i><b>(2)</b></code></pre>
</div>
</div>
<div class="colist arabic">
<table>
<tr>
<td><i class="conum" data-value="1"></i><b>1</b></td>
<td>En esta línea estamos configurando el <em>listener</em> para que acepte conexiones desde cualquiera de sus interfaces de red, es equivalente a poner <code>listener 1883 0.0.0.0</code>. Si sólo quisiéramos recibir conexiones por una interfaz de red específica se podría configurar aquí de forma manual.</td>
</tr>
<tr>
<td><i class="conum" data-value="2"></i><b>2</b></td>
<td>A partir de la <strong>versión 2</strong> es necesario indicar un método de autenticación para el <em>listener</em>. Con esta configuración vamos a permitir conexiones desde cualquier cliente. Aquí sería posible definir un método de autenticación basado en contraseña para restringir el acceso al broker.</td>
</tr>
</table>
</div>
<div class="paragraph">
<p><a href="https://mosquitto.org/documentation/migrating-to-2-0/">En la documentación oficial</a>, puede consultar los cambios que hay que tener en cuenta en el archivo de configuración para migrar de la versión 1.x a la 2.0.</p>
</div>
<div class="paragraph">
<p>También puede encontrar más información sobre todas las opciones de configuración posibles en la <a href="https://mosquitto.org/man/mosquitto-conf-5.html">documentación oficial</a>.</p>
</div>
<div class="paragraph">
<p>En este punto, el contenido de nuestro directorio de trabajo debe tener los siguientes archivos.</p>
</div>
    
# captura 1
<div class="sect2">
<h3 id="_cliente_mqtt_para_publicar_en_un_topic_publish">4.3. Cliente MQTT para publicar en un topic (<em>publish</em>)</h3>
<div class="paragraph">
<p>En esta sección vamos a explicar cómo podemos hacer uso de un cliente MQTT para publicar mensajes de prueba en el broker MQTT que acabamos de crear.</p>
</div>
<div class="paragraph">
<p>Esta comprobación deberíamos realizarla antes de empezar a trabajar directamente con los sensores sobre el broker MQTT. Una vez que hayamos comprobado que podemos publicar y suscribirnos a los mensajes del broker con este cliente, ya podríamos realizar el siguiente paso para publicar mensajes con los sensores y recibirlos con el agente de <a href="https://www.influxdata.com/time-series-platform/telegraf/">Telegraf</a>.</p>
</div>
<div class="paragraph">
<p><strong>Ejemplo</strong>:</p>
 </div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>docker run --init -it --rm efrecon/mqtt-client sub -h 3.235.176.68 -t "iescelia/#"</code></pre>
</div>
</div>
 <p>Explicación de los parámetros utilizados:</p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>docker run --init -it --rm efrecon/mqtt-client \ <i class="conum" data-value="1"></i><b>(1)</b>
pub \ <i class="conum" data-value="2"></i><b>(2)</b>
-h test.mosquitto.org \ <i class="conum" data-value="3"></i><b>(3)</b>
-p 1883 \ <i class="conum" data-value="4"></i><b>(4)</b>
-t "iescelia/aula21/temperature" \ <i class="conum" data-value="5"></i><b>(5)</b>
-m 30 <i class="conum" data-value="6"></i><b>(6)</b></code></pre>
</div>
</div>
<div class="colist arabic">
<table>
<tr>
<td><i class="conum" data-value="1"></i><b>1</b></td>
<td>Utilizamos la imagen Docker <a href="https://hub.docker.com/r/efrecon/mqtt-client">efrecon/mqtt-client</a> que contiene el cliente MQTT (<code>mosquitto_pub</code>) para publicar mensajes en un topic de un broker MQTT.</td>
</tr>
<tr>
<td><i class="conum" data-value="2"></i><b>2</b></td>
<td>Utilizamos el comando <code>pub</code> para publicar un mensaje en el broker MQTT.</td>
</tr>
<tr>
<td><i class="conum" data-value="3"></i><b>3</b></td>
<td>Con el parámetro <code>-h</code> indicamos el hosts (broker MQTT) con el que queremos conectarnos. En este ejemplo estamos utilizando el broker de prueba de <code>test.mosquitto.org</code>, pero <strong>tendremos que cambiar este broker por la dirección IP de nuestro broker MQTT</strong>.</td>
</tr>
<tr>
<td><i class="conum" data-value="4"></i><b>4</b></td>
<td>Con el parámetro <code>-p</code> indicamos el puerto, que por defecto, será el puerto <code>1883</code>.</td>
</tr>
<tr>
<td><i class="conum" data-value="5"></i><b>5</b></td>
<td>Con el parámetro <code>-t</code> indicamos el topic que vamos a utilizar para publicar nuestro mensaje. En este ejemplo, estamos utilizando el topic <code>iescelia/aula21/temperature</code>.</td>
</tr>
<tr>
<td><i class="conum" data-value="6"></i><b>6</b></td>
<td>Con el parámetro <code>-m</code> indicamos el contenido del mensaje que queremos publicar. En este ejemplo, estamos publicando el mensaje <code>30</code>.</td>
</tr>
</table>
</div>
    <div class="admonitionblock important">
<table>
<tr>
<td class="icon">
<i class="fa icon-important" title="Important"></i>
</td>
<td class="content">
<div class="paragraph">
<p>No olvide que en este ejemplo debemos reemplazar el broker de prueba de <code>test.mosquitto.org</code> por la dirección IP de nuestro broker MQTT.</p>
</div>
</td>
</tr>
</table>
</div>
</div>
