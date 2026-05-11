---
title: "Agenda básica CLI con Python"
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
│   ├── models.py           # Definición de la entidad `Contacto`.
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
| `menu_anadir()`      | Submenú de creación de contacto                  |
| `menu_listar()`      | Muestra lista paginada                           |
| `menu_buscar()`      | Solicita término y muestra resultados            |
| `menu_ver()`         | Muestra detalle de un contacto por ID            |
| `menu_editar()`      | Submenú de edición campo a campo                 |
| `menu_eliminar()`    | Solicita confirmación y elimina                  |

 ---
### 3.3 `cli/formatters.py`
Funciones de presentación puras (sin lógica de negocio).
| Función                       | Descripción                              |
|-------------------------------|------------------------------------------|
| `tabla_contactos(contactos)`  | Renderiza lista con `tabulate`           |
| `detalle_contacto(contacto)`  | Muestra todos los campos de uno          |
| `mensaje_error(codigo, msg)`  | Formatea mensajes de error               |
| `mensaje_exito(msg)`          | Formatea mensajes de confirmación        |

---
### 3.4 `logic/models.py`
Define la estructura de datos del dominio.
```python

@dataclass
class Contacto:
    nombre: str
    apellidos: str
    telefono1: Optional[str] = None
    telefono2: Optional[str] = None
    email: Optional[str] = None
    notas: Optional[str] = None
    id: Optional[int] = None
```
---
### 3.5 `logic/validators.py`
Funciones de validación sin efectos secundarios. Devuelven `True` o lanzan `ValidationError` con el código de error correspondiente.

**Funciones:**

| Función                   | Regla aplicada                              |
|---------------------------|---------------------------------------------|
| `validar_nombre(nombre)`  | No vacío, 2–100 chars, caracteres permitidos|
| `validar_telefono(tel)`   | Formato numérico con signos aceptados       |
| `validar_email(email)`    | Formato básico usuario@dominio.tld          |
| `validar_contacto(datos)` | Al menos teléfono o email presente          |

---
### 3.6 `logic/servicse.py`
Orquesta los casos de uso. Es el único punto de contacto entre CLI y repositorio.

**Métodos:**

| Método                              | Caso de uso      |
|-------------------------------------|------------------|
| `crear_contacto(datos: dict)`       | CU-01            |
| `listar_contactos(pagina, tam)`     | CU-02            |
| `buscar_contactos(termino: str)`    | CU-03            |
| `obtener_contacto(id: int)`         | CU-04            |
| `actualizar_contacto(id, cambios)`  | CU-05            |
| `eliminar_contacto(id: int)`        | CU-06            |

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
| `insertar(contacto)`                | `INSERT INTO contactos ...`          |
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

