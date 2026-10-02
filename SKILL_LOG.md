# SKILL_LOG - Mi Asistente 4Geeks

Mi agente es Clawdio (OpenClaw), conectado por Telegram. Cada skill la construí conversando con él: yo le describí lo que quería, él propuso la skill (nombre, qué hace y endpoint), yo di el sí, aprobé el permiso de OpenClaw y la probó con mi cuenta real de 4Geeks.

El token de estudiante no aparece en ningún archivo de skill ni en este repositorio.

## Resumen

| # | Skill | Endpoint principal | Estado |
|---|-------|--------------------|--------|
| 1 | breathecode-whoami | GET /v1/admissions/user/me | Funciona |
| 2 | breathecode-projects | GET /v1/assignment/user/me/task (task_type=PROJECT) | Funciona |
| 3 | breathecode-pending-tasks | GET /v1/assignment/user/me/task (task_status) | Funciona |
| 4 | breathecode-progress | GET /v1/assignment/user/me/task (sin filtro) | Funciona |
| 5 | breathecode-exercises (extra) | GET /v1/registry/technology y GET /v1/registry/asset | Funciona |
| 6 | breathecode-events (extra) | GET /v1/events/all | Funciona |

## Configuración inicial: guardar el token de forma segura

- Copié mi token de la cookie `4g_tok` en learn.4geeks.com.
- Lo guardé en `~/.openclaw/.env` (un archivo fuera de la carpeta del repositorio) con el nombre `FOURGEEKS_TOKEN`. Para guardarlo usé un comando que pide el valor oculto (`read -s`), así no queda en pantalla ni en el historial, y le puse permisos solo para mi usuario (`chmod 600`).
- Agregué la carpeta `memory/` al `.gitignore`, para que las notas de las conversaciones con el agente no se suban a GitHub.
- Todas las skills usan únicamente la variable `FOURGEEKS_TOKEN`, y sus instrucciones dicen que nunca se debe mostrar, registrar ni escribir el token.
- Un detalle: la primera vez copié mal el token (tenía 74 caracteres y la API respondía 401). Lo volví a copiar y comprobé con una consulta directa que la API respondía 200.

## Conversación de descubrimiento

**Prompt que usé para iniciar:**

> Quiero darte la habilidad de conectarte a mi cuenta de 4Geeks usando mi token de estudiante, sin que tenga que desarrollar código de mi parte. ¿Qué debemos hacer? Mi token ya está guardado en la variable FOURGEEKS_TOKEN del archivo ~/.openclaw/.env. Nunca lo muestres ni lo escribas en ningún archivo, y no crees nada hasta que yo te diga que sí.

**Qué sugirió el agente:**
- Un plan en cuatro pasos: verificar si 4Geeks ofrece una API oficial, comprobar cómo usar `FOURGEEKS_TOKEN` de forma segura, preparar la integración como una skill y probarla con cuidado.
- Aclaró que no iba a leer el archivo `.env` ni a pedirme el token, y que no iba a crear nada sin mi sí.
- No pudo buscar la documentación en internet, así que me pidió que le contara qué quería consultar. Le pasé la URL base de la API y el endpoint que necesitaba, tomados de la referencia de la API de estudiantes del curso.

**Qué información me pidió:** qué quería poder hacer con mi cuenta (tareas, progreso, fechas). A partir de ahí fuimos creando una skill por vez.

## Skill 1: breathecode-whoami (Autenticar)

**Prompt:**

> Gracias. Empecemos por la primera skill: verificar que mi token es válido. La API de estudiantes de 4Geeks (BreatheCode) está en https://breathecode.herokuapp.com. El endpoint para saber quién soy es GET /v1/admissions/user/me, con la cabecera "Authorization: Token <mi token>". El token está en la variable de entorno FOURGEEKS_TOKEN. Usala desde el comando, por ejemplo con curl, sin imprimirla ni escribirla en ningún archivo. Solo lectura. Antes de crear nada, mostrame qué skill proponés (nombre, qué hace y qué endpoint usa) y esperá mi sí. Más adelante vamos a sumar otras skills, pero de a una.

**Qué hace:** comprueba si el token es válido y muestra solo "Token válido" y mi nombre. Distingue un token rechazado (401/403) de un fallo de red.
**Endpoint:** `GET /v1/admissions/user/me`

**Prueba:** el agente respondió "Token válido" y mostró mi nombre completo, sin mostrar el token.
Antes de este resultado, la skill respondió que el token era inválido; la causa era la copia errónea del token (ver configuración inicial). Después de volver a guardarlo y reiniciar el servicio del agente, funcionó.

## Skill 2: breathecode-projects (Obtener mis proyectos)

**Prompt:**

> Ahora la segunda skill: ver mis proyectos y su estado. El endpoint de la API es GET /v1/assignment/user/me/task con el parámetro task_type=PROJECT (se puede paginar con limit y offset). Los estados posibles son PENDING (pendiente), DONE (entregado), APPROVED (aprobado) y REJECTED (rechazado, requiere correcciones). Usá FOURGEEKS_TOKEN igual que en breathecode-whoami, solo lectura, sin mostrar el token. Que devuelva, por cada proyecto, el título y el estado. Antes de crear nada, mostrame qué skill proponés (nombre, qué hace y endpoint) y esperá mi sí.

**Qué hace:** lista mis proyectos con su título y su estado en español (Pendiente, Entregado, Aprobado o Requiere correcciones), recorriendo todas las páginas.
**Endpoint:** `GET /v1/assignment/user/me/task` con `task_type=PROJECT`, `limit` y `offset`.

**Prueba:** el agente encontró 28 proyectos: 9 pendientes y 19 entregados (estados `PENDING` y `DONE`), sin proyectos aprobados ni rechazados.

## Skill 3: breathecode-pending-tasks (Obtener trabajo pendiente)

**Prompt inicial:**

> Ahora la tercera skill: ver qué me falta completar. Usa el mismo endpoint, GET /v1/assignment/user/me/task, pero con el filtro task_status, tanto para PENDING (pendiente) como para REJECTED (rechazado, requiere correcciones), y para todos los tipos de tarea (PROJECT, EXERCISE, LESSON y QUIZ), no solo proyectos. Se puede paginar con limit y offset. Usá FOURGEEKS_TOKEN igual que en las otras skills, solo lectura, sin mostrar el token. Que devuelva, por cada tarea pendiente, el tipo, el título y el estado en español, y al final cuántas hay en total. Antes de crear nada, mostrame qué skill proponés (nombre, qué hace y endpoint) y esperá mi sí.

**Qué hace (versión actual):** muestra qué proyectos me faltan, en tres categorías: "Pendiente de entregar", "Requiere correcciones" y "Entregada, esperando revisión". Indica el cohorte de cada uno, cuenta cada tarea una sola vez y no cuenta como pendiente lo que ya está entregado o aprobado en algún cohorte. Si le pido lecciones o ejercicios, también los incluye.
**Endpoint:** `GET /v1/assignment/user/me/task` con `task_type=PROJECT` por defecto, `limit` y `offset`.

**Iteraciones (la skill dio resultados confusos y la corregí con el agente):**

1. **Versión 1:** consultaba todos los tipos de tarea con el estado de entrega (`task_status`). Prueba: 38 tareas pendientes y ninguna rechazada. Primer cambio: el agente dejó la skill como propuesta sin aplicar, y le pedí que la aplicara.
2. **El problema:** en una prueba por Telegram ("¿Qué me falta por entregar en el curso?") el agente listó como pendientes proyectos que yo ya tenía aprobados. Para investigarlo hice consultas directas a la API desde la terminal, sin mostrar el token, y descubrí dos cosas:
   - Cada tarea tiene dos estados: `task_status` (la entrega: `PENDING` o `DONE`) y `revision_status` (la revisión del instructor: `PENDING`, `APPROVED` o `REJECTED`). La skill solo miraba el primero, por lo que un proyecto rechazado, *Milestone 2*, aparecía como "entregado".
   - La misma tarea aparece en varios cohortes. Por ejemplo, *Command Line Challenge* está aprobado en el cohorte "Command line - Git & Github" y pendiente en `latam-aie-pt-5`.
3. **Versión 2:** le pedí que usara los dos estados y mostrara el cohorte de cada tarea. Resultado: 36 pendientes de entregar, 1 que requiere correcciones y 21 entregadas esperando revisión, agrupadas por cohorte. Seguía mostrando como pendientes tareas que ya había entregado en otro cohorte.
4. **Versión 3:** le pedí que agrupara las apariciones por `associated_slug` (el identificador de cada tarea) y que no contara como pendiente una tarea entregada o aprobada en cualquier cohorte. Resultado: 28 pendientes de entregar, 1 que requiere correcciones y 21 entregadas esperando revisión, 86 aprobadas y 136 tareas únicas. Seguía sin ser útil: casi todas eran lecciones y ejercicios, que no me importan para saber qué me falta.
5. **Versión 4 (actual):** le pedí que se enfocara en proyectos por defecto. Prompt:

   > Ahora quiero que breathecode-pending-tasks se enfoque en proyectos. Por defecto, consultá solo task_type=PROJECT, y no incluyas lecciones, ejercicios ni cuestionarios. Mantené todo lo demás igual: agrupar por associated_slug, no contar como pendiente lo que esté entregado o aprobado en algún cohorte, y mostrar aparte "Pendiente de entregar", "Requiere correcciones" y "Entregada, esperando revisión", con el nombre del cohorte. Si más adelante pido lecciones o ejercicios, ahí sí incluilos.

**Prueba (versión actual):** el agente mostró 4 proyectos pendientes de entregar (*Todo List CLI with Python*, *My 4Geeks Assistant*, *Building context from an existing project - Financial dashboard* y *Milestone 3 — Talent Pipeline Tracker*), 1 que requiere correcciones (*Milestone 2 — Building Scripts to Automate Tasks*) y 1 entregado esperando revisión (*My Agent, My Way*). Coincide con los datos que revisé directo en la API.

## Skill 4: breathecode-progress (Obtener resumen de progreso)

**Prompt:**

> Ahora la cuarta skill: un resumen de mi progreso en el curso. Usá el mismo endpoint, GET /v1/assignment/user/me/task, sin filtrar por estado, para traer todas mis tareas (paginando con limit y offset). Que cuente cuántas hay de cada estado (PENDING, DONE, APPROVED y REJECTED) y de cada tipo (PROJECT, EXERCISE, LESSON y QUIZ), y que calcule el porcentaje de avance: las tareas entregadas o aprobadas sobre el total. Que me muestre el resumen en español, corto y fácil de leer. Usá FOURGEEKS_TOKEN igual que en las otras skills, solo lectura, sin mostrar el token. Antes de crear nada, mostrame qué skill proponés (nombre, qué hace y endpoint) y esperá mi sí.

**Qué hace:** cuenta todas mis tareas por estado y por tipo, y calcula el porcentaje de avance (entregadas o aprobadas sobre el total).
**Endpoint:** `GET /v1/assignment/user/me/task` sin filtro de estado, con `limit` y `offset`.

**Prueba:** el agente respondió: 169 tareas en total (38 pendientes, 131 entregadas, 0 aprobadas y 0 que requieren correcciones); por tipo, 28 proyectos, 114 ejercicios, 27 lecciones y 0 cuestionarios; avance de 77,5 % (131 de 169).
Verifiqué que los números coinciden con las skills anteriores: los 28 proyectos y las 38 pendientes son los mismos, y 38 + 131 = 169.

## Skill 5 (extra): breathecode-exercises (Ejercicios para practicar por tecnología)

**Necesidad que identifiqué:** estoy estudiando React y TypeScript, y quiero que mi agente me sugiera ejercicios para practicar justo esas tecnologías.

**Prompt:**

> Ahora la sexta skill: buscar ejercicios para practicar según una tecnología. El endpoint es GET /v1/registry/asset, con asset_type=EXERCISE, technologies=<tecnología>, difficulty (opcional: BEGINNER, EASY, INTERMEDIATE o HARD) y limit (por ejemplo 10). Yo le voy a decir la tecnología, por ejemplo react, typescript o javascript; si no la digo, que me pregunte. Que devuelva, por cada ejercicio, el título, la dificultad y su slug. Usá FOURGEEKS_TOKEN igual que en las otras skills, solo lectura, sin mostrar el token. Antes de crear nada, mostrame qué skill proponés (nombre, qué hace y endpoint) y esperá mi sí.

**Qué hace:** busca ejercicios de una tecnología que yo le diga y muestra título, dificultad (en español) y slug. Si no le digo la tecnología, me la pregunta.
**Endpoints:** `GET /v1/registry/technology?like=<tecnología>&limit=5` (para encontrar el nombre exacto de la tecnología) y `GET /v1/registry/asset` con `asset_type=EXERCISE`, `technologies=<slug>` y `limit=10`. Son dos consultas de lectura al mismo catálogo de contenido de 4Geeks.

**Iteración (la skill falló y la corregí con el agente):**
1. Primera versión: al probarla con "react", respondió que no encontró ejercicios.
2. Investigué con consultas directas a la API: con `technologies=react` la respuesta tenía `count: 0`, y al buscar las tecnologías disponibles vi que la etiqueta de React en 4Geeks se llama `reactjs`.
3. Le pedí al agente que actualizara la skill para resolver primero el nombre exacto de la tecnología, y que me preguntara cuál elegir si había varias coincidencias.
4. Segunda versión: al probarla con "react" encontró varias tecnologías, usó `reactjs` y devolvió dos ejercicios: "Curso de React.js desde cero y ejercicios interactivos" (Fácil, `curso-react-desde-cero`) y "Learn React.js Tutorial and Interactive Exercises" (Fácil, `react-js-tutorial-exercises`).

## Skill 6 (extra): breathecode-events (Próximos eventos)

**Necesidad que identifiqué:** quiero saber qué eventos de la academia vienen pronto, sin tener que entrar a la plataforma.

**Prompt:**

> Ahora la sexta skill: ver los próximos eventos de la academia. El endpoint es GET /v1/events/all con upcoming=true y limit=10 (opcionalmente academy, el id de la academia). Ya probé desde mi terminal que este endpoint responde bien con mi token. Que devuelva, por cada evento, el título y la fecha y hora de inicio, usando los campos que traiga la respuesta, sin inventar ninguno. Si no hay eventos próximos, que lo diga claramente. Usá FOURGEEKS_TOKEN igual que en las otras skills, solo lectura, sin mostrar el token. Antes de crear nada, mostrame qué skill proponés (nombre, qué hace y endpoint) y esperá mi sí.

**Qué hace:** muestra hasta 10 próximos eventos con título y fecha y hora de inicio. Si no hay, lo dice claramente. El filtro por academia es opcional.
**Endpoint:** `GET /v1/events/all` con `upcoming=true` y `limit=10`.

**Prueba:** el agente encontró 1 evento próximo: "El mapa real del AI Engineer", con inicio `2026-10-07T17:00:00Z` (las 11:00 en Costa Rica).

## Skills que probé y descarté

Mi primera idea para la skill 5 era ver mis certificados, y para la skill 6 ver mi cohorte actual. Las dos fallaron con el mismo problema, y las quité del repositorio:

- `breathecode-certificates` usaba `GET /v1/certificate/` y la API respondía 403.
- `breathecode-cohort` usaba `GET /v1/admissions/academy/cohort/me` y la API respondía 403.

Para entender el motivo probé varios endpoints con una consulta directa desde la terminal, usando el mismo token (sin mostrarlo):

| Endpoint | Respuesta |
|----------|-----------|
| /v1/certificate/ | 403 |
| /v1/admissions/academy/cohort/me | 403 |
| /v1/activity/me | 403 |
| /v1/events/all?upcoming=true | 200 |
| /v1/registry/asset/me | 200 |

El token es válido (las otras consultas dan 200), así que el 403 significa que esos endpoints no están disponibles con un token de estudiante, aunque aparezcan en la referencia del curso. En su lugar construí las skills de ejercicios y de eventos, que sí funcionan.

## Correcciones posteriores: skills 2 y 4

Al revisar las respuestas del agente con mis datos reales, encontré que las skills de proyectos (2) y de progreso (4) mostraban información incompleta, por el mismo problema que había tenido la skill 3. Las corregí con el agente, de a una.

**El problema:** las dos solo miraban el estado de entrega (`task_status`) y no el de revisión del instructor (`revision_status`). Resultado: decían "0 aprobados" aunque tengo 17 proyectos aprobados, y un proyecto rechazado (*Milestone 2*) aparecía como "entregado". Además, la misma tarea aparece en varios cohortes, y se identifica por `associated_slug`.

### Skill 2: breathecode-projects (corrección)

**Prompt:**

> Encontré un problema de exactitud en breathecode-projects: solo mira task_status (la entrega) y no revision_status (la revisión del instructor), por eso no muestra los proyectos aprobados ni los rechazados. Además, la misma tarea aparece en varios cohortes y se identifica por associated_slug. Actualizá la skill así: (1) agrupá las apariciones por associated_slug, para contar cada proyecto una sola vez; (2) si el proyecto está aprobado (revision_status APPROVED) en algún cohorte, mostralo como "Aprobado", aunque figure pendiente en otro; (3) si no, si está entregado (task_status DONE) con revision_status REJECTED, mostralo como "Requiere correcciones"; (4) si está entregado con revision_status PENDING, mostralo como "Entregado, esperando revisión"; (5) si no está entregado en ningún cohorte, mostralo como "Pendiente de entregar"; (6) en cada proyecto mostrá el título y el estado, y el cohorte donde tiene ese estado; (7) al final dá los totales por estado, sobre proyectos únicos. Solo lectura, sin mostrar el token, un solo endpoint.

**Qué hace ahora:** lista cada proyecto una sola vez, con un único estado (Aprobado, Requiere correcciones, Entregado esperando revisión o Pendiente de entregar), el cohorte y los totales por estado.

**Prueba:** 29 apariciones agrupadas en 23 proyectos únicos. Totales: 17 aprobados, 1 requiere correcciones, 1 entregado esperando revisión y 4 pendientes de entregar. Verifiqué que coincide con las consultas directas que hice a la API desde la terminal.

### Skill 4: breathecode-progress (corrección)

**Prompt:**

> Encontré el mismo problema de exactitud en breathecode-progress: solo mira task_status (la entrega) y no revision_status (la revisión del instructor), por eso muestra 0 aprobadas. Además, la misma tarea aparece en varios cohortes y se identifica por associated_slug. Actualizá la skill así: (1) agrupá las apariciones por associated_slug, para contar cada tarea una sola vez; (2) si la tarea está aprobada (revision_status APPROVED) en algún cohorte, contala como "Aprobada"; (3) si no, si está entregada (task_status DONE) con revision_status REJECTED, "Requiere correcciones"; (4) si está entregada con revision_status PENDING, "Entregada, esperando revisión"; (5) si no está entregada en ningún cohorte, "Pendiente de entregar"; (6) mostrá los totales por estado y por tipo (PROJECT, EXERCISE, LESSON, QUIZ), sobre tareas únicas; (7) calculá el porcentaje de avance como las tareas aprobadas o entregadas sobre el total de tareas únicas, y mostralo también solo para proyectos. Resumen corto y en español. Solo lectura, sin mostrar el token, un solo endpoint.

**Qué hace ahora:** cuenta cada tarea una sola vez, con un único estado, y calcula el avance general y el avance de proyectos.

**Prueba:** 173 apariciones agrupadas en 136 tareas únicas.
- Por estado: 86 aprobadas, 1 requiere correcciones, 21 entregadas esperando revisión y 28 pendientes de entregar.
- Por tipo: 23 proyectos, 86 ejercicios, 27 lecciones y 0 cuestionarios.
- Avance general: 79,4 % (108 de 136 aprobadas o entregadas).
- Avance de proyectos: 82,6 % (19 de 23 aprobados o entregados), que coincide con lo que calculé a mano a partir de la skill de proyectos.

**Qué aprendí:** probar una skill con mis datos reales fue lo que hizo aparecer los problemas. Los números que salen de distintas skills tienen que coincidir entre sí, y conviene compararlos con la plataforma.

## Qué aprendí

- Cada skill tiene una sola responsabilidad y consulta un endpoint (o dos muy relacionados), y se puede combinar en la conversación.
- Un 401 y un 403 significan cosas distintas: 401, el token no sirve; 403, el token sirve pero no tiene permiso para ese endpoint.
- Antes de culpar al token conviene probar la API directamente, sin el agente, para saber dónde está el problema.
- Los nombres de las etiquetas (como `reactjs`) hay que consultarlos, no suponerlos.
- Las skills siguen un mismo esquema: qué necesitan (el token y los parámetros), qué hacen (los pasos), qué devuelven, cómo manejan los errores (401/403, red, lista vacía) y cómo las probé.
- Cada tarea de 4Geeks tiene dos estados distintos: `task_status` (si la entregué) y `revision_status` (qué dijo el instructor). Para saber si algo está aprobado hay que mirar el segundo, no el primero.
- La misma tarea puede aparecer en varios cohortes con estados diferentes, y se identifica por `associated_slug`. Para contarla una sola vez hay que agrupar por ese campo.