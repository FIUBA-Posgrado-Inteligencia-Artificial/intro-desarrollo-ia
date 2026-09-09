# Demo en vivo: el modelo escribe el openapi.yaml

Este archivo es el guion del demo de cierre. No es un capítulo conceptual: es la coreografía. La clase termina con el profesor pegando un prompt en un chat de IA, leyendo el yaml que aparece, pidiendo un endpoint más desde el chat, y después pegando el yaml en `editor.swagger.io` para verlo renderizado y editarlo a mano ahí mismo. La intención es que cada beat conecte un concepto que ya se nombró en los archivos previos — sobre todo el bloque de contrato del §4.

**El demo no depende de ninguna función especial de editor colaborativo.** Cualquier chat de IA sirve: alcanza con que devuelva el yaml en un bloque de código copiable. El editor donde el yaml se vuelve tangible es `editor.swagger.io`, que además renderiza las docs al lado.

## Qué tiene que mostrar el demo

Cinco cosas tienen que ser visibles para que el demo cumpla el rol pedagógico. En este orden:

1. Tres methods HTTP en juego, mínimo: GET, POST y DELETE.
2. Una jerarquía de recursos visible en el path: `projects → projects/{id}/tasks → projects/{id}/tasks/{taskId}`. La pertenencia se ve, no se explica.
3. Al menos una respuesta de error documentada: 400 o 404. Los errores en el yaml dejan de ser adorno.
4. Schemas tipados: `type`, `required`, y `format` cuando aplique (`format: date`, `format: date-time`).
5. Iteración en vivo: agregar un endpoint nuevo desde el chat, sin reescribir el archivo entero.

## Dominio: projects/tasks (fijo)

El dominio no se decide en el momento. Se reusa el del §4 — el `POST /projects/{id}/tasks` con `title` requerido, `due_date` opcional, 201/400/404 — porque la fuerza del demo está justamente en que el alumno lo reconozca. Endpoints completos:

```
GET    /projects
POST   /projects
GET    /projects/{id}/tasks
POST   /projects/{id}/tasks
DELETE /projects/{id}/tasks/{taskId}
```

## El primer prompt (visible en pantalla mientras se tipea)

```
Necesito un openapi.yaml (3.1) para una API de proyectos y tareas.

recursos:
  Project   { id, name }
  Task      { id, title, due_date?, project_id }

endpoints:
  GET    /projects
  POST   /projects
  GET    /projects/{id}/tasks
  POST   /projects/{id}/tasks   → 201 / 400 / 404
  DELETE /projects/{id}/tasks/{taskId}
```

El alumno tiene que ver el prompt aparecer letra por letra. Esa parte del demo es tan importante como el yaml que sale: lo que se está mostrando es que el contrato se dicta, no se tipea. Notar en voz alta que el prompt no contiene ninguna instrucción sobre la herramienta — lo que hace el trabajo es el vocabulario.

## Los seis beats

**Beat 1 — Pegar el prompt.** El modelo devuelve el yaml completo. Frase para el aire: *"Esto no lo escribimos; lo dictamos."*

**Beat 2 — Leer el primer endpoint.** Bajar al `POST /projects/{id}/tasks` y recorrerlo en voz alta: `paths`, después la key `post`, después `parameters`, después `requestBody`, después `responses`. Mientras lo recorrés, mapeás cada cosa a las cinco piezas del §4 — method, path, schema-in, schema-out, errores. La idea es que el alumno escuche la traducción una vez, en vivo.

**Beat 3 — Pedir un endpoint más desde el chat.** Escribir en el chat: *"agregá `DELETE /projects/{id}`, con 404 si el proyecto no existe."* Vuelve el yaml con el endpoint nuevo. Frase para el aire: *"El contrato es editable; no es un prompt de un solo tiro."*

**Beat 4 — Pegar el yaml en `editor.swagger.io`.** Copiar el yaml del chat, abrir `editor.swagger.io`, pegarlo en el panel izquierdo. Aparece Swagger UI renderizado del lado derecho. Click en `POST /projects` → "Try it out". Frase para el aire: *"Del mismo archivo salieron docs interactivas. Codegen es real, no abstracción."* No hace falta que el endpoint conecte con un servidor real; alcanza con ver la UI funcional.

**Beat 5 — Editar el yaml a mano ahí mismo.** En el panel izquierdo del editor, agregar `description` opcional al schema de `Task`, o cambiar `due_date` de `string` plano a `string` con `format: date-time`. La derecha se actualiza sola. Frase para el aire: *"El contrato es un archivo: lo editás vos también, no sólo la IA."*

**Beat 6 — Cierre 30 segundos.** Enmarcar lo que pasó. *"Ustedes no tipearon nada. Lo que dirigieron fue el contrato. Y ahora tiene archivo."* Bridge a la próxima clase: *"La próxima clase tomamos este yaml y se lo damos a una IA local para que escriba la implementación."*

## Lo que NO se intenta hoy

No hay implementación, no hay base de datos real, no hay deploy, no hay tests automatizados. Solo el contrato como archivo, leído y editable, con su render como bonus. Si en algún momento la IA ofrece "y querés que te genere el código del servidor también", el profesor lo corta: hoy es el contrato, mañana la implementación.

## Plan B

Si el modelo devuelve el yaml partido en varios bloques o con comentarios intercalados, pedirle *"devolvémelo completo en un solo bloque de código"*. Si el chat que estés usando falla, cualquier otro sirve — el prompt no depende del proveedor. Y si `editor.swagger.io` no carga, el yaml se puede leer igual desde el chat; se pierde el render, no el concepto.
