E# Laboratorio 11: Pila ELK

## Preparar el entorno:

### Levantar ELK: 

```bash
docker compose up -d
```

## Analisis de logs conectando con Syslog

### Configuración de logstash.  configurar una fuente de entrada tipo “conexión TCP” a la escucha en el puerto 10500, un filtro de Grok para parsear cada evento de log y Elasticsearch como destino al que reenviar los datos (a un índice llamado “logs-sistema”).

##### Debemos hacer en Samples en Grook meter el coso que nos dan:  Dec 1 11:33:22 as-vm systemd[1]: systemd-tmpfiles-clean.service: Succeeded  y luego iremos poniendo cada etiqueta en Grok pattern para crear el "message" de abajo. (date cuenta de los espacios)

```bash
$ nano longstash.conf

  GNU nano 6.2                                logstash.conf *                                        
input{
  tcp{
      port => 10500
    }
}
filter{
    grok{
        match => { "message" => "%{MONTH:mes} %{MONTHDAY:dia} %{TIME:hora} %{USERNAME:usuario} %{DATA:maquina} %{GREEDYDATA:servicio} %{WORD:resultado}"}
    }
}
output{
    elasticsearch{
        hosts => ["34.38.180.70:9200"]
        index => "logs-sistema"
    }
}


```

### Ahora hay que modificar el fichero  /etc/rsyslog.d/50-default.conf  para poder exponer los logs por ese puerto.

```bash
sudo nano /etc/rsyslog.d/50-default.conf  # Editamos el fichero y debajo de los logs, añadimos:
...
*.* @@127.0.0.1:10500
...

sudo systemctl restart rsyslog.service          #Aplicamos los cambios

```

### Ahora, tambien deberemos cambiar el docker-compose.yaml de ELK porque el puerto al que apunta logstash será el 9901 y ahora tenemos el 10500

```bash
$ sudo nano docker-compose.yaml


version: '3'
services:
  logstash:
    image: docker.elastic.co/logstash/logstash:8.11.1
    container_name: logstash
    volumes:
      - ./pipeline:/usr/share/logstash/pipeline
    depends_on:
      - elasticsearch
    ports:
      - 10500:10500

```

### Hay que modificar tambien los puerto TCP y UDP del Firewall al 9200 los dos para que aparezca.
### Una vez tenemos tenemos todo modificado, tendremos que hacer lo siguiente: 
```bash
docker compose up -d                            #Levantamos el contenedor de logstash

logger "Hola mundo"                             #Generamos un log

curl -X GET "34.38.180.70:9200/logs-sistema/_search?pretty"

```

## Analisis de logs usando Beats:

### FileBeat

```bash
# Descargar Filebeat
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-8.11.1-amd64.deb

# Instalar Filebeat
sudo dpkg -i filebeat-8.11.1-amd64.deb

# Configurar Filebeat
sudo nano /etc/filebeat/filebeat.yml

# Hay que cambiar estas dos cosas:
# 1. Comentar la línea “output.elasticsearch” y las indentadas inmediatamente debajo, para evitar conexiones
directas a Elasticsearch.
# 2. Descomentar la línea “output.logstash” y la línea “hosts” que se encuentra de inmediatamente después. Se
debe editar la línea hosts para incluir la dirección del servicio Logstash en formato <IP_A DONDE QUIERES IR>:<PUERTO>

# Listar los logs que puede monitorizar y recopilar filebeat
sudo filebeat modules list

# Activar la monitorizaion de los logs del sistema
sudo filebeat modules enable system

# Configurar system.yml. En este fichero, se deben cambiar los valores “enabled: false”
a “enabled: true” para los apartados syslog y auth
sudo nano /etc/filebeat/modules.d/system.yml

# Iniciar Filebeat
sudo service filebeat start
```

### Logstash

```bash
# Editar el fichero de configuracion
sudo nano logstash.conf
    input{
        beats{
            port => 5044
        }
    }
    output{
        elasticsearch{
            hosts => ["IP_QUE-RECIBE:9200"]
            index => "logs-filebeat"
        }
    }

# Editar docker-compose.yml
sudo nano docker-compose.yml
    version: '3'
    services:
        logstash:
            image: docker.elastic.co/logstash/logstash:8.11.1
            container_name: logstash
            volumes:
            - ./pipeline:/usr/share/logstash/pipeline
            depends_on:
            - elasticsearch
            ports:
            - 5044:5044

        elasticsearch:
            image: docker.elastic.co/elasticsearch/elasticsearch:8.11.1
            container_name: elasticsearch
            environment:
            - discovery.type=single-node
            - xpack.security.enabled=false
            volumes:
            - data01:/usr/share/elasticsearch/data
            ports:
            - 9200:9200
            - 9300:9300

        kibana:
            image: docker.elastic.co/kibana/kibana:8.11.1
            container_name: kibana
            ports:
            - 80:5601
            depends_on:
            - elasticsearch
    volumes:
        data01:
```


