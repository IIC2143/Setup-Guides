# 04 - CI/CD y Render

Objetivo: correr checks automáticos en cada PR y desplegar en Render solo desde `main`.

Esta guía es complementaria. Si tu sección del curso no usará deploy, puedes concentrarte en CI: checks locales, GitHub Actions, lectura de fallos y protección de `main`. La parte de Render muestra cómo se conecta el flujo con un despliegue real, pero no es necesaria para todas las entregas.

La separación importante:

- CI responde: "¿este cambio pasa los checks definidos por el equipo?".
- CD/deploy responde: "¿la versión aprobada llegó al ambiente publicado?".

Render no valida si el cambio es correcto. Solo despliega lo que llega a `main`. Por eso la calidad se controla antes, en el PR.

## Flujo esperado

1. Trabaja en una rama `feature/`, `fix/`, `docs/` o `chore/`.
2. Abre un PR hacia `main`.
3. GitHub Actions corre CI.
4. Si CI está verde, revisa y mergea a `main`.
5. Render detecta el cambio en `main`, ejecuta build y despliega.

No despliegues ramas de trabajo por defecto. Para este curso, `main` es la rama estable y la fuente del deploy.

## Checks locales

Antes de abrir PR, corre el mismo chequeo que usará CI:

```bash
bin/ci
```

Ese comando ejecuta:

- `bin/setup --skip-server`
- `bin/rails test`
- `bin/rubocop`
- `bin/brakeman --quiet --no-pager --exit-on-warn --exit-on-error`

Qué valida cada parte:

- `bin/rails test`: ejecuta las pruebas automatizadas. Sirve para confirmar que el comportamiento esperado sigue funcionando después de cada cambio.
- `bin/rubocop`: revisa estilo y errores comunes de Ruby/Rails. Sirve para mantener el código legible y consistente sin discutir formato en cada PR.
- `bin/brakeman`: analiza posibles problemas de seguridad en Rails, como uso inseguro de parámetros, SQL manual riesgoso o exposición accidental de datos.

Dónde se configuran:

- CI: `config/ci.rb` define los pasos que corre `bin/ci`.
- GitHub Actions: `.github/workflows/ci.yml` ejecuta `bin/ci` en PRs y pushes a `main`.
- RuboCop: `.rubocop.yml` hereda reglas de `rubocop-rails-omakase`; `bin/rubocop` fuerza ese archivo con `--config`.
- Brakeman: por ahora usa la configuración por defecto de la gema y las opciones definidas en `config/ci.rb`.

La separación importante: `config/ci.rb` define qué se valida; `.github/workflows/ci.yml` define cuándo y en qué ambiente se ejecuta en GitHub.

Si un equipo necesita ajustar RuboCop, edita `.rubocop.yml`. Si necesita ignorar un falso positivo real de Brakeman, puede crear `config/brakeman.ignore` con una nota que explique por qué se acepta ese riesgo.

No arregles CI apagando checks sin entender el fallo. Si un check molesta porque encontró un problema real, corrige el problema. Si es un falso positivo, deja una explicación revisable.

Si necesitas aislar un fallo:

```bash
bin/rails test
bin/rubocop
bin/brakeman
```

Durante el desarrollo, no esperes al PR para probar. Corre primero el test más cercano al cambio, por ejemplo:

```bash
bin/rails test test/models/product_test.rb
bin/rails test test/controllers/level_1_controller_test.rb
```

Cuando ese test pase, corre `bin/ci` antes de abrir o actualizar el PR. Esto evita subir cambios rotos y hace que GitHub Actions confirme algo que ya validaste localmente.

## GitHub Actions

El workflow vive en `.github/workflows/ci.yml` y corre en:

- cada PR;
- cada push a `main`.

El workflow prepara el ambiente remoto: Ubuntu, Ruby y PostgreSQL. Luego ejecuta `bin/ci`. No duplica los comandos de CI; reutiliza la misma receta que corres localmente.

La regla práctica: si falla CI, no se mergea. Primero se arregla el fallo en la misma rama del PR.

Para que GitHub bloquee el merge automáticamente, configura una regla sobre `main` con:

- Require a pull request before merging.
- Require status checks to pass before merging.
- Status check requerido: `Tests, style and security`.

## Leer fallos

Orden recomendado:

1. Primer error real.
2. Comando que falló.
3. Archivo y línea.
4. Diferencia entre local y CI.

Prompt útil:

```text
Analiza este fallo de CI. Dime la causa probable, el archivo a revisar y el cambio mínimo. No edites todavía.
```

No pegues todo el log si tiene cientos de líneas. Primero identifica el comando fallido y el primer error real. El resto del log suele ser consecuencia.

## Fallos comunes

| Síntoma | Causa probable | Primer paso |
| --- | --- | --- |
| `bin/rails test` falla local y en CI | Bug real o test desactualizado. | Corre el test más cercano y revisa el primer failure. |
| Pasa local pero falla en CI | Diferencia de ambiente, dependencia, orden de tests o base de datos. | Lee versión de Ruby/PostgreSQL y comando exacto en Actions. |
| RuboCop falla | Estilo o regla de Rails incumplida. | Corre `bin/rubocop` local y corrige el archivo indicado. |
| Brakeman falla | Posible riesgo de seguridad o falso positivo. | Lee la advertencia completa y revisa si hay input de usuario involucrado. |
| Render build falla | Dependencia, assets, comando de build o variable faltante. | Revisa etapa exacta: build, pre-deploy o start. |
| App deploya pero no arranca | Falta variable, migración o comando start incorrecto. | Revisa logs de runtime y variables requeridas. |

## Render

Este repo incluye un template de Blueprint en `render.yaml`. La idea es que la configuración de Render quede versionada y pueda revisarse en PR, igual que el código.

Flujo recomendado:

1. CI corre en cada PR.
2. Si CI está verde, se revisa y mergea a `main`.
3. Render sincroniza `render.yaml` desde `main`.
4. Render ejecuta build y despliega la app.

Antes de crear el Blueprint, cada equipo debe editar en `render.yaml`:

- `replace-me-web`: nombre del servicio web.
- `replace-me-db`: nombre de la base PostgreSQL.
- `plan`: tipo de instancia según el caso.

Configuración esperada en Render:

- Servicio conectado al repo de GitHub.
- Rama de deploy: `main`.
- Auto deploy activado para `main`.
- Build command: `./bin/render-build.sh`.
- Start command: `./bin/rails server`.
- Variables de entorno definidas en Render, no en Git.
- Base PostgreSQL creada desde el mismo `render.yaml`.
- `DATABASE_URL` conectado desde la base PostgreSQL al servicio web.

Render ejecuta build, luego pre-deploy y finalmente start. Si falla una etapa, el deploy falla y sigue viva la última versión correcta.

Render no decide si el cambio está correcto: despliega lo que llega a `main`. Por eso el control de calidad ocurre antes, en el PR con CI verde.

Nota: `Pre-deploy Command` requiere un servicio web pagado. Si usan plan pagado, descomenta `preDeployCommand: ./bin/rails db:migrate` en `render.yaml`. Si usan plan free, ejecuta migraciones manualmente desde Render Shell o mueve temporalmente `bundle exec rails db:migrate` al final de `bin/render-build.sh`.

El script `bin/render-build.sh` instala gems, compila assets y limpia assets antiguos. No guarda secretos ni configura producción.

Si el proyecto usa Docker, Render debe construir con el `Dockerfile` del repo y el build/start se definen desde la configuración del servicio.

Si el curso no evalúa deploy, no gastes tiempo ajustando Render antes de tener CI verde y PR revisable. Deploy sin checks solo publica incertidumbre.

## Variables de entorno

Mantén secretos fuera del repositorio. En Render configura, según corresponda:

- `RAILS_MASTER_KEY`
- `DATABASE_URL`
- claves de proveedores externos

Nunca copies valores reales de variables a `render.yaml`, README, PRs o screenshots. En el repo puede quedar el nombre de la variable; el valor vive en Render o en tu entorno local.

## Checklist

- [ ] `bin/ci` pasa localmente.
- [ ] PR apunta a `main`.
- [ ] GitHub Actions pasa.
- [ ] PR aprobado y mergeado.
- [ ] Si Render queda fuera del scope del curso, esa decisión está clara para el equipo.
- [ ] `render.yaml` tiene nombres propios del equipo.
- [ ] Render despliega desde `main`.
- [ ] `bin/render-build.sh` está configurado como build command.
- [ ] PostgreSQL de Render está conectado por `DATABASE_URL`.
- [ ] Variables de entorno están en Render, no en Git.
- [ ] URL de despliegue verificada.

## Fuentes

- Render deploys: <https://render.com/docs/deploys>
- Render Rails 8: <https://render.com/docs/deploy-rails-8>
- Render Blueprints: <https://render.com/docs/infrastructure-as-code>
- Render Blueprint YAML: <https://render.com/docs/blueprint-spec>
- GitHub rulesets: <https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets>
- RuboCop configuration: <https://docs.rubocop.org/rubocop/latest/configuration.html>
- RuboCop Rails: <https://docs.rubocop.org/rubocop-rails/latest/usage.html>
- Brakeman docs: <https://brakemanscanner.org/docs/>
- Brakeman options: <https://brakemanscanner.org/docs/options/>
