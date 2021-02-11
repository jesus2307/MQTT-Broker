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
