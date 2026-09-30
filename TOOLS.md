# TOOLS.md - Mis herramientas

Acá van los datos prácticos de mi entorno. Las reglas de lo que nunca debo hacer están en `AGENTS.md`.

## Herramientas conectadas

- **Telegram:** el canal donde hablo con Nadine. Le respondo y le confirmo todo por acá.
- **Google Docs:** por Zapier (servidor `zapier`, con `mcporter`).
- **Google Calendar:** por Zapier (servidor `zapier`, con `mcporter`).

Zapier puede ofrecer más apps en general (Gmail, Google Drive, Google Tasks, GitHub), pero hoy en mi servidor solo están habilitadas Google Calendar y Google Docs. Las demás no las uso ni las propongo hasta que Nadine me diga que están habilitadas. Antes de usar cualquier app, confirmo con `inspect_zapier_actions` que aparece habilitada. Si no aparece, se lo aviso a Nadine y no intento habilitarla yo.

## Mi cuenta de Google

- Google Docs y Google Calendar usan la cuenta de Nadine:
  - Nombre de la cuenta: Lorem Ipsum
  - Correo: loremipsum2690@gmail.com
- Es la cuenta de Nadine, aunque el nombre de la cuenta no diga "Nadine". No hace falta confirmarla cada vez.
- Uso la conexión por defecto de Zapier. No paso `connection_id` a mano, salvo que Nadine me pida usar otra cuenta.

## Cómo uso Zapier

Zapier funciona a través de la skill `mcporter`. Se usa en dos pasos: primero consulto qué puedo hacer y después ejecuto.

- Siempre corro `mcporter` desde la carpeta `/root/.openclaw/workspace`. Desde otra carpeta no encuentra la configuración.
- Pongo la llamada completa entre comillas. Sin comillas falla. Ejemplo: `mcporter call "zapier.inspect_zapier_actions()"`
- **Paso 1, consultar:** `inspect_zapier_actions` me muestra las apps habilitadas (Google Calendar y Google Docs) y el nombre exacto de cada acción.
- **Paso 2, ejecutar:** uso `execute_zapier_read_action` para leer o buscar, y `execute_zapier_write_action` para crear o cambiar algo.
- Nunca adivino el nombre de una acción ni sus parámetros. Antes de ejecutar, consulto con `inspect_zapier_actions` el nombre exacto y los parámetros de esa acción.
- Acción ya probada: `newtxtdocument` (crear un documento de Google Docs a partir de texto, con título y contenido).
- Si algo falla, le cuento a Nadine el error tal cual. No invento el resultado.

## Convenciones para Google Docs

- **Diario de aprendizaje:** el nombre del documento es "Diario de aprendizaje".
- Cada entrada lleva la fecha de hoy, un título corto, los puntos aprendidos redactados con claridad y una línea final de "próximo paso para practicar".
- Si no puedo agregar texto a un documento que ya existe, creo un documento nuevo por entrada, con la fecha en el título. Por ejemplo: "Diario de aprendizaje - 2026-10-01".
- Después de crear algo, le mando a Nadine el enlace por Telegram.

## Convenciones para Google Calendar

- Uso el calendario principal de Nadine.
- Zona horaria: Costa Rica (UTC-6).
- El título de cada evento es "Estudio: <tema>".
- Cada evento lleva fecha y hora concretas, duración correcta y un recordatorio.
- Si Nadine me dice un momento aproximado ("el jueves por la tarde"), propongo una hora concreta y se la confirmo antes de crear el evento.
- Si falta algún dato, como la duración, lo pregunto.

## Antes de crear o cambiar algo

1. Le muestro a Nadine exactamente lo que voy a hacer.
2. Espero su "sí".
3. Ejecuto la acción.
4. Le confirmo por Telegram lo que quedó hecho, con el enlace si corresponde.

## Seguridad

Nunca escribo claves, tokens ni direcciones con credenciales en este archivo ni en ningún otro del repositorio.