# Python para administración de sistemas
## Práctica de Desarrollo: Sistema de Gestión "EcoRoute" con Metodología SPEC

### 1. Contexto del Proyecto
La empresa de logística EcoRoute necesita una herramienta interna para gestionar su nueva flota de furgonetas eléctricas. A diferencia de un desarrollo tradicional, utilizaremos SPEC-Driven Development (SDD).

Vuestra labor no es escribir el código directamente, sino actuar como Arquitectos de Software y Product Owners. Deberéis redactar la documentación técnica necesaria para que una Inteligencia Artificial (Gemini, Claude, ChatGPT) genere el sistema completo sin errores y bajo vuestras reglas.

---

### 2. El Reto Técnico
Debéis definir un sistema en Python que permita:
* **Registrar vehículos**: Almacenar ID, modelo y capacidad de batería (0-100%).
* **Gestionar Entregas**: Cada entrega tiene una ubicación y un consumo estimado de batería.
* **Lógica de Seguridad (Crítico)**: Si una ruta consume más del 80% de la batería actual, el sistema debe rechazar la ruta o sugerir una parada en un punto de recarga.

#### Modelo de datos:

**Entidad: Vehículo (Vehicle)**
* **id_vehiculo (String)**: Identificador único (ej: "VAN-001"). Debe seguir un patrón alfanumérico.
* **modelo (String)**: Nombre del modelo (ej: "Tesla Semi", "Renault Kangoo ZE").
* **capacidad_bateria_total (Float)**: Capacidad máxima en kWh.
* **nivel_bateria_actual (Int/Float)**: Porcentaje de carga actual (0 a 100).
* **autonomia_maxima_km (Int)**: Cuántos km puede recorrer con el 100% de carga.
* **estado (Enum)**: Disponible, En Ruta, Cargando, Mantenimiento.

**Entidad: Entrega / Pedido (Delivery)**
* **id_entrega (String)**: Identificador único del paquete.
* **destino_coordenadas (Tuple: lat, lon)**: Ubicación exacta de la entrega.
* **peso_kg (Float)**: Influye en el consumo (opcional para aumentar dificultad).
* **prioridad (Int)**: Nivel de urgencia (1-3).
* **ventana_horaria (String)**: Ejemplo: "09:00 – 11:00".

**Entidad: Ruta (Route)**
* **id_ruta (String)**: ID de la jornada.
* **vehiculo_asignado (FK)**: Referencia al ID del vehículo.
* **lista_entregas (List)**: Lista ordenada de IDs de entregas.
* **distancia_total_estimada (Float)**: Suma de los trayectos en km.
* **consumo_estimado_bateria (Float)**: Porcentaje que se restará tras completar la ruta.

---

### 3. Entrega
Deberéis crear una carpeta en tu espacio github llamada `/ecoroute` con los siguientes archivos Markdown. Cada uno tiene un propósito estricto:

| Archivo | Propósito | Contenido Requerido |
| :--- | :--- | :--- |
| **architecture.md** | La Estructura | Organización del código. Arquitectura limpia y persistencia (JSON o SQLite). |
| **spec.md** | Los Requisitos | Qué hace el programa. Funciones, parámetros, tipos y cálculos de autonomía. |
| **agents.md** | Los Roles de la IA | Cómo debe "pensar" la IA. Instrucciones para Senior Developer, QA Tester y Documentador. |
| **decisions.md** | Histórico de Decisiones | Justificación técnica. Python 3.10+, librerías específicas y manejo de errores. |
| **constitution.md** | Las Reglas de Oro | Restricciones éticas y de formato. Idioma (Código: Inglés / Comentarios: Español). Nomenclatura: snake_case, PascalCase y SCREAMING_SNAKE_CASE. |
| **proyecto_ecoroute.zip** | Con la estructura de la carpeta /ecoroute con todos los ficheros comprimidos en sus respectivas carpetas generados por la IA. | |
| **readme.md** | Con la descripción del proyecto | |

* Compartir el proyecto (en privado) con el usuario mnarrieta (mnarrieta@iesfuengirola1.es)
* Todos los ficheros .md deben tener control de versiones, tablas y un correcto formato cumpliendo los estándares Markdown.

#### Librerías necesarias:
* **Validación de Datos**: Pydantic.
* **Interfaz de Usuario**: Rich.
* **Persistencia**: TinyDB o Sqlite3.
* **Cálculos Geográficos**: Geopy.
* **Configuración**: python-dotenv.

---

### 4. Flujo de Trabajo
1. **Redacción**: Escribid los 5 ficheros sin contradicciones.
2. **Prompting**: Subid los ficheros con el comando: *"Analiza estos 5 ficheros de especificación... genera el código Python necesario..."*
3. **Validación**: Ejecutad el código. Si hay errores, corregid el fichero SPEC, no el código a mano.

---

### 5. Rúbrica de Evaluación
* **Calidad de la Especificación**: Precisión en endpoints, batería y tipos.
* **Arquitectura y Decisiones**: Justificación de librerías y estructura modular.
* **Cumplimiento de la Constitución**: Estilo de código, nomenclatura y Type Hints.
* **Gestión de Agentes**: Roles específicos que mejoren la calidad.
* **Funcionalidad y Validación**: Lógica del 20% de batería y tests unitarios correctos.
