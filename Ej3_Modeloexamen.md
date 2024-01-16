# Ejercicio 3: Configurar un pipeline Logstash con las siguientes caracteristicas: 


##  Recibir datos en formato JSON a través de conexiones HTTP en el puerto 9870
##  Eliminar el campo "ciudad" de cada objeto JSON recibido
##  Escribir cada objeto JSON en el índice "usuarios" de Elasticsearch

```bash
input {
    http {
        port => 9870
        codec => "json"
    }
}
filter {
    mutate {
        remove_field => ["ciudad"]
    }
}
output {
    elasticsearch {
        hosts => ["34.38.180.70:9200"]
        index => "usuarios"
    }
}

```
