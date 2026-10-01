---
name: "breathecode-projects"
description: "Consulta tus proyectos de BreatheCode y muestra el título y su estado."
---

# BreatheCode: proyectos

Usá esta skill cuando Nadine quiera consultar sus proyectos de BreatheCode y su estado.

## Procedimiento

1. Usá exclusivamente la variable de entorno `FOURGEEKS_TOKEN`. Nunca leas archivos de configuración de secretos, muestres, registres, copies, guardes o incluyas el token en la salida.
2. Consultá en modo de solo lectura `GET https://breathecode.herokuapp.com/v1/assignment/user/me/task` con `task_type=PROJECT`, `Authorization: Token ${FOURGEEKS_TOKEN}` y `limit`/`offset` para paginar. No sigas redirecciones a otro host.
3. Capturá respuesta y código HTTP en memoria. No imprimas el cuerpo crudo, encabezados, token ni detalles de transporte; no uses modo verbose ni trazas que puedan revelar credenciales.
4. Recorre todas las páginas disponibles, respetando el esquema de paginación que devuelva la API. Si no hay una indicación de páginas siguientes, detenete cuando una página traiga menos elementos que el límite solicitado. Evitá repetir offsets y limita la paginación ante respuestas inconsistentes.
5. Para cada elemento, extraé únicamente el título y `task_status` (o el campo de estado equivalente documentado en la respuesta). Presentá los estados así: `PENDING` → «Pendiente», `DONE` → «Entregado», `APPROVED` → «Aprobado», `REJECTED` → «Requiere correcciones». Para valores desconocidos, mostrálos como estado no reconocido sin inferir.
6. Si la respuesta es HTTP 401/403, informá que el token no es válido o no tiene acceso. Para fallos de red, respuestas inesperadas o errores del servidor, indicá que no se pudo consultar; no afirmes que el token es inválido. Si no hay proyectos, decilo claramente.
7. No hagas operaciones de escritura ni consultes endpoints adicionales.
