# SKILLS_DESIGN.md — Backlog de Skills para Luna

Cada skill responde tres preguntas. Las skills se añaden secuencialmente según las desarrollemos.

---

## Skill 1: Diario de aprendizaje diario

**¿Qué hace esta skill?**
Cuando Daniel comparte lo que aprendió hoy, formatea y añade una entrada estructurada a un Google Doc que funciona como diario personal de conocimiento.

**¿Qué input necesita el agente?**
- **Input directo de Daniel:** unos pocos puntos sueltos o frases sobre lo que aprendió hoy (ej: "Hoy aprendí que PostgreSQL tiene ventanas con `RANGE` y `ROWS`, y que en React 19 los Server Components cambian el mental model de los hooks").
- **Input del contexto (ya lo sé por los archivos de configuración):**
  - Zona horaria: CET → fecheo automático con `Europe/Madrid`.
  - Idioma preferido: español → entrada en español, salvo que Daniel escriba en inglés.
  - Stack técnico: PostgreSQL, React, Python, JS/TS, Docker, etc. → el Doc puede ser multi-tema.
  - TOOLS.md revela que tenemos Google Drive vía Composio (calendar, mail, drive). No hay un Doc ID preconfigurado → **necesito establecerlo en la primera ejecución**.

- **Lo que NO sé y debo preguntar/persistir:**
  - ¿Qué Doc concreto es el diario? (ID o nombre) → preguntar en primera ejecución, luego guardar en MEMORY.md o un fichero de configuración.

**¿Cómo es un buen output?**
- **Formato de la entrada:**
  ```markdown
  ## 2026-06-29 — Lunes

  ### Temas
  PostgreSQL, React 19, Server Components

  ### Qué aprendí
  - `RANGE` vs `ROWS` en window functions: RANGE agrupa ties, ROWS no.
  - React Server Components cambian el mental model: el fetch va en el componente, no en useEffect.

  ### Reflexión / Para seguir explorando
  - Probar RANGE con EXCLUDE en mi BBDD de pruebas.
  - Leer la doc de RSC con ejemplos prácticos.
  ```

- **Destino:** Google Doc "Diario de Aprendizaje — Daniel" (o el que se acuerde). Se usa `drive.files.create` si no existe y `documents.batchUpdate` para añadir al final.
- **Validación de éxito:** La API de Google Docs responde OK con el contenido actualizado. Opcional: enviar un extracto por Telegram si está configurado.

**Confirmación enviada a Telegram ✅** (2026-06-29 19:27 UTC)
- Mensaje ID: `61`
- Estado: `ok: true`
- Incluye: descripción de la skill, formato del output y próximo paso.

**🧪 Prueba real ejecutada** (2026-06-29 19:41 UTC)
- Input: "guarda esto que aprendí hoy: PostgreSQL LATERAL JOIN... / React 19 useActionState..."
- Output: entrada markdown guardada en `diary/learning-diary.md` (18 líneas, temas: PostgreSQL, React 19, OpenClaw)
- Confirmación Telegram: msg_id `63` ✅
- Pendiente: crear Google Doc cuando configuremos gog CLI

---

## Skill 2: Triaje de bandeja d

**¿Qué hace esta skill?**
Lee correos no leídos de Gmail, decide cuáles requieren acción humana y crea una Google Task por cada uno con un resumen de qué hacer.

**¿Qué input necesita el agente?**
- **Input directo de Daniel:** Un comando — normalmente "revisa mi bandeja" o se dispara desde heartbeat si hay no leídos. No necesita más, porque la skill es autónoma.

- **Input del contexto (config files + memoria):**
  - Gmail vía Composio (TOOLS.md lo confirma, con firma configurada).
  - Zona horaria CET → fechas y deadlines correctas.
  - Idioma preferido español → la tarea y el resumen van en español.
  - Si hay un Google Task list preferido ya persistido en memoria (primera vez habrá que establecerlo).

- **Heurística de triaje** (reglas internas del agente, no preguntar cada vez):
  - **Requiere acción:** emails de personas conocidas con verbs de petición, preguntas directas, adjuntos de review, palabras como "urge", "necesito", "confirmar", "revisa", "pendiente".
  - **NO requiere acción:** newsletters, promos, notificaciones automáticas de GitHub/CI/CD, confirmaciones de entrega.
  - **Criterio ajustable:** si Daniel ve falsos positivos/negativos, se refina la heurística y se guarda en `TOOLS.md` o `MEMORY.md`.

- **Lo que NO sé y debo preguntar/persistir:**
  - ¿En qué Google Task list crear las tareas? Primera ejecución: pregunto o uso "default". Persisto el ID.
  - ¿Deadline por defecto? Hoy, a menos que el correo especifique una fecha. Si el correo dice "para el viernes", extraer la fecha.
  - ¿Cuántos emails procesar? Últimos 20 no leídos o los de las últimas 24h, lo que sea menor.

**¿Cómo es un buen output?**
- **Por cada email actionable:**
  - Se crea una Google Task con:
    - **Título:** `[Asunto truncado] — {remitente}` (ej: "Revisar PR #42 — María")
    - **Descripción:** breve resumen de la acción necesaria (1-2 líneas).
    - **Fecha de vencimiento:** hoy o la fecha inferida del correo.
    - **Lista:** la acordada (default o personalizada).

- **Resumen para Daniel** (mismo canal donde pidió la skill o heartbeat):
  ```
  📬 Revisé 12 correos. 3 requieren acción:
  • Revisar PR #42 — María
  • Confirmar disponibilidad reunión Jueves — Carlos
  • Aprobar factura servicios cloud — Contabilidad
  ✅ Tareas creadas en Google Tasks.
  ```

- **Validación de éxito:** Las 3 llamadas a Google Tasks API responden 200. El resumen se entrega a Daniel.

- **Caso borde:** si hay 0 acciones → "📬 Revisé 12 correos. Todo tranquilo, solo boletines y notificaciones." No crear tareas vacías.

**Confirmación enviada a Telegram ✅** (2026-06-29 19:27 UTC)
- Mensaje ID: `62`
- Estado: `ok: true`
- Incluye: descripción de la skill, formato del output y próximo paso.

**🧪 Prueba real ejecutada** (2026-06-29 19:42 UTC)
- Input: simulación de 8 correos no leídos basada en perfil de Daniel (PR review, reunión, factura)
- Triaje: 5 SILENT (newsletters/notificaciones) + 3 ACTIONABLE
- Output: entrada markdown guardada en `diary/inbox-triage-2026-06-29.md` con tabla detallada
- Confirmación Telegram: msg_id `64` ✅
- Pendiente: instalar gog + OAuth para Google Tasks reales

-