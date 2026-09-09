# Semana 3 — Arquitectura Backend y Datos

Este es el material fuente de la clase de Semana 3. La presentación reveal.js se genera con `/build-class` a partir de estos archivos.

## Orden de lectura

| # | Archivo | Tema | Bloque de clase |
|---|---------|------|-----------------|
| 1 | [01-backend-y-el-supervisor.md](01-backend-y-el-supervisor.md) | Por qué hace falta vocabulario backend cuando la IA escribe el código | Apertura (~10 min) |
| 2 | [02-cliente-servidor-y-http.md](02-cliente-servidor-y-http.md) | Cliente-servidor, methods HTTP y status codes | Anatomía (~15 min) |
| 3 | [03-rest-como-estilo.md](03-rest-como-estilo.md) | REST como estilo: recurso, jerarquía, idempotencia | Anatomía (~12 min) |
| 4 | [04-rutas-controladores-y-contratos.md](04-rutas-controladores-y-contratos.md) | El endpoint como contrato dictable | Anatomía (~12 min) |
| 5 | [05-datos-relaciones-vs-documentos.md](05-datos-relaciones-vs-documentos.md) | SQL vs NoSQL: cuándo conviene cada uno | Anatomía (~15 min) |
| 6 | [06-errores-y-observabilidad.md](06-errores-y-observabilidad.md) | Leer fallas: 4xx, 5xx, logs, stack traces | Anatomía (~10 min) |
| 7 | [07-openapi-el-contrato-escrito.md](07-openapi-el-contrato-escrito.md) | OpenAPI como formalización del contrato del §4 | Cierre conceptual (~10 min) |
| 8 | [08-demo-en-vivo.md](08-demo-en-vivo.md) | Guion del demo: el modelo escribe el openapi.yaml; render en Swagger UI | Demo (~25 min) |

## Hilo conductor

Esta clase enseña vocabulario backend para que el alumno pueda **dirigir** una IA cuando el código que se genera ya no vive en una pestaña del navegador. Es el espejo simétrico de la semana 02: igual que ahí aprendiste a nombrar las piezas del frontend, acá aprendés a nombrar las piezas del backend — methods, paths, contratos, datos, errores — y a pedirlas con precisión.

La clase cierra formalizando ese vocabulario: el contrato del §4 tiene nombre y archivo — **OpenAPI** — y el demo en vivo lo muestra apareciendo en pantalla mientras el profesor se lo dicta a una IA. Los alumnos no tipean código, pero salen viendo el yaml renderizado en Swagger UI.
