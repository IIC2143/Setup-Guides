# Prompt de tarea con IA

Este recurso es un template para pedir ayuda a una herramienta de IA sin perder contexto, alcance ni trazabilidad.

La idea es reemplazar los textos entre `<...>` por información concreta de tu tarea. No es obligatorio usar todas las secciones, pero mientras más ambigua sea la tarea, más conviene completar el template antes de pedir cambios.

Úsalo especialmente cuando:

- la tarea puede tocar varios archivos;
- necesitas que el agente lea documentación o reglas del repo;
- quieres limitar el alcance antes de editar;
- necesitas criterios de éxito claros para revisar el resultado.

## Template vacío

```text
Rol:
Actúa como <rol del agente: desarrollador Rails, reviewer, debugger, arquitecto, etc.>.

Conocimiento esperado:
- Lee <archivos, docs o carpetas relevantes>.
- Considera <reglas del repo, AGENTS.md, restricciones del curso>.

Problema:
<describe claramente qué falla o qué se necesita construir>

Contexto:
<explica dónde ocurre, por qué importa y qué flujo de usuario o sistema afecta>

Restricciones:
- Mantener el cambio pequeño y revisable.
- Reusar patrones existentes del repo.
- No agregar dependencias sin justificar.
- No tocar archivos no relacionados.
- No guardar secretos, tokens ni credenciales.

Resultado esperado:
<describe qué debe quedar funcionando o documentado>

Formato de salida:
<opcional: tabla, checklist, diff summary, plan, JSON, markdown, etc.>

Criterios de éxito:
- <criterio verificable 1>
- <criterio verificable 2>
- <criterio verificable 3>

Antes de editar:
1. Lee los archivos relevantes.
2. Resume el flujo actual.
3. Propone un plan corto.
4. Indica qué archivos tocarás.
5. Indica cómo validarás el cambio.

Después de editar:
1. Resume archivos modificados.
2. Muestra checks ejecutados.
3. Indica riesgos o pendientes.

Review final:
- Verifica que el cambio cumple los criterios de éxito.
- Revisa que no haya cambios fuera de alcance.
- Revisa que no se hayan agregado secretos ni credenciales.
- Si no pudiste validar algo, dilo explícitamente.
```

## Ejemplo de aplicación

```text
Rol:
Actúa como desarrollador Rails senior.

Conocimiento esperado:
- Lee app/models/task.rb, app/controllers/tasks_controller.rb y config/routes.rb.
- Considera las reglas del repo en AGENTS.md.

Problema:
El filtro de tareas por estado no está funcionando. Al seleccionar "completadas", la vista sigue mostrando tareas pendientes.

Contexto:
El problema ocurre en la pantalla de listado de tareas. Afecta a usuarios que necesitan revisar el avance del equipo por estado.

Restricciones:
- Mantener el cambio pequeño y revisable.
- Reusar patrones existentes del repo.
- No agregar dependencias.
- No tocar archivos no relacionados.
- No guardar secretos, tokens ni credenciales.

Resultado esperado:
El listado debe filtrar correctamente tareas pendientes y completadas según el parámetro recibido.

Formato de salida:
Resumen breve con archivos modificados, validación ejecutada y riesgos.

Criterios de éxito:
- Al pedir tareas completadas, no aparecen tareas pendientes.
- Al pedir tareas pendientes, no aparecen tareas completadas.
- Si no se entrega filtro, se mantiene el comportamiento actual.

Antes de editar:
1. Lee los archivos relevantes.
2. Resume el flujo actual.
3. Propone un plan corto.
4. Indica qué archivos tocarás.
5. Indica cómo validarás el cambio.

Después de editar:
1. Resume archivos modificados.
2. Muestra checks ejecutados.
3. Indica riesgos o pendientes.

Review final:
- Verifica que el cambio cumple los criterios de éxito.
- Revisa que no haya cambios fuera de alcance.
- Revisa que no se hayan agregado secretos ni credenciales.
- Si no pudiste validar algo, dilo explícitamente.
```
