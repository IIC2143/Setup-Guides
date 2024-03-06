# Manual de Instalación

## 1. Instalar WSL

**¿Estás usando Windows?** 

Para que no tengas problemas en este y próximos cursos deberás descargar [WSL o WSL2](https://es.wikipedia.org/wiki/Windows_Subsystem_for_Linux) (Windows Subsystem for Linux). Para esto existen diferentes guías:
* [Guía oficial](https://docs.microsoft.com/en-us/windows/wsl/install-win10)
* [Tutorial de Youtube](https://www.youtube.com/watch?v=n-J9438Mv-s)

La diferencia entre WSL y WSL2 es la compatibilidad con la versión de Windows.

Luego, les recomendamos que instalen, desde Microsoft Store, algún lanzamiento de Ubuntu desde la versión 18.04 para utilizarlo como consola, [esta por ejemplo](https://www.microsoft.com/store/productId/9N6SVWS3RX71).

De ahora en adelante, al referirnos a "la consola" o "el terminal", aquellos utilizando Windows deben utilizar la consola de Ubuntu.

## 2. Dependencias

Instalar las dependencias necesarias. Para eso, desde la consola, se deben ejecutar los siguientes comandos:

```bash
sudo apt-get update
sudo apt-get install build-essential patch liblzma-dev libpq-dev
```

## 3. Ruby Version Manager

Instalar _Ruby Version Manager_, también conocido como _rvm_. Para eso, ejecutar los siguientes comandos desde la consola:

```bash
sudo apt install gnupg2
gpg --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
curl -sSL https://get.rvm.io | bash -s stable
```

Con el primero comando instalamos GNU Privacy Guard, que es una herramienta de cifrado y firmas digitales. Con el segundo comando instalamos una llave que se utiliza (_automágicamente_) para verificar el paquete a instalar. Con el segundo comando instalamos _rvm_.

Para verificar que _rvm_ se instaló correctamente, **reinicia tu consola** y corre:
```bash
rvm -v
```

## 4. PostgresSQL

Instalar PostgreSQL en tu máquina. Para eso, ejecutaremos el siguiente comando:

```bash
sudo apt-get install postgresql postgresql-contrib
```

## 5. Ruby y Rails

Instalar Ruby 3.1.0 e instalar Ruby on Rails 7.0.4

```bash
rvm install 3.1.0
gem install rails -v 7.0.4
```

## 6. Node

Ahora tenemos que instalar Node (un [_runetime_](https://en.wikipedia.org/wiki/Runtime_library) de JavaScript), para lo que utilizaremos _Node Version Manager_ o _nvm_ (las versiones más recientes de RoR necesitan Node).

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash
```

Reinicia tu consola y luego corre

```bash
nvm install --lts
```


## 7. PostgreSQL: superuser

Ahora lo último antes de iniciar el proyecto! Debemos crear un nuevo `superuser` de PostgreSQL. Para esto primero debemos iniciar un servicio de PostgreSQL, ejecutando el siguiente comando:

```bash
sudo service postgresql start
```

Ahora ingresamos a la consola de PostgreSQL como usuario `postgres` (que viene creado junto con la instalación):

```bash
sudo -u postgres psql
```

Luego, creamos un nuevo usuario. Para mantener consistencia entre todos y para que nadie tenga problemas relacionados al método de autenticación por default que les viene con la instalación, **vamos a crear un usuario con el mismo nombre que el usuario de tu sistema** (acá tomaremos como ejemplo que mi usuario se llama myuser):

```bash
CREATE USER myuser WITH SUPERUSER PASSWORD 'mypassword';
```

Ojo con que la contraseña esté entre comillas simples.

Para salir de la consola de postgres corre `\q`.

## 8. ¡Crear proyecto!

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

## 9. .env

Ahora vamos a abrir el proyecto recién creado en nuestro editor favorito. Si tienes VS Code puedes correr `code .`.

Ahora que ya tenemos los archivos del proyecto, necesitamos setear la configuración de la base de datos que ocuparemos. 

Primero crea en la raiz de tu proyecto (directamente adentro de la carpeta `<nombre proyecto>`) un archivo llamado `.env`  y adentro escribe las credenciales del usuario que creaste en el paso 7:

```env
# no se usan comillas
DB_USER=myuser
DB_PASSWORD=mypassword
```
Recuerda cambiar myuser y mypassword por tu usuario y contraseña!

## 10. .gitignore

Para evitar subir tu archivo .env a GitHub, debes agregarlo a tu archivo .gitignore. Para eso, desde la consola, corre:

```bash
echo ".env" >> .gitignore
```
Tambien incluiremos la carpeta `node_modules` en el archivo `.gitignore`

```bash
echo "node_modules" >> .gitignore
```

## 11. database.yml

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

## 12. Crear base de datos

Necesitamos una base de datos para tu app! El siguiente comando creará una por ti:

```bash
rails db:create
```

## 13. ¡Correr el servidor!

Ahora utilizando tu consola, corre el servidor del proyecto con el siguiente comando:

```
rails server
```

Ahora que tienes el servidor corriendo, dirígete a http://localhost:3000/ y debería aparecernos el logo de Ruby on Rails.
