---
title: "Gestión de furgonetas eléctricas CLI con Python"
version: "0.1"
date: "12/05/2026"
---
# decisions.md — Registro de Decisiones de Diseño
Este documento registra las decisiones técnicas y de diseño significativas tomadas durante el desarrollo de la aplicación de agenda de contactos. Cada entrada sigue el formato ADR (Architecture Decision Record).
---

## ADR-001: Elección de base de datos — MySQL
**Fecha:** 2026-05-12

**Estado:** Aceptado

**Contexto:**  
Se necesita un motor de base de datos relacional para persistir los contactos con soporte para consultas de búsqueda, integridad de datos y fácil despliegue local.

**Opciones consideradas:**
- MySQL 8.0
- PostgreSQL 16
- SQLite 3

**Decisión:**  
Se elige **MySQL 8.0**.

**Justificación:**
- Es el requisito explícito del proyecto.
- Compatible con MariaDB, lo que facilita migraciones a entornos alternativos.

**Consecuencias:**
- Se requiere un servidor MySQL corriendo localmente o accesible en red.
- El driver elegido es `mysql-connector-python` (oficial de Oracle, sin dependencias C).

---

## ADR-002: Interfaz de usuario — CLI interactiva

**Fecha:** 2026-05-12  

**Estado:** Aceptado

**Contexto:**  
La aplicación necesita una interfaz de usuario. Las opciones principales son CLI, GUI de escritorio o aplicación web.

**Opciones consideradas:**
- CLI con menús interactivos

**Decisión:**  
Se implementa una **CLI interactiva** (menús en bucle).

**Justificación:**
- Menor complejidad de implementación y mantenimiento.
 
**Consecuencias:**
- La experiencia de usuario está limitada a terminal.

---

## ADR-003: Librería de conexión a MySQL — `mysql-connector-python`

**Fecha:** 2026-05-12 
**Estado:** Aceptado

**Opciones consideradas:**
- `mysql-connector-python` (oficial Oracle)

**Decisión:**  
Se usa **`mysql-connector-python`**.

**Justificación:**
- Driver oficial, mantenido activamente por Oracle.

**Consecuencias:**
- Las consultas SQL son explícitas en el código (ventaja para legibilidad en un
  proyecto educativo).

---

## ADR-004: Estrategia de borrado — Soft Delete

**Fecha:** 2026-05-12  
**Estado:** Aceptado

**Contexto:**  
Al eliminar un contacto, ¿se borra físicamente el registro o se marca como eliminado?

**Opciones consideradas:**
- Hard delete (DELETE FROM contactos WHERE id = ?)
- Soft delete (UPDATE contactos SET deleted_at = NOW() WHERE id = ?)

**Decisión:**  
**Soft delete** mediante columna `deleted_at DATETIME`.

**Justificación:**
- Permite recuperar contactos eliminados accidentalmente.
- Conserva el historial de datos para auditoría.

**Consecuencias:**
- Todas las consultas de lectura deben incluir el filtro `deleted_at IS NULL`.

---

## ADR-005: Gestión de configuración — Variables de entorno con `.env`

**Fecha:** 2026-04-27  
**Estado:** Aceptado

**Contexto:**  
Las credenciales de MySQL (host, puerto, usuario, contraseña, nombre de base de datos)
deben estar disponibles en tiempo de ejecución sin hardcodearlas en el código.

**Opciones consideradas:**
- Archivo `.env` cargado con `python-dotenv`

**Decisión:**  
**Archivo `.env`** cargado mediante `python-dotenv`.

**Justificación:**
- Estándar de facto en proyectos Python modernos.

**Variables requeridas:**
```
DB_HOST=localhost
DB_PORT=3306
DB_NAME=EcoRoute_db
DB_USER=EcoRoute_user
DB_PASSWORD=zxASqw!"
```

---

## ADR-006: Formato de presentación de tablas — `tabulate`

**Fecha:** 2026-04-27  
**Estado:** Aceptado

**Opciones consideradas:**
- Librería `tabulate`

**Decisión:**  
**`tabulate`** con formato `grid` o `simple`.

**Justificación:**
- Librería ligera y sin subdependencias relevantes.

**Consecuencias:**
- Añadir `tabulate` a `requirements.txt`.

---

## ADR-007: Patrón de arquitectura — Capas (Layered Architecture)

**Fecha:** 2026-04-27  
**Estado:** Aceptado

**Decisión:**  
Se aplica una arquitectura en tres capas estrictas:

```
Presentación (cli_agent)  →  Lógica de negocio (logic_agent)  →  Datos (db_agent)
```

**Justificación:**
- Facilita el reemplazo de cualquier capa sin afectar a las demás.
- Consistente con los principios de `constitution.md`.

**Consecuencias:**  
Ver `architecture.md` para detalle de módulos y dependencias.


