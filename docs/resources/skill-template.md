# Template de Skill

Estructura sugerida para crear una skill reutilizable en `.agents/skills/<nombre>/SKILL.md`, `.claude/skills/<nombre>/SKILL.md` o `.opencode/skills/<nombre>/SKILL.md`.

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
