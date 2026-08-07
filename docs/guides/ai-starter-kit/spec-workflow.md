# Spec Workflow

Objetivo: transformar una necesidad vaga en un cambio pequeño, revisable y validado.

## Spec Kit simplificado

Este repo usa Spec Kit, pero con un flujo reducido para no generar demasiados archivos.

La idea no es burocratizar una tarea chica. La idea es evitar que una necesidad vaga termine en un cambio grande, difícil de revisar y sin criterios claros. Un spec corto obliga a separar tres cosas que suelen mezclarse cuando se trabaja con IA:

- Problema: qué necesidad se quiere resolver.
- Alcance: qué entra y qué queda fuera.
- Validación: cómo sabremos que el cambio quedó bien.

Si el problema ya está claro, usa un prompt directo. Si todavía hay dudas de alcance, usa Spec Kit antes de editar.

## Prompt one-shot o Spec Kit

La diferencia es el alcance.

| Usa | Cuando |
| --- | --- |
| `docs/template/ai-task-prompt-template.md` | Cambio chico, claro, validable con un check simple y bajo riesgo de tocar archivos extra. |
| Spec Kit | Alcance ambiguo, varios archivos/flujos, casos borde relevantes o necesidad de acordar criterios antes de editar. |

Ejemplos:

- Prompt one-shot: cambiar un texto, corregir un typo, ajustar una validación simple o arreglar un test puntual.
- Spec Kit: agregar un filtro, cambiar un flujo de usuario, modificar permisos, tocar base de datos o implementar una feature con casos borde.

Puedes usar el template de prompt como input inicial para `$speckit-specify`.

Flujo recomendado:

1. `$speckit-specify`: crea `spec.md` en una carpeta con timestamp.
2. Revisar y ajustar el spec.
3. `$speckit-plan`: crea `plan.md` con tareas embebidas.
4. `$speckit-implement`: implementa solo lo planificado.
5. `$speckit-review`: revisa diff, checks y criterios de éxito.

Resultado esperado por feature:

```text
specs/YYYYMMDD-HHMMSS-nombre-feature/
├── spec.md
└── plan.md
```

El prefijo timestamp evita que dos personas creen specs con el mismo correlativo cuando trabajan en paralelo. Usa la hora local de creación y un nombre corto en kebab-case, por ejemplo `specs/20260729-153000-filtro-productos/`.

`tasks.md`, `research.md`, `data-model.md`, `quickstart.md` y `contracts/` quedan como modo avanzado.

Si falta información crítica, el agente debe preguntar antes de cerrar `spec.md` o `plan.md`.

## Ejemplo rápido

```text
$speckit-specify "Agregar filtro por estado a la lista de tareas. Debe permitir ver todas, pendientes y completadas. No cambiar autenticación."

$speckit-plan

$speckit-implement

$speckit-review
```

## Ejemplo completo

Necesidad vaga:

```text
Quiero que la lista de tareas sea más fácil de usar.
```

Esa frase no alcanza para implementar. Puede significar filtros, orden, búsqueda, diseño, accesibilidad o performance. Un buen primer prompt reduce el alcance:

```text
$speckit-specify "Agregar filtro por estado a la lista de tareas. Debe permitir ver todas, pendientes y completadas. No cambiar autenticación, permisos ni diseño general. El cambio debe validarse con el test más cercano y una revisión manual del filtro."
```

Un buen `spec.md` debería dejar claro:

```text
Problema:
Los estudiantes no pueden separar tareas pendientes de completadas en la lista principal.

Alcance:
- Agregar filtro por estado: todas, pendientes y completadas.
- Mantener la lista actual como vista por defecto.
- No cambiar autenticación ni permisos.

Criterios de aceptación:
- Al elegir "pendientes", solo aparecen tareas no completadas.
- Al elegir "completadas", solo aparecen tareas completadas.
- Al elegir "todas", aparecen ambos estados.
```

Un buen `plan.md` debería aterrizarlo a pasos técnicos chicos:

```text
Archivos a revisar:
- app/controllers/tasks_controller.rb
- app/views/tasks/index.html.erb
- test/controllers/tasks_controller_test.rb

Tareas:
- Revisar cómo se carga la lista actual.
- Agregar parámetro de filtro permitido.
- Actualizar la vista con controles mínimos.
- Agregar o ajustar test del controlador.
- Correr el test más cercano.
```

Si el spec o el plan no pueden decir qué archivos tocar, qué queda fuera o cómo validar, todavía no están listos para implementar.

## Crear spec

```text
$speckit-specify <describe el problema, contexto, restricciones y resultado esperado>
```

El spec debe responder:

- Qué problema se resuelve.
- Qué incluye y qué no incluye.
- Qué usuarios o actores participan.
- Qué requisitos son verificables.
- Qué casos borde importan.
- Cómo se sabrá que quedó bien.

Evita requisitos imposibles de verificar:

- Malo: "la página debe sentirse mejor".
- Mejor: "la lista debe permitir filtrar por estado sin perder el orden actual".

También evita meter la solución técnica demasiado pronto si todavía no entiendes el problema. Primero acuerda comportamiento; después decide implementación.

## Crear plan

```text
$speckit-plan
```

El plan debe incluir:

- Archivos leídos y qué se aprendió.
- Archivos que probablemente se tocarán.
- Pasos técnicos pequeños.
- Tareas embebidas.
- Validación.
- Riesgos y fuera de alcance.

Antes de implementar, lee el plan como reviewer. Pregunta:

- ¿Toca solo los archivos necesarios?
- ¿Tiene pasos chicos?
- ¿Define un check concreto?
- ¿Evita refactors no pedidos?
- ¿Declara riesgos o supuestos?

## Implementar

```text
$speckit-implement
```

El agente debe leer `spec.md` y `plan.md`, tocar solo los archivos planificados y correr la validación definida.

Si durante la implementación aparece un dato que cambia el alcance, no sigas parchando. Actualiza el spec o el plan antes de continuar. La implementación no debería inventar requisitos nuevos.

## Revisar

```text
$speckit-review
```

La review debe verificar:

- Requisitos del spec cubiertos.
- Tareas del plan completas.
- Diff sin cambios fuera de alcance.
- Checks ejecutados o pendientes declarados.
- Sin secretos ni credenciales.

La review final debe mirar el diff, no solo la respuesta del agente. Si el diff incluye archivos fuera del plan, el agente debe explicar por qué o revertirlos.

## Templates

Templates relevantes:

- `.specify/templates/overrides/spec-template.md`
- `.specify/templates/overrides/plan-template.md`
- `docs/template/ai-task-prompt-template.md`

Para tareas one-shot, usa `docs/template/ai-task-prompt-template.md`.

## Criterio de salida del spec

Una tarea está lista para implementar cuando tiene:

- Problema claro.
- Alcance acotado.
- Criterios de aceptación verificables.
- Plan de validación.
- Fuera de alcance explícito.

## Criterio de salida del plan

Un plan está listo cuando tiene:

- Archivos concretos.
- Tareas pequeñas.
- Checks claros.
- Riesgos conocidos.
- Nada fuera de alcance.

## Criterio de salida de la implementación

Una implementación está lista cuando:

- Cumple el spec.
- Sigue el plan o explica desviaciones.
- Tiene checks ejecutados.
- Fue revisada contra el diff.

## Errores frecuentes

- Crear spec para una tarea trivial: agrega fricción sin mejorar el resultado.
- Crear spec demasiado amplio: "mejorar toda la app" no es implementable en un PR chico.
- Escribir criterios no verificables: si no puedes probarlo o revisarlo manualmente, no sirve como criterio de aceptación.
- Saltarse el plan: el agente termina editando por intuición.
- No revisar el diff: el spec puede estar bien y la implementación igual salirse de alcance.

## Fuentes

- Repositorio oficial de Spec Kit: <https://github.com/github/spec-kit>
- Documentación oficial de Spec Kit: <https://github.github.com/spec-kit/>
- Referencia de comandos de Spec Kit: <https://github.github.com/spec-kit/reference/core.html>
