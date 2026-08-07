# Devise

Devise es una gema que nos permite agregar autenticación a nuestras aplicaciones Rails. Es una de las gemas más populares y utilizadas en el ecosistema de Ruby on Rails.

## Instalación

Para instalar Devise en un proyecto de Rails, debemos agregar la gema a nuestro Gemfile de la siguiente manera:

```ruby
gem "devise"
```

Luego ejecutamos en la consola:

```bash
bundle install
```

Esto instalará la gema en nuestro proyecto.

## Configuración

Para configurar Devise, debemos ejecutar el siguiente comando:

```bash
rails generate devise:install
```

Este comando creará un archivo de configuración en `config/initializers/devise.rb` y agregará una ruta en `config/routes.rb` para el controlador de Devise. Además, indicará una serie de pasos que debemos seguir para configurar Devise. A continuación detallaremos algunos de ellos y haremos una configuración personalizada.

Lo primero que debemos hacer es agregar la siguiente línea en `config/environments/development.rb`:

```ruby
  ...
config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }
  ...
```

La ubicación exacta no es crítica, pero es importante que se agregue en el ambiente de desarrollo.

A continuación, debemos ejecutar el siguiente comando:

```bash
rails generate devise:views
```

Este comando creará una serie de vistas en `app/views/devise` que nos permitirán personalizar la interfaz de usuario de Devise. Como Devise está pensado para autenticar usuarios, las vistas creadas serán para registro, inicio de sesión, edición de cuenta, entre otras.

## Generación de modelos

Ya teniendo el flujo de Devise configurado, podemos generar el modelo de usuario que queramos. Al final, un usuario es otra tabla o modelo en nuestra base de datos. Para esto, debemos ejecutar el siguiente comando:

```bash
rails generate devise User
```

Donde `User` es el nombre del modelo que queremos crear. Este comando creará el modelo respectivo en `app/models/user.rb` y una migración en `db/migrate` para crear la tabla `users` en nuestra base de datos. Esta migración trae varios atributos por defecto, como `email`, `encrypted_password`, `reset_password_token`, entre otros. Más adelante veremos cómo agregar más atributos a nuestro modelo de usuario.

## Migración

Luego de generar la migración, debemos ejecutarla para crear la tabla `users` en nuestra base de datos. Para esto, debemos ejecutar el siguiente comando:

```bash
rails db:migrate
```

## Vistas

Ya teniendo el modelo creado, vamos a personalizar un poco el flujo de Devise. Para esto, agregaremos algunos elementos a la vista `app/views/layouts/application.html.erb`. Dentro del `<body>`, antes de `<%= yield %>`, colocamos lo siguiente:

```html
  ...

  <body>

    <% if user_signed_in? %>
      <%= link_to "Edit", edit_user_registration_path %>
      <%= button_to "Sign out", destroy_user_session_path, method: :delete %>
    <% else %>
      <%= link_to "Sign in", new_user_session_path %>
      <%= link_to "Sign up", new_user_registration_path %>
    <% end %>

    <% if flash[:notice] %>
      <p class="notice"><%= flash[:notice] %></p>
    <% end %>

    <% if flash[:alert] %>
      <p class="alert"><%= flash[:alert] %></p>
    <% end %>

    <%= yield %>

  </body>

  ...
```

Esto nos permitirá mostrar enlaces en la parte superior de nuestra aplicación para iniciar sesión, registrarse, editar el perfil y cerrar sesión. Estos enlaces se mostrarán dependiendo de si el usuario está autenticado o no. Devise nos entrega una serie de métodos para saber si el usuario está autenticado y acceder a su información. Estos métodos son:

- `user_signed_in?`: Devuelve `true` si el usuario está autenticado, `false` en caso contrario.
- `current_user`: Devuelve el usuario autenticado.
- `user_session`: Devuelve la sesión del usuario autenticado.

Cabe destacar que, si nuestro modelo se llamara de otra forma, por ejemplo `Person`, los métodos serían `person_signed_in?`, `current_person` y `person_session`. Recomendamos usar el nombre `User`, ya que es el nombre por defecto que utiliza Devise.

## Turbo Failure App

Debido a cómo funciona Rails con Turbo, debemos agregar algunos pasos extra para que Devise funcione correctamente. Para esto, agregamos lo siguiente al inicio de `config/initializers/devise.rb`:

```ruby
# frozen_string_literal: true

class TurboFailureApp < Devise::FailureApp
  def respond
    if request_format == :turbo_stream
      redirect
    else
      super
    end
  end

  def skip_format?
    %w[html turbo_stream */*].include? request_format.to_s
  end
end
  ...

Devise.setup do |config|

...

```
Luego, en el mismo archivo, cerca de la línea 88, colocamos:

```ruby
  ...
  config.navigational_formats = ['*/*', :html, :turbo_stream]
  ...
```

  
Y al final, justo antes de la sección `==> Hotwire/Turbo configuration`, agregamos:

```ruby
  ...
  config.warden do |manager|
    manager.failure_app = TurboFailureApp
  end
  ...
end
```

Con esto, ya tenemos Devise funcionando correctamente con Turbo. Puedes reiniciar (o iniciar) el servidor de Rails y probar el flujo de autenticación.

## Atributos adicionales

Si queremos agregar atributos adicionales a nuestro modelo de usuario, debemos generar una nueva migración. Una migración es un archivo que permite modificar la estructura de una tabla en nuestra base de datos. Para generarla, ejecutamos el siguiente comando:

```bash
rails generate migration AddNameToUsers
```

En este caso queremos agregar el atributo `name` a nuestro modelo, por eso el nombre de la migración es `AddNameToUsers`. Luego, debemos abrir el archivo creado en `db/migrate` y agregar lo siguiente:

```ruby
class AddNameToUsers < ActiveRecord::Migration[7.0]
  def change
    add_column :users, :name, :string
  end
end
```

De forma resumida, estamos diciendo que queremos agregar una columna llamada `name` a la tabla `users`, y que el tipo de dato de esta columna es `string`. Luego, al igual que siempre, para que se apliquen los cambios en nuestra base de datos:

```bash
rails db:migrate
```

## Controlador

Por último, debemos agregar algunos métodos al controlador de usuarios. Para esto, primero generamos los controladores con el siguiente comando:

```bash
rails generate devise:controllers users
```

Para que nuestra aplicación use efectivamente estos controladores, debemos reemplazar dentro de `config/routes.rb`:

```ruby
  ...
  devise_for :users
  ...
```

Por:

```ruby
  ...
  devise_for :users, controllers: { sessions: 'users/sessions', registrations: 'users/registrations' }, 
                   path: '', path_names: {sign_in: 'login', sign_out: 'logout', sign_up: 'register'}
  ...
```

## Permitir atributos

Como el atributo `name` no viene por defecto en el modelo de usuario, debemos permitir que este atributo sea enviado desde el formulario de registro. Para esto, en `app/controllers/users/registrations_controller.rb` debemos descomentar las siguientes líneas:

```ruby
  ...
  before_action :configure_sign_up_params, only: [:create]
  before_action :configure_account_update_params, only: [:update]
  ...

  protected

  # If you have extra params to permit, append them to the sanitizer.
  def configure_sign_up_params
    devise_parameter_sanitizer.permit(:sign_up, keys: [:attribute])
  end

  # If you have extra params to permit, append them to the sanitizer.
  def configure_account_update_params
    devise_parameter_sanitizer.permit(:account_update, keys: [:attribute])
  end
```

Y reemplazar `:attribute` por `:name`. Si fueran varios atributos, por ejemplo `:username`, `:description`, etc., se deberían separar por comas, quedando de la siguiente forma:

```ruby
  def configure_sign_up_params
    devise_parameter_sanitizer.permit(:sign_up, keys: [:name, :username, :description])
  end

  # If you have extra params to permit, append them to the sanitizer.
  def configure_account_update_params
    devise_parameter_sanitizer.permit(:account_update, keys: [:name, :username, :description])
  end
```

## Formularios

Finalmente, debemos agregar el campo de nuestros nuevos atributos en las vistas. Para esto, abrimos `app/views/devise/registrations/new.html.erb` y agregamos lo siguiente entre los campos de `:password_confirmation` y `:submit`:

```html
  ...
  <div class="field">
    <%= f.label :name %><br />
    <%= f.text_field :name %>
  </div>
  ...
```

Repetimos lo mismo en `app/views/devise/registrations/edit.html.erb`.

También, en `app/views/layouts/application.html.erb`, editamos el bloque agregado anteriormente y lo dejamos de la siguiente forma:

```html
  ...
    <% if user_signed_in? %>
      <p>Logged in as <%= current_user.name %></p>
      <%= link_to "Edit", edit_user_registration_path %>
      <%= button_to "Sign out", destroy_user_session_path, method: :delete %>
    <% else %>
  ...
```
Es decir, agregamos `current_user.name` para mostrar el nombre del usuario autenticado.

## Prueba

Para probar que todo funciona correctamente, debemos reiniciar el servidor de Rails y probar el flujo de registro, inicio de sesión, edición de perfil y cierre de sesión. Si todo funciona correctamente, las vistas deberían cambiar las opciones de Login, Register, Logout y Edit, además de mostrar el nombre del usuario autenticado.
