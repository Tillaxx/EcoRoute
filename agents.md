---
title: "Gestion De furgonetas EcoRoute"
version: "0.1"
date: "12/05/2026"
---
# agents.md — Gestion de furgonetas

## Visión general

Este documento describe el comportamiento de los agentes que podran operar sobre nuesta aplicacion. Cada agente tiene sus funciones especificas y sus herraminetas.

| Rol | Responsabilidad |
| :--- | :--- |
| **db_agent** | Gestiona todo lo relacionado a la base de datos como accesos y lecturas. |
| **logic_agent** | Contiene toda la lógica: validaciones, normalización de datos y orquestación de operaciones. |
| **cli_agent** | Proporcionar la interfaz de usuario que en este caso es en línea de comandos (CLI) . |
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
