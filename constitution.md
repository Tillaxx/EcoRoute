---
title: "Agenda básica CLI con Python"
version: "0.1"
date: "04/05/2026"
---
# constitution.md — Agenda de Contactos
Este documento establece los principios irrenunciables, las reglas de diseño y las restricciones éticas/técnicas que gobiernan el desarrollo y la operación de la aplicación de gestión de agenda de contactos. Todos los agentes, desarrolladores y decisiones de diseño deben cumplir este contrato.

## 1. Principios fundamentales

### 1.1 Privacidad por diseño
- Los datos de contacto son información personal. El sistema **no** los enviará a   servicios externos ni los registrará en logs sin cifrado.
- El acceso a la base de datos requiere autenticación; las credenciales nunca se  almacenan en el código fuente ni en el control de versiones.

### 1.2 Integridad de datos
- Toda escritura en base de datos se realiza mediante sentencias parametrizadas   (prepared statements). 
- Antes de insertar o actualizar un contacto, el `logic_agent` valida todos los   campos obligatorios.
- Las eliminaciones son **soft-delete** por defecto (campo `deleted_at`), preservando   el historial de datos.

### 1.3 Separación de responsabilidades
- Ningún agente asume responsabilidades de otro (ver `agents.md`).

### 1.4 Simplicidad sobre complejidad
- Se prefieren soluciones simples y legibles frente a abstracciones prematuras.
- No se añadirán dependencias externas sin justificación documentada en `decisions.md`.

---

## 2. Reglas de diseño de código

| Regla | Descripción |
|-------|-------------|
| R-01  | Toda función pública debe tener docstring con parámetros y valor de retorno. |
| R-02  | El manejo de errores usa excepciones. |
| R-03  | Las constantes de configuración se leen desde variables de entorno. |
| R-04  | Los nombres de variables y funciones están en **snake_case** en español o inglés, consistentemente por módulo. |
| R-05  | Cada módulo tiene al menos un test unitario antes de considerarse "completo". |

---

## 3. Restricciones de seguridad

- **SQL Injection:** Uso obligatorio de parámetros en todas las consultas.
- **Credenciales:** Cargadas desde `.env` (excluido de `.gitignore`). Nunca en código.
- **Permisos MySQL:** El usuario de la aplicación solo tiene `SELECT, INSERT, UPDATE, DELETE` sobre el esquema `agenda_db`. **No** tiene `DROP`, `CREATE` ni `GRANT`.
- **Contraseñas de usuarios (futuro):** Si se añade autenticación, se usará `bcrypt`con coste mínimo 12. Nunca MD5 ni SHA-1.

---

## 4. Restricciones de comportamiento de agentes

- Los agentes **no** pueden modificar este documento de forma autónoma.
- Los agentes **no** pueden ejecutar DDL (CREATE TABLE, DROP, ALTER) en producción sin aprobación humana explícita.
- El `cli_agent` **no** puede omitir confirmaciones en operaciones destructivas (eliminar contacto, vaciar agenda).
- El `test_agent` **no** puede conectarse a la base de datos de producción bajo ninguna circunstancia.

---

## 5. Compatibilidad y portabilidad

- Python >= 3.10.
- MySQL >= 8.0 (o MariaDB >= 10.6 como alternativa compatible).
- El proyecto debe funcionar en Linux, macOS y Windows (WSL2 aceptable para Windows).

---

## 6. Ciclo de vida de cambios

1. Cualquier cambio que contradiga este documento requiere una PR con justificación    explícita y aprobación de al menos un revisor humano.
2. Los cambios de esquema de base de datos se documentan en `migrations/` con    timestamp y descripción.
3. Los cambios de arquitectura se registran en `decisions.md` antes de implementarse.

---

## 7. Definición de "hecho" (Definition of Done)

Una funcionalidad se considera completa cuando:
- [ ] El código pasa todos los tests sin fallos.
- [X] La cobertura de tests del módulo es >= 80 %.
