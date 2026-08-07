# Git y Pull Requests

Objetivo: entregar cambios pequeños, trazables y fáciles de revisar.

## Gitflow mínimo

Para el curso, usa un flujo simple:

1. Actualiza `main`.
2. Crea una rama corta desde `main`.
3. Haz commits pequeños.
4. Abre PR hacia `main`.
5. Corrige feedback.
6. Fusiona cuando esté aprobado y los checks pasen.

No trabajes directo sobre `main`. No usamos rama `stage` por defecto; agrégala solo si el equipo necesita probar integraciones antes de llegar a `main`.

`main` representa la versión estable del proyecto. Si todos trabajan directo sobre `main`, cualquier cambio incompleto puede romper el trabajo del resto. La rama corta permite experimentar, el PR permite revisar y CI confirma que los checks siguen pasando antes de mezclar el cambio.

La regla práctica: una rama debe representar una intención clara. Si cuesta ponerle nombre a la rama, probablemente la tarea todavía está demasiado amplia.

## Ramas

- `main`: versión estable.
- `feature/<tema>`: nueva funcionalidad.
- `fix/<tema>`: corrección.
- `docs/<tema>`: documentación.
- `chore/<tema>`: mantención sin cambio funcional.

Ejemplos:

```bash
git switch main
git pull
git switch -c docs/setup-opencode
git switch -c feature/login
git switch -c fix/navbar-mobile
git switch -c chore/update-deps
git push -u origin <rama>
```

Regla práctica: nombre corto, en minúsculas, separado por guiones, y asociado al cambio.

Ejemplos buenos:

- `feature/task-filter`
- `fix/login-email-spaces`
- `docs/ai-tools-guide`
- `chore/update-deps`

Ejemplos débiles:

- `cambios`
- `arreglos-finales`
- `mi-rama`
- `feature/todo`

Si una rama empieza a acumular temas distintos, cierra lo que está listo y abre otra rama. PR chico gana a PR completo pero difícil de revisar.

## Commits

Un commit debe explicar una unidad de cambio.

Formato sugerido:

```text
tipo: resumen corto
```

Tipos útiles:

- `feat`: funcionalidad.
- `fix`: corrección.
- `docs`: documentación.
- `test`: pruebas.
- `refactor`: cambio interno sin comportamiento nuevo.
- `chore`: mantención, dependencias o configuración.

Ejemplo:

```text
docs: add opencode setup guide
```

Buenos commits:

- Tocan una idea.
- Se pueden revertir sin romper cambios no relacionados.
- No mezclan formato, feature y fixes en el mismo commit.

Ejemplos:

```text
fix: normalize login email
docs: clarify opencode setup
test: cover completed task filter
```

Evita:

```text
cambios varios
avance
fix final ahora si
```

No necesitas un commit por cada línea editada. Tampoco conviene un solo commit con toda la entrega si mezcla documentación, feature, tests y formato. Busca unidades que otra persona pueda leer y entender.

## Antes de abrir PR

```bash
git status
git diff
git diff --stat
```

Revisa:

- Que el diff solo contiene lo que querías cambiar.
- Que no hay secretos ni archivos temporales.
- Que corriste los checks que correspondan.
- Que el branch está actualizado contra `main` si hubo cambios recientes.

Cómo leer tu diff:

- `git status`: muestra archivos modificados, nuevos o sin trackear.
- `git diff --stat`: muestra resumen por archivo; sirve para detectar cambios demasiado grandes.
- `git diff`: muestra el detalle línea por línea.

Antes de abrir PR, busca señales de ruido:

- cambios de formato en archivos no relacionados;
- archivos temporales, logs o capturas que no debían subirse;
- tokens, claves o valores reales de configuración;
- cambios generados por IA que no puedes explicar.

## Pull Request

Usa `.github/pull_request_template.md`. La copia de referencia vive en `docs/template/pull-request-template.md`.

Un buen PR responde:

- Contexto del problema: qué se intenta resolver y dónde nace la necesidad. Si usaste Spec Kit, enlaza `spec.md` o `plan.md` desde `specs/YYYYMMDD-HHMMSS-nombre-feature/`.
- Implementación: qué se cambió y qué archivos o flujos toca.
- Validación: comandos, pruebas manuales y criterios cubiertos.
- Impacto y riesgos: qué podría romperse y qué queda fuera.

Tamaño recomendado: chico. Si el PR necesita mucha explicación para ser revisable, probablemente conviene partirlo.

Ejemplo breve:

```markdown
## Contexto del problema

- La lista de tareas no permite separar pendientes de completadas.
- Spec: specs/20260729-153000-filtro-tareas/spec.md

## Implementación

- Agregué filtro por estado en TasksController.
- Actualicé la vista index con controles simples.
- Agregué cobertura del filtro en test de controlador.

## Validación

- [x] bin/rails test test/controllers/tasks_controller_test.rb
- [x] Revisión manual de filtros: todas, pendientes, completadas.

## Impacto y riesgos

- Impacto esperado: solo lista de tareas.
- Riesgos: parámetros inválidos vuelven a la vista por defecto.
- Fuera de alcance: búsqueda, ordenamiento y paginación.
```

Un PR no debe vender el cambio. Debe hacerlo revisable.

## Revisión con IA

Prompt recomendado:

```text
Revisa este diff como code review. Prioriza bugs, riesgos, cambios no pedidos y tests faltantes. No propongas refactors grandes si no bloquean la entrega.
```

Usa IA para encontrar riesgos, no para aprobarte solo. Una buena revisión con IA debe mirar el diff y señalar problemas concretos con archivo, línea o flujo afectado.

## Responder feedback

El feedback de PR es parte normal del trabajo. Responde con cambios concretos:

- Si el comentario es correcto, ajusta el código y responde qué cambiaste.
- Si no estás de acuerdo, explica el motivo con contexto técnico.
- Si el comentario abre un tema más grande, propón dejarlo fuera de alcance o crear otro PR.
- Marca conversaciones como resueltas solo cuando el cambio o la respuesta ya estén listos.

Evita reescribir todo el PR por cada comentario. Corrige el punto indicado y mantén el diff chico.

## Si algo sale mal

Casos frecuentes:

- Estás en `main` con cambios locales: crea una rama antes de commitear con `git switch -c <rama>`.
- Hay archivos que no reconoces en `git status`: revisa antes de agregarlos.
- CI falla en el PR: reproduce el comando local si puedes y corrige en la misma rama.
- `main` avanzó mientras trabajabas: actualiza tu rama antes de mergear si el PR quedó desactualizado.
- El agente editó demasiado: revisa `git diff` y conserva solo lo que corresponde al alcance.

Antes de mergear:

- [ ] PR apunta a `main`.
- [ ] Template completo.
- [ ] Checks pasaron o pendientes explicados.
- [ ] Feedback resuelto.
- [ ] No hay cambios fuera de alcance.

## Fuentes

- GitHub pull requests: <https://docs.github.com/en/pull-requests>
- GitHub protected branches: <https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches>
