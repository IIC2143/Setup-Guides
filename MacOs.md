# Manual de Instalación
Se recomienda a los usuarios de Mac M1 o M2, que esta instalacion se haga en un terminal de Rosetta.
## 1. Homebrew

Primero verifica si tienes _Homebrew_ instalado ejecutando:

```bash
brew -v
```

Si es que no lo tienes instalado, ejecuta:

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

## 2. Ruby Version Manager

Luego instalermos _Ruby Version Manager_, también conocido como _rvm_. Para eso, ejecuta los siguientes comandos desde la consola:

```bash
brew install gnupg2
gpg --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
curl -sSL https://get.rvm.io | bash -s stable
```

Con el primero comando instalamos GNU Privacy Guard, que es una herramienta de cifrado y firmas digitales. Con el segundo comando instalamos una llave que se utiliza (_automágicamente_) para verificar el paquete a instalar. Con el tercero comando instalamos _rvm_.

Para verificar que _rvm_ se instaló correctamente, reincia tu consola y corre:
```bash
rvm -v
```

## 3. PostgreSQL

Ahora instala PostgreSQL en tu máquina. Para eso, ejecuta el siguiente comando:

```bash
brew install postgresql
```

## 4. Ruby y Rails

Instala Ruby 3.1.0 y Ruby on Rails 7.0.4

```bash
rvm install 3.1.0
gem install rails -v 7.0.4
```

## 5. PostgreSQL: superuser

Ahora lo último antes de iniciar el proyecto! Debemos crear un nuevo `superuser` de PostgreSQL. Para esto primero debemos iniciar un servicio de PostgreSQL, ejecutando el siguiente comando:

```bash
brew services start postgresql
```

Ahora ingresamos a la consola de PostgreSQL:

```bash
psql postgres
```

Luego creamos un nuevo usuario. Para mantener consistencia entre todos y para que nadie tenga problemas relacionados al método de autenticación por default que les viene con la instalación, **vamos a crear un usuario con el mismo nombre que el usuario de tu sistema** (acá tomaremos como ejemplo que mi usuario se llama myuser).

Ahora, es posible que este usuario ya exista. Para verificar esto corre `\du` y revisa si ya está creado. En el caso de que no aparezca, ejecuta:

```bash
CREATE USER myuser WITH SUPERUSER PASSWORD 'mypassword';
```

Ojo con que la contraseña esté entre comillas simples.

Para salir de la consola de postgres corre `\q`.

## 6. Node

Ahora tenemos que instalar Node (un runetime de JavaScript): (las versiones más recientes de RoR necesitan Node).

```bash
brew install node
```

Reinicia tu consola y luego corre

```bash
npm -v
```

## 7. ¡Crear proyecto!

Ya tenemos todo para iniciar nuestro proyecto. Para esto, dirígete a la consola y ejecuta el siguiente comando:

```bash
rails new <nombre proyecto> --database=postgresql
```

Ahora nos movemos a nuestra carpeta del proyecto con:

```bash
cd <nombre proyecto>
```

Desde este punto en adelante, en nuestra consola siempre estaremos en el directorio del proyecto.

Y por último, corremos el siguiente comando que instalará sus dependencias del proyecto:

```bash
bundle install
```

## 8. .env

Ahora vamos a abrir el proyecto recién creado en nuestro editor favorito. Si tienes VS Code puedes correr `code .`.

Ahora que ya tenemos los archivos del proyecto, necesitamos setear la configuración de la base de datos que ocuparemos. 

Primero crea en la raiz de tu proyecto (directamente adentro de la carpeta `<nombre proyecto>`) un archivo llamado `.env`  y adentro escribe las credenciales del usuario que creaste en el paso 7:

```env
# no se usan comillas
DB_USER=myuser
DB_PASSWORD=mypassword
```
Recuerda cambiar myuser y mypassword por tu usuario y contraseña!

## 9. .gitignore

Para evitar subir tu archivo .env a GitHub, debes agregarlo a tu archivo .gitignore. Para eso, desde la consola, corre:

```bash
echo ".env" >> .gitignore
```
Tambien incluiremos la carpeta `node_modules` en el archivo `.gitignore`

```bash
echo "node_modules" >> .gitignore
```

## 10. database.yml

Luego dirígete dentro de tu proyecto a `config/database.yml` y reemplaza su contenido con lo siguiente:

```yaml
default: &default
  adapter: postgresql
  encoding: unicode
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: <%= ENV["DB_USER"] %>
  password: <%= ENV["DB_PASSWORD"] %>

development:
  <<: *default
  database: <nombre proyecto>_development

test:
  <<: *default
  database: <nombre proyecto>_test

production:
  <<: *default
  url: <%= ENV["DATABASE_URL"] %>
```

Recuerda reemplazar `<nombre proyecto>` por el nombre que elegiste para el proyecto.

## 11. Crear base de datos

Necesitamos una base de datos para tu app! El siguiente comando creará una por ti:

```bash
rails db:create
```

## 12. ¡Correr el servidor!

Ahora utilizando tu consola, corre el servidor del proyecto con el siguiente comando:

```
rails s
```

Ahora que tienes el servidor corriendo, dirígete a http://localhost:3000/ y debería aparecernos el el logo de Ruby on Rails.
