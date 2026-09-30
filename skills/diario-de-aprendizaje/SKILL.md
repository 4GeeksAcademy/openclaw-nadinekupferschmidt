---
name: diario-de-aprendizaje
description: Convierte los puntos que Nadine aprendió hoy en una entrada ordenada de su diario de aprendizaje en Google Docs. Usar cuando Nadine diga "diario", "aprendí hoy" o mande una lista de lo que estudió.
---

# Diario de aprendizaje

Convierto unos puntos sueltos sobre lo que Nadine aprendió hoy en una entrada ordenada de su diario en Google Docs.

## Qué necesito de Nadine

Entre 2 y 5 puntos de lo que aprendió, en texto libre. Si no me manda ningún punto, se los pido antes de seguir.

## Pasos

1. Armo la entrada con este formato:
   - La fecha de hoy (zona horaria de Costa Rica).
   - Un título corto que resuma el día.
   - Los puntos aprendidos, redactados con claridad y palabras simples.
   - Una línea final: "Próximo paso para practicar: ..." con una sugerencia concreta.
2. Le muestro la entrada completa a Nadine y espero su "sí". No creo nada antes de eso.
3. Consulto qué acciones de Google Docs tengo disponibles, desde la carpeta `/root/.openclaw/workspace`: `mcporter call "zapier.inspect_zapier_actions()"`
4. Si existe una acción para agregar texto a un documento ya creado, y existe el documento "Diario de aprendizaje", agrego la entrada ahí. Si no, creo un documento nuevo con la acción `newtxtdocument`, con el título "Diario de aprendizaje - AAAA-MM-DD" (la fecha de hoy).
5. Le confirmo a Nadine por Telegram que quedó hecho, con el enlace al documento.

## Reglas

- Nunca borro ni modifico entradas anteriores.
- No habilito apps ni acciones nuevas en Zapier.
- Si algo falla, le cuento a Nadine el error tal cual y no digo que lo hice.
- No invento contenido que Nadine no me dio: solo ordeno y redacto lo que aprendió.
- Siempre pregunto si algo no está claro antes de seguir.
