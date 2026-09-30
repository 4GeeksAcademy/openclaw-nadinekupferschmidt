# SKILLS_DESIGN

Diseño de las dos skills personalizadas de mi agente OpenClaw. Ambas usan solo las herramientas que ya tengo conectadas: Telegram y Zapier (Google Docs y Google Calendar).

## Skill 1: Diario de aprendizaje

**1. ¿Qué hace?**
Convierte unos puntos sueltos sobre lo que aprendí hoy en una entrada ordenada dentro de mi diario de estudio en Google Docs.

**2. ¿Qué input necesita?**
- Lo que yo le doy: un mensaje de Telegram con 2 a 5 puntos de lo que aprendí (texto libre, en español).
- Lo que ya sabe por los archivos de configuración: quién soy y que estoy aprendiendo a programar (`USER.md`), el tono que debe usar (`SOUL.md`), el nombre del Doc del diario y cómo usar Zapier (`TOOLS.md`) y que nunca debe borrar nada (`AGENTS.md`).

**3. ¿Cómo es un buen output?**
- Una entrada con la fecha de hoy, un título corto, los puntos aprendidos redactados con claridad y una línea final de "próximo paso para practicar".
- Destino: Google Docs, en mi diario de aprendizaje (si Zapier no permite agregar texto a un Doc existente, se crea un Doc nuevo por entrada con la fecha en el título).
- Confirmación por Telegram con el enlace al Doc.
- Sé que funcionó cuando abro el Doc y la entrada está ahí, bien formateada y sin duplicados.

## Skill 2: Sesiones de estudio en el calendario

**1. ¿Qué hace?**
Convierte una frase en lenguaje natural ("estudiar React el jueves por la tarde, 2 horas") en un evento de Google Calendar con sus detalles completos.

**2. ¿Qué input necesita?**
- Lo que yo le doy: una frase por Telegram con el tema, el día, el momento aproximado y la duración.
- Lo que ya sabe por los archivos de configuración: mi zona horaria y mi horario habitual de estudio (`USER.md`), el calendario por defecto y el formato de los títulos, por ejemplo "Estudio: <tema>" (`TOOLS.md`), y que debe pedirme confirmación antes de crear el evento (`AGENTS.md`).

**3. ¿Cómo es un buen output?**
- Un evento con título "Estudio: <tema>", fecha y hora concretas, duración correcta y un recordatorio.
- Destino: Google Calendar.
- Antes de crearlo me muestra los datos y espera mi "sí"; después me confirma por Telegram.
- Sé que funcionó cuando el evento aparece en mi calendario en el día y la hora correctos.

## Reglas comunes
- No habilitar apps ni acciones nuevas en Zapier.
- No crear, modificar ni borrar nada sin mi confirmación.