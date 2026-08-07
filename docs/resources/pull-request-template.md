# Template de Pull Request

Este recurso sirve como base para describir un PR de forma clara y revisable. La idea es reemplazar los textos entre `<...>` por información concreta del cambio.

Úsalo para que quien revise entienda rápido:

- qué problema resuelve el PR;
- qué archivos o flujos cambian;
- cómo se validó;
- qué riesgos o límites tiene el cambio.

## Template vacío

```markdown
## Contexto del problema

- <qué problema resuelve este PR>
- <link a issue/spec/tarea si existe>

## Implementación

- <qué cambiaste a nivel de código o docs>
- <archivos o flujos principales tocados>

## Validación

- [ ] <comando ejecutado o revisión manual>
- [ ] <caso probado>
- [ ] <criterio de aceptación cubierto>

## Impacto y riesgos

- Impacto esperado: <usuarios, flujo o módulo afectado>
- Riesgos: <riesgo conocido o "ninguno conocido">
- Fuera de alcance: <qué no incluye este PR>
```

## Ejemplo de aplicación

```markdown
## Contexto del problema

- La lista de tareas no permite filtrar por estado, lo que dificulta revisar tareas pendientes y completadas.
- Relacionado con issue #12.

## Implementación

- Agregué un parámetro `status` al listado de tareas.
- Actualicé el controlador para filtrar tareas pendientes, completadas o todas.
- Agregué controles simples en la vista `tasks/index`.

## Validación

- [x] `bin/rails test test/controllers/tasks_controller_test.rb`
- [x] Revisión manual del filtro en la vista de tareas.
- [x] Criterios de aceptación de tareas pendientes, completadas y todas cubiertos.

## Impacto y riesgos

- Impacto esperado: afecta solo el listado de tareas.
- Riesgos: ninguno conocido.
- Fuera de alcance: no cambia permisos, autenticación ni diseño general.
```
