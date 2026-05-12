---
title: "Gestión de furgonetas eléctricas CLI con Python"
version: "0.1"
date: "04/05/2026"
---
# architecture.md - Arquitectura técnica #

## 1. Visión general
La aplicación sigue una **arquitectura en capas** (Layered Architecture) para separar la lógica de presentación de la persistencia de datos con tres niveles bien diferenciados. La comunicación entre capas es siempre descendente: la capa superior llama a la inferior; nunca al revés.

```
┌─────────────────────────────────────────────────────────┐
│                   CAPA DE PRESENTACIÓN                  │
│                   cli/  (cli_agent)                     │
│   main.py  ·  menu.py  ·  formatters.py                 │
└───────────────────────┬─────────────────────────────────┘
                        │  llama a
┌───────────────────────▼─────────────────────────────────┐
│                 CAPA DE LÓGICA DE NEGOCIO               │
│                logic/  (logic_agent)                    │
│   contact_service.py  ·  validators.py  ·  models.py    │
└───────────────────────┬─────────────────────────────────┘
                        │  llama a
┌───────────────────────▼─────────────────────────────────┐
│                   CAPA DE DATOS                         │
│                   db/  (db_agent)                       │
│   connection.py  ·  contact_repo.py  ·  migrations/     │
└───────────────────────┬─────────────────────────────────┘
                        │
                    ┌───▼───┐
                    │ MySQL │
                    └───────┘
```

## 2. Estructura de directorios

```
agenda_contactos/
│
├── main.py                 # Punto de entrada de la aplicación
│
├── cli/                    # Capa de presentación
│   ├── __init__.py
│   ├── menu.py             # Menú principal y submenús
│   └── formatters.py       # Formateo de tablas y mensajes
│
├── logic/                  # Capa de lógica de negocio
│   ├── __init__.py
│   ├── models.py           # Definición de las entidades `Furgoneta` y `Entregas`.
|   ├── services.py         # Orquesta los casos de uso. Es el único punto de contacto entre CLI y repositorio.
│   └── validators.py       # Validación de campos
│
├── db/                     # Capa de acceso a datos
│   ├── __init__.py
│   ├── connection.py       # Clase encargada de la conexión (Singleton) y cierre de sesión.
│   └── contact_repo.py     # Repositorio de acceso a datos. Funciones específicas para sentencias SQL (INSERT, SELECT, UPDATE, DELETE).
│
├── exceptions.py           # Excepciones personalizadas del proyecto
│
└── tests/
    ├── __init__.py
    └── test_validators.py
```

## 3. Descripción de módulos
### 3.1 `main.py`
Punto de entrada. Inicializa la conexión a la base de datos, instancia los
componentes de las capas y lanza el bucle principal del menú CLI.
### 3.2 `cli/menu.py`
Controla el flujo de navegación de la interfaz de usuario.
**Funciones principales:**
| Función              | Descripción                                      |
|----------------------|--------------------------------------------------|
| `run()`              | Bucle principal del menú                         |
| `menu_anadir()`      | Submenú de creación de furgoneta y/o entregas    |
| `menu_listar()`      | Muestra lista paginada                           |
| `menu_buscar()`      | Solicita término y muestra resultados            |
| `menu_ver()`         | Muestra detalle de una furgoneta y/o entrega por ID          |
| `menu_asignar()`      | Submenú para asignar entrega a furgoneta                 |
| `menu_desvincular()`      | Submenú para desvincular entrega de furgoneta                |
| `menu_eliminar()`    | Solicita confirmación y elimina                  |

 ---
### 3.3 `cli/formatters.py`
Funciones de presentación puras (sin lógica de negocio).
| Función                       | Descripción                              |
|-------------------------------|------------------------------------------|
| `tabla_furgonetas(furgoneta)`  | Renderiza lista con `tabulate`           |
| `detalle_furgonetas(furgoneta)`  | Muestra todos los campos de uno          |
| `detalle_entregas(entrega)`  | Muestra todos los campos de uno          |
| `detalle_entregas(entrega)`  | Muestra todos los campos de uno          |
| `mensaje_error(codigo, msg)`  | Formatea mensajes de error               |
| `mensaje_exito(msg)`          | Formatea mensajes de confirmación        |

---
### 3.4 `logic/models.py`
Define la estructura de datos del dominio.
```python

@dataclass
class Furgoneta:
    Matricula: str
    Modelo: str
    PorcentajeBateria: str
    EntregasAsignadas: Optional[str] = None
```
---
### 3.5 `logic/validators.py`
Funciones de validación sin efectos secundarios. Devuelven `True` o lanzan `ValidationError` con el código de error correspondiente.

**Funciones:**

| Función                   | Regla aplicada                              |
|---------------------------|---------------------------------------------|
| `validar_matrícula(nombre)`  | No vacío, 4 numeros y posteriormente 3 letras|
| `validar_modelo(modelo)`   | No vacío, cadena de caracteres entre 5-150       |
| `validar_porcentajebateria(bateria)`    | Numero de 0 a 100          |
| `validar_entregasasignadas(entregas)` | lista de entregas referenciadas por id         |

---
### 3.6 `logic/servicse.py`
Orquesta los casos de uso. Es el único punto de contacto entre CLI y repositorio.

**Métodos:**

| Método                              | Caso de uso      |
|-------------------------------------|------------------|
| `crear_furgoneta(datos: dict)`       | CU-01            |
| `listar_furgoneta(pagina, tam)`     | CU-02            |
| `buscar_furgoneta(termino: str)`    | CU-03            |
| `obtener_furgoneta(id: int)`         | CU-04            |
| `actualizar_furgoneta(id, cambios)`  | CU-05            |
| `eliminar_furgoneta(id: int)`        | CU-06            |
| `crear_entrega(datos: dict)`       | CU-07            |
| `listar_entrega(pagina, tam)`     | CU-08           |
| `buscar_entrega(termino: str)`    | CU-09            |
| `obtener_entrega(id: int)`         | CU-10            |
| `actualizar_entrega(id, cambios)`  | CU-11            |
| `eliminar_entrega(id: int)`        | CU-12            |
| `vincular_ambos(id: int, id: int)`        | CU-12            |

---
### 3.7 `db/connection.py`
Gestiona la conexión a MySQL.
Carga la configuración desde variables de entorno:
`DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD`.

---
### 3.8 `db/contact_repo.py`
Repositorio de acceso a datos. Todas las consultas usan parámetros (`%s`).

**Métodos:**

| Método                              | SQL generado                         |
|-------------------------------------|--------------------------------------|
| `insertar(furgoneta)`                | `INSERT INTO furgonetas ...`          |
| `insertar(entrega)`                | `INSERT INTO entregas ...`          |
| `obtener_por_id(id)`               | `SELECT ... WHERE id=%s AND deleted_at IS NULL` |
| `listar(offset, limit)`            | `SELECT ... WHERE deleted_at IS NULL LIMIT ...` |
| `buscar(termino)`                  | `SELECT ... WHERE (nombre LIKE %s OR ...)` |
| `actualizar(id, campos)`           | `UPDATE contactos SET ... WHERE id=%s` |
| `eliminar_soft(id)`                | `UPDATE contactos SET deleted_at=NOW()` |
| `contar_total()`                   | `SELECT COUNT(*) WHERE deleted_at IS NULL` |

---

### 3.9 `exceptions.py`
Jerarquía de excepciones del proyecto.

---

## 4. Dependencias externas (Opcional)

| Paquete                    | Versión  | Uso                            |
|----------------------------|----------|--------------------------------|
| `mysql-connector-python`   | >=8.3.0  | Conexión a MySQL               |
| `python-dotenv`            | >=1.0.0  | Carga de variables de entorno  |
| `tabulate`                 | >=0.9.0  | Formateo de tablas en consola  |
| `pytest`                   | >=8.0.0  | Framework de testing           |
| `pytest-cov`               | >=5.0.0  | Cobertura de tests             |

---

