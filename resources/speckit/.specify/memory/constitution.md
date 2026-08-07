# AI Starter Kit Constitution

## Principios

### I. Cambios pequeños

Cada tarea debe producir el cambio más chico que resuelve el problema. Evitar refactors, abstracciones o dependencias fuera de alcance.

### II. Contexto antes de editar

Antes de proponer implementación, el agente debe leer el spec, el plan, `AGENTS.md` y los archivos relevantes del repo.

### III. Trazabilidad mínima

Para tareas no triviales, mantener `spec.md` y `plan.md` en `specs/YYYYMMDD-HHMMSS-nombre/`. Las tareas van embebidas en `plan.md`.

### IV. Validación explícita

Todo plan debe incluir cómo validar el cambio: comando, test, revisión manual o criterio observable.

### V. Seguridad

Nunca guardar tokens, API keys, credenciales o datos personales en Git. Pedir permiso antes de acciones destructivas.

## Flujo

1. Crear `spec.md` con `$speckit-specify`.
2. Revisar alcance y criterios de éxito.
3. Crear `plan.md` con `$speckit-plan`.
4. Implementar solo lo planificado.
5. Cerrar con diff, checks ejecutados y riesgos.

## Gobernanza

Estas reglas priorizan claridad para estudiantes. Los artefactos avanzados de Spec Kit (`tasks.md`, `research.md`, `data-model.md`, `quickstart.md`, `contracts/`) se usan solo si la tarea lo justifica o el equipo docente lo pide.

**Versión**: 1.0.0 | **Ratified**: 2026-07-28 | **Last Amended**: 2026-07-28
