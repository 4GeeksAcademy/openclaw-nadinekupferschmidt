---
name: "breathecode-exercises"
description: "Busca ejercicios de BreatheCode por tecnología, resolviendo primero el slug exacto."
---

# BreatheCode: buscar ejercicios por tecnología

Usá esta skill cuando Nadine quiera encontrar ejercicios para practicar una tecnología.

## Procedimiento

1. Si Nadine no indicó la tecnología, preguntásela y esperá la respuesta antes de llamar a la API.
2. Usá exclusivamente la variable de entorno `FOURGEEKS_TOKEN`. Nunca leas archivos de secretos, muestres, registres, copies, guardes o incluyas el token en la salida.
3. Primero resolvé el slug de la tecnología con una petición de solo lectura a `GET https://breathecode.herokuapp.com/v1/registry/technology`, pasando `like=<tecnología tal como Nadine la expresó>` y `limit=5`. No sigas redirecciones a otro host.
4. Si no hay coincidencias, decilo claramente y no consultes ejercicios. Si hay una sola coincidencia, usá el slug exacto devuelto. Si hay varias coincidencias, mostrale a Nadine las opciones de tecnología (solo nombre y slug, si están disponibles) y preguntale cuál elegir; esperá su respuesta antes de consultar ejercicios. No adivines cuál quiso decir.
5. Una vez identificado el slug, hacé una petición de solo lectura a `GET https://breathecode.herokuapp.com/v1/registry/asset`, con `asset_type=EXERCISE`, `technologies=<slug exacto>` y `limit=10`. No sigas redirecciones a otro host. Si Nadine especifica una dificultad, aceptá únicamente `BEGINNER`, `EASY`, `INTERMEDIATE` o `HARD` e incluí `difficulty` en la consulta; si el valor no es uno de esos, pedí que elija una opción válida.
6. Capturá respuesta y código HTTP en memoria. No imprimas cuerpo crudo, encabezados, token ni detalles de transporte; no uses modo verbose ni trazas que puedan revelar credenciales.
7. De cada ejercicio, extraé solo el título, la dificultad y el `slug`. Presentá los resultados en español cuando haya traducciones claras para dificultad: `BEGINNER` «Principiante», `EASY` «Fácil», `INTERMEDIATE` «Intermedio», `HARD` «Difícil». No inventes campos faltantes; marcá el dato como no disponible.
8. Si no hay ejercicios, decilo claramente. Si la respuesta es HTTP 401/403, informá que el token no es válido o no tiene acceso. Para fallos de red, respuestas inesperadas o errores del servidor, indicá que no se pudo consultar; no afirmes que el token es inválido.
9. No hagas operaciones de escritura ni consultes datos adicionales fuera de los dos endpoints indicados.
