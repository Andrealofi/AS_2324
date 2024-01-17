# Ejercicio 1

## Configurar pipeline Logstash

### Recibir datos a traves de conexiones HTTP en el puerto 9900 (teneis que crear la regla de Firewall para el puerto 9900 de entrada)
### Formato: JSON
### Escribir cada objeto JSON en el indice "mis-logs" de Elasticsearch


```conf
input {
    http {
        port => 9900
        codec => "json"
    }
}

output {
    elasticsearch {
        hosts => ["34.38.180.70:9200"]
        index => "mis-logs"
    }
}
```

## Iniciar Logstash y Elasticsearch con Compose

### Redirigir puerto 9900 para Logstash

```bash
docker compose up -d
```

## Enviar los siguientes objetos JSON a Logstash:

```JSON
{
"timestamp": "Nov 28 07:55:04",
"device" : "server",
"process" : "kernel",
"event-number" : "1150.308049",
"message" : "port 2(veth78fa91b) entered blocking state"
}

{
"timestamp": "Nov 28 07:55:05",
"device" : "server",
"process" : "systemd-networkd",
"event-number" : "730",
"message" : "Gained IPv6LL"
}
```

```bash
# Ahora, enviamos los objetos esos:
curl -X POST -H "Content-Type: application/json" -d '{"timestamp": "Nov 28 07:55:04","device" : "server","process" : "kernel","event-number" : "1150.308049","message" : "port 2(veth78fa91b) entered blocking state"}' http://localhost:9900

curl -X POST -H "Content-Type: application/json" -d '{"timestamp": "Nov 28 07:55:05","device" : "server","process" : "systemd-networkd","event-number" : "730","message" : "Gained IPv6LL"}' http://localhost:9900
```

## Verificar que los datos estan en el indice "mis-logs" de Elasticsearch

```bash
curl -X GET "localhost:9200/mis-logs/_search?pretty"
```
