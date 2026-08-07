# Prompt de tarea con IA

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
<opciónal: tabla, checklist, diff summary, plan, JSON, markdown, etc.>

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
- Si no pudiste validar algo, dilo explicitamente.
```
