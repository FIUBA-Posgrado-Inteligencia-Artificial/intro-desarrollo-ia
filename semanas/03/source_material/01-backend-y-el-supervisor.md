# Backend y el supervisor arquitectónico

## Qué es un backend

El **backend** es la parte de un sistema que el usuario no ve: ejecuta la lógica, guarda el estado y hace cumplir las reglas. Nadie lo usa de forma directa. Siempre alguien le pide algo —una interfaz, otro programa, o simplemente un reloj que dice "son las tres de la mañana, arrancá"— y él responde. Es exactamente lo que la semana pasada quedaba nombrado como "los datos y las reglas del negocio viven detrás".

Backend no quiere decir "servidor web". Toma varias formas:

- **Servidor web**: espera pedidos por red y contesta de a uno. Detrás de casi todo sitio o app.
- **Proceso programado**: nadie le pide nada, arranca solo a una hora fija. Facturación de fin de mes, reportes de la madrugada.
- **Worker de cola**: toma trabajos de una fila y los procesa de a uno. Mandar mails, convertir videos.
- **Embebido**: sin red y sin servidor, vive dentro de la misma aplicación. Una app de escritorio que guarda todo en un archivo local también tiene backend.

Las cuatro hacen lo mismo: custodian los datos y ejecutan las reglas que nadie puede saltear desde afuera. Esa última parte es la que le da peso al rol. Una regla que vive en el frontend se esquiva abriendo las herramientas del navegador; una que vive en el backend, no. Por eso el backend es donde terminan las decisiones que de verdad importan.

Vamos a trabajar sobre el servidor web por dos razones: es la forma más común con diferencia, y es la única que obliga a definir un contrato explícito con quien le habla. Ese contrato es el corazón de todo lo que sigue.

Si una IA puede levantar un servidor en treinta segundos, ¿por qué seguimos aprendiendo qué es un `POST` o cómo se modela una tabla? La respuesta es la misma que la semana pasada: el cuello de botella no es la sintaxis, es la decisión. Y ahora la decisión vive en un servidor.

La semana pasada nombramos las piezas del frontend para dirigir lo que la IA escribe en el navegador. Esta semana hacemos lo mismo del otro lado: aprendemos el **vocabulario backend** mínimo para seguir siendo **supervisor arquitectónico** cuando el código deja de correr en la pestaña del usuario y empieza a correr en una máquina que vos no ves. El rol no cambia. Cambia el terreno.

## La pregunta en versión backend

La pregunta vuelve, tramposa. Si Claude te genera una API con cinco paths en un prompt, ¿qué sentido tiene aprender qué es un path, un recurso, una respuesta? Memorizar la firma de un framework no es el punto. Nunca lo fue.

Lo que sí es el punto es decidir qué se construye. Qué endpoints existen, qué reciben, qué devuelven, qué pasa cuando algo falla, dónde se guardan los datos y bajo qué forma. Esa lista de decisiones no la toma la IA por vos: la IA implementa lo que vos especifiques. Si no podés nombrar las piezas, no podés especificar; y si no especificás, estás aceptando lo primero que el modelo te tire. Nombrar es lo que te permite dirigir.

## Lo que cambia cuando el código vive en un servidor

Mover el código del navegador al servidor mueve tres cosas al mismo tiempo, y conviene verlas por separado.

Primero, la **persistencia**. En el frontend un error es efímero: el usuario refresca la pestaña y el estado malo desaparece. En el backend no. Un bug que escribe datos incorrectos los deja escritos. Mañana, cuando vuelvas a leer la base, ese error sigue ahí, multiplicado por cada usuario que pasó por el path rota. La gravedad cambia de orden de magnitud, y eso obliga a supervisar antes de ejecutar, no después.

Segundo, los **contratos** entre cliente y servidor se vuelven reales. Frontend y backend son dos programas distintos que se hablan por la red, y solamente se entienden si están de acuerdo en qué se pide y qué se devuelve. La IA puede inventarte un endpoint que el frontend no sabe consumir, o devolver un campo con otro nombre del que el cliente espera. Si vos no podés escribir el contrato, la IA lo improvisa y te enterás cuando algo se rompe.

Tercero, **leer la respuesta** se vuelve tan importante como leer el código. En el frontend leés HTML y CSS generado; en el backend leés además status codes, cuerpos JSON y logs del servidor. Si el modelo te dice "ya quedó funcionando" pero la respuesta trae un error, y vos no sabés mirar la respuesta, le estás creyendo a la IA en lugar de creerle a la máquina.

Para hacer las tres cosas necesitás vocabulario.

## La anatomía en seis partes

El resto de la semana la vamos a pasar desarmando seis piezas. Una frase por pieza, lo justo para anclarte:

1. **HTTP** — el protocolo de pedido y respuesta entre cliente y servidor.
2. **REST** — el estilo arquitectónico que organiza esos pedidos alrededor de recursos.
3. **Endpoints** — la unidad mínima dictable: method + path + entrada + salida + códigos.
4. **Datos** — cómo se persiste, relaciones vs. documentos.
5. **Errores y observabilidad** — leer fallas como parte de supervisar.
6. **Salto al stack local** — por qué el navegador ya no alcanza, qué herramienta abre la siguiente clase.

Cada archivo siguiente desarma una de estas piezas. El orden tampoco es arbitrario: primero el protocolo, encima el estilo, encima la unidad que vas a dictar, después los datos que esa unidad mueve, después cómo se ven las cosas cuando se rompen, y al final el salto hacia la próxima semana.

## A qué prestamos atención esta clase

Quiero ser explícito, igual que la semana pasada, con lo que esta clase no es. No te estoy enseñando FastAPI, ni Python de producción, ni SQL ni diseño de bases de datos serio. Para cada una de esas cosas hay materias enteras y no caben en noventa minutos.

Te estoy enseñando a *nombrar las piezas*. A reconocer un path cuando la IA la escribe, a distinguir un status code de un cuerpo de respuesta, a notar cuando un contrato no cierra. Cada concepto entra acá porque sirve directo al diálogo con un LLM: o te ayuda a especificar mejor lo que querés, o te ayuda a leer lo que la IA devuelve sin tragarte un error silencioso.

Volvé al rol cuando dudes: sos supervisor arquitectónico también del lado del servidor. La IA implementa; vos decidís el contrato. La clase termina con una demo en vivo: le vamos a pedir a un modelo que levante una API y la vamos a leer juntos, endpoint por endpoint, respuesta por respuesta. La demo se enseña sola si tenemos las palabras.
