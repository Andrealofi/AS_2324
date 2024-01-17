# Ejercicio 2: Realizar las siguientes operacione en Elasticsearch utilizando su API REST.

#### Crear un índice “usuarios” con las siguientes características:
#### § Campo “nombre”, tipo text.
#### § Campo “apellido”, tipo text.
#### § Campo “edad”, tipo integer.

```bash
# Primero hacer esto:
curl -X PUT "34.38.180.70:9200/usuarios?pretty" -H 'Content-Type: application/json' -d'
# Luego meter esto: 
{
  "mappings": {
    "properties": {
      "nombre": {
        "type": "text"
      },
      "apellido": {
        "type": "text"
      },
      "edad": {
        "type": "integer"
      }
    }
  }
}
'
```
### Añadir los siguientes elementos al indice: 

| ID | Nombre | Apellido | Edad (años) |
|----|--------|----------|-------------|
| 10 | Jon    | Blanco   | 27          |
| 11 | Amaia  | Lopez    | 28          |
| 12 | Hodei  | Bilbao   | 33          |

```bash
curl -X POST "34.38.180.70:9200/usuarios/_doc/10?pretty" -H 'Content-Type: application/json' -d'
{
  "nombre": "Jon",
  "apellido": "Blanco",
  "edad": 27
}
'

curl -X POST "34.38.180.70:9200/usuarios/_doc/11?pretty" -H 'Content-Type: application/json' -d'
{
  "nombre": "Amaia",
  "apellido": "Lopez",
  "edad": 28
}
'

curl -X POST "34.38.180.70:9200/usuarios/_doc/12?pretty" -H 'Content-Type: application/json' -d'
{
  "nombre": "Hodei",
  "apellido": "Bilbao",
  "edad": 33
}
'

```
### Realizar las siguientes búsquedas: 
#### Obtener los datos de los usuarios cuyo apellido empiece por la letra B:
```bash
curl -X GET "34.38.180.70:9200/usuarios/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "prefix": { "apellido": "b" } }
}
'
# Con la B mayuscula no te deja, solo con minuscula. Porque para que podamos buscar mayuscula tiene que ser keyword.
```

#### Recuperar los datos de “Amaia Lopez”, pero utilizando “Loepz” como término de búsqueda:
```bash
curl -X GET "34.38.180.70:9200/usuarios/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "fuzzy": { "apellido": "loepz" } }
}
'
# Aqui tambien hay que utilizar mimuscula porque con mayuscula no te deja.
```


