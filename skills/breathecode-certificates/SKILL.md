---
name: "breathecode-certificates"
description: "Consulta certificados de BreatheCode y muestra nombre o cohorte, estado y si no hay resultados."
---

# BreatheCode: certificados

Usá esta skill cuando Nadine quiera consultar sus certificados de BreatheCode, opcionalmente filtrados por cohorte.

## Procedimiento

1. Usá exclusivamente la variable de entorno `FOURGEEKS_TOKEN`. Nunca leas archivos de secretos, muestres, registres, copies, guardes o incluyas el token en la salida.
2. Hacé únicamente una petición de solo lectura a `GET https://breathecode.herokuapp.com/v1/certificate/`, enviando `Authorization: Token ${FOURGEEKS_TOKEN}`. Si Nadine proporciona un ID de cohorte, incluí el parámetro `cohort` con ese valor; si no, no filtres por cohorte. No sigas redirecciones a otro host.
3. Capturá respuesta y código HTTP en memoria. No imprimas cuerpo crudo, encabezados, token ni detalles de transporte; no uses modo verbose ni trazas que puedan revelar credenciales.
4. Para cada certificado, extraé únicamente su nombre y estado. Si no hay nombre de certificado, usá el nombre del cohorte cuando esté disponible; si ninguno está disponible, indicá «nombre no disponible». Presentá el estado tal como venga, traduciendo valores conocidos al español cuando sea claro y sin inferir estados desconocidos.
5. Si no hay certificados, decilo claramente. No consultes endpoints adicionales.
6. Si la respuesta es HTTP 401/403, informá que el token no es válido o no tiene acceso. Para fallos de red, respuestas inesperadas o errores del servidor, indicá que no se pudo consultar; no afirmes que el token es inválido.
7. No hagas operaciones de escritura ni consultes datos adicionales fuera de este endpoint.
