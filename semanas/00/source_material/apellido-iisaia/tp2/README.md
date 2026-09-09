# TP 2 — API de proyectos y tareas

Un `openapi.yaml` que describe una API donde cada proyecto agrupa tareas y una tarea no existe fuera de un proyecto. Cinco endpoints, tres paths, sin nada implementado: el entregable es el contrato.

## Cómo se lee

Pegar el contenido de [openapi.yaml](openapi.yaml) en [editor.swagger.io](https://editor.swagger.io). Aparece la documentación navegable del lado derecho, con cada endpoint desplegable.

## Qué me propuse construir

El dominio más chico donde la jerarquía se justifica sola. Necesitaba dos recursos con una relación de pertenencia real —no una relación opcional que se resolvería con un filtro— para que anidar el path fuera la decisión correcta y no una preferencia estética. Proyectos y tareas cumple: una tarea suelta, sin proyecto, no significa nada en este modelo.

Salió en tres prompts, en una sola conversación.

## Decisiones que tomé yo

**Anidar `tasks` dentro de `projects` en vez de `/tasks?project=4`.** La decisión de fondo del contrato. Elegí anidar porque en este dominio la pertenencia es estructural: si se borra el proyecto, sus tareas no tienen dónde vivir. Si hubiera querido que una tarea pudiera existir sin proyecto, la forma correcta era el path plano con filtro. Las dos son válidas; lo que no es válido es elegir sin darse cuenta de que se está eligiendo.

**Schemas de entrada y de salida separados.** `TaskInput` y `Task` no son el mismo objeto. El de salida tiene `id` y `project_id`, que los genera o los deduce el servidor; el de entrada no los tiene porque el cliente no los manda. Es la diferencia entre lo que pedís y lo que te devuelven, y colapsarla en un solo schema con campos opcionales la esconde.

**`204` sin cuerpo en el borrado.** Devolver `200` con la tarea borrada adentro es lo que sale por default y no tiene sentido: si el recurso ya no existe, mandarlo de vuelta es describir algo que no está.

**`404` en el `GET` de tareas, no lista vacía.** Si alguien pide las tareas del proyecto 99 y ese proyecto no existe, devolver `[]` miente: sugiere que el proyecto existe y no tiene tareas. Son dos situaciones distintas y ameritan respuestas distintas.

**`due_date` opcional, `title` requerido.** Una tarea sin título no es una tarea. Una tarea sin fecha sí.

## Qué salió mal y cómo lo corregí

En el primer prompt describí `Task` como `{ id, title, due_date?, project_id }`, y el modelo hizo lo razonable con esa descripción: puso `project_id` en los dos schemas, el de salida **y el de entrada**. Quedó un `TaskInput` que pedía el `project_id` en el body de un `POST` cuyo path ya era `/projects/{projectId}/tasks`.

O sea que el contrato pedía el mismo dato dos veces, por dos vías distintas. Y como consecuencia deja abierta una pregunta que nadie contestó: si el `projectId` del path dice `4` y el `project_id` del body dice `7`, ¿cuál gana? Un contrato que permite esa contradicción va a producir una implementación que la resuelve sola, en silencio, del modo que se le ocurra al modelo.

No lo vi al leer la respuesta del prompt 1. Lo encontré recién en el prompt 2, releyendo el yaml entero para chequear que el `DELETE` no hubiera roto nada. El arreglo fue de una línea, pero el problema no era el yaml: era el prompt. Yo escribí la forma del recurso completo y la usé como si fuera la forma de la entrada, sin separarlas. El modelo copió mi confusión.

La regla que me llevo es la de la clase: el path identifica, el body transporta contenido nuevo. Un dato que ya está en el path no vuelve a viajar en el body. La tenía escrita en los apuntes y aun así la pasé por alto, porque escribí el prompt pensando en el recurso y no en el pedido.

## Prompts

El registro completo está en [prompts.md](prompts.md). El que más pesó fue el primero, que fija los cuatro endpoints y la separación entre schema de entrada y de salida; el tercero es la corrección.
