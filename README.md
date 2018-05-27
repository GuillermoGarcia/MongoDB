# MongoDB  - Práctica para Bases de Datos (Windows)


## Crear una base de datos

```console
> use GestorDePartidas
switched to db GestorDePartidas
```


## Tener una colección
```console
> db.createCollection('Partidas')
{ "ok" : 1 }
```


## Insertar, modificar y borrar documentos en la colección

### Insertar
```console
> db.Partidas.insert({nombre: "Paseando con Orcos", descripcion: "Aventuras y Desventuras de un clan de Orcos que vagan por 'La Tierra Media' tras la destrucción del ojo.", director: "Enrique", reglas: "Paramo 3.2", numMaxJug: 5, numActJug: 2})
WriteResult({ "nInserted" : 1 })
> db.Partidas.insert({nombre: "Buscando un Hogar", descripcion: "El 10º Regimiento de Al’Ratk ha perdido su planeta tras ser destruidos por las fuerzas malignas.", director: "José Rut", reglas: "Paramo 2.5", numMaxJug: 8, numActJug: 6})
WriteResult({ "nInserted" : 1 })
> db.Partidas.insert({nombre: "Ravenloft", descripcion: "Explora las tierras de Ravenloft donde el mal crece y se hace fuerte.", director: "Nildik", reglas: "DnD 3.5", numMaxJug: 5, numActJug: 5})
WriteResult({ "nInserted" : 1 })
> db.Partidas.insert({nombre: "Sombras en la Estepa", descripcion: "El grupo mercenario Camacho`s Caballeros debe defender el planeta Gibraltar.", director: "Ulric", reglas: "Battletech", numMaxJug: 4, numActJug: 0})
WriteResult({ "nInserted" : 1 })
> db.Partidas.insert({nombre: "Sombras en la Estepa", descripcion: "El grupo mercenario Camacho`s Caballeros debe defender el planeta Gibraltar.", director: "Phelan", reglas: "Battletech", numMaxJug: 4, numActJug: 0})
WriteResult({ "nInserted" : 1 })
```

### Modificar
```console
> p = db.Partidas.findOne({nombre: "Sombras en la Estepa"})
{
        "_id" : ObjectId("5b0954225715367cd8ddb782"),
        "nombre" : "Sombras en la Estepa",
        "descripcion" : "El grupo mercenario Camacho`s Caballeros debe defender el planeta Gibraltar.",
        "director" : "Ulric",
        "reglas" : "Battletech",
        "numMaxJug" : 4,
        "numActJug" : 0
}
> p.numActJug = 1
1
> db.Partidas.update({nombre: "Sombras en la Estepa"}, p)
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
```

### Borrar
```console
> db.Partidas.remove({director: "Phelan"})
WriteResult({ "nRemoved" : 1 })
```



## Crear un índice sobre un campo de la colección

Creamos un indice sobre el campo "director" para que sea único y por tanto una misma persona no pueda dirigir más de una partida a la vez.

### Comprobación sin el Indice

Primero comprobamos que al no existir el indice podemos añadir otra partida con un director que ya está en otra partida.

```console
> db.Partidas.insert({nombre: "Metrodungeon", descripcion: "Las estaciones de metro se han convertido en dungeons plagados de monstruos.", director: "Ulric", reglas: "Paramo 3.3", numMaxJug: 8, numActJug: 0})
WriteResult({ "nInserted" : 1 })
> db.Partidas.find()
{ "_id" : ObjectId("5b0953ed5715367cd8ddb77f"), "nombre" : "Paseando con Orcos", "descripcion" : "Aventuras y Desventuras de un clan de Orcos que vagan por 'La Tierra Media' tras la destrucción del ojo.", "director" : "Enrique", "reglas" : "Paramo 3.2", "numMaxJug" : 5, "numActJug" : 2 }
{ "_id" : ObjectId("5b0954085715367cd8ddb780"), "nombre" : "Buscando un Hogar", "descripcion" : "El 10º Regimiento de Al’Ratk ha perdido su planeta tras ser destruidos por las fuerzas malignas.", "director" : "José Rut", "reglas" : "Paramo 2.5", "numMaxJug" : 8, "numActJug" : 6 }
{ "_id" : ObjectId("5b0954155715367cd8ddb781"), "nombre" : "Ravenloft", "descripcion" : "Explora las tierras de Ravenloft donde el mal crece y se hace fuerte.", "director" : "Nildik", "reglas" : "DnD 3.5", "numMaxJug" : 5, "numActJug" : 5 }
{ "_id" : ObjectId("5b0954225715367cd8ddb782"), "nombre" : "Sombras en la Estepa", "descripcion" : "El grupo mercenario Camacho`s Caballeros debe defender el planeta Gibraltar.", "director" : "Ulric", "reglas" : "Battletech", "numMaxJug" : 4, "numActJug" : 1 }
{ "_id" : ObjectId("5b0a92015715367cd8ddb784"), "nombre" : "Metrodungeon", "descripcion" : "Las estaciones de metro se han convertido en dungeons plagados de monstruos.", "director" : "Ulric", "reglas" : "Paramo 3.3", "numMaxJug" : 8, "numActJug" : 0 }
```
Efectivamente nos ha dejado añadir otra partida con un "director" que ya está en otra guardada.

### Creamos el índice

Como hemos dicho creamos un indice tipo único sobre el campo "director".

```console
> db.Partidas.createIndex({"director": 1}, {"unique":true})
{
        "ok" : 0,
        "errmsg" : "E11000 duplicate key error collection: test.Partidas index: director_1 dup key: { : \"Ulric\" }",
        "code" : 11000,
        "codeName" : "DuplicateKey"
}
```

No nos deja crearlo ya que no se cumple al tener dor partidas con el mismo "director". Así borramos la partida duplicada y volvemos a probar.

```console
> db.Partidas.remove({nombre: "Metrodungeon"})
WriteResult({ "nRemoved" : 1 })
> db.Partidas.createIndex({"director": 1}, {"unique":true})
{
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 2,
        "ok" : 1
}
```

### Tras crear el índice

Nuevamente comprobamos si nos deja tener más de una partida con el mismo "director".

```console
> db.Partidas.insert({nombre: "Metrodungeon", descripcion: "Las estaciones de metro se han convertido en dungeons plagados de monstruos.", director: "Ulric", reglas: "Paramo 3.3", numMaxJug: 8, numActJug: 0})
WriteResult({
        "nInserted" : 0,
        "writeError" : {
                "code" : 11000,
                "errmsg" : "E11000 duplicate key error collection: test.Partidas index: director_1 dup key: { : \"Ulric\" }"
        }
})
```
Efectivamente, no solo no se inserta sino que también nos muestra el mensaje de error con la razón de porqué no se inserta.



## Realizar consultas en las que utilices, igual, mayor y menor que

Usando la función pretty, mostramos los resultados de una forma más legible.

### Operador 'Igual'

Partidas que permitan un máximo de 8 Jugadores.


```console
> db.Partidas.find({numMaxJug: {$eq: 8}}).pretty()
{
        "_id" : ObjectId("5b0954085715367cd8ddb780"),
        "nombre" : "Buscando un Hogar",
        "descripcion" : "El 10º Regimiento de Al’Ratk ha perdido su planeta tras ser destruidos por las fuerzas malignas.",
        "director" : "José Rut",
        "reglas" : "Paramo 2.5",
        "numMaxJug" : 8,
        "numActJug" : 6
}
```

La expresión {numMaxJug: {$eq: 8}} se puede simplificar como {numMaxJug: 8} dando el mismo resultado

```console
> db.Partidas.find({numMaxJug: 8}).pretty()
{
        "_id" : ObjectId("5b0954085715367cd8ddb780"),
        "nombre" : "Buscando un Hogar",
        "descripcion" : "El 10º Regimiento de Al’Ratk ha perdido su planeta tras ser destruidos por las fuerzas malignas.",
        "director" : "José Rut",
        "reglas" : "Paramo 2.5",
        "numMaxJug" : 8,
        "numActJug" : 6
}
```

### Operador 'Mayor que'

Partidas que tengan más de 2 jugadores actualmente.

```console
> db.Partidas.find({numActJug: {$gt: 2}}).pretty()
{
        "_id" : ObjectId("5b0954085715367cd8ddb780"),
        "nombre" : "Buscando un Hogar",
        "descripcion" : "El 10º Regimiento de Al’Ratk ha perdido su planeta tras ser destruidos por las fuerzas malignas.",
        "director" : "José Rut",
        "reglas" : "Paramo 2.5",
        "numMaxJug" : 8,
        "numActJug" : 6
}
{
        "_id" : ObjectId("5b0954155715367cd8ddb781"),
        "nombre" : "Ravenloft",
        "descripcion" : "Explora las tierras de Ravenloft donde el mal crece y se hace fuerte.",
        "director" : "Nildik",
        "reglas" : "DnD 3.5",
        "numMaxJug" : 5,
        "numActJug" : 5
}
```


### Operador 'Menor que'

Partidas con menos de 4 jugadores actualmente.

``` console
> db.Partidas.find({numActJug: {$lt: 4}}).pretty()
{
        "_id" : ObjectId("5b0953ed5715367cd8ddb77f"),
        "nombre" : "Paseando con Orcos",
        "descripcion" : "Aventuras y Desventuras de un clan de Orcos que vagan por 'La Tierra Media' tras la destrucción del ojo.",
        "director" : "Enrique",
        "reglas" : "Paramo 3.2",
        "numMaxJug" : 5,
        "numActJug" : 2
}
{
        "_id" : ObjectId("5b0954225715367cd8ddb782"),
        "nombre" : "Sombras en la Estepa",
        "descripcion" : "El grupo mercenario Camacho`s Caballeros debe defender el planeta Gibraltar.",
        "director" : "Ulric",
        "reglas" : "Battletech",
        "numMaxJug" : 4,
        "numActJug" : 1
}
```

### Otros Operadores

#### Operador 'Mayor o Igual que'

Partidas que admita 4 o más jugadores como máximo.

```console
> db.Partidas.find({numMaxJug: {$gte: 4}}).pretty()       
```

#### Operador 'Menor o Igual que'

Partidas que admita 4 o menos jugadores como máximo.

```console
> db.Partidas.find({numMaxJug: {$lte: 4}}).pretty()       
```

#### Operador 'Igual a alguno'

Partidas cuyo director sea Ulric o Phelan o Enrique

```console
> db.Partidas.find({director: {$in: ['Ulric', 'Phelan', 'Enrique']}}).pretty()
```

#### Operador 'Igual a ninguno o no existe'

Partidas cuyo director no sea Enrique ni Phelan. (También se incluirían las partidas sin director especificado o definido.)

```console
> db.Partidas.find({director: {$nin: [ 'Phelan', 'Enrique']}}).pretty()
```

#### Operador 'No es igual'

Partidas cuyo director no sea Nildik.

```console
> db.Partidas.find({director: {$ne: 'Nildik'}}).pretty()
```


## Realizar una consulta en la que los documentos aparezcan ordenados y se limite el número de estos mostrados.

Mostrar de mayor a menos las tres partidas con más jugadores actualmente.

```console
> db.Partidas.find().sort({numJugAct: -1}).limit(3).pretty()
{
        "_id" : ObjectId("5b0953ed5715367cd8ddb77f"),
        "nombre" : "Paseando con Orcos",
        "descripcion" : "Aventuras y Desventuras de un clan de Orcos que vagan por 'La Tierra Media' tras la destrucción del ojo.",
        "director" : "Enrique",
        "reglas" : "Paramo 3.2",
        "numMaxJug" : 5,
        "numActJug" : 2
}
{
        "_id" : ObjectId("5b0954085715367cd8ddb780"),
        "nombre" : "Buscando un Hogar",
        "descripcion" : "El 10º Regimiento de Al’Ratk ha perdido su planeta tras ser destruidos por las fuerzas malignas.",
        "director" : "José Rut",
        "reglas" : "Paramo 2.5",
        "numMaxJug" : 8,
        "numActJug" : 6
}
{
        "_id" : ObjectId("5b0954155715367cd8ddb781"),
        "nombre" : "Ravenloft",
        "descripcion" : "Explora las tierras de Ravenloft donde el mal crece y se hace fuerte.",
        "director" : "Nildik",
        "reglas" : "DnD 3.5",
        "numMaxJug" : 5,
        "numActJug" : 5
}
```


## Realizar una consulta con agrupamiento y una función para mostrar la media, o suma, o la que tú decidas.


Para las partidas con el misma cantidad máxima de jugadores, la cantidad de partidas y la media de jugadores activos por partida.

```console
> db.Partidas.aggregate([{$group: {_id: "$numMaxJug", numeroPartidas: {$sum: 1}, mediaJugadores: {$avg: "$numActJug"}}}])
{ "_id" : 4, "numeroPartidas" : 1, "mediaJugadores" : 1 }
{ "_id" : 8, "numeroPartidas" : 1, "mediaJugadores" : 6 }
{ "_id" : 5, "numeroPartidas" : 2, "mediaJugadores" : 3.5 }
```




