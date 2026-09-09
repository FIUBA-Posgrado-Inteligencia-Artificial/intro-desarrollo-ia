# Trabajo Práctico 2 — el contrato de tu API

Este archivo es la consigna del TP 2, que se entrega en esta clase. Va después del demo a propósito: el alumno acaba de ver el movimiento completo —dictar un contrato, recibir un `openapi.yaml`, leerlo endpoint por endpoint— y ahora lo hace sobre un dominio que elige él.

## La consigna

Elegí un dominio que conozcas y describilo como API. **No hay que implementar nada**: no hay servidor, no hay base de datos, no hay código que corra. El entregable es el contrato, el mismo recorte que hizo la clase.

Ejemplos de dominio: una biblioteca con libros y préstamos, un gimnasio con socios y clases, un recetario con recetas e ingredientes, una veterinaria con mascotas y turnos, un torneo con equipos y partidos. El requisito real detrás de la elección es que haya **dos recursos que se relacionen**, porque si no la jerarquía no tiene dónde aparecer.

## Qué tiene que cumplir el yaml

Son los mismos requisitos que se le pidieron al yaml del demo, menos la iteración en vivo, que acá se evidencia en el `prompts.md`.

1. **Tres methods** como mínimo: `GET`, `POST` y `DELETE`.
2. **Jerarquía de recursos** visible en el path: un recurso adentro de otro, del estilo `/algo/{id}/otra-cosa`.
3. **Al menos una respuesta de error documentada**: un `400` o un `404`.
4. **Schemas tipados**: `type`, `required`, y `format` donde aplique.

Un consejo que no es requisito: pegar el yaml en `editor.swagger.io` antes de entregar. Si abre sin errores, es válido; si se queja, dice en qué línea.

## Constraints

- Trabajo **individual**.
- Una sola conversación de IA, de principio a fin, así el registro se lee como un proceso y no como pedazos sueltos.
- La herramienta es libre: cualquier chat sirve. El prompt del demo no depende de ningún producto en particular.

## Entregable

Una carpeta `tp2/` nueva en el repositorio de siempre, con tres archivos:

- `openapi.yaml` — el contrato.
- `prompts.md` — la secuencia de prompts en orden, con una anotación breve por prompt explicando qué se buscaba con cada uno.
- `README.md` — el informe: qué API elegiste, qué decidiste vos, qué salió mal y cómo lo corregiste.

Las dos últimas secciones del README son las que más pesan al corregir. **Qué decidiste vos** es especialmente concreto en un contrato: por qué anidaste en vez de filtrar, por qué `404` y no `400`, qué campo dejaste opcional. **Qué salió mal** muestra que se leyó el yaml en vez de darlo por bueno.

## Ejemplo resuelto

Hay un `tp2/` completo en el repo de referencia (`apellido-iisaia/tp2/`), con la misma API de proyectos y tareas que se usa en la clase. Está para mostrar la forma y el nivel de detalle esperado, no para que lo copien: el dominio lo elige cada uno.

El error que documenta ese ejemplo vale la pena mencionarlo en clase si alguien pregunta qué cuenta como "qué salió mal": el modelo dejó `project_id` en el schema de entrada de la tarea, cuando el proyecto ya viajaba en el path. El contrato pedía el mismo dato dos veces por dos vías, y no decía cuál gana si difieren. Es exactamente el error contra el que advierte el mantra path/query/body, y vino del prompt, no del modelo.
