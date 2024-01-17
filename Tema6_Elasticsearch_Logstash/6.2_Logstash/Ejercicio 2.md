# Ejercicio 2

## Crear un patron Grok que encaje con el formato de Logs mostrados en la diapositiva anterior

```patron
%{IP:cliente} - - %{DATE:fecha} - %{INT:estado} %{WORD:metodo} %{URI:direccion}
```

## Configurar pipeline Logstash
### Recibir datos como conexiones HTTP al puerto 9901 (crear la regla de firewall del puerto de entrada 9901)
### Utilizar el patron Grok para parsear cada linea recibida
### Escribir cada linea log en el indice "logs-apache" de Elasticsearch

```conf
input{
    http{
        port => 9901
    }
}
filter{
    grok{
        match => { "message" => "%{IP:cliente} - - %{DATE:fecha} - %{INT:estado} %{WORD:metodo} %{URI:direccion}"}
    }
}
output{
    elasticsearch{
        hosts => ["http://elasticsearch:9200"]
        index => "logs-apache"
    }
}
```

## Iniciar Logstash y Elasticsearch con Docker Compose

```bash
docker compose up -d
```

## Enviar las siguientes lineas de Log a Logstash:

```logs
124.173.67.77 - - 23/07/2016 - 0400 GET http://www.059boss.com/index.php
195.182.131.107 - - 23/07/2016 - 0400 GET http://asconprofi.ru/common/proxy.php
155.94.224.168 - - 23/07/2016 - 0400 GET http://www.daqimeng.com/user/login
119.29.32.85 - - 23/07/2016 - 0400 GET http://www.tianx.top
```

```bash
curl -X POST "localhost:9901" -d "124.173.67.77 - - 23/07/2016 - 0400 GET http://www.059boss.com/index.php"
curl -X POST "localhost:9901" -d "195.182.131.107 - - 23/07/2016 - 0400 GET http://asconprofi.ru/common/proxy.php"
curl -X POST "localhost:9901" -d "155.94.224.168 - - 23/07/2016 - 0400 GET http://www.daqimeng.com/user/login"
curl -X POST "localhost:9901" -d "119.29.32.85 - - 23/07/2016 - 0400 GET http://www.tianx.top"
```

## Verificar que los datos se almacenan correctamente en el indice "logs-apache" de Elasticsearch

```bash
curl -X GET "localhost:9200/logs-apache/_search?pretty"
```
