---
name: sesiones-de-estudio
description: Convierte una frase en lenguaje natural sobre una sesión de estudio en un evento de Google Calendar para Nadine. Usar cuando Nadine diga "agendame", "sesión de estudio" o describa cuándo quiere estudiar algo.
---

# Sesiones de estudio

Convierto una frase como "estudiar React el jueves por la tarde, 2 horas" en un evento de Google Calendar, con todos sus detalles.

## Qué necesito de Nadine

Una frase con el tema, el día, el momento aproximado y la duración. Si falta algún dato, por ejemplo la duración, se lo pregunto antes de seguir.

## Pasos

1. Interpreto la frase y la convierto en una fecha y una hora concretas. Uso la zona horaria de Costa Rica (UTC-6) y la fecha de hoy para saber a qué día se refiere ("el jueves", "mañana").
2. Si Nadine me dio un momento aproximado ("por la tarde"), propongo una hora de inicio concreta dentro de su horario habitual de estudio (está en `USER.md`). Si ese horario no está completo, propongo una hora razonable y se la confirmo.
3. Le muestro los datos del evento y espero su "sí". No creo nada antes de eso:
   - Título: "Estudio: <tema>".
   - Fecha, hora de inicio y hora de fin.
   - Un recordatorio 30 minutos antes (salvo que Nadine pida otro).
4. Consulto qué acciones de Google Calendar tengo disponibles, desde la carpeta `/root/.openclaw/workspace`: `mcporter call "zapier.inspect_zapier_actions()"`. Busco la acción para crear un evento y consulto sus parámetros exactos. Nunca adivino el nombre de una acción ni sus parámetros.
5. Si existe una acción para buscar eventos, reviso que el nuevo no se pise con otro. Si se pisa, se lo aviso a Nadine antes de crear nada.
6. Creo el evento con `execute_zapier_write_action`. Si la acción no permite agregar el recordatorio, se lo digo a Nadine.
7. Le confirmo por Telegram lo que quedó hecho: título, día, hora y duración, con el enlace al evento si lo tengo.

## Reglas

- Nunca borro ni modifico eventos que ya existen.
- No habilito apps ni acciones nuevas en Zapier.
- Si algo falla, le cuento a Nadine el error tal cual y no digo que lo hice.
- No invento datos que Nadine no me dio: si falta algo importante, pregunto.
- Siempre confirmo con Nadine antes de crear el evento.
