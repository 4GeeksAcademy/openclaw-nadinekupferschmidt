---
name: "breathecode-exercises"
description: "Busca ejercicios de práctica de BreatheCode por tecnología y muestra título, dificultad y slug."
---

# BreatheCode: buscar ejercicios por tecnología

Usá esta skill cuando Nadine quiera encontrar ejercicios para practicar una tecnología.

## Procedimiento

1. Si Nadine no indicó la tecnología, preguntásela y esperá la respuesta antes de llamar a la API. Si la indicó (por ejemplo, React, TypeScript o JavaScript), usá ese valor tal como lo expresó en el parámetro `technologies`.
2. Usá exclusivamente la variable de entorno `FOURGEEKS_TOKEN`. Nunca leas archivos de secretos, muestres, registres, copies, guardes o incluyas el token en la salida.
3. Hacé únicamente una petición de solo lectura a `GET https://breathecode.herokuapp.com/v1/registry/asset`, con `asset_type=EXERCISE`, `technologies=<tecnología>` y `limit=10`. No sigas redirecciones a otro host. Si Nadine especifica una dificultad, aceptá únicamente `BEGINNER`, `EASY`, `INTERMEDIATE` o `HARD` e incluí `difficulty` en la consulta; si el valor no es uno de esos, pedí que elija una opción válida.
4. Capturá respuesta y código HTTP en memoria. No imprimas cuerpo crudo, encabezados, token ni detalles de transporte; no uses modo verbose ni trazas que puedan revelar credenciales.
5. De cada ejercicio, extraé solo el título, la dificultad y el `slug`. Presentá los resultados en español cuando haya traducciones claras para dificultad: `BEGINNER` «Principiante», `EASY` «Fácil», `INTERMEDIATE` «Intermedio», `HARD` «Difícil». No inventes campos faltantes; marcá el dato como no disponible.
6. Si no hay ejercicios, decilo claramente. Si la respuesta es HTTP 401/403, informá que el token no es válido o no tiene acceso. Para fallos de red, respuestas inesperadas o errores del servidor, indicá que no se pudo consultar; no afirmes que el token es inválido.
7. No hagas operaciones de escritura ni consultes datos adicionales fuera de este endpoint.
