# Base de datos Champions League (MongoDB)

Proyecto de práctica de MongoDB creado con MongoDB Compass.
Los datos son de ejemplo y no son oficiales.

**Autor:** Andres Felipe Vargas Cardenas
**Curso:** 
**Fecha:** 

## Contenido del repositorio

| Archivo | Descripción |
|---|---|
| `Equipos.json` | Colección Equipos |
| `Jugadores.json` | Colección Jugadores |
| `Estadios.json` | Colección Estadios |
| `Titulos.json` | Colección Titulos |
| `Partidos.json` | Colección Partidos |
| `consultas.js` | Consultas de práctica para mongosh |

## Base de datos

Nombre: `ChampionsLeague`

| Colección | Documentos | Descripción |
|---|---|---|
| Equipos | 10 | Equipos de la Champions League |
| Jugadores | 10 o más | Jugadores con posición, dorsal y estadísticas (goles y asistencias) |
| Estadios | 10 | Estadio de cada equipo |
| Titulos | 10 | Títulos de Champions por equipo |
| Partidos | 10 | Partidos jugados (resultados ficticios) |

## Estructura de los documentos

**Equipos**
```json
{ "equipo_id": 1, "nombre": "Real Madrid", "pais": "España", "fundacion": 1902 }
```

**Jugadores**
```json
{
  "jugador_id": 1, "nombre": "Vinícius Júnior", "posicion": "Delantero",
  "nacionalidad": "Brasil", "dorsal": 7, "equipo_id": 1,
  "estadisticas": { "goles": 15, "asistencias": 9, "partidos": 12 }
}
```

**Estadios**
```json
{ "estadio_id": 1, "nombre": "Santiago Bernabéu", "ciudad": "Madrid", "capacidad": 83000, "equipo_id": 1 }
```

**Titulos**
```json
{ "palmares_id": 1, "equipo_id": 1, "titulos_champions": 15, "ultimo_titulo": 2024 }
```

**Partidos**
```json
{ "partido_id": 1, "local_id": 1, "visitante_id": 2, "goles_local": 2, "goles_visitante": 1, "fase": "Semifinal", "temporada": "2025-26" }
```

## Relaciones entre colecciones

Las colecciones se relacionan con el campo `equipo_id`, que apunta a Equipos:

- `Jugadores.equipo_id` → `Equipos.equipo_id`
- `Estadios.equipo_id` → `Equipos.equipo_id`
- `Titulos.equipo_id` → `Equipos.equipo_id`
- `Partidos.local_id` y `Partidos.visitante_id` → `Equipos.equipo_id`

## Cómo importar los datos

**Desde MongoDB Compass**

1. Crea la base de datos `ChampionsLeague`.
2. Crea una colección por cada archivo JSON.
3. En cada colección haz clic en **Add Data → Import JSON or CSV file**.
4. Selecciona el archivo `.json` correspondiente y el formato **JSON**.

**Desde la terminal**

```bash
mongoimport --db ChampionsLeague --collection Equipos --file Equipos.json --jsonArray
mongoimport --db ChampionsLeague --collection Jugadores --file Jugadores.json --jsonArray
mongoimport --db ChampionsLeague --collection Estadios --file Estadios.json --jsonArray
mongoimport --db ChampionsLeague --collection Titulos --file Titulos.json --jsonArray
mongoimport --db ChampionsLeague --collection Partidos --file Partidos.json --jsonArray
```

## Consultas de ejemplo

Todas las consultas están en el archivo `consultas.js`. Algunas de ellas:

```js
use ChampionsLeague

// Equipos de España
db.Equipos.find({ pais: "España" })

// Jugadores del Real Madrid
db.Jugadores.find({ equipo_id: 1 })

// Estadios con más de 70.000 de capacidad
db.Estadios.find({ capacidad: { $gt: 70000 } })

// Ranking de títulos
db.Titulos.find().sort({ titulos_champions: -1 })

// Jugadores con el nombre de su equipo (equivalente a un JOIN)
db.Jugadores.aggregate([
  { $lookup: { from: "Equipos", localField: "equipo_id", foreignField: "equipo_id", as: "equipo" } }
])
```

## Herramientas

- MongoDB
- MongoDB Compass
- mongosh



// ============================================================
// CONSULTAS - Base de datos ChampionsLeague (MongoDB)
// Colecciones: Equipos, Jugadores, Estadios, Titulos, Partidos
// Se ejecutan en mongosh (pestaña _MONGOSH de Compass)
// Copia y pega una consulta a la vez.
// ============================================================

use ChampionsLeague
show collections


// ============================================================
// 1. EQUIPOS
// ============================================================

// Ver todos los equipos
db.Equipos.find()

// Equipos de un país
db.Equipos.find({ pais: "Italia" })

// Buscar un equipo por nombre
db.Equipos.find({ nombre: "Real Madrid" })

// Equipos fundados después de 1900
db.Equipos.find({ fundacion: { $gt: 1900 } })

// Equipos fundados entre 1890 y 1910
db.Equipos.find({ fundacion: { $gte: 1890, $lte: 1910 } })

// Equipos de España o Inglaterra
db.Equipos.find({ pais: { $in: ["España", "Inglaterra"] } })

// Solo nombre y país
db.Equipos.find({}, { nombre: 1, pais: 1, _id: 0 })

// Del más antiguo al más nuevo
db.Equipos.find().sort({ fundacion: 1 })

// Los 3 más antiguos
db.Equipos.find().sort({ fundacion: 1 }).limit(3)

// Contar equipos de España
db.Equipos.countDocuments({ pais: "España" })


// ============================================================
// 2. JUGADORES
// ============================================================

// Ver todos los jugadores
db.Jugadores.find()

// Solo delanteros
db.Jugadores.find({ posicion: "Delantero" })

// Delanteros argentinos (AND)
db.Jugadores.find({ posicion: "Delantero", nacionalidad: "Argentina" })

// Jugadores con dorsal 9 o 10
db.Jugadores.find({ dorsal: { $in: [9, 10] } })

// Solo nombre y dorsal
db.Jugadores.find({}, { nombre: 1, dorsal: 1, _id: 0 })

// Ordenados por nombre
db.Jugadores.find().sort({ nombre: 1 })

// Jugadores del Real Madrid (equipo_id 1)
db.Jugadores.find({ equipo_id: 1 })

// Contar jugadores del Real Madrid
db.Jugadores.countDocuments({ equipo_id: 1 })


// ============================================================
// 3. ESTADIOS
// ============================================================

// Estadios con más de 70.000 de capacidad
db.Estadios.find({ capacidad: { $gt: 70000 } })

// Estadios de Madrid
db.Estadios.find({ ciudad: "Madrid" })

// Los 3 estadios más grandes
db.Estadios.find().sort({ capacidad: -1 }).limit(3)

// Solo nombre y capacidad
db.Estadios.find({}, { nombre: 1, capacidad: 1, _id: 0 })


// ============================================================
// 4. TITULOS
// (revisa tus campos con db.Titulos.findOne() y ajusta si
//  los nombraste distinto)
// ============================================================

// Equipos con 5 o más Champions
db.Titulos.find({ titulos_champions: { $gte: 5 } })

// Equipos sin ningún título
db.Titulos.find({ titulos_champions: 0 })

// Ranking de más a menos títulos
db.Titulos.find().sort({ titulos_champions: -1 })

// Último título después de 2015
db.Titulos.find({ ultimo_titulo: { $gt: 2015 } })


// ============================================================
// 5. PARTIDOS
// ============================================================

// Partidos de semifinal
db.Partidos.find({ fase: "Semifinal" })

// Partidos con empate
db.Partidos.find({ $expr: { $eq: ["$goles_local", "$goles_visitante"] } })

// Partidos donde el local marcó 2 o más goles
db.Partidos.find({ goles_local: { $gte: 2 } })

// Partidos del Real Madrid (local o visitante)
db.Partidos.find({ $or: [{ local_id: 1 }, { visitante_id: 1 }] })


// ============================================================
// 6. INSERTAR, ACTUALIZAR Y ELIMINAR
// ============================================================

// Insertar un jugador (cambia el jugador_id para que no se repita)
db.Jugadores.insertOne({
  jugador_id: 11,
  nombre: "Kylian Mbappé",
  posicion: "Delantero",
  nacionalidad: "Francia",
  dorsal: 10,
  equipo_id: 1
})

// Insertar varios jugadores
db.Jugadores.insertMany([
  { jugador_id: 12, nombre: "Federico Valverde", posicion: "Mediocampista", nacionalidad: "Uruguay", dorsal: 8, equipo_id: 1 },
  { jugador_id: 13, nombre: "Thibaut Courtois", posicion: "Portero", nacionalidad: "Bélgica", dorsal: 1, equipo_id: 1 }
])

// Ver el último jugador_id usado
db.Jugadores.find().sort({ jugador_id: -1 }).limit(1)

// Actualizar un campo
db.Estadios.updateOne({ estadio_id: 5 }, { $set: { capacidad: 47929 } })

// Eliminar un documento
db.Jugadores.deleteOne({ jugador_id: 13 })


// ============================================================
// 7. ESTADISTICAS (subdocumento goles / asistencias)
// ============================================================

// Agregar estadísticas a un jugador
db.Jugadores.updateOne(
  { nombre: "Vinícius Júnior" },
  { $set: { estadisticas: { goles: 15, asistencias: 9, partidos: 12 } } }
)

// Agregar estadísticas en 0 a todos los jugadores
db.Jugadores.updateMany(
  {},
  { $set: { estadisticas: { goles: 0, asistencias: 0, partidos: 0 } } }
)

// Jugadores con más de 10 goles
db.Jugadores.find({ "estadisticas.goles": { $gt: 10 } })

// Ranking de goleadores
db.Jugadores.find({}, { nombre: 1, "estadisticas.goles": 1, _id: 0 }).sort({ "estadisticas.goles": -1 })

// Cambiar los goles de un jugador
db.Jugadores.updateOne({ nombre: "Vinícius Júnior" }, { $set: { "estadisticas.goles": 16 } })

// Sumarle 1 gol a un jugador
db.Jugadores.updateOne({ nombre: "Vinícius Júnior" }, { $inc: { "estadisticas.goles": 1 } })


// ============================================================
// 8. CONSULTAS COMBINADAS ($lookup = JOIN)
// ============================================================

// Jugadores con el nombre de su equipo
db.Jugadores.aggregate([
  {
    $lookup: {
      from: "Equipos",
      localField: "equipo_id",
      foreignField: "equipo_id",
      as: "equipo"
    }
  },
  { $unwind: "$equipo" },
  { $project: { _id: 0, nombre: 1, dorsal: 1, equipo: "$equipo.nombre" } }
])

// Estadios con el nombre de su equipo
db.Estadios.aggregate([
  {
    $lookup: {
      from: "Equipos",
      localField: "equipo_id",
      foreignField: "equipo_id",
      as: "equipo"
    }
  },
  { $unwind: "$equipo" },
  { $project: { _id: 0, estadio: "$nombre", capacidad: 1, equipo: "$equipo.nombre" } }
])

// Títulos con el nombre del equipo, de más a menos
db.Titulos.aggregate([
  {
    $lookup: {
      from: "Equipos",
      localField: "equipo_id",
      foreignField: "equipo_id",
      as: "equipo"
    }
  },
  { $unwind: "$equipo" },
  { $project: { _id: 0, equipo: "$equipo.nombre", titulos_champions: 1 } },
  { $sort: { titulos_champions: -1 } }
])

// Partidos con el nombre del equipo local
db.Partidos.aggregate([
  {
    $lookup: {
      from: "Equipos",
      localField: "local_id",
      foreignField: "equipo_id",
      as: "local"
    }
  },
  { $unwind: "$local" },
  { $project: { _id: 0, local: "$local.nombre", goles_local: 1, goles_visitante: 1, fase: 1 } }
])

// Partidos con el nombre del local Y del visitante
db.Partidos.aggregate([
  { $lookup: { from: "Equipos", localField: "local_id", foreignField: "equipo_id", as: "local" } },
  { $lookup: { from: "Equipos", localField: "visitante_id", foreignField: "equipo_id", as: "visitante" } },
  { $unwind: "$local" },
  { $unwind: "$visitante" },
  { $project: {
      _id: 0,
      local: "$local.nombre",
      goles_local: 1,
      goles_visitante: 1,
      visitante: "$visitante.nombre",
      fase: 1
  } }
])

// Equipos de España con su estadio
db.Equipos.aggregate([
  { $match: { pais: "España" } },
  {
    $lookup: {
      from: "Estadios",
      localField: "equipo_id",
      foreignField: "equipo_id",
      as: "estadio"
    }
  },
  { $project: { _id: 0, nombre: 1, "estadio.nombre": 1, "estadio.capacidad": 1 } }
])

// Jugadores del Real Madrid buscando por el nombre del equipo
db.Equipos.aggregate([
  { $match: { nombre: "Real Madrid" } },
  {
    $lookup: {
      from: "Jugadores",
      localField: "equipo_id",
      foreignField: "equipo_id",
      as: "jugadores"
    }
  },
  { $project: { _id: 0, nombre: 1, "jugadores.nombre": 1, "jugadores.dorsal": 1 } }
])


// ============================================================
// 9. AGRUPACIONES ($group)
// ============================================================

// Cuántos equipos hay por país
db.Equipos.aggregate([
  { $group: { _id: "$pais", total: { $sum: 1 } } }
])

// Capacidad promedio de los estadios
db.Estadios.aggregate([
  { $group: { _id: null, promedio: { $avg: "$capacidad" } } }
])

// Total de goles del local en todos los partidos
db.Partidos.aggregate([
  { $group: { _id: null, goles_totales: { $sum: "$goles_local" } } }
])

// Total de goles por equipo (usa el subdocumento estadisticas)
db.Jugadores.aggregate([
  { $group: { _id: "$equipo_id", total_goles: { $sum: "$estadisticas.goles" } } },
  { $sort: { total_goles: -1 } }
])
