# Ejercicio 3: 

## Crear un indice llamado "shakespeare" siguiendo el esquema de la diapositiva anterior:

```bash
curl -XPUT localhost:9200/shakespeare -H 'Content-Type: application/json' -d'
{
    "mappings": {
        "properties": {
            "speaker": {"type": "keyword"},
            "play_name": {"type": "keyword"},
            "line_id": {"type": "integer"},
            "speech_number": {"type": "integer"}
        }
    }

```

## Cargar el dataset de las obras de Shakespeare en el indice "shakespeare"

```bash
curl -H 'Content-Type: application/x-ndjson' -XPOST 'localhost:9200/shakespeare/_bulk?pretty' --data-binary @shakes_cut.json
```

## Encontrar

### A que obra de Shakespeare pertenece la frase "To be or not to be" y en que linea de la obra se encuentra

```bash
curl -XGET 'localhost:9200/shakespeare/_search?pretty' -H 'Content-Type: application/json' -d'
{
    "query" : {
        "match_phrase" : {
            "text_entry" : "To be or not to be"
        }
    }
}'
```

### Cuantas frases tiene el personaje "OCTAVIUS CAESAR" en la obra "Antony and Cleopatra"

```bash
curl -XGET 'localhost:9200/shakespeare/_count?pretty' -H 'Content-Type: application/json' -d'
{
    "query" : {
        "bool" : {
            "must" : [
                { "match" : { "play_name" : "Antony and Cleopatra" } },
                { "match" : { "speaker" : "OCTAVIUS CAESAR" } }
            ]
        }
    }
}'
```
