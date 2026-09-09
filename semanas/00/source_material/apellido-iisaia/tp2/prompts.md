# Prompts — TP 2

El registro del proceso, en orden. Tres prompts en una sola conversación. El contrato quedó terminado en el tercero.

---

## 1 — Prompt inicial

```
Necesito un openapi.yaml (3.1) para una API de proyectos y tareas.

recursos:
  Project   { id, name }
  Task      { id, title, due_date?, project_id }

endpoints:
  GET    /projects                      → 200 lista
  POST   /projects                      → 201 / 400 si falta name
  GET    /projects/{projectId}/tasks    → 200 lista / 404 si el proyecto no existe
  POST   /projects/{projectId}/tasks    → 201 / 400 si falta title / 404 si el proyecto no existe

Los schemas de entrada y de salida son distintos: el de salida incluye
el id que genera el servidor, el de entrada no.
```

**Qué buscaba:** fijar los cuatro endpoints y, sobre todo, la separación entre schema de entrada y de salida. Si no la nombro, el modelo suele reusar un solo schema con el `id` marcado como opcional, y ahí se pierde que el `id` lo genera el servidor y no el cliente.

Volvió el yaml con los cuatro paths, `components/schemas` con `Project`, `ProjectInput`, `Task` y `TaskInput`, y los códigos pedidos.

---

## 2 — Agregar el borrado

```
Agregá DELETE /projects/{projectId}/tasks/{taskId}, que devuelva 204 sin
cuerpo si borró y 404 si la tarea no existe.
```

**Qué buscaba:** completar el tercer method. Pedí `204` explícito porque si no lo digo el modelo tiende a devolver `200` con el objeto borrado, que es raro: si lo borraste, no tiene sentido devolverlo.

Lo agregó bien y no tocó nada de lo anterior, que era la otra cosa que quería comprobar.

---

## 3 — Sacar project_id del body

```
En TaskInput sacá project_id. El proyecto ya viene en el path, no tiene
que viajar también en el body.
```

**Qué buscaba:** corregir el error que había pasado sin que yo lo notara en el prompt 1. Ver el detalle en el README.

---

## Conversación completa

Una sola conversación, sin reiniciar el hilo. El yaml final tiene 5 endpoints repartidos en 3 paths.
