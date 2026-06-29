# Skill: Obtener trabajo pendiente de 4Geeks

Muestra qué tareas te faltan por completar, agrupadas por cohorte y tipo.

## Endpoint

```
GET /v1/assignment/user/me/task
```

Base: `https://breathecode.herokuapp.com/v1`

## Dependencias

- Token de estudiante en `.env` como `GEEKS_TOKEN`
- `curl` y `python3`

## Uso básico — resumen por cohorte

```bash
source /root/.openclaw/workspace/.env

curl -s -H "Authorization: Token $GEEKS_TOKEN" \
  "https://breathecode.herokuapp.com/v1/assignment/user/me/task" \
  | python3 -c "
import json,sys
data = json.load(sys.stdin)

pending = [t for t in data if t['task_status'] == 'PENDING']
done = len([t for t in data if t['task_status'] == 'DONE'])
total = len(data)
by_cohort = {}
for t in pending:
    by_cohort.setdefault(t['cohort']['name'], []).append(t)

print(f'Completadas: {done} | Pendientes: {len(pending)} | Total: {total}')
print()

for cohort, tasks in sorted(by_cohort.items()):
    projects = len([t for t in tasks if t['task_type'] == 'PROJECT'])
    exercises = len([t for t in tasks if t['task_type'] == 'EXERCISE'])
    lessons = len([t for t in tasks if t['task_type'] == 'LESSON'])
    print(f'📁 {cohort}')
    print(f'   Proyectos: {projects} | Ejercicios: {exercises} | Lecciones: {lessons}')
    print()
"
```

## Uso detallado — lista cada tarea pendiente

```bash
source /root/.openclaw/workspace/.env

curl -s -H "Authorization: Token $GEEKS_TOKEN" \
  "https://breathecode.herokuapp.com/v1/assignment/user/me/task" \
  | python3 -c "
import json,sys
data = json.load(sys.stdin)

pending = [t for t in data if t['task_status'] == 'PENDING']
by_cohort = {}
for t in pending:
    by_cohort.setdefault(t['cohort']['name'], []).append(t)

for cohort, tasks in sorted(by_cohort.items()):
    print(f'📁 {cohort} ({len(tasks)} pendientes)')
    for t in sorted(tasks, key=lambda x: (x['task_type'], x['title'])):
        tipo = {'PROJECT':'📦','EXERCISE':'🔧','LESSON':'📖'}.get(t['task_type'], '📄')
        rev = t.get('revision_status','?')
        print(f'   {tipo} [{t[\"task_type\"]}] {t[\"title\"]} (rev: {rev})')
        if t.get('opened_at'):
            print(f'       abierto: {t[\"opened_at\"][:10]}')
    print()
"
```

## Importante: diferencias entre tarea completada vs proyecto entregado

- Una tarea puede tener `task_status: DONE` sin tener `delivered_at` ni feedback
- Un proyecto entregado (`task_type: PROJECT` + `delivered_at` con valor) suele tener feedback
- Las tareas PENDING pueden estar "abiertas" (tienen `opened_at`) o ni siquiera iniciadas (sin `opened_at`)

## Notas

- Filtra por `task_status == "PENDING"` — eso te da TODO lo que falta
- Opcionalmente filtrar por cohorte con `t['cohort']['id'] == <id>` para un curso específico
- Los datos cambian en tiempo real a medida que haces ejercicios en learnpack