---
title: "Gestion De furgonetas EcoRoute"
version: "0.1"
date: "11/05/2026"
---
# agents.md — Gestion de furgonetas

## Visión general

Este documento describe los agentes (roles de IA o automatizados) que pueden operar sobre la aplicación de gestión de furgonetas. Cada agente tiene un propósito concreto, un conjunto de herramientas permitidas y restricciones explícitas.

| Rol | Responsabilidad |
| :--- | :--- |
| **db_agent** | Gestionar todas las operaciones de lectura y escritura sobre la base de datos MySQL. Es el único agente con acceso directo a la capa de persistencia. |
| **logic_agent** | Contener toda la lógica de negocio de la agenda: validaciones, normalización de datos y orquestación de operaciones. |
| **cli_agent** | Proporcionar la interfaz de usuario en línea de comandos (CLI) para interactuar con la gestopm. |
| **test_agent (opcional)** | Ejecutar la suite de pruebas automatizadas del proyecto. |
---
## Diagrama de interacción

```
Usuario
  │
  ▼
cli_agent  ──►  logic_agent  ──►  db_agent  ──►  MySQL
                                                   │
test_agent ◄─────────────────────────────────────┘
            (entorno de pruebas aislado)
```

---
