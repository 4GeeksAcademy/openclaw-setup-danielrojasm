# Skill: Resumen de progreso en 4Geeks

Proporciona una visión general de todo tu avance en 4Geeks. Incluye un modo detallado para el programa `spain-aie-pt-1` que lista cada tarea pendiente.

## Endpoint

```
GET /v1/assignment/user/me/task
```

Base: `https://breathecode.herokuapp.com/v1`

## Dependencias

- Token de estudiante en `.env` como `GEEKS_TOKEN`
- `curl` y `python3`

## Uso: global (todos los cohorts)

```bash
source /root/.openclaw/workspace/.env

curl -s -H "Authorization: Token $GEEKS_TOKEN" \
  "https://breathecode.herokuapp.com/v1/assignment/user/me/task" \
  | python3 -c "
import json,sys
data = json.load(sys.stdin)

by_cohort = {}
for t in data:
    c = t['cohort']['name']
    by_cohort.setdefault(c, {'DONE':0,'PENDING':0,'total':0,'projects':{'DONE':0,'PENDING':0},'exercises':{'DONE':0,'PENDING':0},'lessons':{'DONE':0,'PENDING':0}})
    s = t['task_status']
    tt = t['task_type']
    by_cohort[c]['total'] += 1
    by_cohort[c][s] += 1
    if tt == 'PROJECT':
        by_cohort[c]['projects'][s] += 1
    elif tt == 'EXERCISE':
        by_cohort[c]['exercises'][s] += 1
    elif tt == 'LESSON':
        by_cohort[c]['lessons'][s] += 1

print(f\"{'Cohorte':45s} {'Total':>6s} {'Done':>5s} {'Pend':>5s} {'%':>5s} | {'Pry_OK':>6s} {'Pry_P':>5s} {'Ej_OK':>5s} {'Ej_P':>4s}\")
print('-'*95)
total_done = sum(c['DONE'] for c in by_cohort.values())
total_all = sum(c['total'] for c in by_cohort.values())
for cohort, c in sorted(by_cohort.items()):
    pct = 100 * c['DONE'] / c['total'] if c['total'] else 0
    print(f\"{cohort:45s} {c['total']:>6d} {c['DONE']:>5d} {c['PENDING']:>5d} {pct:>4.0f}% | {c['projects']['DONE']:>6d} {c['projects']['PENDING']:>5d} {c['exercises']['DONE']:>5d} {c['exercises']['PENDING']:>4d}\", end='')
    if c['projects']['PENDING'] > 0:
        print('  ⬅️', end='')
    print()

pct_all = 100 * total_done / total_all if total_all else 0
print('-'*95)
print(f\"{'TOTAL':45s} {total_all:>6d} {total_done:>5d} {total_all-total_done:>5d} {pct_all:>4.0f}%\")
"
```

## Uso: detalle de spain-aie-pt-1

Filtra exclusivamente el programa principal y muestra tareas pendientes agrupadas por tipo con título y slug.

```bash
source /root/.openclaw/workspace/.env

curl -s -H "Authorization: Token $GEEKS_TOKEN" \
  "https://breathecode.herokuapp.com/v1/assignment/user/me/task" \
  | python3 -c "
import json,sys
data = json.load(sys.stdin)

cohort = [t for t in data if t['cohort']['slug'] == 'spain-aie-pt-1']
total = len(cohort)
done = len([t for t in cohort if t['task_status'] == 'DONE'])
pending = [t for t in cohort if t['task_status'] == 'PENDING']
pct = 100 * done / total if total else 0

print(f\"📊  spain-aie-pt-1: {done}/{total} ({pct:.0f}%)\n\")

# Agrupar pendientes por tipo
by_type = {'PROJECT': [], 'EXERCISE': [], 'LESSON': []}
for t in pending:
    by_type.setdefault(t['task_type'], []).append(t)

for tt, label, icon in [('PROJECT', 'Proyectos', '📦'), ('LESSON', 'Lecciones', '📖'), ('EXERCISE', 'Ejercicios', '🔧')]:
    items = by_type.get(tt, [])
    total_tt = len([x for x in cohort if x['task_type'] == tt])
    done_tt = len([x for x in cohort if x['task_type'] == tt and x['task_status'] == 'DONE'])
    pend_tt = len([x for x in cohort if x['task_type'] == tt and x['task_status'] == 'PENDING'])
    print(f\"{icon} {label}: {done_tt}/{total_tt} completados ({pend_tt} pendientes)\")
    for item in items:
        print(f\"    • {item['title']}\")
        print(f\"      slug: {item['associated_slug']}\")
    print()
"
```

## Output de ejemplo (spain-aie-pt-1)

```
📊  spain-aie-pt-1: 0/54 (0%)

📦 Proyectos: 0/7 completados (7 pendientes)
    • Showcase your friend's artist talent with a website
      slug: building-your-first-website
    • Cinema Seat Manager in TypeScript
      slug: cinema-seat-manager-ts
    ...

📖 Lecciones: 0/13 completados (13 pendientes)
    • Introduction to Numpy
      slug: intro-to-numpy
    • Learning to program with Python
      slug: learning-to-code-with-python
    ...

🔧 Ejercicios: 0/34 completados (34 pendientes)
    • (ejercicios por slug)
```

## Interpretación

| Columna | Significado |
|---------|-------------|
| Total | Tareas asignadas en ese cohorte |
| Done | Completadas (`task_status: DONE`) |
| Pend | Pendientes (`task_status: PENDING`) |
| % | Porcentaje de avance |
| Pry_OK | Proyectos completados |
| Pry_P | Proyectos pendientes |
| Ej_OK | Ejercicios completados |
| Ej_P | Ejercicios pendientes |
| ⬅️ | Hay proyectos por entregar (atención) |

## Notas

- Los cohorts con 0% probablemente son nuevos (no has empezado aún el curso)
- Los cohorts con 100% están completamente terminados
- Prioriza cohorts con proyectos pendientes (marcados con ⬅️)
- Los datos se actualizan en tiempo real según tu actividad en learnpack