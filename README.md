# Context engineering utilizando SPEC #

SPEC (a veces llamada Spec-Driven Development) es una metodología que antepone la especificación escrita a la escritura de código. La idea central es que un agente de IA, o un equipo humano, no debería empezar a programar hasta que los documentos de especificación estén completos y acordados.
El conjunto de ficheros forma una jerarquía de abstracción descendente:
---
| Campo | Descripción |
| :--- | :--- |
| constitution.md | Principios y límites globales (nunca cambia) |
| agents.md | Quién actúa y con qué restricciones |
| SPEC.md | Qué se construye (requisitos funcionales) |
| architecture.md | Cómo se construye (diseño técnico) |
| decisions.md | Por qué se construyó así (razonamiento) |
---


## Contenido del proyecto
### agents.md
*Describe quién hace qué: los componentes o roles del sistema, sus responsabilidades, sus herramientas permitidas y sus restricciones. En proyectos con IA agentica es especialmente crítico porque define el perímetro de actuación de cada agente autónomo.*
### constitution.md
*Define los límites que nunca se cruzan: seguridad, privacidad, reglas de código, Definition of Done. No describe funcionalidades sino restricciones que gobiernan todo lo demás. Es el documento más estable del proyecto porque cambia muy poco.*
### architecture.md
*Responde al cómo se construye técnicamente: la estructura de directorios, los módulos, sus responsabilidades y cómo fluyen los datos entre capas. Es la guía de navegación del código.*
### decision.md
*Responde al por qué se tomaron las decisiones que hay. Cada ADR documenta qué alternativas se evaluaron y por qué se descartó cada una. Es el documento que evita repetir debates y que explica el código a futuros desarrolladores (o a ti mismo seis meses después).*
### spec.md
*Define qué construye el sistema: el modelo de datos, los casos de uso, las reglas de validación y los requisitos no funcionales. Es el contrato entre quien pide la aplicación y quien la desarrolla.*
### agenda_contactos.zip
*Programa resultante de utilizar la especificación SPEC detallada en los ficheros anteriores con CLAUDE (05/05/2026)*
