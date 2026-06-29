---
name: learning-diary
description: "Añade una entrada estructurada al diario de aprendizaje personal de Daniel en Google Docs + confirma por Telegram."
user-invocable: true
metadata:
  openclaw:
    emoji: "📖"
---

# Diario de Aprendizaje Diario

## Cuándo se activa

Cuando Daniel dice frases como:

- "apunté esto" / "toma nota"
- "he aprendido…" / "aprendí que…"
- "guarda esto en el diario"
- "hoy aprendí…"
- `/skill learning-diary`

O cuando explícitamente pida guardar algo en su diario de aprendizaje.

## Workflow

### 1. Pedir el Doc ID (solo primera vez)

Si no hay un Doc ID guardado, preguntar a Daniel:

> "¿Qué Google Doc usamos como diario de aprendizaje? Dame el enlace o el nombre. Si no existe, lo creo yo."

Guardar la respuesta en `MEMORY.md` bajo el bloque `learning-diary:` con el campo `docId`.

### 2. Formatear la entrada

Siempre en español. Fecha + día de la semana en `Europe/Madrid`.

```
## {fecha} — {día semana}

### Temas
{lista de etiquetas técnicas separadas por comas}

### Qué aprendí
- {bullet point 1}
- {bullet point 2}

### Reflexión / Para seguir explorando
- {punto de reflexión o enlace a profundizar}
```

Extraer los temas del texto de Daniel usando su stack conocido (PostgreSQL, React, Python, JS/TS, Docker, etc.).

### 3. Escribir en Google Doc

Usar la API de Google Docs vía Composio para añadir la entrada al final del documento.

Si Composio/Google Docs no está disponible, escribir en un archivo local en:

```
{workspace}/diary/learning-diary.md
```

### 4. Confirmar por Telegram

Enviar a `chat_id=7113321755`:

```
📖 *Diario actualizado — {fecha}*

*Temas:* {temas}

{bullet más relevante}

[Entrada completada ✅]
```

### 5. Guardar en heartbeat-state

Actualizar `memory/heartbeat-state.json` con:
```json
{ "lastDiaryEntry": "{fecha}" }
```