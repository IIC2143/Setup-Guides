# Template de Skill

Estructura sugerida para crear una skill reutilizable en `.agents/skills/<nombre>/SKILL.md`, `.claude/skills/<nombre>/SKILL.md` o `.opencode/skills/<nombre>/SKILL.md`.

Una skill sirve para guardar una forma repetible de trabajar con un agente. Conviene usarla cuando un equipo repite el mismo tipo de tarea y quiere que la herramienta cargue instrucciones específicas sin copiar el mismo prompt cada vez.

## Template vacío

```markdown
---
name: <nombre-en-kebab-case>
description: <qué hace la skill y cuándo debe usarse. Sé específico: esta descripción decide si el agente la carga o no.>
---

# <Nombre de la skill>

## Objetivo

<Describe en 2-3 líneas qué tarea repetible resuelve esta skill.>

## Cuándo usarla

Usa esta skill cuando:

- <caso concreto 1>
- <caso concreto 2>
- <caso concreto 3>

No la uses cuando:

- <caso fuera de alcance 1>
- <caso fuera de alcance 2>

## Contexto que debe leer

Antes de actuar, revisa:

- `<archivo-o-carpeta-relevante>`
- `<referencia-opcional>`

Si existe `references/`, carga solo el archivo necesario para la tarea:

- `references/<tema>.md`: usar cuando <condición>.
- `references/<otro-tema>.md`: usar cuando <condición>.

## Flujo de trabajo

1. Lee el contexto mínimo necesario.
2. Resume el estado actual.
3. Identifica el cambio o acción mínima.
4. Explica qué archivos tocarás antes de editar.
5. Implementa solo lo necesario.
6. Ejecuta el check más chico que valide el cambio.
7. Resume diff, checks y riesgos.

## Cuándo preguntar antes de seguir

Pregunta al usuario si:

- falta un dato que cambia la solución;
- el cambio puede borrar datos, secretos o configuración;
- hay más de una interpretación razonable del objetivo;
- necesitas usar una herramienta externa con permisos de escritura.

No preguntes si puedes asumir algo chico, reversible y coherente con el repo.

## Gates

Antes de editar:

- [ ] Leí el contexto relevante.
- [ ] Entiendo el alcance.
- [ ] Sé qué archivos tocaré.
- [ ] Sé cómo validaré.

Antes de cerrar:

- [ ] El cambio cumple el objetivo.
- [ ] No hay cambios fuera de alcance.
- [ ] No se agregaron secretos.
- [ ] Ejecuté o declaré el check correspondiente.

## Recursos opcionales

- `scripts/`: usar solo para pasos repetitivos que conviene ejecutar siempre igual.
- `references/`: usar para documentación larga, criterios del curso, APIs, esquemas o ejemplos.
- `assets/`: usar para plantillas o archivos base que la skill necesita copiar o modificar.
```

## Ejemplo de aplicación

```markdown
---
name: rails-controller-review
description: Revisa controladores Rails cuando se modifican acciones, filtros, parámetros permitidos o respuestas HTTP.
---

# Rails Controller Review

## Objetivo

Revisar cambios en controladores Rails para detectar problemas de permisos, parámetros, respuestas HTTP y cobertura mínima antes de abrir un PR.

## Cuándo usarla

Usa esta skill cuando:

- se modifica un archivo en `app/controllers/`;
- se agregan o cambian parámetros permitidos;
- cambia el comportamiento de una acción usada por una vista o API.

No la uses cuando:

- el cambio solo toca documentación;
- el cambio solo modifica estilos o copy;
- el cambio está fuera de Rails.

## Contexto que debe leer

Antes de actuar, revisa:

- `app/controllers/`
- `config/routes.rb`
- tests cercanos en `test/controllers/` o `spec/requests/`

## Flujo de trabajo

1. Identifica qué acción del controlador cambió.
2. Revisa rutas, parámetros permitidos y flujo de autenticación/autorización.
3. Busca tests existentes relacionados.
4. Señala riesgos concretos con archivo y línea.
5. Propón el cambio mínimo o el test mínimo que cubriría el riesgo.
6. Resume hallazgos, validación y pendientes.

## Cuándo preguntar antes de seguir

Pregunta al usuario si:

- no queda claro el comportamiento esperado;
- hay más de una forma válida de manejar permisos;
- corregir el problema requiere cambiar modelo, rutas o vistas.

No preguntes si el fix es local, chico y coherente con el patrón existente.

## Gates

Antes de editar:

- [ ] Leí el controlador afectado.
- [ ] Revisé rutas relacionadas.
- [ ] Revisé tests cercanos.
- [ ] Sé cómo validaré el cambio.

Antes de cerrar:

- [ ] El cambio o recomendación cubre el riesgo detectado.
- [ ] No hay cambios fuera de alcance.
- [ ] No se agregaron secretos.
- [ ] Ejecuté o declaré el check correspondiente.

## Recursos opcionales

- `references/controller-review.md`: usar si el curso define criterios específicos para controladores.
```
