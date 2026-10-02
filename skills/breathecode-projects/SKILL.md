---
name: "breathecode-projects"
description: "Consulta proyectos BreatheCode únicos por associated_slug y muestra estado, cohorte y totales."
---

# BreatheCode: proyectos

Usá esta skill cuando Nadine quiera consultar sus proyectos de BreatheCode y su estado.

## Procedimiento

1. Usá exclusivamente la variable de entorno `FOURGEEKS_TOKEN`. Nunca leas archivos de configuración de secretos, muestres, registres, copies, guardes o incluyas el token en la salida.
2. Consultá en modo de solo lectura únicamente `GET https://breathecode.herokuapp.com/v1/assignment/user/me/task` con `task_type=PROJECT`, `Authorization: Token ${FOURGEEKS_TOKEN}` y `limit`/`offset` para paginar. No sigas redirecciones a otro host.
3. Capturá respuesta y código HTTP en memoria. No imprimas el cuerpo crudo, encabezados, token ni detalles de transporte; no uses modo verbose ni trazas que puedan revelar credenciales.
4. Recorre todas las páginas disponibles, respetando el esquema de paginación que devuelva la API. Si no hay una indicación de páginas siguientes, detenete cuando una página traiga menos elementos que el límite solicitado. Evitá repetir offsets y limita la paginación ante respuestas inconsistentes.
5. De cada elemento, extraé solo título, `associated_slug`, `task_status`, `revision_status` y el cohorte que devuelve la respuesta. Agrupá todas las apariciones con el mismo `associated_slug`; cada grupo cuenta como un proyecto único. Si falta `associated_slug`, informá el proyecto por separado como identificador no disponible y no lo incluyas en los totales de proyectos únicos, ya que no se puede garantizar la deduplicación.
6. Determiná un único estado por proyecto, aplicando esta prioridad al conjunto de apariciones:
   - Si al menos una aparición tiene `revision_status=APPROVED`, estado «Aprobado».
   - Si no, si al menos una aparición tiene `task_status=DONE` y `revision_status=REJECTED`, estado «Requiere correcciones».
   - Si no, si al menos una aparición tiene `task_status=DONE` y `revision_status=PENDING`, estado «Entregado, esperando revisión».
   - Si no hay ninguna aparición con `task_status=DONE`, estado «Pendiente de entregar».
   - Si hay una combinación de estados no cubierta por las reglas anteriores, mostrá el estado como no reconocido sin inferir ni incluirlo en los totales de los cuatro estados conocidos.
7. En cada proyecto, mostrá el título, el estado y el cohorte donde aparece la evidencia que determina ese estado. Para «Aprobado», usá un cohorte con revisión aprobada; para «Requiere correcciones» y «Entregado, esperando revisión», usá un cohorte con la pareja de estados correspondiente; para «Pendiente de entregar», mostrá los cohortes revisados donde no aparece entregado. Si hay más de uno, podés listarlos todos. Si el cohorte no está disponible, indicá «cohorte no disponible». No atribuyas un estado a un cohorte cuya aparición no respalda ese estado.
8. Al final, informá los totales de cada uno de los cuatro estados conocidos, contando proyectos únicos agrupados por `associated_slug`. Aclarà cuántos registros no pudieron deduplicarse por falta de `associated_slug`, si los hubiera.
9. Si la respuesta es HTTP 401/403, informá que el token no es válido o no tiene acceso. Para fallos de red, respuestas inesperadas o errores del servidor, indicá que no se pudo consultar; no afirmes que el token es inválido. Si no hay proyectos, decilo claramente.
10. No hagas operaciones de escritura ni consultes endpoints adicionales.
