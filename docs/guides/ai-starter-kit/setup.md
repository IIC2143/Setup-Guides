# 00 - Setup

Objetivo: dejar el repositorio y al menos una herramienta de IA en terminal lista para una primera tarea asistida.

Estado de referencia: verificado el 28 de julio de 2026 contra documentación pública de GitHub, OpenCode, OpenAI, Anthropic y NVIDIA.

## 1. Preparar cuentas

Mínimo recomendado:

- GitHub Student: <https://github.com/settings/education/benefits>
- GitHub Copilot: <https://github.com/settings/copilot>

Opcional para más créditos/modelos:

- NVIDIA Build: crea una cuenta en <https://build.nvidia.com/> y genera un `NVIDIA_API_KEY`.

No subas tokens ni claves al repositorio. Usa variables de entorno locales.

## 2. Clonar el repositorio

Usa tu fork personal del template.

```bash
git clone <URL_DE_TU_REPO>
cd <NOMBRE_DEL_REPO>
git status
```

Resultado esperado: `git status` muestra una rama limpia antes de empezar.

## 3. Preparar entorno

Linux, macOS y WSL pueden seguir los mismos comandos base. En Windows, instala WSL primero y trabaja dentro de Ubuntu:

```powershell
wsl --install
```

Luego abre Ubuntu y continúa con los pasos de Linux.

### Base de datos local

Para preparar la app sin borrar datos locales:

```bash
bin/rails db:prepare
```

Si necesitas volver a un estado limpio:

```bash
bin/rails db:reset
```

`db:reset` borra la base de datos del ambiente actual, la crea de nuevo, carga el esquema y ejecuta `db/seeds.rb`. Úsalo solo para desarrollo o test; no para producción.

## 4. Instalar herramientas

Instala solo las que vayas a probar.

OpenCode:

```bash
curl -fsSL https://opencode.ai/install | bash
```

Alternativas:

```bash
npm i -g opencode-ai
brew install anomalyco/tap/opencode
```

Verifica:

```bash
opencode --version
```

GitHub Copilot CLI:

```bash
npm install -g @github/copilot
gh auth login --web
gh copilot
```

Codex:

```bash
npm install -g @openai/codex
codex
```

Claude Code:

```bash
npm install -g @anthropic-ai/claude-code
claude
```

### Comandos útiles para partir

Los comandos cambian con el tiempo. Usa `--help` o escribe `/` dentro de cada herramienta para ver la lista completa disponible en tu instalación. Estos son los comandos que conviene conocer primero para trabajar en el curso.

#### OpenCode

| Comando | Para qué sirve | Cuándo usarlo |
| --- | --- | --- |
| `opencode` | Abre OpenCode en el directorio actual. | Al comenzar una tarea desde la raíz del repo. |
| `/init` | Inicializa o mejora instrucciones del proyecto. | Cuando el repo no tiene contexto para el agente o está incompleto. |
| `/help` | Muestra comandos disponibles. | Cuando no recuerdas cómo cambiar modo, modelo o flujo. |
| `/undo` | Revierte el último cambio hecho por el agente. | Cuando aceptaste una edición equivocada. |
| `/share` | Comparte la sesión o genera una referencia de trabajo, según configuración. | Cuando necesitas mostrar una conversación o diagnóstico al equipo. |

#### GitHub Copilot CLI

| Comando | Para qué sirve | Cuándo usarlo |
| --- | --- | --- |
| `copilot` | Abre la interfaz interactiva. | Al trabajar con un agente dentro del repo. |
| `copilot login` | Autentica tu cuenta de GitHub/Copilot. | Primera configuración o cambio de cuenta. |
| `copilot init` | Genera o actualiza `.github/copilot-instructions.md`. | Cuando el repo necesita instrucciones para Copilot. |
| `copilot plugins list` | Lista plugins, MCPs, skills e instrucciones detectadas. | Para verificar qué contexto está cargando Copilot. |
| `/diff` | Revisa cambios del directorio actual dentro de la sesión. | Antes de aceptar o cerrar una tarea con cambios. |

#### Codex

| Comando | Para qué sirve | Cuándo usarlo |
| --- | --- | --- |
| `codex` | Abre Codex en modo interactivo. | Al iniciar una tarea asistida desde el repo. |
| `codex exec "..."` | Ejecuta una tarea no interactiva y termina. | Para preguntas o revisiones puntuales. |
| `codex resume` | Retoma una conversación anterior. | Cuando quieres continuar una tarea sin repetir contexto. |
| `/init` | Crea o actualiza `AGENTS.md` con contexto del proyecto. | Al preparar un repo nuevo o incompleto. |
| `/status` | Muestra modelo, permisos, sandbox y estado de sesión. | Antes de permitir acciones o diagnosticar comportamiento raro. |

#### Claude Code

| Comando | Para qué sirve | Cuándo usarlo |
| --- | --- | --- |
| `claude` | Abre Claude Code en modo interactivo. | Al iniciar trabajo desde la raíz del repo. |
| `claude "..."` | Abre una sesión con un prompt inicial. | Para entrar directo a una tarea concreta. |
| `claude -p "..."` | Ejecuta una consulta, imprime respuesta y sale. | Para análisis rápido o uso en scripts. |
| `claude -c` | Continúa la conversación más reciente del directorio. | Cuando cerraste la terminal y quieres retomar. |
| `/plan` | Entra en modo planificación sin editar archivos. | Antes de una tarea ambigua o riesgosa. |

## 5. Configuración compartida

Un agente de IA no solo responde al prompt que escribes en el momento. También puede leer instrucciones persistentes del repositorio y de tu computador. Esa diferencia importa porque el repo se comparte con tu equipo y con el curso, mientras que tu carpeta `$HOME` es personal.

Para este curso, deja versionado solo lo que ayude a todos a trabajar igual: comandos para levantar la app, checks, criterios de entrega, convenciones de commits, reglas de seguridad y recordatorios para no subir claves. Deja fuera de Git lo que depende de tu cuenta, tu máquina o tus permisos: tokens, API keys, rutas locales, modelos preferidos, MCPs privados y configuraciones personales.

Regla práctica para estudiantes: si una instrucción debe aplicar a todo el equipo, va en el repo. Si solo aplica a ti, va en tu configuración local.

### Archivos de instrucciones

No todos los agentes leen los mismos archivos ni con la misma prioridad. Por eso este repo usa `AGENTS.md` como fuente común y archivos puente cuando una herramienta espera otro nombre.

| Archivo | Para qué sirve en este repo |
| --- | --- |
| `AGENTS.md` | Instrucciones compartidas del proyecto: contexto del curso, comandos, estilo, seguridad y flujo esperado. Codex lo lee de forma nativa. Copilot CLI y OpenCode también pueden usarlo como instrucciones de proyecto. |
| `CLAUDE.md` | Archivo esperado por Claude Code. En este repo solo importa `@AGENTS.md` para evitar duplicar reglas. |
| `.github/copilot-instructions.md` | Archivo propio de GitHub Copilot. Si el equipo usa Copilot CLI de forma intensiva, puede importar o resumir las mismas reglas de `AGENTS.md`. |

No asumas que cualquier carpeta oculta del repo reemplaza tu configuración personal. Cada herramienta tiene sus propias reglas de precedencia.

### Configuración por herramienta

| Herramienta | Configuración del repo | Configuración local |
| --- | --- | --- |
| Codex | `AGENTS.md`; opcional `.codex/config.toml` para configuración del proyecto cuando el repo es confiable. | `~/.codex/config.toml`, `~/.codex/AGENTS.md`, auth, sesiones y preferencias personales. |
| Claude Code | `CLAUDE.md`, `.claude/settings.json`, `.claude/skills/`, `.claude/agents/` si el equipo decide compartirlas. | `~/.claude/CLAUDE.md`, `~/.claude/settings.json`, `~/.claude/skills/` y preferencias personales. |
| OpenCode | `opencode.json`, `.opencode/agents/`, `.opencode/skills/` si el repo necesita una configuración común. | Configuración personal de OpenCode y claves de proveedores fuera del repo. |
| GitHub Copilot CLI | `.github/copilot-instructions.md`, `.github/instructions/*.instructions.md`, `.github/agents/` y `.github/skills/` si el equipo las usa. | `~/.copilot/settings.json`, `~/.copilot/copilot-instructions.md`, skills, agentes y MCPs personales. |

Para una primera entrega, no necesitas configurar todo. Basta con que el repo tenga instrucciones comunes claras y que la herramienta elegida pueda leerlas.

### Cómo leer esta decisión

Ejemplos que sí pueden ir al repo:

- `bin/ci` es el comando esperado antes de abrir un PR.
- No se deben commitear tokens, claves ni credenciales.
- Las tareas no triviales deben partir desde un spec y un plan.
- Los PRs deben apuntar a `main` y pasar CI antes de mergear.

Ejemplos que no deben ir al repo:

- `OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, `NVIDIA_API_KEY` o `RENDER_API_KEY`.
- Tu modelo favorito para trabajar.
- Rutas absolutas de tu computador.
- MCPs conectados a tus cuentas personales.

Si una herramienta genera archivos de configuración, revisa antes de commitear. La pregunta no es "¿funciona en mi máquina?", sino "¿esto debería aplicarle a todo el equipo?".

## 6. Conectar proveedores

OpenCode no depende de GitHub Student. Conéctalo con el proveedor que tengas disponible:

```bash
opencode
```

Dentro de OpenCode usa:

```text
/connect
```

Opciones útiles:

- `NVIDIA`: usa el `NVIDIA_API_KEY` de Build para probar modelos gratuitos.
- `Anthropic`, `OpenAI` u otro proveedor: usa una clave personal si tienes créditos.

También puedes dejar NVIDIA como variable local:

```bash
export NVIDIA_API_KEY="nvapi-..."
```

## 7. Probar el agente

Desde la raíz del repositorio:

```bash
opencode
```

Prompt de prueba:

```text
Lee el README y dime qué comandos mínimos necesito para levantar este proyecto. No edites archivos.
```

Resultado esperado:

- La herramienta responde usando contexto del repositorio.
- No aparecen errores de autenticación.
- No se crean cambios en Git.

Comprueba:

```bash
git status
```

## 8. Checklist de entrega

- [ ] Fork creado.
- [ ] Repositorio clonado.
- [ ] Entorno Linux, macOS o WSL listo.
- [ ] GitHub Education activo.
- [ ] Copilot CLI, OpenCode, Codex o Claude instalado.
- [ ] Al menos una herramienta autenticada.
- [ ] La herramienta elegida lee instrucciones del repo (`AGENTS.md`, `CLAUDE.md` o `.github/copilot-instructions.md`, según corresponda).
- [ ] Prompt de prueba ejecutado sin editar archivos.

## Fuentes

- GitHub Docs: <https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/enable-copilot/set-up-for-students>
- GitHub Copilot CLI: <https://docs.github.com/en/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli>
- GitHub Copilot CLI reference: <https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-command-reference>
- GitHub Copilot custom instructions: <https://code.visualstudio.com/docs/agent-customization/custom-instructions>
- OpenCode providers: <https://opencode.ai/docs/providers>
- OpenCode agents: <https://opencode.ai/docs/agents/>
- OpenCode commands: <https://opencode.ai/docs/commands/>
- OpenCode config: <https://dev.opencode.ai/docs/config>
- Codex CLI: <https://www.npmjs.com/package/@openai/codex>
- Codex config: <https://learn.chatgpt.com/docs/config-file/config-basic>
- Codex environment variables: <https://learn.chatgpt.com/docs/config-file/environment-variables>
- Codex AGENTS.md: <https://learn.chatgpt.com/docs/agent-configuration/agents-md>
- Codex getting started: <https://help.openai.com/en/articles/11096431>
- Claude Code: <https://docs.anthropic.com/en/docs/claude-code/getting-started>
- Claude Code CLI reference: <https://code.claude.com/docs/en/cli-usage>
- Claude Code cheatsheet: <https://support.claude.com/en/articles/14553413-claude-code-cheatsheet>
- Claude Code settings: <https://docs.anthropic.com/en/docs/claude-code/settings>
- Claude memory: <https://support.claude.com/en/articles/14553240-give-claude-context-claude-md-and-better-prompts>
- NVIDIA Build: <https://build.nvidia.com/>
