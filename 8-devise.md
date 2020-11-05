# Autenticación con Devise

[Devise](https://github.com/plataformatec/devise) es una gema que facilita la implementación de la  autenticación en nuestras aplicaciones.

Generalmente lo que hago cuando voy a utilizar [Devise](https://github.com/plataformatec/devise) es ir a la documentación y seguir los pasos, pero los copiamos acá por referencia.

Vamos a continuar trabajando en nuestra aplicación de libros. Puedes utilizar la aplicación que has venido construyendo o clonar el proyecto y descargar la rama `step-5`:

```
$ git clone https://github.com/makeitrealcamp/books-app.git
$ cd books-app
$ git checkout step-5
```

1. El primer paso es agregar la gema al final del archivo `Gemfile`:

    ```ruby
    gem 'devise'
    ```

2. Ejecuta `bundle install` para instalarla.

3. Ejecuta el generador de [Devise](https://github.com/plataformatec/devise):

    ```
    $ rails generate devise:install
    ```

4. El siguiente paso es crear el modelo de los usuarios de la aplicación, generalmente `User`:

    ```
    $ rails generate devise User
    ```

5. Ejecuta `rails db:migrate` para correr las migraciones.

## Rutas que crea Devise

Asumiendo que el usuario es `User`, [Devise](https://github.com/plataformatec/devise) crea, entre otras, las siguientes rutas:

| Ruta  | Nombre | Descripción |
| --- | --- | --- |
| `GET /users/sign_up` | new_user_registration | Formulario de registro |
| `POST /users` |  | Registrarse |
| `GET /users/sign_in` | new_user_session | Formulario de login |
| `POST /users/sign_in` | | Login |
| `DELETE /users/sign_out` | destroy_user_session | Salir |

Para ver el listado completo ejecuta `rails routes`. Las demás rutas son, en su mayoría, para el manejo de contraseñas (recuperar, cambiar, etc).

## Agregando los links de Devise

Para que el usuario pueda registrarse, ingresar y salir de nuestra aplicación vamos a agregar algunos links en el layout de la aplicación.

Abre  `app/views/layouts/application.html.erb` y agrega lo siguiente después del `<body>`, antes de `<%= yield %>`:

```erb
<div>
  <% if signed_in? %>
    <%= link_to "Salir", destroy_user_session_path, method: :delete %>
  <% else %>
    <%= link_to "Registrarse", new_user_registration_path %>
    <%= link_to "Ingresar", new_user_session_path %>
  <% end %>
</div>
```

## Filtros y helpers de controlador

[Devise](https://github.com/plataformatec/devise) crea algunos métodos de ayuda y filtros para que puedas manejar la autenticación en tu aplicación.

Asumiendo que tu usuario se llama `User`, el filtro que puedes agregar sobre los controladores que quieres proteger es el siguiente:

```ruby
before_action :authenticate_user!
```

En nuestra aplicación de libros vamos a proteger todas las acciones del controlador `books` exceptuando `index` y `show` que son las que nos van a permitir listar y mostrar los detalles de un libro.

Agrega la siguiente línea dentro de la clase:

```ruby
before_action :authenticate_user!, except: [:index, :show]
```

Los métodos de ayuda a los que vas a tener acceso en los controladores y vistas son los siguientes:

| Método | Descripción |
| --- | --- |
| `signed_in?` | Retorna `true` si el usuario está autenticado, `false` de lo contrario |
| `current_user` | Retorna el usuario que está autenticado o `nil` |
| `user_session` | Retorna la sesión del usuario |

En nuestra aplicación sólo vamos a mostrar los links de "Editar" y "Eliminar" si el usuario está autenticada. Cambia el archivo `app/views/books/index.html.erb` para que quede los links queden de la siguiente forma:

```
<td>
  <%= link_to "Mostrar", book %>
  <% if signed_in? %>
    <%= link_to "Editar", edit_book_path(book) %>
    <%= link_to "Eliminar", book, method: :delete, data: { confirm: "¿Estás seguro de eliminar este libro?" } %>
  <% end %>
</td>
```

Ahora sólo deberían aparecer los links de "Editar" y "Eliminar" si el usuario está autenticado.

## El modelo de usuario

Asumiendo que al usuario lo llamaste `User` al generarlo, el modelo que genera [Devise](https://github.com/plataformatec/devise) es el siguiente:

```ruby
class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable
end
```

Este es un modelo normal pero utiliza el método `devise` que recibe una serie de módulos que puedes activar o desactivar.

Los módulos que vienen activados son los siguientes:

| Módulo | Descripción |
| --- | --- |
| `database_authenticatable` | Los usuarios se van a poder autenticar con un nombre de usuario y contraseña almacenado en la base de datos. |
| `registereable` | Los usuarios van a poder registrarse, actualizar y eliminar su perfil. |
| `recoverable` | Los usuarios van a poder recuperar su contraseña |
| `rememberable ` | Habilita la opción "Recordarme" en el login |
| `trackable` | Habilita el seguimiento al usuario (de dónde se autenticó, cuantas veces, de qué IP, etc.) |
| `validatable` | Valida el email y el password |  

## Modificando las vistas

Por defecto no vas a ver vistas ni controladores de [Devise](https://github.com/plataformatec/devise). Sin embargo, si quieres modificarlos puedes decirle a [Devise](https://github.com/plataformatec/devise) que los genere.

Para generar las vistas utiliza el siguiente comando:

```
$ rails generate devise:views
```

Para generar los controladores utiliza el siguiente comando:

```
$ rails generate devise:controllers users
```
Luego incorpora el controlador que se quieras sobreescribir en routes de la siguiente manera:
```ruby
devise_for :users, :controllers => { registrations: 'users/registrations' }
```

De esta forma puedes modificar las vistas y cambiar funcionalidad de [Devise](https://github.com/plataformatec/devise).
