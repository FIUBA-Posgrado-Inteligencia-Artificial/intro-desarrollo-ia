# Paths, controllers y el contrato

Le decís a la IA "hacé un endpoint para crear una tarea" y te devuelve trescientas líneas que parecen razonables. Mirás de cerca y descubrís que inventó diez decisiones que vos no dictaste: qué campos son requeridos, qué pasa si `due_date` viene mal formateada, qué forma exacta tiene la respuesta, qué código devuelve cuando todo sale bien, qué hace si el proyecto al que pertenece la tarea no existe. Cada una de esas decisiones es una bifurcación que la IA tomó por su cuenta, y que vos vas a descubrir cuando algo se rompa. Un *contrato* saca esas decisiones de la imaginación del modelo y las pone en tus manos antes de que se escriba una sola línea.

## Un path es una promesa

Un endpoint, mirado de cerca, es la suma de cinco piezas. El **method** dice qué hace: leer, crear, reemplazar, modificar, borrar. La **path** o **path** dice sobre qué actúa: qué recurso de los que la API expone está en juego. El **schema de entrada** describe qué datos llegan: la estructura del payload, los tipos de cada campo, cuáles son requeridos y cuáles opcionales. El **schema de salida** describe qué se devuelve cuando todo sale bien: la forma exacta del JSON que el cliente va a recibir. Y los **códigos posibles** enumeran qué status codes puede emitir el endpoint y bajo qué condición se dispara cada uno.

Esas cinco piezas no son adornos: cada una corresponde a una decisión concreta que alguien tiene que tomar. Si vos no las dictás, las toma la IA, y las toma sin avisarte.

## Path, query y body: dónde viajan los datos

Los datos que mandás al servidor pueden viajar en tres lugares distintos, y dónde los pongas no es una cuestión estética: cambia la semántica del pedido.

Los **path params** identifican un recurso puntual y son parte de el path. Cuando ves `/projects/{id}` y pedís `/projects/4`, ese `4` es un path param: dice "este proyecto, no otro". El path param identifica.

Los **query params** modifican una lectura. Aparecen después de un `?`, y se usan para filtrar, paginar u ordenar resultados. `/projects?page=2&sort=date` no apunta a un proyecto distinto; apunta a la misma colección de proyectos pero pidiendo la segunda página y ordenada por fecha. El query param modifica.

El **body** lleva el payload de una creación o actualización. Cuando hacés POST a `/projects/{id}/tasks`, el `id` viaja en el path porque identifica al proyecto, pero los datos de la tarea nueva (su título, su fecha de vencimiento) viajan en el body, normalmente como JSON. El body es el contenido de la operación.

La regla mental: path identifica, query ajusta el pedido, body transporta el contenido nuevo.

## El controller: quién atiende la promesa

Del lado del servidor, el **controller** es la función que recibe el request y produce la response. Es quien atiende la promesa que el path hace. En este curso vos no vas a escribir controllers a mano: los vas a *especificar*, y la IA los va a implementar. La unidad mínima que le entregás a la IA no es código; es un bloque de contrato.

Un bloque de contrato, en pseudocódigo, se ve así:

```
POST /projects/{id}/tasks
  entrada (body):
    title       string, requerido
    due_date    fecha ISO, opcional
  salida 201 (creado):
    { id: int, title: string, due_date: ISO|null, project_id: int }
  salida 400 (datos inválidos):
    { error: "title es requerido" }
  salida 404 (proyecto no existe):
    { error: "no se encontró el proyecto {id}" }
```

Ese bloque entero es la unidad mínima que se le dicta a la IA sin ambigüedad. Method, path, qué entra, qué sale en éxito, qué sale en cada falla anticipada. No queda decisión silenciosa para que el modelo improvise: si falta `title`, no es opinable, está escrito que devuelve 400.

## El átomo dictable

Esto es el equivalente, del lado del backend, a lo que en semana 2 fue "un componente con sus props y su estado bien definidos". Lo que para el frontend era un componente con props tipadas, para el backend es un endpoint con su contrato. Un átomo dictable: una pieza chica, con bordes claros, que podés pegarle a una IA sin tener que aclarar nada más, y volverte. Si tu prompt es ese bloque y nada más, alcanza.

## Vago vs. específico

```
hacé el endpoint de crear tarea
```

```
POST /projects/{id}/tasks
  entrada (body):
    title       string, requerido
    due_date    fecha ISO, opcional
  salida 201:
    { id: int, title: string, due_date: ISO|null, project_id: int }
  salida 400:
    { error: "title es requerido" }
  salida 404:
    { error: "no se encontró el proyecto {id}" }
```

Los dos pueden producir código que arranca y compila; un LLM es bueno en eso. La diferencia está en los tres ejes de siempre. Determinismo: el bloque específico fija method, path, forma del body, código de éxito y códigos de falla, así que dos generaciones convergen en estructuras casi idénticas; con LLMs nunca es total, pero el espacio de variación se achica mucho. Iteración: podés cambiar una línea del contrato (agregar un campo, mover un 400 a 422) y regenerar solo esa pieza, en vez de rehacer todo. Auditoría: leés el código generado contra el contrato que escribiste y ves de una si el endpoint cumple lo prometido o no. El específico es el contrato; el vago lo deja implícito y la IA lo completa por vos.
