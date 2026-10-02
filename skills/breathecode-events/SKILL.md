---
name: "breathecode-events"
description: "Consulta los próximos eventos de BreatheCode y muestra título y fecha/hora de inicio."
---

# BreatheCode: próximos eventos

Usá esta skill cuando Nadine quiera ver próximos eventos de la academia.

## Procedimiento

1. Usá exclusivamente la variable de entorno `FOURGEEKS_TOKEN`. Nunca leas archivos de secretos, muestres, registres, copies, guardes o incluyas el token en la salida.
2. Hacé una única petición de solo lectura a `GET https://breathecode.herokuapp.com/v1/events/all`, con los parámetros `upcoming=true` y `limit=10`, y el encabezado `Authorization: Token ${FOURGEEKS_TOKEN}`. Si Nadine proporciona explícitamente el ID de una academia o pide filtrar por ella, incluí también `academy=<id>`. No inventes ni deduzcas el ID. No sigas redirecciones a otro host.
3. Capturá la respuesta y el código HTTP en memoria, sin imprimir el cuerpo crudo, encabezados, token ni detalles de transporte. No uses modo verbose ni trazas que puedan revelar credenciales.
4. Procesá la respuesta como la lista de eventos devuelta por el endpoint (o el arreglo de resultados si la respuesta JSON usa un envoltorio claro). Para cada evento, mostrá únicamente el título y la fecha/hora de inicio, usando los valores y campos presentes en la respuesta. No inventes valores ni construyas fechas a partir de datos que no estén disponibles; si uno de esos datos falta, indicalo como «no disponible».
5. Si la lista está vacía, decí claramente que no hay eventos próximos.
6. Si la respuesta es HTTP 401/403, informá que el token no es válido o no tiene acceso. Para fallos de red, respuestas inesperadas o errores del servidor, indicá que no se pudo consultar; no afirmes que el token es inválido.
7. No hagas operaciones de escritura ni consultes endpoints adicionales.
