# Devise

Devise es una gema que nos permite agregar autenticación a nuestras aplicaciones Rails. Es una de las gemas más populares y utilizadas en el ecosistema de Ruby on Rails.

## Instalación

Pata instalar Devise en un proyecto de Rails, debemos agregar la gema a nuestro Gemfile de la siguiente manera:

```ruby
gem "devise"
```

Luego ejecutar en la consola:

```bash
bundle install
```

Esto instalará la gema en nuestro proyecto.

## Configuración

Para configurar Devise, debemos ejecutar el siguiente comando:

```bash
rails generate devise:install
```

Este comando nos creará un archivo de configuración en `config/initializers/devise.rb` y nos agregará una ruta en `config/routes.rb` para el controlador de Devise. Ademas, nos indicara una serie de pasos que debemos seguir para configurar Devise. A continuacion detallaremos un poco en esto, personalizando un poco la misma.

Lo primero que debemos hacer es agregar la siguiente linea en `config/environments/development.rb`:

```ruby
  ...
config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }
  ...
```

No importa mucho el lugar donde se agregue, pero es importante que se agregue en el ambiente de desarrollo.

A continuacion, debemos ejecutar el siguiente comando:

```bash
rails generate devise:views
```

El cual nos creará una serie de vistas en `app/views/devise` que nos permitirán personalizar la interfaz de usuario de Devise. Como Devise esta pensado en autentificar usuarios, las vistas que nos creará serán para el registro de usuarios, inicio de sesión, etc.

## Generación de modelos

Ya teniendo el flujo de Devise configurado, podemos generar el modelo de usuario que queramos, ya que al final, un usuario no es mas que otra tabla o modelo en nuestra base de datos. Para esto, debemos ejecutar el siguiente comando:

```bash
rails generate devise User
```

Donde `User` es el nombre del modelo que queremos crear. Este comando nos creará el modelo respectivo en `app/models/user.rb` y nos creará una migración en `db/migrate` para crear la tabla `users` en nuestra base de datos. Pueden notar que esta migracion trae varios atributos por defecto, como `email`, `encrypted_password`, `reset_password_token`, entre otros. Mas adelante veremos como podemos agregar mas atributos a nuestro modelo de usuario.

## Migración

Luego de generar la migración, debemos ejecutarla para crear la tabla `users` en nuestra base de datos. Para esto, debemos ejecutar el siguiente comando:

```bash
rails db:migrate
```

## Vistas

Ya teniendo el modelo creado, vamos a personalizar un poco el flujo de Devise. Para esto, vamos a agregar un par de elementos a la vista `app/views/layouts/application.html.erb`, para ello colocamos entre las etiquetas `<>` y `<%= yield %>` lo siguiente:

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

Esto nos permitirá mostrar un par de links en la parte superior de nuestra aplicación, para poder iniciar sesión, registrarse, editar el perfil y cerrar sesión, los cuales se mostraran dependiendo si el usuario esta autenticado o no. Devise nos entrega una serie de métodos que nos permiten saber si el usuario esta autenticado o no, y que nos permiten acceder a la información del usuario autenticado. Estos métodos son:

- `user_signed_in?`: Devuelve `true` si el usuario esta autenticado, `false` en caso contrario.
- `current_user`: Devuelve el usuario autenticado.
- `user_session`: Devuelve la sesión del usuario autenticado.

Vale decir, que si nuestro modelo se llamara de otra forma, por ejemplo `Person`, los métodos serían `person_signed_in?`, `current_person` y `person_session`. Recomendamos fuertemente que el nombre del modelo sea `User`, ya que es el nombre por defecto que utiliza Devise.

## Turbo Failure App

Debido a como funciona `rails`, debemos agregar una serie se pasos extra para que Devise funcione correctamente. Para esto, debemos agregar lo siguiente al inicio de `config/initializers/devise.rb`:

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
Luego, en el mismo archivo cerca de la linea 88, colocar:

```ruby
  ...
  config.navigational_formats = ['*/*', :html, :turbo_stream]
  ...
```

  
Y al final, justo antes de la seccion `==> Hotwire/Turbo configuration`, agregar:

```ruby
  ...
  config.warden do |manager|
    manager.failure_app = TurboFailureApp
  end
  ...
end
```

Con esto, ya tenemos Devise funcionando correctamente con Turbo. Puedes reiniciar (o solo iniciar) el servidor de rails y probar el flujo de autenticación.

## Atributos adicionales

Si queremos agregar atributos adicionales a nuestro modelo de usuario, debemos generar una nueva migracion. Una migracion es un archivo que nos permite modificar la estructura de una tabla en nuestra base de datos. Para generarla, debemos ejecutar el siguiente comando:

```bash
rails generate migration AddNameToUsers
```

En este caso, queremos agregar el atributo `name` a nuestro modelo, por eso, el nombre de la migracion es `AddNameToUsers`. Luego, debemos abrir el archivo que se nos creó en `db/migrate` y agregar lo siguiente:

```ruby
class AddNameToUser < ActiveRecord::Migration[7.0]
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

Por último, debemos agregar un par de métodos al controlador de usuarios. Para esto, primero debemos generarlos con el siguiente comando:

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

Como el atributo `name` no viene por defecto en el modelo de usuario, debemos permitir que este atributo sea enviado desde el formulario de registro. Para esto, en `app/controllers/users/registrations_controller.rb` debemos descomentar las lineas que señalan:

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

Y reemplazar `:attribute` por `:name`, si fueran varios atributos, por ejemplo, `:username`, `:description`, etc, se deberían separar por comas, quedando de la siguiente forma:

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

Finalmente, debemos agregar el campo de nuestros nuevos atributos en las vistas. Para esto debemos abrir `app/views/users/registrations/new.html.erb` y agregar lo siguiente entre los campos de `:password_confirmation` y `:submit`:

```html
  ...
  <div class="field">
    <%= f.label :name %><br />
    <%= f.text_field :name %>
  </div>
  ...
```

Repetimos lo mismo en `app/views/users/registrations/edit.html.erb`.

Tambien, en `app/views/layouts/application.html.erb` editamos el bloque agregado anteriormente dejandolo de la siguiente forma:

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

Para probar que todo funciona correctamente, debemos reiniciar el servidor de rails y probar el flujo de registro, iniciar sesión, editar perfil y cerrar sesión. Si todo funciona correctamente, las vistas deberían cambiar las opciones de Login, Register, Logout y Edit, ademas de mostrar el nombre del usuario autenticado.
