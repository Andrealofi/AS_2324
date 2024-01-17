# Laboratorio 10: Elasticsearch

## 1. Preparar el entorno

### Crear un indice llamado "bank" e importar el accounts.json: 
#### El _bulk se pone para poder pasar todos los documentos (filas) de los datos, si no lo pones solo conseguiras pasar la primera fila) El pretty y refresh se usan para refrescar el indie y proporcionar una respuesta formateada de manera legible.

```bash
curl -H "Content-Type: application/json" -XPOST "localhost:9200/bank/_bulk?pretty&refresh" --data-binary "@accounts.json"
```

### Comprobar que se ha importado correctamente (para poder ver toda la estructura)

```bash
curl -XGET "localhost:9200/bank/_search?pretty"
```

--- 
## Paginación (Obtener los resultados agrupados): --> por ejemplo, para recuperar los resultados de búsqueda en bloques y después mostrarlos de forma "bonita".

### Recuperar los 20 primeros documentos del indice "bank": 

```bash
curl -XGET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 20
}
'
```
### Recuperar los segundos 20 documentos del indice "bank": 

```bash
curl -XGET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 20,
  "from": 20
}
'
```

### Buscar los datos de las personas que residen en Texas (codigo de estado "TXT") y devolver los primeros 15 resultados.

```bash
curl -XGET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match": { "state": "TX" } },
  "size": 15
}
'
```

--- 
## Ordenación: Para poder mostrar los datos ordenados segun los criterios que quieras

### Recuperar los datos de los residentes en el estado de Los Angeles (codigo de estado "LA") y mostrar los resultados ordenados por edad de forma ASCENDENTE

```bash
curl -XGET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match": { "state": "LA" }},
  "sort" : { "age": { "order": "asc" } }
}
'
```
### Recuperar los datos de los residentes en el estado de New Jersey (código de estado “NJ”) y mostrar los resultados ordenados por su balance de forma ASCENDENTE.

```bash
curl -XGET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match": { "state": "NJ" }},
  "sort" : { "balance": { "order": "asc" } }
}
'
```

--- 
## Filtros: Eliminar documentos d elos resultados de una busqueda.

### Recuperar los datos de usuarios cuyo estado de residencia sea Los Ángeles (código de estado “LA”) pero que su ciudad de residencia NO sea Loretto, y que además su edad sea SUPERIOR a los 33 años.

```bash
curl -XGET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool": {
      "must": { "match": { "state": "LA" } },
      "must_not": { "match": { "city": "Loretto" } },
      "filter": { "range" : { "age" : { "gte" : 33 }}}
}
}
}'

```
### Recuperar los datos de usuarios cuyo estado de residencia sea Ohio (código de estado “OH”) y su edad sea SUPERIOR a 39 años.

```bash
curl -XGET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool": {
       "must": {"match": { "state": "OH" } },
        "filter": { "range": {"age": { "gte": 39} } }
}
}
}
'

```

--- 
## Busquedas difusas: busquedas tolerantes a fallos gramaticales y errores de escritura.

### Utilizar una búsqueda difusa para recuperar los datos de la persona residente en Wyoming, pero utilizando ”Woyming” como término de búsqueda.

```bash
curl -XGET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "fuzzy": { "city": "Woyming" } }
}
'
```

### Modificar la búsqueda anterior para mostrar los datos de la misma persona, pero utilizando “Wyomin” como término de búsqueda.

```bash
curl -XGET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "fuzzy": { "city": "Wyomin" } }
}
'
```

--- 
## Prefijos de búsqueda y comodines: se pueden realuzar busquedas solo una parte del comienzo de los terminos a buscar, por ejemplo con prefijos y tambien se puede utilizar comodines (wildcards) para realizar terminos de busqueda.

### Recuperar los datos de las personas cuyos apellidos comiencen por “Mc”

```bash
curl -XGET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "prefix": { "lastname.keyword": "Mc" } }
}
'
```

### Recuperar los datos de las personas cuya ciudad de residencia comience por la letra “G” y acabe con “field”

```bash
curl -XGET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "wildcard": { "city.keyword": "G*field" } }
}
'
```
--- 
## Expresiones regulares: más allá de utilizar comodines en las búsquedas, expresiones regulares para encontrar patrrones en concreto.

### Utilizando expresiones regulares, recuperar los datos de las personas cuyo nombre de empleador comience por la letra "A" y esté compuesto por 4 o 5 letras, p.e. los empleadores "Avit" o "Amtap".

```bash
curl -XGET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "regexp": { "firstname.keyword": "A.{3,4}" } }
}
'
```

