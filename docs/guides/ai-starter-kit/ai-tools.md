# AI Tools

Objetivo: usar IA con contexto suficiente, permisos mínimos y cambios revisables.

## Cómo trabajar con un agente sin perder control

Un agente de IA puede leer archivos, proponer cambios, editar código y ejecutar comandos. Eso lo hace útil, pero también significa que no debes tratarlo como un chat aislado. En el curso, el agente debe trabajar dentro del mismo flujo que usaría una persona del equipo: entender el problema, revisar el contexto, proponer una solución chica, editar solo lo necesario y validar el resultado.

Flujo recomendado:

1. Da contexto suficiente: problema, archivos relevantes, restricciones y criterio de éxito.
2. Pide lectura antes de editar: el agente debe decir qué encontró y qué tocará.
3. Mantén el alcance chico: una tarea, un flujo, una validación.
4. Revisa el diff: no aceptes cambios que no entiendes.
5. Ejecuta el check más cercano: test, lint, `bin/ci` o revisión manual, según el cambio.
6. Cierra con evidencia: archivos modificados, checks ejecutados y riesgos pendientes.

La IA no reemplaza la revisión. Sirve para acelerar lectura, implementación y diagnóstico, pero el estudiante sigue siendo responsable del cambio que sube al PR.

## AGENTS.md

`AGENTS.md` es el contexto durable del repo. Sirve para que distintos agentes partan con las mismas reglas: stack, comandos, estilo, restricciones y flujo de trabajo.

No pongas secretos, tokens ni datos personales.

Piensa este archivo como la pauta de trabajo del proyecto, no como un prompt gigante. Debe ayudar a que cualquier estudiante o agente entienda cómo trabajar en el repo sin preguntar lo mismo en cada tarea.

Qué conviene poner:

- Cómo levantar, testear y revisar el proyecto.
- Reglas de seguridad: no subir claves, pedir permiso para acciones destructivas.
- Convenciones del curso: cambios pequeños, diffs revisables, criterios de entrega.
- Contexto que un estudiante o agente tendría que leer en cada tarea.

Qué no conviene poner:

- Tokens, API keys o credenciales.
- Preferencias personales de una máquina.
- Instrucciones largas para una tarea única.

Claude Code usa `CLAUDE.md`, por eso este repo tiene un `CLAUDE.md` que importa `@AGENTS.md`. Así evitamos duplicar reglas.

Ejemplo de buena regla para `AGENTS.md`:

```text
Después de cambios de código, corre el chequeo más chico que valide el cambio.
```

Ejemplo de mala regla para `AGENTS.md`:

```text
Para mi usuario local, usa siempre /home/sofia/proyectos y mi token personal.
```

La primera regla sirve al equipo. La segunda mezcla ruta personal y secreto; debe quedarse fuera del repo.

## Prompting de tarea

Usa prompts largos solo para la tarea actual. Cuando una instrucción se repite en varias tareas, considera moverla a `AGENTS.md`.

Template base: `docs/template/ai-task-prompt-template.md`.

Un buen prompt no necesita ser largo, pero sí debe reducir ambigüedad. Si escribes "arregla el login", el agente tiene que adivinar qué falla, dónde mirar, qué no tocar y cómo validar. Si le das contexto, puede trabajar con menos vueltas y menos cambios fuera de alcance.

Estructura mínima de un buen prompt:

- Rol: qué tipo de agente necesitas.
- Conocimiento esperado: qué debe leer o considerar.
- Problema: qué hay que resolver.
- Contexto: dónde ocurre y por qué importa.
- Restricciones: qué no puede tocar, cambiar o asumir.
- Resultado esperado: qué debe entregar.
- Output: formato de respuesta, si importa.
- Criterios de éxito: cómo sabremos que quedó bien.
- Review: qué debe verificar antes de cerrar.

Usa esta estructura para un cambio one-shot o como input inicial para crear un spec.

Ejemplo débil:

```text
Arregla el login.
```

Ejemplo útil:

```text
Lee app/controllers/sessions_controller.rb y test/controllers/sessions_controller_test.rb.
El login falla cuando el email viene con espacios al inicio o al final.
Corrige solo ese caso, mantén el cambio pequeño y valida con el test más cercano.
Antes de editar, dime qué flujo encontraste y qué archivo tocarás.
```

Si la tarea tiene más de un flujo, criterios poco claros o casos borde relevantes, no fuerces un prompt one-shot. Usa Spec Kit para acordar primero el problema, alcance, plan y validación.

## Skills y workflows

Una skill es una receta reutilizable para una familia de tareas. Agrégala solo si el equipo la usará varias veces.

Template base: `docs/template/skill-template.md`.

Una skill no es solo un prompt guardado. Es un procedimiento que el agente puede cargar cuando reconoce una tarea repetible. Por eso debe explicar cuándo usarla, qué contexto leer, qué pasos seguir y qué revisar antes de cerrar.

Ejemplos razonables:

- Revisar un PR.
- Preparar una entrega.
- Diagnosticar un test fallido.

Ejemplos que no conviene guardar:

- Una pregunta puntual.
- Un bug único.
- Una preferencia personal no compartida.

Dónde pueden vivir:

- Proyecto: `.agents/skills/<nombre>/SKILL.md`, `.claude/skills/<nombre>/SKILL.md` o `.opencode/skills/<nombre>/SKILL.md`.
- Personal: `~/.agents/skills/`, `~/.claude/skills/`, `~/.config/opencode/skills/` o `~/.codex/skills/`.

Estructura mínima de una buena skill:

- `name`: nombre corto en kebab-case.
- `description`: qué hace y cuándo usarla. Es el dato más importante para que el agente decida si debe cargarla.
- Objetivo: tarea repetible que resuelve.
- Contexto que debe leer: archivos, carpetas o referencias necesarias.
- Flujo de trabajo: pasos concretos.
- Preguntas necesarias: cuándo debe pedir aclaración antes de avanzar.
- Gates: condiciones antes de editar y antes de cerrar.
- Recursos opcionales: `scripts/`, `references/` o `assets/` solo si aportan valor real.

Regla práctica: primero escribe una skill solo con `SKILL.md`. Agrega `references/` si el contexto es largo, `scripts/` si el paso debe ser determinístico, y `assets/` si necesitas plantillas o archivos base.

Repos útiles para explorar:

- `openai/skills`: skills para Codex.
- `anthropics/skills`: skills compatibles con Claude.
- `github/awesome-copilot`: colección comunitaria para Copilot.

Crea una skill propia cuando notes que estás copiando el mismo prompt, checklist o procedimiento en varias tareas.

Antes de versionar una skill, pruébala como prompt normal dos o tres veces. Si el equipo la repite y mejora la entrega, recién ahí vale la pena guardarla. Si solo sirve para una tarea puntual, déjala como prompt de esa tarea.

## MCP y permisos

MCP conecta el agente con herramientas externas: GitHub, Notion, bases de datos, navegadores o APIs internas.

Un MCP amplía lo que el agente puede hacer. Ya no solo lee el repo: puede consultar servicios, abrir issues, revisar información externa o interactuar con datos. Por eso la regla no es "conectar todo", sino conectar solo lo que la tarea necesita.

Regla mínima:

- Leer antes que escribir.
- Pedir permiso antes de acciones destructivas.
- No entregar acceso amplio si basta acceso al repositorio.
- Desconectar integraciones que no se usarán en la tarea.

Referencia de riesgo:

| Acción | Riesgo | Qué hacer |
| --- | --- | --- |
| Leer información externa o listar recursos | Bajo | Permitido si ayuda a diagnosticar. |
| Crear un issue, comentar un PR o modificar un recurso externo | Medio | Confirmar antes de ejecutar. |
| Cambiar variables de entorno, modificar permisos o borrar recursos | Alto | Evitar salvo instrucción explícita del equipo/profesor. |

Si un agente pide permisos amplios para una tarea simple, baja el alcance. Para diagnosticar un problema, normalmente basta leer información; no necesita modificar variables ni ejecutar acciones externas automáticamente.

## Modelo y costo

Empieza con el modelo recomendado por la herramienta. Sube a un modelo más capaz o a mayor esfuerzo solo cuando:

- El agente no entiende el código.
- La tarea cruza varios archivos.
- Hay decisiones de arquitectura.
- La primera respuesta falla de forma clara.

Regla práctica:

| Caso | Tipo de modelo | Uso esperado |
| --- | --- | --- |
| Pregunta corta o cambio trivial | Económico, esfuerzo bajo | Respuestas rápidas, bajo costo |
| Trabajo diario de código | Balanceado, esfuerzo medio | Buen equilibrio entre calidad, velocidad y costo |
| Problema difícil o refactor grande | Avanzado, esfuerzo alto | Más razonamiento para decisiones complejas |
| Mucho contexto | Contexto largo | Leer repos, trazas o documentos extensos |

No elijas por nombre solamente: revisa la documentación actual de la herramienta y elige según dificultad, contexto y costo. Para una tarea chica, un modelo robusto puede ser desperdicio; para una tarea ambigua o de arquitectura, un modelo barato puede salir caro si falla y hay que repetir.

Costo no es solo dinero. También cuenta el tiempo perdido en respuestas malas, cambios grandes de revisar y prompts repetidos sin mejorar el contexto. Si el agente falla porque la tarea era ambigua, cambiar de modelo no siempre arregla el problema. Primero mejora el prompt, reduce el alcance o crea un spec.

## Cuando el agente se equivoca

No aceptes una respuesta solo porque parece segura. Señales de alerta:

- Edita archivos que no mencionaste y no explica por qué.
- Cambia formato, dependencias o arquitectura para resolver un bug chico.
- No puede decir qué test o check valida el cambio.
- Ignora restricciones del curso o del repo.
- Propone guardar tokens, claves o configuración personal.

Qué hacer:

1. Detén la edición o pide que no modifique más archivos.
2. Pide resumen del flujo actual y del diff.
3. Reduce la tarea a un caso concreto.
4. Vuelve al archivo o test más cercano.
5. Si el cambio ya quedó mal, revisa `git diff` y revierte solo lo incorrecto.

La respuesta correcta no siempre es "usa un modelo mejor". Muchas veces es "dale menos alcance y mejor contexto".

## Prompt de uso responsable

Este prompt sirve como freno básico antes de permitir ediciones. Úsalo al iniciar tareas donde el agente podría modificar archivos:

```text
Trabaja con cambios pequeños. Antes de modificar archivos, dime qué leíste y qué tocarás. Después de editar, muestra el resumen del diff y los checks ejecutados.
```

Por qué funciona:

- Obliga al agente a leer antes de actuar.
- Deja claro que los cambios deben ser pequeños.
- Hace visible qué archivos se tocaron.
- Exige evidencia de validación.

## Fuentes

- Codex AGENTS.md: <https://learn.chatgpt.com/docs/agent-configuration/agents-md>
- OpenAI skills catalog: <https://github.com/openai/skills>
- OpenCode skills: <https://opencode.ai/docs/skills/>
- OpenCode agents: <https://opencode.ai/docs/agents/>
- OpenCode MCP: <https://opencode.ai/v2/docs/mcp-servers>
- GitHub agent skills: <https://docs.github.com/en/copilot/concepts/agents/about-agent-skills>
- Claude custom skills: <https://support.claude.com/en/articles/12512198-how-to-create-custom-skills>
- Claude Code skills: <https://code.claude.com/docs/en/slash-commands>
- Claude models: <https://code.claude.com/docs/en/model-config>
- OpenAI model guidance: <https://developers.openai.com/api/docs/guides/latest-model>
