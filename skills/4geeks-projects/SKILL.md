# Skill: Obtener mis proyectos de 4Geeks

Recupera la lista de proyectos (task_type=PROJECT) asignados al estudiante desde la API de BreatheCode, con su estado y revisión.

## Endpoint

```
GET /v1/assignment/user/me/task
```

Base: `https://breathecode.herokuapp.com/v1`

## Dependencias

- Token de estudiante en `.env` como `GEEKS_TOKEN`
- `curl` y `python3` (para parsear JSON)

## Uso

```bash
source /root/.openclaw/workspace/.env

curl -s -H "Authorization: Token $GEEKS_TOKEN" \
  "https://breathecode.herokuapp.com/v1/assignment/user/me/task" \
  | python3 -c "
import json,sys

data = json.load(sys.stdin)
total = len(data)

# Filtrar solo proyectos
proyectos = [t for t in data if t.get('task_type') == 'PROJECT']

print(f'Total tareas: {total}')
print(f'Proyectos: {len(proyectos)}')
print()

for p in proyectos:
    estado = p['task_status']
    revision = p.get('revision_status', '?')
    cohorte = p['cohort']['name']
    slug = p['associated_slug']

    print(f\"  [{estado}] [{revision}] {p['title']}\")
    print(f\"    Cohorte: {cohorte}\")
    print(f\"    Slug: {slug}\")

    if p.get('github_url'):
        print(f\"    GitHub: {p['github_url']}\")
    if p.get('description'):
        print(f\"    Feedback: {p['description']}\")
    if p.get('delivered_at'):
        print(f\"    Entregado: {p['delivered_at']}\")
    if p.get('reviewed_at'):
        print(f\"    Revisado: {p['reviewed_at']}\")
    print()
"
```

## Campos clave por tarea

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | int | ID único de la tarea |
| `title` | string | Nombre del proyecto |
| `task_type` | string | `PROJECT`, `EXERCISE`, `LESSON` |
| `task_status` | string | `PENDING` o `DONE` |
| `revision_status` | string | `PENDING`, `APPROVED` (u otros) |
| `associated_slug` | string | Slug del contenido asociado |
| `github_url` | string/null | URL del repo si se entregó |
| `description` | string | Feedback del revisor |
| `delivered_at` | datetime/null | Fecha de entrega |
| `reviewed_at` | datetime/null | Fecha de revisión |
| `cohort` | object | `{id, name, slug}` del cohort |

## Estados de revisión

- `PENDING` — No revisado aún
- `APPROVED` — Aprobado por el mentor
- (pueden existir otros como `REJECTED`)

## Notas

- El endpoint devuelve **todas** las tareas (ejercicios, lecciones, proyectos). Filtra por `task_type == "PROJECT"` para proyectos.
- El token se lee de `.env` — nunca hardcodearlo.
- No compartir el output con información personal del usuario (feedback, URLs) sin permiso.