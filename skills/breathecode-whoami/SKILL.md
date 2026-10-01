---
name: "breathecode-whoami"
description: "Comprueba el token de BreatheCode y muestra únicamente su validez y el nombre del usuario."
---

# BreatheCode: verificar identidad

Usá esta skill cuando Nadine pida comprobar su token o saber quién es en BreatheCode.

## Procedimiento

1. Usá exclusivamente la variable de entorno `FOURGEEKS_TOKEN`. Nunca leas el archivo `.env` directamente, muestres, registres, copies, guardes o incluyas el token en la salida.
2. Hacé una única petición de solo lectura a `GET https://breathecode.herokuapp.com/v1/admissions/user/me`, enviando `Authorization: Token ${FOURGEEKS_TOKEN}`. No sigas redirecciones a otro host.
3. Capturá la respuesta y el código HTTP en memoria, sin imprimir el cuerpo crudo ni el encabezado. Suprimí errores de transporte que pudieran revelar detalles; no uses modo verbose ni trazas de shell.
4. Si la respuesta es HTTP 2xx y es JSON válido, extraé solo el nombre de usuario disponible en la respuesta (por ejemplo, `first_name` y `last_name`, o `name`). No muestres otros campos. Si el nombre no está disponible, indicá «nombre no disponible».
5. Para HTTP 401/403, informá «Token no válido». Para un resultado 2xx, informá «Token válido» y el nombre. Para fallos de red, respuestas inesperadas o errores del servidor, indicá que no se pudo verificar; no afirmes que el token es inválido.

No hagas ninguna operación de escritura ni consultes endpoints adicionales.
