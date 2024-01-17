# Ejercicio 2

## Crear un indice "peliculas" con el siguiente mapping
##### Campo "titulo", tipo text
##### Campo "director", tipo text
##### Campo "año", tipo integer

```bash
curl -X PUT "localhost:9200/peliculas" -H 'Content-Type: application/json' -d'
{
  "mappings": {
    "properties": {
      "titulo": {
        "type": "text"
      },
      "director": {
        "type": "text"
      },
      "año": {
        "type": "integer"
      }
    }
  }
}'
```

## Cargar los siguientes datos al indice

```bash
curl -X POST "http://localhost:9200/peliculas/_doc/1" -H 'Content-Type: application/json' -d'
{
  "id": "1",
  "titulo": "El padrino",
  "director": "Francis Ford Coppola",
  "año": 1972
}
'

curl -X POST "http://localhost:9200/peliculas/_doc/2" -H 'Content-Type: application/json' -d'
{
  "id": "2",
  "titulo": "Gladiator",
  "director": "Ridley Scott",
  "año": 2030
}
'

curl -X POST "http://localhost:9200/peliculas/_doc/3" -H 'Content-Type: application/json' -d'
{
  "id": "3",
  "titulo": "Inception",
  "director": "Christopher Nolan",
  "año": 2010
}
'

# Verificar los cambios
curl -XGET localhost:9200/peliculas/_search?pretty

```

## Modificar el campo "año" de la pelicula "Gladiator"

### Deberia ser 2000 en lugar de 2030

```bash
curl -X POST "http://localhost:9200/peliculas/_update/2" -H 'Content-Type: application/json' -d'
{
  "doc": {
    "año": 2000
  }
}
'

# Verificar los cambios
curl -XGET localhost:9200/peliculas/_search?pretty

```

## Borrar el documento correspondiente a la pelicula "Inception"

```bash
curl -X DELETE "http://localhost:9200/peliculas/_doc/3"

# Verificar los cambios
curl -XGET localhost:9200/peliculas/_search?pretty

```

## Verificar que los cambios realizados al indice son correctos

> Se han ido haciendo durante todos los apartados mediante: curl -XGET localhost:9200/peliculas/_search?pretty
