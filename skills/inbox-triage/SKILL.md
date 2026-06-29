---
name: inbox-triage
description: "Lee los no leídos de Gmail, filtra los que requieren acción y crea Google Tasks + confirma por Telegram."
user-invocable: true
metadata:
  openclaw:
    emoji: "📬"
---

# Triaje de Bandeja de Entrada

## Cuándo se activa

Cuando Daniel dice:

- "revisa mi bandeja"
- "qué emails tengo"
- "triaje de correos"
- cualquier heartbeat que detecte que hay correos no leídos (se activa auto si heartbeat menciona emails no leídos)
- `/skill inbox-triage`

## Workflow

### 1. Pedir configuración inicial (solo primera vez)

Preguntar:

> "¿En qué lista de Google Tasks creo las tareas? (Nombre o 'default') ¿Deadline por defecto? (hoy, mañana) ¿Límite de emails a procesar? (por defecto 20)"

Guardar en `MEMORY.md` bajo `inbox-triage:` con campos `taskListId`, `defaultDeadline`, `maxEmails`.

### 2. Leer correos no leídos

Usar Gmail via Composio para leer los últimos N no leídos (20 por defecto):

- Asunto
- Remitente
- Cuerpo (primeros ~200 caracteres para contexto)
- Fecha del correo

### 3. Triaje (heurística)

Clasificar cada correo como **ACTIONABLE** o **SILENT**:

**ACTIONABLE** si:
- El remitente es una persona (no newsletter, no bot)
- Contiene verbos de petición: "necesito", "revisa", "confirma", "aprueba", "pendiente", "urge", "puedes", "podrías"
- Es una pregunta directa
- Tiene un adjunto que parece requerir revisión
- Menciona una fecha límite

**SILENT** si:
- Es newsletter, promo, oferta
- Es notificación automática (GitHub, CI/CD, delivery, confirmación de pedido)
- Es un "gracias", "recibido", acuse sin acción
- Es un correo de bienvenida o cambio de términos

### 4. Crear Google Tasks

Por cada email ACTIONABLE:

| Campo | Valor |
|-------|-------|
| Título | `[{asunto corto}] — {remitente}` (máx 60 chars) |
| Descripción | 1-2 líneas: qué hay que hacer exactamente |
| Fecha vencimiento | Hoy (o fecha inferida del correo si dice "para el viernes") |
| Lista | taskListId guardado |

Si el correo dice "para el viernes 5 de julio" → fijar due esa fecha.

### 5. Resumen para Daniel

Devolver en el mismo canal (chat/Telegram):

```
📬 Revisé {N} correos. {M} requieren acción:

• [{Asunto1}] — {Remitente1}
• [{Asunto2}] — {Remitente2}
• [{Asunto3}] — {Remitente3}

✅ {M} tareas creadas en Google Tasks.
```

Si 0 acciones:

```
📬 Revisé {N} correos. Todo tranquilo, solo boletines y notificaciones.
```

### 6. Confirmación Telegram

Enviar el mismo resumen a `chat_id=7113321755`.

### 7. Marcar como revisados

Opcionalmente marcar los correos SILENT como leídos para que no se acumulen en próximas ejecuciones. Preguntar la primera vez, persistir preferencia.