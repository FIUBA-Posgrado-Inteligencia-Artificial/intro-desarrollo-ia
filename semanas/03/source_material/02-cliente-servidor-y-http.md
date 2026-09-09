# Cliente, servidor y el protocolo HTTP

Cuando abrís Twitter y aparece el feed, algo viajó. Tu teléfono le pidió a una computadora en un data center "dame los últimos posts de las cuentas que sigo", y esa computadora le contestó con una lista. Cuando refrescás Gmail, igual: tu navegador pregunta "¿hay correos nuevos?", y un servidor responde sí o no. Cuando apretás "enviar" en WhatsApp Web, tu mensaje sale, cruza la red, y aterriza en un servidor de Meta que se encarga de hacérselo llegar al destinatario.

Eso pasa decenas de veces por minuto en cualquier dispositivo que uses. No es magia: es una conversación con reglas y vocabulario fijo. Las partes tienen nombre, y hasta los errores tienen nombre. Acá se los ponemos.

## Cliente y servidor: dos roles, una conversación

Los dos protagonistas son simples. El **cliente** es quien pide. El **servidor** es quien responde. Esa es toda la asimetría: uno habla primero, el otro contesta. El servidor no manda cosas porque sí; espera que le pregunten.

Conviene desarmar la idea de que el cliente es siempre un navegador. *Cualquier cosa* puede ser un cliente. Una app de iPhone es un cliente. Otro servidor que consume una API ajena, en ese momento, es un cliente. Un `curl` en la terminal es un cliente. La palabra describe un rol, no un tipo de programa.

De hecho, la página que armaste en semana 2 ya era un cliente; no la nombramos así porque todavía no había con quién hablar. Ahora aparece la otra mitad, y entre las dos hace falta un idioma común.

## HTTP: el idioma del pedido y la respuesta

Ese idioma es **HTTP**: un protocolo de pedido y respuesta. El cliente arma un **request**, el servidor arma una **response**, y entre los dos cabe casi todo lo que hace internet hoy.

Un request tiene cuatro partes: un método (el method que ya viene), una URL (a qué recurso le hablás), unos headers (metadatos) y, opcionalmente, un body. Una response tiene tres: un status code, unos headers, y un body.

Un par concreto se ve así:

```
GET /projects/4 HTTP/1.1
Host: api.example.com

— — — respuesta — — —

HTTP/1.1 200 OK
Content-Type: application/json

{ "id": 4, "name": "Tesis" }
```

Ese ida y vuelta, repetido millones de veces por segundo, es internet.

## Los methods: el repertorio de operaciones

HTTP define un puñado de métodos, llamados *methods*. La palabra engaña: no son acciones que vos componés libremente, son el repertorio fijo de operaciones que pedís — las que se repiten en toda API.

Los cinco que importan:

- **GET** — leer un recurso, sin efectos.
- **POST** — crear un recurso nuevo.
- **PUT** — reemplazar un recurso entero por la versión que mandás.
- **PATCH** — modificar parte de un recurso, dejando el resto como estaba.
- **DELETE** — borrar un recurso.

Eso es todo el repertorio que vas a necesitar el 95% del tiempo. El resto de la conversación va por methods y paths.

## Status codes: el vocabulario de las fallas

Cuando el servidor responde, lo primero que dice es un número de tres dígitos: el status code. Está agrupado por familias, y cada familia te dice de qué tipo es la respuesta.

- **2xx** (todo bien) — 200 (OK) para un GET que devolvió lo pedido, 201 (creado) para un POST, 204 (sin contenido) para un DELETE.
- **4xx** (vos, cliente, te equivocaste) — el pedido llegó pero algo del cliente está mal. 400 (mal pedido) si el body no tiene la forma esperada, 404 (no existe) si el path o el recurso no aparecen. Acá también viven 401 y 403, autenticación y permisos; *los vemos en semana 7*.
- **5xx** (yo, servidor, me caí) — el pedido estaba bien pero el servidor no pudo cumplirlo. 500 (algo se rompió adentro), 503 (servicio no disponible).

Cuando leés un código, estás leyendo *de qué lado* fue la falla. Si arranca con 4, mirá tu pedido. Si arranca con 5, mirá los logs del servidor. Esa lectura te ahorra horas de buscar en el lugar equivocado.

## Por qué importa para vos como supervisor

Cuando le pedís a la IA que te arme un endpoint, deberías poder especificar el method, predecir el status code que va a devolver, y leer la respuesta para confirmar que hizo lo que dijo. Si no podés hacer esas tres cosas, estás aceptando decisiones que no sabías que estabas tomando, y vos te enterás cuando algo se rompe.

## Vago vs. específico

Comparemos dos prompts que piden lo mismo: un endpoint para guardar tareas.

```
hacé un endpoint para guardar tareas
```

```
definí un endpoint POST /tasks que reciba en el body { title: string (requerido), due_date: string ISO (opcional) }, devuelva 201 con el objeto creado o 400 si falta title. Servir respuesta como JSON.
```

Los dos pueden generar código que parece correcto a primera vista — un LLM es bueno produciendo cosas que compilan. La diferencia no está en si funciona, está en si es auditable. El específico fija method, path, forma del body, código de éxito y código de falla; eso es un contrato. El vago hereda decisiones invisibles: el modelo elige todo eso por vos y no lo sabés hasta leer línea por línea.

Tres ejes. Determinismo: cuanto más específico el prompt, más cerca estás de obtener siempre la misma respuesta; con LLMs nunca es total, dos corridas pueden diferir en detalles, pero el espacio de variación cae mucho. Iteración: el prompt específico te deja cambiar una pieza nombrada, no rehacer todo. Auditoría: verificás el código punto por punto contra el contrato que escribiste, no contra una idea vaga en tu cabeza.
