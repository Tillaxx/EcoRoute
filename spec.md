---
title: "Gestión de furgonetas eléctricas CLI con Python"
version: "0.1"
date: "12/05/2026"
---
# spec.md - Especificación funcional #
## 1. Descripción general 
La aplicación es una herramienta de línea de comandos (CLI) escrita en lenguaje Python que permite gestionar las furgonetas electricas y/o vincularla con entregas que seran tambien gestionadas, las furgonetas tendran batería que serán importantes almacenada en una base de datos MySQL. Ofreciendo operaciones propias de CRUD.

## 2. Funcionalidades
- Crear, listar, actualizar y eliminar furgonetas y entregas.
- Buscar furgonetas por matricula, por modelo y por entrega asignada
- Listar todas las furgonetas
- Listar todas las entregas
- Crear un menú de opciones
- CLI sea interactiva
- Almacene información en una base de datos MySQL

## 3. Modelo de datos

Tabla furgoneta en base de datos MySQL
| Campo | Tipo | Obligatorio | Descripcion |
|-------|------|-------------|-------------|
| id_vehiculo  | INT AUTOINCREMENT | SI | Identificador único |
| matricula | VARCHAR(7) | SI | Matrícula furgoneta |
| modelo | VARCHAR(200) | SI | Modelo furgoneta | 
| capacidad_bateria_total  | INTEGER | SI | capacidad de batería total en Kwh|
| nivel_bateria_actual  | INTEGER | SI | cantidad de batería | 
| autonomia_maxima_km  | INTEGER | SI | autonomia en km| 
| estado  | enum(Disponible, En Ruta, Cargando, Mantenimiento) | SI | cantidad de batería | 


Tabla entrega en base de datos MySQL
| Campo | Tipo | Obligatorio | Descripcion |
|-------|------|-------------|-------------|
| id_entrega  | INT AUTOINCREMENT | SI | Identificador único |
| destino_coordenadas | VARCHAR(200) | SI | Ubicación entrega |
| prioridad  | int| SI | prioridad del 1 al 3 |
| ventana_horaria  | VARCHAR(200)| SI | duracion |


Tabla ruta en base de datos MySQL
| Campo | Tipo | Obligatorio | Descripcion |
|-------|------|-------------|-------------|
| id_ruta   | INT AUTOINCREMENT | SI | Identificador único |
| vehiculo_asignado  | VARCHAR(200) | SI | id de vehiculo asignado |
| lista_entregas   | list | SI | lista de entregas |
| distancia_total_estimada   | Float| SI | distancia en Km |
| consumo_estimado_bateria   | Float| SI | porcentaje |

## 4. Casos de uso
### CU-01: Añadir furgoneta
1. El usuario selecciona "Añdir furgoneta"
2. El sistema pide: matricula, modelo y campos bateria
3. El usuario introduce los datos
4. El sistema valida los datos
5. El sistema comprueba si existe una furgoneta con la misma matricula.
6. El sistema inserta la furgoneta en la base de datos y te muestra el id con los datos de la furgoneta

**Flujo alternativo A**  (validación falla)
   - El sistema muestra un mensaje de error y solicta corregir el error

**Flujo Alternativo B** (validacion correcta)
  - El sistema inserta los datos en la base de datos

**Flujo Alternativo C** (furgoneta duplicado)
   - El sistema nos advierte de que existe la furgoneta y pide confirmación de guardado

### CU-02: Añadir entrega
1. El usuario selecciona "Añdir entrega"
2. El sistema pide: destino_coordenadas, prioridad y ventana_horaria
3. El usuario introduce los datos
4. El sistema valida los datos
5. El sistema comprueba si existe una entrega igual.
6. El sistema inserta la entrega en la base de datos y te muestra el id con los datos de la entrega

**Flujo alternativo A**  (validación falla)
   - El sistema muestra un mensaje de error y solicta corregir el error

**Flujo Alternativo B** (validacion correcta)
  - El sistema inserta los datos en la base de datos

**Flujo Alternativo C** (entrega duplicado)
   - El sistema nos advierte de que existe la furgoneta y pide confirmación de guardado
   - 

### CU-03: Añadir ruta
1. El usuario selecciona "Añadir ruta"
2. El sistema pide: vehiculo_asignado, lista_entregas , distancia_total_estimada y consumo_estimado_bateria
3. El usuario introduce los datos
4. El sistema valida los datos
5. El sistema comprueba si existe una ruta igual.
6. El sistema inserta la ruta en la base de datos y te muestra el id con los datos de la ruta

**Flujo alternativo A**  (validación falla)
   - El sistema muestra un mensaje de error y solicta corregir el error

**Flujo Alternativo B** (validacion correcta)
  - El sistema inserta los datos en la base de datos

**Flujo Alternativo C** (entrega duplicado)
   - El sistema nos advierte de que existe la furgoneta y pide confirmación de guardado
   - 
**Flujo Alternativo D** (entrega lejos)
   - El sistema rechaza debido a que la entrega gastaría mas del 80% de la batería

### CU-04: Ver furgoneta
1. El usuario introduce el `id` de la furgoneta (o lo selecciona de una lista).
2. El sistema muestra todos los campos de la furgoneta en formato legible.

### CU-05: Ver entrega
1. El usuario introduce el `id` de la entrega (o lo selecciona de una lista).
2. El sistema muestra todos los campos de la entrega en formato legible.

### CU-06: Eliminar entrada
1. El usuario selecciona "Eliminar entrada", se le pide si es furgoneta, entrega o ruta e introduce el `id`.
2. El sistema muestra los datos del objeto y solicita confirmación ("¿Seguro? [s/N]").
3. Si el usuario confirma, el sistema realiza soft-delete (`deleted_at = NOW()`).
4. El sistema confirma la eliminación.

### CU-07: Editar entrada
1. El usuario selecciona "Editar entrada", se le pide si es furgoneta, entrega o ruta e introduce el `id`.
2. El sistema muestra los valores actuales campo a campo.
3. Para cada campo, el usuario puede introducir un nuevo valor o dejarlo vacío para
   mantener el actual.
4. El sistema valida los nuevos valores.
5. El sistema actualiza el registro y muestra confirmación.
  
### CU-08: Listar entrada
1. El usuario selecciona "Listar entrada" y se le pide si es furgoneta, entrega o ruta.
2. El sistema muestra todas las entradas (sin `deleted_at`) en formato tabla.
3. Si hay más de 20 entradas, pagina de 20 en 20.
4. El usuario puede navegar entre páginas o volver al menú.

---
## 5. Reglas de validación

| Campo | Regla |
|-------|------|
| id_vehiculo  | No vacío, numerico. |
| matricula |  No vacío, 4 numeros y consecutivamente 3 letras |
| modelo |  No vacío, texto de entr 10-200 carácteres| 
| capacidad_bateria_total  |  No vacío, numérico para indicar KwH|
| nivel_bateria_actual  |  No vacío, numérico para indicar porcentaje de 0 a 100| 
| autonomia_maxima_km  | No vacío, numérico para indicar km | 
| estado  | Deberá de tener uno de estos valores (Disponible, En Ruta, Cargando, Mantenimiento) | 

| Campo | Regla |
|-------|------|
| id_entrega  | No vacío, numerico. |
| destino_coordenadas | No vacío, que siga el formato de coordenadas |
| prioridad  | No vacío, numérico y un numero del 1 al 3 |
| ventana_horaria  | No vacío y que sea siga el patron XX:XX-ZZ:ZZ |

| Campo | Regla |
|-------|------|
| id_ruta   | No vacío, numerico. |
| vehiculo_asignado  | No vacío, numerico |
| lista_entregas   | En formato de lista |
| distancia_total_estimada   | No vacío, numerico para medir distancia en Km |
| consumo_estimado_bateria   | No vacío, numerico para medir el % que se va a consumir |

