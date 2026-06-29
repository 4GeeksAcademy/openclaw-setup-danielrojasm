# 📖 Diario de Aprendizaje — Daniel

## 2026-06-29 — Monday

### Temas
PostgreSQL, React 19, OpenClaw

### Qué aprendí
- LATERAL JOIN en PostgreSQL permite que subconsultas en FROM accedan a columnas de tablas externas. Es como un foreach SQL: por cada fila de la tabla externa se ejecuta la subconsulta. Muy útil para top-N por grupo y cálculos por fila sin subconsultas correlacionadas complejas.
- useActionState en React 19 reemplaza a useFormStatus. Unifica form actions con estados de carga y validación en una sola API, simplificando el patrón submit + loading + feedback.
- Las skills de OpenClaw se definen con SKILL.md (frontmatter YAML + markdown). El campo name debe ser slug (minúsculas, guiones). Se cargan desde workspace/skills/ con skills.load.extraDirs.

### Reflexión / Para seguir explorando
- Probar LATERAL JOIN con EXPLAIN ANALYZE en una query real de la BBDD de pruebas.
- Leer la RFC de useActionState y comparar con useFormStatus actual.
- Evaluar si merece la pena instalar gog para escribir directo a Google Docs.
