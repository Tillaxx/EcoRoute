---
title: "Agenda básica CLI con Python"
version: "0.1"
date: "29/04/2026"
---
# spec.md - Especificación funcional #
## 1. Descripción general 
La aplicación es una herramienta de línea de comandos (CLI) escrita en lenguaje Python que permite gestionar una agenda de contactos almacenada en una base de datos MySQL. Ofreciendo operaciones propias de CRUD.

## 2. Funcionalidades
- Crear, listar, actualizar y eliminar contactos.
- Buscar contacto por nombre, por teléfono y por email
- Listar todos los contactos
- Crear un menú de opciones
- CLI sea interactiva
- Almacene información en una base de datos MySQL

## 3. Modelo de datos

Tabla contacto en base de datos MySQL
| Campo | Tipo | Obligatorio | Descripcion |
|-------|------|-------------|-------------|
| id | INT AUTOINCREMENT | SI | Identificador único |
| name | VARCHAR(100) | SI | Nombre contacto |
| surname | VARCHAR(200) | NO | Apellidos contacto |
| tel1 | INTEGER | SI | Telefono contacto1 |
| tel2 | INTEGER | NO | Telefono contacto2 |
| email | VARCHAR(100) | NO | Email contacto |
| notes | TEXT | NO | Informacion contacto |

## 4. Casos de uso
### CU-01: Añadir contacto
1. El usuario selecciona "Añdir contacto"
2. El sistema pide: Nombre (name) surname, número (tel1), tel2, email, notes
3. El usuario introduce los datos
4. El sistema valida los datos
5. El sistema comprueba si existe un contacto con el mismo telefono.
6. El sistema inserta el conatcto en la base de datos y te muestra el id con los datos del contacto

**Flujo alternativo A**  (validación falla)
   - El sistema muestra un mensaje de error y solicta corregir el error

**Flujo Alternativo B** (validacion correcta)
  - El sistema inserta los datos en la base de datos

**Flujo Alternativo C** (contacto duplicado)
   - El sistema nos advierte de que existe el contacto y pide confirmación de guardado
   
### CU-02: Ver contacto
1. El usuario introduce el `id` del contacto (o lo selecciona de una lista).
2. El sistema muestra todos los campos del contacto en formato legible.

### CU-03: Eliminar contacto
1. El usuario selecciona "Eliminar contacto" e introduce el `id`.
2. El sistema muestra los datos del contacto y solicita confirmación ("¿Seguro? [s/N]").
3. Si el usuario confirma, el sistema realiza soft-delete (`deleted_at = NOW()`).
4. El sistema confirma la eliminación.

### CU-04: Editar contacto
1. El usuario selecciona "Editar contacto" e introduce el `id`.
2. El sistema muestra los valores actuales campo a campo.
3. Para cada campo, el usuario puede introducir un nuevo valor o dejarlo vacío para
   mantener el actual.
4. El sistema valida los nuevos valores.
5. El sistema actualiza el registro y muestra confirmación.
  
### CU-05: Listar contactos
1. El usuario selecciona "Listar contactos".
2. El sistema muestra todos los contactos activos (sin `deleted_at`) en formato tabla.
3. Si hay más de 20 contactos, pagina de 20 en 20.
4. El usuario puede navegar entre páginas o volver al menú.

---
## 5. Reglas de validación
| Campo | Regla|
|-------|------|
| name | No vacío. Longitud 2–100 caracteres. Solo letras, espacios, guiones |
| surname | No vacío. Longitud 2–200 caracteres. Solo letras, espacios, guiones |
| tel1 | Formato: dígitos, espacios, `+`, `(`, `)`, `-`. Longitud 7–20 chars |
| tel2 | Formato: dígitos, espacios, `+`, `(`, `)`, `-`. Longitud 7–20 chars |
| email | Formato RFC 5322 básico: `usuario@dominio.tld` |
| notes | Sin restricción de formato. Máximo 5 000 caracteres. |

## 6. Requisitos no funcionales (opcional)

| ID    | Requisito                                                              |
|-------|------------------------------------------------------------------------|
| RNF-01| El tiempo de respuesta de cualquier operación debe ser < 2 segundos.   |
| RNF-02| La aplicación debe funcionar con Python 3.10+ y MySQL 8.0+.            |
| RNF-03| El código debe tener cobertura de tests >= 80 % en módulos de lógica.  |
| RNF-04| El esquema de base de datos debe incluir índices en campos de búsqueda.|
| RNF-05| Los mensajes de error deben ser claros y en el idioma de la interfaz.  |

---

## 7. Mensajes de error estándar (opcional)

| Código  | Mensaje                                              |
|---------|------------------------------------------------------|
| ERR-001 | "El nombre es obligatorio."                          |
| ERR-002 | "Debe indicar al menos un teléfono o un email."      |
| ERR-003 | "El formato del email no es válido."                 |
| ERR-004 | "El formato del teléfono no es válido."              |
| ERR-005 | "No se encontró ningún contacto con ese ID."         |
| ERR-006 | "Error de conexión a la base de datos."              |
| ERR-007 | "Ya existe un contacto con ese teléfono o email."    |

