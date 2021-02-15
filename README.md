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
<img src="https://github.com/jesus2307/iaw-practica-18/blob/main/practica18/ciente%20mqtt.PNG" alt="mg">
</div>
</td>
</tr>
</table>
</div>
</div>
<div class="sect2">
<h3 id="_cliente_mqtt_para_suscribirse_a_un_topic_subscribe">4.4. Cliente MQTT para suscribirse a un topic (<em>subscribe</em>)</h3>
<div class="paragraph">
<p>Podemos hacer uso de un cliente MQTT para suscribirnos a los mensajes que se publican en un topic específico del broker MQTT.</p>
</div>
<div class="paragraph">
<p>Esta comprobación deberíamos realizarla antes de empezar a trabajar directamente con los sensores sobre el broker MQTT. Una vez que hayamos comprobado que podemos publicar y suscribirnos a los mensajes del broker con este cliente, ya podríamos realizar el siguiente paso para publicar mensajes con los sensores y recibirlos con el agente de <a href="https://www.influxdata.com/time-series-platform/telegraf/">Telegraf</a>.</p>
</div>
<div class="paragraph">
<p><strong>Ejemplo:</strong></p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>docker run --init -it --rm efrecon/mqtt-client sub -h 3.235.176.68 -t "iescelia/#"</code></pre>
</div>
</div>
<div class="paragraph">
<p>Explicación de los parámetros utilizados:</p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>docker run --init -it --rm efrecon/mqtt-client \ <i class="conum" data-value="1"></i><b>(1)</b>
sub \ <i class="conum" data-value="2"></i><b>(2)</b>
-h test.mosquitto.org \ <i class="conum" data-value="3"></i><b>(3)</b>
-t "iescelia/#" <i class="conum" data-value="4"></i><b>(4)</b></code></pre>
</div>
</div>
<div class="colist arabic">
<table>
<tr>
<td><i class="conum" data-value="1"></i><b>1</b></td>
<td>Utilizamos la imagen Docker <a href="https://hub.docker.com/r/efrecon/mqtt-client">efrecon/mqtt-client</a> que contiene el cliente MQTT (<code>mosquitto_sub</code>) para suscribirse a un topic de un broker MQTT.</td>
</tr>
<tr>
<td><i class="conum" data-value="2"></i><b>2</b></td>
<td>Utilizamos el comando <code>sub</code> para suscribirnos a un topic del broker MQTT.</td>
</tr>
<tr>
<td><i class="conum" data-value="3"></i><b>3</b></td>
<td>Con el parámetro <code>-h</code> indicamos el hosts (broker MQTT) con el que queremos conectarnos. En este ejemplo estamos utilizando el broker de prueba de <code>test.mosquitto.org</code>, pero <strong>tendremos que cambiar este broker por la dirección IP de nuestro broker MQTT</strong>.</td>
</tr>
<tr>
<td><i class="conum" data-value="4"></i><b>4</b></td>
<td>Con el parámetro <code>-t</code> indicamos el topic al que vamos a suscribirnos. En este ejemplo, estamos utilizando el topic <code>iescelia/<mark></code>. Con el <em>wildcard</em> <code></mark></code> estamos indicando que queremos suscribirnos a todos los topics que existan dentro de <code>iescelia/</code>, es decir, todos los mensajes que se publiquen en cada una de las aulas. También sería posible suscribirnos al topic específico de un aula, por ejemplo: <code>iescelia/aula21/temperature</code>.</td>
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
</div>
</div>
<div class="sect1">
<h2 id="_telegraf">5. Telegraf</h2>
<div class="sectionbody">
<div class="paragraph">
<p><strong>Telegraf</strong> es un agente que nos permite <strong>recopilar y reportar métricas</strong>. Las métricas recogidas se pueden enviar a almacenes de datos, colas de mensajes o servicios como: <a href="https://www.influxdata.com">InfluxDB</a>, <a href="https://graphiteapp.org">Graphite</a>, <a href="http://opentsdb.net">OpenTSDB</a>, <a href="https://www.datadoghq.com">Datadog</a>, <a href="https://kafka.apache.org">Kafka</a>, <a href="https://mqtt.org">MQTT</a>, <a href="https://nsq.io">NSQ</a>, entre otros.</p>
</div>
<div class="sect2">
<h3 id="_descripción_del_servicio_en_docker_compose_yml_2">5.1. Descripción del servicio en <code>docker-compose.yml</code></h3>
<div class="paragraph">
<p><strong><code>docker-compose.yml</code></strong></p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>  telegraf: <i class="conum" data-value="1"></i><b>(1)</b>
    image: telegraf <i class="conum" data-value="2"></i><b>(2)</b>
      volumes:
        - ./telegraf/telegraf.conf:/etc/telegraf/telegraf.conf <i class="conum" data-value="3"></i><b>(3)</b>
      depends_on: <i class="conum" data-value="4"></i><b>(4)</b>
        - influxdb</code></pre>
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
<td>Utilizamos la imagen Docker <a href="https://hub.docker.com/_/telegraf">telegraf</a>.</td>
</tr>
<tr>
<td><i class="conum" data-value="3"></i><b>3</b></td>
<td>Creamos un volumen de tipo <em>bind mount</em> para enlazar el archivo de configuración <code>telegraf.conf</code> que tenemos en nuestro directorio local <code>telegraf</code> con el archivo <code>/etc/telegraf/telegraf.conf</code> del contenedor.</td>
</tr>
<tr>
<td><i class="conum" data-value="4"></i><b>4</b></td>
<td>Indicamos que este servicio depende del servicio <code>influxdb</code> y que no podrá iniciarse hasta que el servicio de <code>influxdb</code> se haya iniciado.</td>
</tr>
</table>
</div>
</div>
<div class="sect2">
<h3 id="_creación_del_archivo_de_configuración_telegraf_conf">5.2. Creación del archivo de configuración <code>telegraf.conf</code></h3>
<div class="paragraph">
<p>En primer lugar tenemos que crear el archivo de configuración <code>telegraf.conf</code> en nuestra máquina local. Para crear el archivo de configuración haremos uso del comando <code>telegram config</code> dentro del contenedor de Telegraf.</p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>docker run --rm telegraf telegraf config &gt; telegraf.conf</code></pre>
</div>
</div>
<div class="paragraph">
<p>Una vez que hemos creado el archivo <code>telegraf.conf</code> lo movemos al directorio <code>telegraf</code> de nuestro proyecto. El contenido de nuestro directorio de trabajo debe tener los siguientes archivos.</p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>.
├── docker-compose.yml
├── mosquitto
│   └── mosquitto.conf
└── telegraf
    └── telegraf.conf</code></pre>
</div>
</div>
</div>
<div class="sect2">
<h3 id="_configuración_del_archivo_telegraf_conf_para_suscribirnos_a_un_topic_mqtt_inputs_mqtt_consumer">5.3. Configuración del archivo <code>telegraf.conf</code> para suscribirnos a un topic MQTT (<code>inputs.mqtt_consumer</code>)</h3>
<div class="paragraph">
<p>Tendremos que buscar la sección <code>inputs.mqtt_consumer</code> dentro del archivo <code>telegraf.conf</code> y configurar los siguientes valores:</p>
</div>
<div class="ulist">
<ul>
<li>
<p><code>servers</code></p>
</li>
<li>
<p><code>topics</code></p>
</li>
<li>
<p><code>data_format</code></p>
</li>
</ul>
</div>
<div class="paragraph">
<p>Existen más directivas de configuración (<code>qos</code>, <code>client_id</code>, <code>username</code>, <code>password</code>, etc.), pero en este proyecto sólo vamos a utilizar los valores que hemos indicado anteriormente.</p>
</div>
<div class="sect3">
<h4 id="_servers">5.3.1. <code>servers</code></h4>
<div class="paragraph">
<p>En esta directiva de configuración indicamos la URL del broker MQTT al que queremos conectarnos. En nuestro caso pondremos el nombre del servicio <code>mosquitto</code> que es como lo hemos definido en nuestro archivo <code>docker-compose.yml</code>.</p>
</div>
</div>
<div class="sect3">
<h4 id="_topics">5.3.2. <code>topics</code></h4>
<div class="paragraph">
<p>En esta directiva indicamos los topics a los que queremos suscribirnos. En nuestro caso pondremos el topic <code>iescelia/#</code>. El carácter <code>#</code> al final del topic indica que nos vamos a suscribir a cualquier topic que exista después de la cadena <code>iescelia/</code>.</p>
</div>
</div>
<div class="sect3">
<h4 id="_data_format">5.3.3. <code>data_format</code></h4>
<div class="paragraph">
<p>En esta directiva indicamos cómo es el formato de los datos que vamos a recibir por MQTT.</p>
</div>
<div class="paragraph">
<p>Telegraf contiene muchos plugins de propósito general que permiten parsear datos de entrada para convertirlos en el <strong>modelo de datos de métricas que utiliza InfluxDB</strong>.</p>
</div>
<div class="paragraph">
<p>Los mensajes de entrada que recibe Telegraf pueden estar en los siguientes formatos:</p>
</div>
<div class="ulist">
<ul>
<li>
<p><a href="https://github.com/influxdata/telegraf/tree/master/plugins/parsers/value">Value</a></p>
</li>
<li>
<p><a href="https://github.com/influxdata/telegraf/tree/master/plugins/parsers/influx">InfluxDB Line Protocol</a></p>
</li>
<li>
<p>Collectd</p>
</li>
<li>
<p>CSV</p>
</li>
<li>
<p>Dropwizard</p>
</li>
<li>
<p>Graphite</p>
</li>
<li>
<p>Grok</p>
</li>
<li>
<p>JSON</p>
</li>
<li>
<p>Logfmt</p>
</li>
<li>
<p>Nagios</p>
</li>
<li>
<p>Wavefront</p>
</li>
</ul>
</div>
<div class="paragraph">
<p>En este proyecto sólo vamos a estudiar <a href="https://github.com/influxdata/telegraf/tree/master/plugins/parsers/value">Value</a> y <a href="https://github.com/influxdata/telegraf/tree/master/plugins/parsers/influx">InfluxDB Line Protocol</a>.</p>
</div>
<div class="paragraph">
<p>Referencia:</p>
</div>
<div class="ulist">
<ul>
<li>
<p><a href="https://github.com/influxdata/telegraf/blob/master/docs/DATA_FORMATS_INPUT.md" class="bare">https://github.com/influxdata/telegraf/blob/master/docs/DATA_FORMATS_INPUT.md</a></p>
</li>
</ul>
</div>
</div>
</div>
<div class="sect2">
<h3 id="_formato_value">5.4. Formato: <code>Value</code></h3>
<div class="paragraph">
<p>El formato <code>value</code> permite convertir valores simples en métricas de Telegraf.</p>
</div>
<div class="paragraph">
<p>Es necesario indicar el tipo de dato al que queremos convertir el valor leído,
utilizando la opción de configuración <code>data_type</code>. Los tipos de datos
disponibles son:</p>
</div>
<div class="olist arabic">
<ol class="arabic">
<li>
<p><code>integer</code></p>
</li>
<li>
<p><code>float</code> o <code>long</code></p>
</li>
<li>
<p><code>string</code></p>
</li>
<li>
<p><code>boolean</code></p>
</li>
</ol>
</div>
<div class="sect3">
<h4 id="_ejemplo_de_configuración_de_la_sección_inputs_mqtt_consumer">5.4.1. Ejemplo de configuración de la sección <code>inputs.mqtt_consumer</code></h4>
<div class="paragraph">
<p>Si los mensajes que vamos a recibir por MQTT sólo contienen valores numéricos de tipo real que representan valores de temperatura, tendríamos que indicar:</p>
</div>
<div class="ulist">
<ul>
<li>
<p><code>data_format = "value"</code> y</p>
</li>
<li>
<p><code>data_type = "float"</code>.</p>
</li>
</ul>
</div>
<div class="paragraph">
<p>Una posible configuración para la sección <code>inputs.mqtt_consumer</code> del archivo <code>telegraf.conf</code> podría ser la siguiente.</p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code> [[inputs.mqtt_consumer]]
   servers = ["tcp://mosquitto:1883"] <i class="conum" data-value="1"></i><b>(1)</b>

   topics = [
     "iescelia/#" <i class="conum" data-value="2"></i><b>(2)</b>
   ]

  data_format = "value" <i class="conum" data-value="3"></i><b>(3)</b>
  data_type = "float" <i class="conum" data-value="4"></i><b>(4)</b></code></pre>
</div>
</div>
<div class="colist arabic">
<table>
<tr>
<td><i class="conum" data-value="1"></i><b>1</b></td>
<td>Indicamos la URL del broker MQTT al que queremos conectarnos. En nuestro caso pondremos el nombre del servicio <code>mosquitto</code> que es como lo hemos definido en nuestro archivo <code>docker-compose.yml</code>.</td>
</tr>
<tr>
<td><i class="conum" data-value="2"></i><b>2</b></td>
<td>Indicamos los topics a los que queremos suscribirnos. El carácter <code>#</code> al final del topic <code>iescelia/#</code> indica que nos vamos a suscribir a cualquier topic que haya después de <code>iescelia/</code>.</td>
</tr>
<tr>
<td><i class="conum" data-value="3"></i><b>3</b></td>
<td>Indicamos cuál es el formato de los datos que vamos a recibir por MQTT. En este caso indicamos el formato <code>value</code>, porque en el mensaje MQTT sólo vamos a recibir un valor numérico.</td>
</tr>
<tr>
<td><i class="conum" data-value="4"></i><b>4</b></td>
<td>Indicamos el tipo de dato del valor numérico que vamos a recibir por MQTT.</td>
</tr>
</table>
</div>
<div class="admonitionblock note">
<table>
<tr>
<td class="icon">
<i class="fa icon-note" title="Note"></i>
</td>
<td class="content">
<div class="paragraph">
<p><strong>Esta será la opción que vamos a elegir</strong> para configurar la sección <code>inputs.mqtt_consumer</code> en nuestro proyecto.</p>
</div>
</td>
</tr>
</table>
</div>
</div>
</div>
<div class="sect2">
<h3 id="_formato_influxdb_line_protocol">5.5. Formato: InfluxDB Line Protocol</h3>
<div class="paragraph">
<p>El formato <code>influx</code> no necesita ninguna configuración adicional. Los valores que
se reciben son parseados automáticamente a métricas de Telegraf.</p>
</div>
<div class="paragraph">
<p>Si utilizamos <code>data_format = "influx"</code> los mensajes de entrada tienen que cumplir la sintaxis del protocolo <a href="https://docs.influxdata.com/influxdb/v1.7/write_protocols/line_protocol_tutorial/">InfluxDB line protocol</a>.</p>
</div>
<div class="paragraph">
<p>A continuación se muestra un ejemplo de un mensaje con las sintaxis de <a href="https://docs.influxdata.com/influxdb/v1.7/write_protocols/line_protocol_tutorial/">InfluxDB line protocol</a>.</p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>weather,location=us-midwest temperature=82 1465839830100400200
  |    -------------------- --------------  |
  |             |             |             |
  |             |             |             |
+-----------+--------+-+---------+-+---------+
|measurement|,tag_set| |field_set| |timestamp|
+-----------+--------+-+---------+-+---------+</code></pre>
</div>
</div>
<div class="paragraph">
<p>El mensaje se compone de cuatro componentes:</p>
</div>
<div class="ulist">
<ul>
<li>
<p><strong><code>measurement</code></strong>: Indica la estructura de datos donde vamos a almacenar los valores en InfluxDB.</p>
</li>
<li>
<p><strong><code>tag_set</code></strong>: Es <strong>opcional</strong>. Son <em>tags</em> opcionales asociados al dato. Van detrás de una coma después del nombre de la <strong>measurement</strong>, <strong>sin espacios en blanco</strong>.</p>
</li>
<li>
<p><strong><code>field_set</code></strong>: Son los datos. Aparecen <strong>después de un espacio en blanco</strong> después de la <strong>meausrement</strong>. Podemos indicar varios datos separados por comas.</p>
</li>
<li>
<p><strong><code>timestamp</code></strong>: Es <strong>opcional</strong>. Es una marca de tiempo en Unix con precisión de nanosegundos.</p>
</li>
</ul>
</div>
<div class="sect3">
<h4 id="_ejemplos_de_mensajes_válidos_con_la_sintaxis_del_protocolo_influxdb_line_protocol">5.5.1. Ejemplos de mensajes válidos con la sintaxis del protocolo <a href="https://docs.influxdata.com/influxdb/v1.7/write_protocols/line_protocol_tutorial/">InfluxDB line protocol</a></h4>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>weather,location=us-midwest temperature=82 1465839830100400200</code></pre>
</div>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>weather temperature=82 1465839830100400200</code></pre>
</div>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>weather temperature=82</code></pre>
</div>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>weather temperature=82,humidity=71 1465839830100400200</code></pre>
</div>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>weather temperature=82,humidity=71</code></pre>
</div>
</div>
</div>
<div class="sect3">
<h4 id="_ejemplo_de_configuración_de_la_sección_inputs_mqtt_consumer_2">5.5.2. Ejemplo de configuración de la sección <code>inputs.mqtt_consumer</code></h4>
<div class="paragraph">
<p>Una posible configuración para la sección <code>inputs.mqtt_consumer</code> del archivo <code>telegraf.conf</code> podría ser la siguiente.</p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code> [[inputs.mqtt_consumer]]
   servers = ["tcp://mosquitto:1883"] <i class="conum" data-value="1"></i><b>(1)</b>

   topics = [
     "iescelia/#" <i class="conum" data-value="2"></i><b>(2)</b>
   ]

   data_format = "influx" <i class="conum" data-value="3"></i><b>(3)</b></code></pre>
</div>
</div>
<div class="colist arabic">
<table>
<tr>
<td><i class="conum" data-value="1"></i><b>1</b></td>
<td>Indicamos la URL del broker MQTT al que queremos conectarnos. En nuestro caso pondremos el nombre del servicio <code>mosquitto</code> que es como lo hemos definido en nuestro archivo <code>docker-compose.yml</code>.</td>
</tr>
<tr>
<td><i class="conum" data-value="2"></i><b>2</b></td>
<td>Indicamos los topics a los que queremos suscribirnos. El carácter <code>#</code> al final del topic <code>iescelia/#</code> indica que nos vamos a suscribir a cualquier topic que haya después de <code>iescelia/</code>.</td>
</tr>
<tr>
<td><i class="conum" data-value="3"></i><b>3</b></td>
<td>Indicamos cuál es el formato de los datos que vamos a recibir por MQTT. En este caso indicamos el formato <code>influx</code>, por lo tanto, los mensajes que vamos a recibir por MQTT tienen que cumplir la sintaxis del protocolo <a href="https://docs.influxdata.com/influxdb/v1.7/write_protocols/line_protocol_tutorial/">InfluxDB line protocol</a>.</td>
</tr>
</table>
</div>
<div class="admonitionblock warning">
<table>
<tr>
<td class="icon">
<i class="fa icon-warning" title="Warning"></i>
</td>
<td class="content">
<div class="paragraph">
<p>Esta opción <strong>no</strong> será la opción que vamos a elegir para configurar la sección <code>inputs.mqtt_consumer</code> en nuestro proyecto.</p>
</div>
</td>
</tr>
</table>
</div>
<div class="paragraph">
<p>Referencia:</p>
</div>
<div class="ulist">
<ul>
<li>
<p><a href="https://docs.influxdata.com/influxdb/v1.7/write_protocols/line_protocol_tutorial/">InfluxDB line protocol tutorial</a></p>
</li>
</ul>
</div>
</div>
</div>
<div class="sect2">
<h3 id="_configuración_del_archivo_telegraf_conf_para_almacenar_los_datos_en_influxdb_outputs_influxdb">5.6. Configuración del archivo <code>telegraf.conf</code> para almacenar los datos en InfluxDB (<code>outputs.influxdb</code>)</h3>
<div class="paragraph">
<p>Tendremos que buscar la sección <code>outputs.influxdb</code> dentro del archivo
<code>telegraf.conf</code> y configurar los siguientes valores:</p>
</div>
<div class="ulist">
<ul>
<li>
<p><code>urls</code></p>
</li>
<li>
<p><code>database</code></p>
</li>
<li>
<p><code>skip_database_creation</code></p>
</li>
<li>
<p><code>username</code></p>
</li>
<li>
<p><code>password</code></p>
</li>
</ul>
</div>
<div class="paragraph">
<p>Existen más directivas de configuración, pero en este proyecto sólo vamos a utilizar los valores que hemos indicado anteriormente.</p>
</div>
<div class="sect3">
<h4 id="_urls">5.6.1. <code>urls</code></h4>
<div class="paragraph">
<p>En esta directiva de configuración indicamos la URL de la instancia de InfluxDB donde vamos a almacenar los datos. En nuestro caso pondremos <code>influxdb</code> que es el nombre que le hemos puesto al servicio en el archivo <code>docker-compose.yml</code></p>
</div>
</div>
<div class="sect3">
<h4 id="_database">5.6.2. <code>database</code></h4>
<div class="paragraph">
<p>En esta directiva indicamos el nombre de la base de datos donde vamos a almacenar las métricas.</p>
</div>
</div>
<div class="sect3">
<h4 id="_skip_database_creation">5.6.3. <code>skip_database_creation</code></h4>
<div class="paragraph">
<p>Permite evitar la creación de bases de datos en InfluxDB cuando está configurada como <code>true</code>. Se recomienda configurarla a <code>true</code> cuando estemos trabajando con usuarios que no tienen permisos para crear bases de datos o cuando la base de datos ya exista.</p>
</div>
</div>
<div class="sect3">
<h4 id="_username_y_password">5.6.4. <code>username</code> y <code>password</code></h4>
<div class="paragraph">
<p>En esta directiva configuramos los parámetros de autenticación para conectarnos a InfluxDB.</p>
</div>
</div>
<div class="sect3">
<h4 id="_ejemplo_de_configuración_de_la_sección_outputs_mqtt_consumer">5.6.5. Ejemplo de configuración de la sección <code>outputs.mqtt_consumer</code></h4>
<div class="paragraph">
<p>Una posible configuración de la sección <code>outputs.influxdb</code> podría ser la siguiente:</p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>[[outputs.influxdb]]
  urls = ["http://influxdb:8086"] <i class="conum" data-value="1"></i><b>(1)</b>

  database = "iescelia_db" <i class="conum" data-value="2"></i><b>(2)</b>

  skip_database_creation = true <i class="conum" data-value="3"></i><b>(3)</b>

  username = "root" <i class="conum" data-value="4"></i><b>(4)</b>
  password = "root"</code></pre>
</div>
</div>
<div class="colist arabic">
<table>
<tr>
<td><i class="conum" data-value="1"></i><b>1</b></td>
<td>Indica la URL de la instancia de InfluxDB. En nuestro caso indicamos el nombre <code>influxdb</code> que es el nombre que le hemos puesto al servicio en el archivo <code>docker-compose.yml</code></td>
</tr>
<tr>
<td><i class="conum" data-value="2"></i><b>2</b></td>
<td>Indica el nombre de la base de datos donde vamos a almacenar las métricas.</td>
</tr>
<tr>
<td><i class="conum" data-value="3"></i><b>3</b></td>
<td>Si está a <code>true</code> no se crearán bases de datos en InfluxDB. Se recomienda configurarlo a <code>true</code> cuando estemos trabajando con usuarios que no tienen permisos para crear bases de datos o cuando la base de datos ya exista.</td>
</tr>
<tr>
<td><i class="conum" data-value="4"></i><b>4</b></td>
<td>Los parámetros de autenticación para conectarnos a InfluxDB.</td>
</tr>
</table>
</div>
<div class="admonitionblock note">
<table>
<tr>
<td class="icon">
<i class="fa icon-note" title="Note"></i>
</td>
<td class="content">
<div class="paragraph">
<p><strong>Esta será la opción que vamos a elegir</strong> para configurar la sección <code>outputs.mqtt_consumer</code> en nuestro proyecto.</p>
</div>
</td>
</tr>
</table>
</div>
</div>
</div>
</div>
</div>
<div class="sect1">
<h2 id="_influxdb">6. InfluxDB</h2>
<div class="sectionbody">
<div class="paragraph">
<p><a href="https://www.influxdata.com/">InfluxDB</a> es un sistema gestor de bases de datos diseñado para almacenar bases de datos de series temporales (TSBD - <em>Time Series Databases</em>). Estas bases de datos se suelen utilizar en aplicaciones de monitorización, donde es necesario almacenar y analizar grandes cantidades de datos con marcas de tiempo, como pueden ser datos de uso de cpu, uso memoria, datos de sensores de IoT, etc.</p>
</div>
<div class="sect2">
<h3 id="_descripción_del_servicio_en_docker_compose_yml_3">6.1. Descripción del servicio en <code>docker-compose.yml</code></h3>
<div class="paragraph">
<p><strong><code>docker-compose.yml</code></strong></p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>  influxdb: <i class="conum" data-value="1"></i><b>(1)</b>
    image: influxdb <i class="conum" data-value="2"></i><b>(2)</b>
    ports:
      - 8086:8086 <i class="conum" data-value="3"></i><b>(3)</b>
    volumes:
      - influxdb_data:/var/lib/influxdb <i class="conum" data-value="4"></i><b>(4)</b>
    environment:
      - INFLUXDB_DB=iescelia_db <i class="conum" data-value="5"></i><b>(5)</b>
      - INFLUXDB_ADMIN_USER=root <i class="conum" data-value="6"></i><b>(6)</b>
      - INFLUXDB_ADMIN_PASSWORD=root <i class="conum" data-value="7"></i><b>(7)</b>
      - INFLUXDB_HTTP_AUTH_ENABLED=true <i class="conum" data-value="8"></i><b>(8)</b></code></pre>
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
<td>Utilizamos la imagen Docker <a href="https://hub.docker.com/_/influxdb">influxdb</a>.</td>
</tr>
<tr>
<td><i class="conum" data-value="3"></i><b>3</b></td>
<td>Este servicio utilizará el puerto <code>8086</code> de nuestra máquina local para enlazarlo con el puerto <code>8086</code> el contenedor.</td>
</tr>
<tr>
<td><i class="conum" data-value="4"></i><b>4</b></td>
<td>Creamos un volumen con el nombre <code>influxdb_data</code> que estará enlazado con el directorio <code>/var/lib/influxdb</code> del contenedor.</td>
</tr>
<tr>
<td><i class="conum" data-value="5"></i><b>5</b></td>
<td>Nombre de la base de datos.</td>
</tr>
<tr>
<td><i class="conum" data-value="6"></i><b>6</b></td>
<td>Usuario de la base de datos.</td>
</tr>
<tr>
<td><i class="conum" data-value="7"></i><b>7</b></td>
<td>Contraseña del usuario de la base de datos.</td>
</tr>
<tr>
<td><i class="conum" data-value="8"></i><b>8</b></td>
<td>Habilitamos la autenticación básica HTTP.</td>
</tr>
</table>
</div>
</div>
<div class="sect2">
<h3 id="_conectar_a_influxdb_desde_un_terminal">6.2. Conectar a InfluxDB desde un terminal</h3>
<div class="paragraph">
<p>Podemos conectarnos al contenedor de InfluxDB desde un terminal para comprobar
si los datos que estamos recogiendo de los sensores se están insertando de forma
correcta en la base de datos.</p>
</div>
<div class="paragraph">
<p>En primer lugar necesitamos obtener el <code>ID</code> del contenedor que se está
ejecutando con InfluxDB. Para obtener el listado de todos los contenedores que
están en ejecución podemos ejecutar el siguiente comando:</p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>docker ps</code></pre>
</div>
</div>
<div class="paragraph">
<p>Ahora sólo tenemos que buscar el <code>ID</code> del contenedor con InfluxDB entre la lista
de contenedores. En el ejemplo que se muestra a continuación sería el valor
<code>27b06d552719</code>.</p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>CONTAINER ID        IMAGE                 COMMAND                  ...
27b06d552719        influxdb              "/entrypoint.sh infl…"   ...
...</code></pre>
</div>
</div>
<div class="paragraph">
<p>Una vez que tenemos el <code>ID</code> del contenedor de InfluxDB, creamos un nuevo proceso
con el comando <code>/bin/bash</code> dentro del contenedor para obtener un terminal que
nos permita interactuar con el contenedor.</p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>docker exec -it 27b06d552719 /bin/bash</code></pre>
</div>
</div>
<div class="admonitionblock warning">
<table>
<tr>
<td class="icon">
<i class="fa icon-warning" title="Warning"></i>
</td>
<td class="content">
<div class="paragraph">
<p>Tenga en cuenta que en su caso tendrá que reemplazar el valor <code>27b06d552719</code> por el identificador de su contenedor.</p>
</div>
</td>
</tr>
</table>
</div>
<div class="paragraph">
<p>Una vez que tenemos un terminal en el contenedor de InfluxDB, utilizamos el
cliente <code>influx</code> para conectarnos al sistema gestor de bases de datos con el
usuario y contraseña que hemos creado en el archivo <code>docker-compose.yml</code>.</p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>influx -username root -password root</code></pre>
</div>
</div>
<div class="paragraph">
<p>Después de autenticarnos, tendremos acceso al shell de InfluxDB donde podemos interaccionar con la base de datos con sentencias <a href="https://docs.influxdata.com/influxdb/v1.8/query_language/">InfluxQL (Influx Query Language)</a>, que tienen una sintaxis muy similar a SQL.</p>
</div>
<div class="paragraph">
<p>A continuación, se muestra una secuencia de sentencias <a href="https://docs.influxdata.com/influxdb/v1.8/query_language/">InfluxQL (Influx Query Language)</a> que podemos utilizar para comprobar si los datos de los sensores se están almacenando en la base de datos.</p>
</div>
<div class="paragraph">
<p><strong>Consultar el listado de bases de datos.</strong></p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>&gt; show databases

name
----
iescelia_db</code></pre>
</div>
</div>
<div class="paragraph">
<p><strong>Seleccionar la base de datos <code>iescelia_db</code>.</strong></p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>&gt; use iescelia_db</code></pre>
</div>
</div>
<div class="paragraph">
<p><strong>Mostrar las tablas que existen para la base de datos seleccionada.</strong></p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>&gt; show measurements

name
----
cpu
disk
diskio
kernel
mem
mqtt_consumer
processes
swap
system</code></pre>
</div>
</div>
<div class="paragraph">
<p><strong>Mostrar todos los datos que existen en la tabla <code>mqtt_consumer</code>.</strong></p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>&gt; select * from mqtt_consumer

time                host         topic                       value
----                ----         -----                       -----
1612791727450709588 3f4be32bd18b iescelia/aula20/temperature 30
1612791734718611922 3f4be32bd18b iescelia/aula20/temperature 30</code></pre>
</div>
</div>
<div class="paragraph">
<p><strong>Mostrar los campos clave de cada tabla.</strong></p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>&gt; show field keys</code></pre>
</div>
</div>
</div>
</div>
</div>
<div class="sect1">
<h2 id="_grafana">7. Grafana</h2>
<div class="sectionbody">
<div class="paragraph">
<p><a href="https://grafana.com">Grafana</a> es un servicio web que nos permite visualizar en un panel de control los datos almacenados en InfluxDB y otros sistemas gestores de bases de datos de series temporales.</p>
</div>
<div class="sect2">
<h3 id="_descripción_del_servicio_en_docker_compose_yml_4">7.1. Descripción del servicio en <code>docker-compose.yml</code></h3>
<div class="paragraph">
<p><strong><code>docker-compose.yml</code></strong></p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>grafana: <i class="conum" data-value="1"></i><b>(1)</b>
    image: grafana/grafana:5.4.3 <i class="conum" data-value="2"></i><b>(2)</b>
    ports:
      - 3000:3000 <i class="conum" data-value="3"></i><b>(3)</b>
    volumes:
      - grafana_data:/var/lib/grafana <i class="conum" data-value="4"></i><b>(4)</b>
    depends_on: <i class="conum" data-value="5"></i><b>(5)</b>
      - influxdb</code></pre>
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
<td>Utilizamos la imagen Docker <a href="https://hub.docker.com/r/grafana/grafana/">grafana</a>.</td>
</tr>
<tr>
<td><i class="conum" data-value="3"></i><b>3</b></td>
<td>Este servicio utilizará el puerto <code>3000</code> de nuestra máquina local para enlazarlo con el puerto <code>3000</code> el contenedor.</td>
</tr>
<tr>
<td><i class="conum" data-value="4"></i><b>4</b></td>
<td>Creamos un volumen con el nombre <code>grafana_data</code> que estará enlazado con el directorio <code>/var/lib/grafana</code> del contenedor.</td>
</tr>
<tr>
<td><i class="conum" data-value="5"></i><b>5</b></td>
<td>Indicamos que este servicio depende del servicio <code>influxdb</code> y que no podrá iniciarse hasta que el servicio de <code>influxdb</code> se haya iniciado.</td>
</tr>
</table>
</div>
</div>
<div class="sect2">
<h3 id="_configuración_de_un_data_source">7.2. Configuración de un <em>data source</em></h3>
<div class="paragraph">
<p>Grafana permite utilizar diferentes <em>data sources</em>, en nuestro proyecto utilizaremos InfluxDB pero también es posible utilizar AWS CloudWatch, Elasticsearch, MySQL o PostgreSQl entre otros.</p>
</div>
<div class="sect3">
<h4 id="_configuración_de_forma_manual">7.2.1. Configuración de forma manual</h4>
<div class="paragraph">
<p>Antes de crear un <em>dashboard</em> es necesario crear un <em>data source</em>. Sólo los usuarios que tengan rol de administrador podrán crear un <em>data source</em>.</p>
</div>
<div class="paragraph">
<p>Para crear un <em>data source</em> debe seguir los siguientes pasos.</p>
</div>
<div class="olist arabic">
<ol class="arabic">
<li>
<p>Accede al menu lateral haciendo clien en el icono de Grafana del encabezado superior.</p>
</li>
<li>
<p>Accede a la sección "Data Sources".</p>
</li>
<li>
<p>Añada un nuevo <em>data source</em>.</p>
</li>
<li>
<p>Seleccione InfluxDB en el desplegable donde se indica el tipo de <em>data source</em>.</p>
</li>
<li>
<p>Configure los parámetros <code>url</code>, <code>database</code>, <code>user</code> y <code>password</code> de su servicio InfluxDB.</p>
</li>
</ol>
</div>
<div class="paragraph">
<p>En la documentación oficial puede encontrar más información sobre <a href="https://grafana.com/docs/grafana/latest/datasources/influxdb/#using-influxdb-in-grafana">cómo utilizar InfluxDB con Grafana</a>.</p>
</div>
</div>
<div class="sect3">
<h4 id="_configuración_con_aprovisionamiento_automático">7.2.2. Configuración con aprovisionamiento automático</h4>
<div class="paragraph">
<p>Es posible configurar Grafana para que utilize un archivo de configuración de forma automática para evitar tener que realizar la configuración de forma manual.</p>
</div>
<div class="paragraph">
<p>En nuestro proyecto, vamos a crear un archivo con el nombre <code>datasource.yml</code> dentro de la siguiente estructura de directorios</p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>├── grafana-provisioning
│   └── datasources
│       └── datasource.yml</code></pre>
</div>
</div>
<div class="paragraph">
<p>El contenido del archivo <code>datasource.yml</code> será el siguiente.</p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code class="language-datasource.yml" data-lang="datasource.yml">apiVersion: 1
datasources:
  - name: InfluxDB
    type: influxdb
    access: proxy
    database: iescelia_db <i class="conum" data-value="1"></i><b>(1)</b>
    user: root <i class="conum" data-value="2"></i><b>(2)</b>
    password: root <i class="conum" data-value="3"></i><b>(3)</b>
    url: http://influxdb:8086 <i class="conum" data-value="4"></i><b>(4)</b>
    isDefault: true
    editable: true</code></pre>
</div>
</div>
<div class="colist arabic">
<table>
<tr>
<td><i class="conum" data-value="1"></i><b>1</b></td>
<td>Nombre de la base de datos InfluxDB</td>
</tr>
<tr>
<td><i class="conum" data-value="2"></i><b>2</b></td>
<td>Nombre de usuario para acceder a la base de datos</td>
</tr>
<tr>
<td><i class="conum" data-value="3"></i><b>3</b></td>
<td>Contraseña del usuario para acceder a la base de datos</td>
</tr>
<tr>
<td><i class="conum" data-value="4"></i><b>4</b></td>
<td>URL del servicio InfluxDB</td>
</tr>
</table>
</div>
<div class="paragraph">
<p>Para que el aprovisionamiento se realice forma correcta, tenemos que definir un nuevo volumen en el servicio de Grafana en el archivo <code>docker-compose.yml</code>.</p>
</div>
<div class="paragraph">
<p><strong><code>docker-compose.yml</code></strong></p>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>grafana: <i class="conum" data-value="1"></i><b>(1)</b>
    image: grafana/grafana:5.4.3 <i class="conum" data-value="2"></i><b>(2)</b>
    ports:
      - 3000:3000 <i class="conum" data-value="3"></i><b>(3)</b>
    volumes:
      - grafana_data:/var/lib/grafana <i class="conum" data-value="4"></i><b>(4)</b>
      - ./grafana-provisioning/:/etc/grafana/provisioning <i class="conum" data-value="5"></i><b>(5)</b>
    depends_on: <i class="conum" data-value="6"></i><b>(6)</b>
      - influxdb</code></pre>
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
<td>Utilizamos la imagen Docker <a href="https://hub.docker.com/r/grafana/grafana/">grafana</a>.</td>
</tr>
<tr>
<td><i class="conum" data-value="3"></i><b>3</b></td>
<td>Este servicio utilizará el puerto <code>3000</code> de nuestra máquina local para enlazarlo con el puerto <code>3000</code> el contenedor.</td>
</tr>
<tr>
<td><i class="conum" data-value="4"></i><b>4</b></td>
<td>Creamos un volumen con el nombre <code>grafana_data</code> que estará enlazado con el directorio <code>/var/lib/grafana</code> del contenedor.</td>
</tr>
<tr>
<td><i class="conum" data-value="5"></i><b>5</b></td>
<td>Creamos un volumen de tipo <em>bind mount</em> entre el directorio local de nuestra máquina <code>./grafana-provisioning/</code> y el directorio <code>/etc/grafana/provisioning</code> del contenedor.</td>
</tr>
<tr>
<td><i class="conum" data-value="6"></i><b>6</b></td>
<td>Indicamos que este servicio depende del servicio <code>influxdb</code> y que no podrá iniciarse hasta que el servicio de <code>influxdb</code> se haya iniciado.</td>
</tr>
</table>
</div>
<div class="paragraph">
<p>En la documentación oficial puede encontrar más información sobre <a href="https://grafana.com/docs/grafana/latest/datasources/influxdb/#configure-the-data-source-with-provisioning" class="bare">https://grafana.com/docs/grafana/latest/datasources/influxdb/#configure-the-data-source-with-provisioning</a>
[cómo configurar un <em>data source</em> con aprovisionamiento].</p>
</div>
</div>
</div>
<div class="sect2">
<h3 id="_configuración_de_un_dashboard">7.3. Configuración de un <em>dashboard</em></h3>
<div class="paragraph">
<p>A continuación se muestra una imagen de cómo se podría configurar un dashboard para Grafana.</p>
</div>
<div class="imageblock">
<div class="content">
<img src="https://github.com/jesus2307/iaw-practica-18/blob/main/practica18/resultado%20graficas.PNG" alt="grafana">
</div>
</div>
</div>
</div>
</div>
<div class="sect1">
<h2 id="_autor">8. Autor</h2>
<div class="sectionbody">
<div class="paragraph">
<p>Este material ha sido desarrollado por <a href="https://josejuansanchez.org">José Juan Sánchez</a>.</p>
</div>
</div>
</div>
<div class="sect1">
<h2 id="_licencia">9. Licencia</h2>
<div class="sectionbody">
<div class="paragraph">
<p>El contenido de esta web está bajo una <a href="http://creativecommons.org/licenses/by-nc-sa/4.0/">licencia de Creative Commons Reconocimiento-NoComercial-CompartirIgual 4.0 Internacional</a>.</p>
</div>
</div>
</div>
<div class="sect1">
<h2 id="_referencias">10. Referencias</h2>
<div class="sectionbody">
<div class="ulist">
<ul>
<li>
<p><a href="https://github.com/josejuansanchez/iot-demo">Proyecto de IoT sencillo con Wemos D1 mini, sensores DHT11, MQTT y un panel de control web con node.js</a>.</p>
</li>
</ul>
</div>
