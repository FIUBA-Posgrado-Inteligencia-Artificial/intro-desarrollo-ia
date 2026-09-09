# Spine — Semana 03: Arquitectura Backend y Datos

**Whole-week through-line:** La semana 02 te enseñó a nombrar las piezas del frontend para dirigir a la IA cuando el código vive en el navegador. Esta semana es el reflejo: mismo rol (supervisor arquitectónico), mismo mecanismo (vocabulario → especificación → código auditable), terreno nuevo (servidor). HTTP, REST, paths, datos y errores son el vocabulario backend que te permite seguir dirigiendo cuando el código se muda al servidor. La clase termina cerrando el espejo en vivo: el profesor le dicta el contrato del §4 a una IA, que devuelve un openapi.yaml; los alumnos lo leen endpoint por endpoint y lo ven renderizado en Swagger UI.

## Section 1: Backend y el supervisor arquitectónico
**Source material:** `source_material/01-backend-y-el-supervisor.md`
**Through-line:** El rol de supervisor arquitectónico no cambia entre frontend y backend; lo que cambia es el terreno. Esta sección establece la simetría con semana 02 y da el mapa de las seis piezas que se desarman en el resto de la clase.
**Key analogy:** El espejo de la semana 02. Frontend ↔ backend como las dos mitades simétricas de la misma habilidad: nombrar piezas para dirigir a una IA.
**What students walk away knowing:**
- El rol "supervisor arquitectónico" sobrevive al cambio de capa; lo que cambia es el vocabulario.
- El mapa de la clase: HTTP → REST → endpoints → datos → errores → OpenAPI (el contrato escrito).
**Animations / interactive:** None new. Considerar reutilizar `pipeline-roadmap` (semana 01) para mostrar el arco de las 6 piezas y volver a iluminarlas al abrir cada sección.
**Slide budget:** 3 slides (espejo → roadmap → disclaimer). Sin hook ni "tres cosas que cambian": ambos pre-cargan vocabulario backend que el alumno todavía no tiene; cada consecuencia (persistencia, contratos, leer la respuesta) se motiva en su propia sección más adelante.

## Section 2: Cliente, servidor y HTTP
**Source material:** `source_material/02-cliente-servidor-y-http.md`
**Through-line:** HTTP es el idioma cliente↔servidor. Cliente y servidor son roles, no programas. Los methods son un repertorio fijo (cinco que importan), y los status codes dicen de qué lado fue la falla antes de mirar nada más.
**Hook:** Cuando abrís Twitter y aparece el feed, algo viajó. Cuando refrescás Gmail, igual. Cuando WhatsApp Web envía un mensaje, lo mismo. Eso pasa decenas de veces por minuto en cualquier dispositivo: una conversación con vocabulario fijo.
**What students walk away knowing:**
- Cliente y servidor son roles, no programas; cualquier cosa puede ser cliente (incluso otro servidor).
- Anatomía de un par request/response: método + URL + headers + body opcional / status + headers + body.
- Los cinco methods que cubren el 95% del trabajo (GET/POST/PUT/PATCH/DELETE) y las tres familias de códigos (2xx/4xx/5xx) — si arranca con 4 mirá tu pedido, si arranca con 5 mirá los logs.
**Animations / interactive:** None new. `flow-with-arrows` para el ida-y-vuelta request/response. Posible diagrama estático con request crudo y response cruda lado a lado.
**Slide budget:** 8–10

## Section 3: REST como estilo
**Source material:** `source_material/03-rest-como-estilo.md`
**Through-line:** HTTP es el cómo (transporte); REST es el qué (convención sobre qué se transporta y cómo se nombra). Todo es recurso, las URLs nombran recursos no acciones, y cuatro reglas (plural, jerarquía, idempotencia, stateless) ordenan cómo se piden las cosas.
**Hook:** Querés borrar la tarea 17 del proyecto 4. ¿`DELETE /projects/4/tasks/17` o `POST /borrar-tarea?id=17&proyecto=4`? Las dos son HTTP válido. Una sola es REST.
**What students walk away knowing:**
- REST es una convención (no la única — GraphQL y RPC existen), y la mayoría de las APIs que vas a dictar la usan.
- Las URLs nombran recursos en plural; el method dice qué hacer, la URL dice sobre qué; anidás cuando la pertenencia es estructural.
- Idempotencia (GET/DELETE/PUT sí, POST no) y stateless: por qué cada propiedad cambia cómo reintentás y cómo escala el servidor.
**Animations / interactive:** None new. `comparison-2col` para `DELETE /projects/4/tasks/17` vs `POST /borrar-tarea?...`. Para idempotencia, slide con dos llamadas seguidas y el estado final visible.
**Slide budget:** 7–9

## Section 4: Paths, controllers y el contrato (climax)
**Source material:** `source_material/04-rutas-controladores-y-contratos.md`
**Through-line:** Un endpoint son cinco piezas — method + path + schema-in + schema-out + códigos posibles. Ese bloque entero es la unidad mínima dictable a una IA: el átomo del backend, igual que en semana 02 lo era un componente con sus props y su estado. El controller atiende; vos no lo escribís, lo especificás.
**Hook:** Le decís a la IA "hacé un endpoint para crear una tarea" y te devuelve trescientas líneas que parecen razonables. Mirás de cerca y descubrís que inventó diez decisiones que vos no dictaste — cada una es una bifurcación que aparece cuando algo se rompe.
**Key analogy:** Endpoint con contrato ↔ componente con props/state (semana 02). El "átomo dictable" del frontend tiene su gemelo del lado del servidor: una pieza chica, con bordes claros, que pegás a la IA sin tener que aclarar nada más.
**What students walk away knowing:**
- Las cinco piezas de un endpoint y por qué cada una corresponde a una decisión que alguien tiene que tomar (o la IA la toma sin avisarte).
- Mantra path/query/body: path identifica, query modifica, body transporta el contenido nuevo.
- Cómo se ve un bloque de contrato concreto y por qué es la unidad mínima dictable que cierra el espejo con semana 02.
**Animations / interactive:** None new. `code-walkthrough` con highlights por línea sobre el bloque de contrato (method, path, entrada, salida, errores, cada uno se ilumina). Posible variante con `clickable-steps` reutilizando el componente de semana 01.
**Slide budget:** 8–10

## Section 5: Datos: relaciones vs documentos
**Source material:** `source_material/05-datos-relaciones-vs-documentos.md`
**Through-line:** Los datos son la memoria del servidor. Dos formas de modelarlos: relational (tablas con schema y foreign keys, integridad de fábrica) y document (objetos JSON anidados en colecciones, schema flexible). La heurística por default: relational, y SQLite como entry point práctico.
**Hook:** Acordate de la to-do list de la clase pasada: vivía en una variable de JavaScript en el navegador. Refrescabas la página y todo desaparecía. Eso no era un bug — la página nunca tuvo memoria. El servidor es lo que le da memoria a la aplicación.
**What students walk away knowing:**
- Tres razones por las que hace falta una base de datos: persistencia, eficiencia de consulta, integridad.
- Relational (tablas + columnas + foreign keys + JOIN) vs. document (documentos JSON anidados en colecciones), con el mismo dominio (proyectos/tareas) modelado de las dos formas.
- La heurística pragmática: arrancar relational con SQLite; ir a document solo cuando un patrón concreto lo justifica.
**Animations / interactive:** None new. `comparison-2col` SQL ↔ NoSQL con el ejemplo proyectos/tareas modelado en ambas formas. `code-walkthrough` con el schema relational para resaltar la foreign key.
**Slide budget:** 7–9

## Section 6: Errores y observabilidad
**Source material:** `source_material/06-errores-y-observabilidad.md`
**Through-line:** Los errores tienen dueño: 4xx es del cliente, 5xx es del servidor — leer el primer dígito antes de tocar nada. Logs y stack traces son la evidencia que le pasás a la IA para que razone en vez de adivinar; sin esa evidencia, el modelo improvisa.
**Hook:** La IA generó un endpoint, lo pegaste al navegador y la respuesta es "500 Internal Server Error". Frente a esa pantalla hay dos reacciones: copiar "no funciona" en el chat y rezar, o abrir la terminal y mandarle a la IA la línea exacta. Las dos tardan parecido. Solo una funciona.
**What students walk away knowing:**
- 4xx = cliente, 5xx = servidor; el movimiento diagnóstico empieza por leer el primer dígito.
- Anatomía de un log típico (request entrante, error interno, status code de salida) y cómo leer un stack trace de adentro hacia afuera buscando la primera línea que menciona código tuyo.
- El prompt bueno trae código + log + stack trace; el malo dice "no funciona". Misma diferencia, otra capa: sin evidencia el modelo adivina, con evidencia razona.
**Animations / interactive:** None new. `code-walkthrough` con highlights sobre log de 3 líneas y stack trace; posible `comparison-2col` para "prompt malo" vs "prompt con evidencia".
**Slide budget:** 6–7

## Section 7: OpenAPI — el contrato escrito
**Source material:** `source_material/07-openapi-el-contrato-escrito.md`
**Through-line:** Lo que vinimos llamando "contrato" tiene un nombre y un archivo: OpenAPI. Las cinco piezas del bloque del §4 (method, path, schema-in, schema-out, errores) se mapean directamente a paths/HTTP-method-keys/requestBody/responses. La industria le puso formato a la idea, y eso desbloquea tres cosas concretas: menos ambigüedad para la IA, codegen automático (docs, clientes, mocks, tests) y durabilidad como artefacto en el repo.
**Key analogy:** El contrato hecho archivo. La derecha del §4 ↔ la izquierda del §7: misma información, dos formatos.
**What students walk away knowing:**
- OpenAPI 3.1 es el formato estándar para describir una API REST: las cinco piezas del §4 con keys oficiales (`paths`, `requestBody`, `responses`).
- Tres razones por las que es un archivo y no un prompt: ambigüedad fija, codegen real (Swagger UI, clientes), durabilidad versionada.
- El supervisor arquitectónico ahora dirige un artefacto durable, no un prompt que se pierde.
**Animations / interactive:** None new. `comparison-2col` con step-through highlights mapeando línea-a-línea bloque del §4 ↔ openapi.yaml.
**Slide budget:** 3 slides (apertura → side-by-side → por qué importa).

## Section 8: Demo en vivo — el modelo escribe el openapi.yaml
**Source material:** `source_material/08-demo-en-vivo.md`
**Through-line:** Coreografía del cierre. El profesor pega un prompt que dicta el contrato del §4 (projects/tasks) en un chat de IA cualquiera y recibe el openapi.yaml. Lee el yaml endpoint por endpoint mapeando al §4, pide un endpoint más desde el chat, y al final pega el yaml en editor.swagger.io para mostrar el render con docs interactivas, un "Try it" funcional y una edición a mano en vivo. El demo no depende de ninguna función de editor colaborativo. La cadena: contrato → archivo → docs renderizadas, en vivo.
**What students walk away knowing:**
- El demo no agrega un concepto nuevo; cierra el espejo y deja al alumno con ganas de tomar el yaml y darlo a una IA local en semana 4.
- (Apropiación visceral) Lo que ustedes dictaron fue el contrato; ahora tiene archivo. El rol — supervisor arquitectónico — sobrevivió la mudanza al servidor.
**Animations / interactive:** None new. Reusa `clickable-steps` (mismo componente del §8 anterior) para los 6 beats. `section-divider` para apertura.
**Slide budget:** 5 slides (divider → 5 requisitos + counter → plantilla del prompt → 6 beats → cierre + bridge a semana 4).
