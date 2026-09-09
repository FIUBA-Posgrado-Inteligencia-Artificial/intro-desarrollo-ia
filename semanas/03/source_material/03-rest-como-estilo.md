# REST como estilo

Ya sabés que HTTP tiene methods y status codes. Pero ese repertorio, por sí solo, no te dice cómo armar una API: te dice qué piezas existen, no cómo combinarlas.

Pensalo así: querés borrar la tarea 17 del proyecto 4. ¿`DELETE /projects/4/tasks/17`? ¿`POST /borrar-tarea?id=17&proyecto=4`? Las dos son HTTP válido. Una sola es REST, y es la que casi cualquier API que vas a dirigir va a usar.

La pregunta no es si funciona; es bajo qué convención está escrito.

## HTTP es el cómo; REST es el qué

HTTP es el transporte: define cómo viaja un pedido y cómo viene la respuesta. REST es una *convención* sobre *cómo usar* ese transporte: qué se expone como recurso y qué operación viaja en el method. No agrega nada al cable; ordena el uso de lo que HTTP ya define. No es la única: existen GraphQL, que arma una sola path y deja que el cliente declare qué campos quiere, y existe RPC, que expone funciones con nombre en lugar de recursos. Pero la mayoría de las APIs públicas, y de los proyectos en los que vas a dictar contrato, hablan REST. Por eso el resto de esta semana lo asume.

## Todo es un recurso

La idea central de REST es que todo lo que la API expone es un **recurso**: una cosa concreta. Un proyecto, una tarea, un usuario, un mensaje. Las URLs nombran recursos, no acciones. Eso es lo que descarta `/borrar-tarea` y deja `/tasks/17`.

Los recursos vienen en dos formas. Una **colección** es el conjunto entero — `/projects` significa "todos los proyectos". Un **item** es uno solo — `/projects/4` es "el proyecto cuatro". Si un recurso solamente tiene sentido dentro de otro, anidás: `/projects/4/tasks` son "las tareas del proyecto cuatro", y `/projects/4/tasks/17` es la tarea 17 de ese proyecto.

La regla mental: el method dice qué hacer; la URL dice sobre qué. `GET /projects/4` lee el proyecto cuatro; `DELETE /projects/4` lo borra. La URL no cambia según la acción; la acción viaja en el method.

## Las cuatro reglas que importan

### Recursos en plural

Convención: `/users`, no `/user`. `/projects`, no `/project`. La URL nombra una colección, y una colección está en plural. Cuando querés uno solo, el item aparece como `/users/42`, no como un recurso distinto. Es chico, pero la consistencia importa: una API mezclada entre singular y plural se vuelve impredecible para quien la consume, incluida la IA que le genera el cliente.

### Jerarquía cuando hay relación

`/projects/4/tasks` dice "las tareas del proyecto cuatro". Cuando un recurso solamente existe en el contexto de otro (una tarea no flota suelta, pertenece a un proyecto), anidás. Cuando puede vivir por sí mismo, mantenelo plano: si una tarea existe sin proyecto, dejá `/tasks` y filtrá por query (`/tasks?project=4`). Anidás cuando la pertenencia es estructural, no cuando es opcional.

### Idempotencia

Una operación es **idempotente** si llamarla dos veces deja el sistema igual que llamarla una. GET lo es: leer dos veces no cambia nada. DELETE también: borrar algo borrado sigue dejándolo borrado. PUT igual: reemplazar un recurso por la misma versión dos veces da el mismo estado final. POST no: dos POST a `/projects` crean dos proyectos distintos.

La consecuencia práctica aparece cuando un request falla y no sabés si llegó. Si era idempotente, reintentás sin miedo. Si era POST, reintentar duplica.

### Sin estado entre requests

REST es **stateless**: cada request lleva todo lo que el servidor necesita para atenderlo. El servidor no recuerda quién llamó hace un segundo ni guarda contexto entre pedidos. Si necesita saber quién sos, eso viaja en el request.

Esa propiedad es lo que permite que un servidor REST escale: cualquier máquina del cluster atiende cualquier request, porque ninguna tiene historia. También es la razón por la que aparece, más adelante, el tema de los tokens de autenticación: si el servidor no recuerda quién sos, lo tenés que mandar cada vez. *Eso lo trabajamos en semana 7.*

## Cómo cambia la forma de dictar a la IA

Una vez que pensás en recursos, tu prompt cambia. Dejás de decir "hacé un endpoint para borrar tareas" y empezás a decir "expone `/projects/{id}/tasks/{taskId}` con DELETE para borrar y GET para consultar". Sin haberlo nombrado, ya estás dictando contrato: recurso, jerarquía, methods permitidos. El archivo siguiente le pone nombre.

## Vago vs. específico

```
hacé una API para gestionar proyectos y sus tareas
```

```
diseñá una API REST con dos recursos anidados:
- /projects con GET (lista) y POST (crear)
- /projects/{id}/tasks con GET (lista por proyecto), POST (crear) y DELETE /projects/{id}/tasks/{taskId} (borrar)
todas las respuestas en JSON. Las idempotentes (GET, DELETE) deben poder llamarse dos veces sin efectos diferentes.
```

Los dos pueden producir código que arranca. La diferencia está en los tres ejes de siempre. Determinismo: el específico fija recursos, jerarquía y methods, así que dos generaciones caen en estructuras casi idénticas; nunca es total (un LLM puede variar nombres entre corridas), pero el espacio de variación se achica mucho. Iteración: podés cambiar una pieza nombrada, agregar PATCH a tasks sin tocar el resto, en vez de regenerar todo. Auditoría: leés el código contra el contrato que escribiste; si falta DELETE en `/projects/{id}/tasks/{taskId}`, lo ves de una.
