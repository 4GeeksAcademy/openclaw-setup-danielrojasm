# Antes de hacer algo dijo esto

Quiero darte la habilidad de conectarte a mi cuenta de https://www.4geeks.com usando mi token de estudiante, sin que tenga que desarrollar código de mi parte. ¿Qué debemos hacer?"                
                                                                                                                                                           
Antes de meterme a construir la integración, dime:                                                                                                                                                 
                                                                                                                                                           
¿Qué te gustaría poder hacer con 4Geeks?                                                                                                                                           
Por ejemplo:                                                                                                                                                                                       
- Consultar tu progreso en los cursos/bootcamps                                                                                                                                                    
- Obtener ejercicios y tareas pendientes                                                                                                                                                           
- Subir/entregar ejercicios automáticamente                                                                                                                                                        
- Revisar feedback de instructores                                                                                                                                                                 
- Hacer seguimiento de tu cohorte/horarios                                                                                                                                                                                                                                                            
Dependiendo de lo que quieras, diseño una skill específica que yo mismo ejecute. No necesitas escribir ni una línea de código — yo me encargo de toda la parte técnica.  

---

# Documentación de Skills

Cada skill está almacenada en `skills/<nombre>/SKILL.md` y utiliza el token `GEEKS_TOKEN` del `.env`. Todas se ejecutan contra la API BreatheCode en `https://breathecode.herokuapp.com/v1/`.

---

## Skill 1: 4geeks-token-verify

### Prompt original
> *"Quiero darte la habilidad de conectarte a mi cuenta de https://www.4geeks.com usando mi token de estudiante, sin que tenga que desarrollar código de mi parte."*

### Descripción
Verifica que el token de estudiante de 4Geeks es válido consultando el perfil del usuario autenticado contra la BreatheCode API.

**Endpoint**: `GET /v1/auth/user/me`

### Resultado de prueba (2026-06-26 18:47 UTC)

```
HTTP 200 OK
{
  "id": 20712,
  "email": "rojas.daniel.0217@gmail.com",
  "first_name": "Daniel",
  "last_name": "Rojas",
  "roles": [{"academy": {"name": "4Geeks Madrid"}, "role": "student"}]
}
```

✅ Token válido — perfil de Daniel Rojas (userId=20712) recuperado correctamente.

---

## Skill 2: 4geeks-projects

### Prompt original
> *"Lista mis proyectos en 4Geeks con su estado actual."*

### Descripción
Obtiene la lista de tareas tipo `PROJECT` asignadas al usuario, mostrando título, slug, estado, cohorte y fechas relevantes.

**Endpoint**: `GET /v1/assignment/user/me/task` (filtrado por `task_type == "PROJECT"`)

### Resultado de prueba (2026-06-26 19:10 UTC)

```
📦 Proyectos en 4Geeks (total: 20)

✅ DONE | Milestone 1 — Web Fundamentals | cohort: ...
✅ DONE | Dashboard with Tailwind CSS | cohort: ...
✅ DONE | Artist Landing Page with SEO | cohort: ...
...
⬜ PENDING | Cinema Seat Manager in TypeScript | cohort: spain-aie-pt-1
⬜ PENDING | Eventonica Project | cohort: spain-aie-pt-1
...
```

✅ 20 proyectos recuperados, clasificados por estado (DONE/PENDING).

---

## Skill 3: 4geeks-pending

### Prompt original
> *"Muéstrame las tareas que aún tengo pendientes agrupadas por cohorte y tipo."*

### Descripción
Filtra las tareas con `task_status == "PENDING"` y las agrupa por cohorte y tipo (proyecto, ejercicio, lección), mostrando título y slug. Incluye modo resumen global y modo detallado por cohorte.

**Endpoint**: `GET /v1/assignment/user/me/task` (filtrado por `task_status == "PENDING"`)

### Resultado de prueba (2026-06-26 19:32 UTC)

```
📊 RESUMEN GLOBAL: 89 tareas pendientes

Cohorte                            Pend  Proy  Ej   Lec
---------------------------------------------------------
spain-aie-pt-1                       54     7   34   13
Web UI fundamentals with Tailwind     4     0    4    0
...
```

✅ 89 tareas PENDING identificadas en 10 cohorts activas.

---

## Skill 4: 4geeks-progress

### Prompt original
> *"Necesito una visión general de mi progreso en todos los cursos de 4Geeks."*

### Descripción
Calcula estadísticas de progreso para todos los cohorts del usuario: total de tareas, completadas, pendientes, porcentaje de avance, y desglose por tipo (proyectos, ejercicios, lecciones).

**Endpoint**: `GET /v1/assignment/user/me/task`

### Resultado de prueba (2026-06-26 19:34 UTC)

```
Cohorte                                        Total   Done  Pend     % | Pry_OK Pry_P  Ej_OK  Ej_P
-----------------------------------------------------------------------------------------------
...
TOTAL                                           192    103    89   54%
```

✅ Progreso global: 54% (103/192) — 11 cohorts analizados, 3 al 100%.
---

## Notas generales

- Todas las skills leen `GEEKS_TOKEN` desde `.env` — nunca hardcodeado.
- Skills 2, 3 y 4 comparten el mismo endpoint (`/v1/assignment/user/me/task`) pero con distintos filtros.
- El token actual pertenece a Daniel Rojas (userId=20712), cohorte activo `spain-aie-pt-1`.
- Las skills se almacenan en `skills/<nombre>/SKILL.md` y son ejecutables directamente con `bash`.
