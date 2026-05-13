# SPEC: Especificación de Pruebas Automáticas (EcoRoute)

Este documento define los requisitos para la generación del script de pruebas (`test_ecoroute.py`) mediante Pytest. El objetivo es validar el cumplimiento de las reglas de negocio y la robustez del sistema.

## 1. Requisitos del Script de Tests
El script generado debe cumplir con los siguientes estándares técnicos:
* **Framework:** Pytest.
* **Cobertura:** Mínimo 90% de la lógica de negocio (especialmente el módulo de seguridad).
* **Aislamiento:** Uso de `fixtures` para la creación de vehículos y rutas de prueba.
* **Tipado:** Uso de Type Hints en todas las funciones de test.

## 2. Casos de Prueba Obligatorios

### A. Validación de Modelos (Pydantic)
* **TC-01:** Verificar que el `id_vehiculo` rechace formatos no alfanuméricos.
* **TC-02:** Verificar que el `nivel_bateria_actual` no permita valores < 0 o > 100.

### B. Lógica de Seguridad (Regla del 80%)
* **TC-03 (Camino Feliz):** Un vehículo con 100% de batería solicita una ruta que consume el 20%. Resultado: **Permitido**.
* **TC-04 (Límite):** Un vehículo con 50% de batería solicita una ruta que consume el 39%. Resultado: **Permitido**.
* **TC-05 (Fallo de Seguridad):** Un vehículo con 50% de batería solicita una ruta que consume el 41% (excede el 80% de la carga disponible). Resultado: **Lanzar BatterySafetyException**.

### C. Cálculos de Autonomía
* **TC-06:** Validar que el consumo aumente proporcionalmente si se incluye el parámetro de `peso_kg`.

## 3. Rúbrica de Evaluación del Código (Auto-Check)
El script de test debe incluir al final una función o un comentario estructurado que evalúe el código de la aplicación según esta rúbrica:

| Criterio | Peso | Indicador de Logro (Puntaje 0-5) |
| :--- | :--- | :--- |
| **Integridad de Datos** | 20% | ¿Valida correctamente todos los campos de Vehicle y Delivery? |
| **Seguridad de Batería** | 40% | ¿Bloquea sin excepciones rutas que violan el margen de seguridad? |
| **Manejo de Errores** | 20% | ¿Usa excepciones personalizadas en lugar de errores genéricos? |
| **Arquitectura** | 20% | ¿Los tests están desacoplados de la base de datos (usan mocks/fixtures)? |

## 4. Output Esperado
Al ejecutar el script de test, este debe mostrar:
1. El resultado detallado de cada test unitario.
2. Un **Informe de Eficiencia** calculado como: `(Tests Pasados / Tests Totales) * 100`.
3. El estado de la rúbrica según los resultados obtenidos.

## 5. Instrucciones para la IA
"Actúa como un QA Automation Engineer Senior. Lee este documento y el código de la aplicación adjunto. Genera un archivo `test_ecoroute.py` que implemente todos los casos de prueba mencionados, asegurando que se sigan las reglas de la Rúbrica de Evaluación."