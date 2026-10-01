---
name: "breathecode-progress"
description: "Resume el progreso de BreatheCode contando tareas por estado y tipo y calculando el porcentaje completado."
---

# BreatheCode: resumen de progreso

Usá esta skill cuando Nadine quiera un resumen de su progreso general en el curso.

## Procedimiento

1. Usá exclusivamente la variable de entorno `FOURGEEKS_TOKEN`. Nunca leas archivos de secretos, muestres, registres, copies, guardes o incluyas el token en la salida.
2. Hacé únicamente peticiones de solo lectura a `GET https://breathecode.herokuapp.com/v1/assignment/user/me/task`, con `Authorization: Token ${FOURGEEKS_TOKEN}`. No filtres por estado ni consultes endpoints adicionales. No sigas redirecciones a otro host.
3. Usá `limit` y `offset` para recuperar todas las tareas, respetando el esquema de paginación que devuelva la API. Si no indica páginas siguientes, incrementá el offset por el límite solicitado y detenete cuando una página traiga menos elementos que el límite. Evitá repetir offsets y limitá la paginación ante respuestas inconsistentes.
4. Capturá respuesta y código HTTP en memoria. No imprimas cuerpo crudo, encabezados, token ni detalles de transporte; no uses modo verbose ni trazas que puedan revelar credenciales. Deduplicá por identificador de tarea para evitar contar dos veces elementos repetidos entre páginas. Si no hay identificador, deduplicá por los campos visibles del registro.
5. Contá cuántas tareas hay de cada estado: `PENDING`, `DONE`, `APPROVED` y `REJECTED`; y de cada tipo: `PROJECT`, `EXERCISE`, `LESSON` y `QUIZ`. Si aparecen valores distintos, contalos aparte como «Otros» sin asignarlos a una categoría conocida.
6. Calculá el porcentaje de avance como `(cantidad DONE + cantidad APPROVED) / total de tareas * 100`. Redondeá a un decimal. Si el total es cero, informá 0 % y que todavía no hay tareas.
7. Mostrá un resumen corto, claro y en español. Traducí estados: `PENDING` «Pendientes», `DONE` «Entregadas», `APPROVED` «Aprobadas», `REJECTED` «Requieren correcciones». Traducí tipos: `PROJECT` «Proyectos», `EXERCISE` «Ejercicios», `LESSON` «Lecciones», `QUIZ` «Cuestionarios». Incluí total, desglose por estado, desglose por tipo y porcentaje de avance.
8. Si la respuesta es HTTP 401/403, informá que el token no es válido o no tiene acceso. Para fallos de red, respuestas inesperadas o errores del servidor, indicá que no se pudo consultar; no afirmes que el token es inválido.
9. No hagas operaciones de escritura ni consultes datos adicionales fuera de este endpoint.
