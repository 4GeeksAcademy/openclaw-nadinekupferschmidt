---
name: "breathecode-pending-tasks"
description: "Consulta tareas BreatheCode pendientes o rechazadas y muestra tipo, título, estado y total."
---

# BreatheCode: tareas pendientes y con correcciones

Usá esta skill cuando Nadine quiera ver qué tareas de BreatheCode tiene pendientes de completar o que fueron rechazadas y requieren correcciones.

## Procedimiento

1. Usá exclusivamente la variable de entorno `FOURGEEKS_TOKEN`. Nunca leas archivos de secretos, muestres, registres, copies, guardes o incluyas el token en la salida.
2. Hacé únicamente peticiones de solo lectura a `GET https://breathecode.herokuapp.com/v1/assignment/user/me/task`, con `Authorization: Token ${FOURGEEKS_TOKEN}`. No sigas redirecciones a otro host.
3. Consultá los cuatro tipos `PROJECT`, `EXERCISE`, `LESSON` y `QUIZ`, combinados con los dos estados `PENDING` y `REJECTED`, usando `task_type` y `task_status` como filtros. Recorre todas las combinaciones para que ningún tipo o estado quede afuera.
4. Usá `limit` y `offset` para paginar. Respetá el esquema de paginación que devuelva la API; si no indica páginas siguientes, avanzá el offset por el límite y detenete cuando una página tenga menos elementos que el límite. Evitá repetir offsets y limitá la paginación ante respuestas inconsistentes. No consultes endpoints adicionales.
5. Capturá respuestas y códigos HTTP en memoria. No imprimas cuerpos crudos, encabezados, token ni detalles de transporte; no uses modo verbose ni trazas que puedan revelar credenciales. Extraé solo tipo, título y estado de cada tarea. Si la API devuelve duplicados entre páginas o filtros, deduplicá por identificador de tarea; si no hay identificador disponible, evitá presentar repetida una misma tarea según los datos visibles.
6. Presentá el tipo de tarea (Proyecto, Ejercicio, Lección o Cuestionario), título y estado en español: `PENDING` → «Pendiente»; `REJECTED` → «Requiere correcciones». Para cada estado desconocido, mostralo como no reconocido sin inferir. Al final indicá el total de tareas únicas encontradas.
7. Si la respuesta es HTTP 401/403, informá que el token no es válido o no tiene acceso. Para fallos de red, respuestas inesperadas o errores del servidor, indicá que no se pudo consultar; no afirmes que el token es inválido. Si no hay resultados, decí claramente que no hay tareas pendientes ni rechazadas y que el total es 0.
8. No hagas operaciones de escritura ni consultes datos adicionales fuera de este endpoint.
