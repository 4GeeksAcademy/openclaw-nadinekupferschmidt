---
name: "breathecode-progress"
description: "Resume el progreso de BreatheCode con tareas únicas por associated_slug, estados de revisión, tipo y avance."
---

# BreatheCode: resumen de progreso

Usá esta skill cuando Nadine quiera un resumen de su progreso general en el curso.

## Procedimiento

1. Usá exclusivamente la variable de entorno `FOURGEEKS_TOKEN`. Nunca leas archivos de secretos, muestres, registres, copies, guardes o incluyas el token en la salida.
2. Hacé únicamente peticiones de solo lectura a `GET https://breathecode.herokuapp.com/v1/assignment/user/me/task`, con `Authorization: Token ${FOURGEEKS_TOKEN}`. No filtres por estado ni consultes endpoints adicionales. No sigas redirecciones a otro host.
3. Usá `limit` y `offset` para recuperar todas las tareas, respetando el esquema de paginación que devuelva la API. Si no indica páginas siguientes, detenete cuando una página traiga menos elementos que el límite solicitado. Evitá repetir offsets y limitá la paginación ante respuestas inconsistentes.
4. Capturá respuesta y código HTTP en memoria. No imprimas cuerpo crudo, encabezados, token ni detalles de transporte; no uses modo verbose ni trazas que puedan revelar credenciales. Agrupá todas las apariciones con el mismo `associated_slug`, que identifica una tarea entre cohortes; contá cada grupo una sola vez. No uses un identificador inventado para registros sin `associated_slug`; informá esos registros por separado y excluilos de los totales únicos.
5. Para cada tarea única, aplicá estas reglas al conjunto de sus apariciones:
   - Si alguna tiene `revision_status=APPROVED`, estado «Aprobada».
   - Si no, si alguna tiene `task_status=DONE` y `revision_status=REJECTED`, estado «Requiere correcciones».
   - Si no, si alguna tiene `task_status=DONE` y `revision_status=PENDING`, estado «Entregada, esperando revisión».
   - Si ninguna tiene `task_status=DONE`, estado «Pendiente de entregar».
   - Si hay apariciones entregadas pero ninguna cumple una pareja de revisión anterior, clasificá la revisión como «Estado no reconocido», sin inferir. No cuentes ese caso en los cuatro estados conocidos.
6. Contá tareas únicas por cada estado anterior y por tipo: `PROJECT`, `EXERCISE`, `LESSON` y `QUIZ`. Para una tarea con más de un tipo entre apariciones, usá el tipo disponible; si hay tipos distintos y no se puede resolver, contala como «Tipo no reconocido» y no la sumes en los cuatro tipos conocidos. Contá otros valores de estado/tipo aparte como no reconocidos, sin asignarlos a categorías conocidas.
7. Calculá el avance general como la cantidad de tareas únicas aprobadas o entregadas (`revision_status=APPROVED` en alguna aparición, o `task_status=DONE` en alguna aparición) dividida por el total de tareas únicas con `associated_slug`. Calculá también el avance solo para proyectos con la misma regla y usando como denominador las tareas únicas de tipo `PROJECT`. Redondeá a un decimal. Si el denominador es cero, informá 0 % y que no hay tareas en esa categoría.
8. Mostrá un resumen corto, claro y en español: total de tareas únicas, totales por estado, totales por tipo, porcentaje de avance general y porcentaje solo para proyectos. Incluí la cantidad de registros sin `associated_slug`, si los hubiera, excluidos del conteo único.
9. Si la respuesta es HTTP 401/403, informá que el token no es válido o no tiene acceso. Para fallos de red, respuestas inesperadas o errores del servidor, indicá que no se pudo consultar; no afirmes que el token es inválido.
10. No hagas operaciones de escritura ni consultes datos adicionales fuera de este endpoint.
