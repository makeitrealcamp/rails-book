# Rutas

Las rutas se definen en el archivo `config/routes.rb`.

Cada ruta está compuesta por los siguientes elementos:

* El **verbo HTTP**.
* El **path**.
* El **controlador** y el **método (acción)** que se van a encargar de procesar la petición que coincida.

Por ejemplo, la siguiente ruta:

```ruby
get '/pages/home', to: 'pages#home'
```

* Utiliza el verbo `GET`.
* Utiliza el path `/pages/home`
* El método `home` del controlador `pages_controller.rb` se va a encargar de procesar la acción.

Las siguientes son otras dos formas en que puedes definir la misma ruta:

```ruby
get '/pages/home' => 'pages#home'
get '/pages/home' # Rails infiere el controlador y el método de la ruta
```

## Variables de path

Es posible definir variables en el path de la ruta para crear "rutas dinámicas". Por ejemplo, la siguiente ruta utiliza una variable llamada `:name` para coincidir con cualquier path que tenga la estructura `/hello/<cualquier_cadena>`:

```ruby
get '/hello/:name', to: "pages#hello"
```

En el controlador utilizamos el hash `params` para obtener el valor de la variable `:name`:

```ruby
class PagesController < ApplicationController
  def hello
    @name = params[:name]
  end
end
```

**Nota:** Aunque en la vista también podemos acceder el hash `params`, en general se recomienda pasar la información a través **variables de instancia**.

## Nombrando las rutas

Es posible darle un nombre a cada ruta para no tener que usar el path (p.e. `/pages/home`) en vínculos y redireccionamientos.

El nombre se le da con la opción `as` como se muestra en el siguiente ejemplo:

```ruby
get '/pages/home', to: 'pages#home', as: "home"
```

De esa forma, ahora podemos utilizar el nombre seguido de `_path` o `_url` (p.e. `home_path` o `home_url`).

```ruby
home_path # /pages/home
home_url  # http://localhost:3000/pages/home
```

En general utiliza `_path` a menos de que estés compartiendo un link en correos o quieras mostrar el URL completo.

Puedes usar el nombre de la ruta en vínculos así:

```erb
<a href="<%= home_path %>">Ir al Home</a>
```

De hecho Rails viene con un método de ayuda que vamos a ver más adelante llamado `link_to` que te permite crear vínculos de la siguiente forma:

```erb
<%= link_to "Ir al home", home_path %>
```

Esta última es la forma en la que se recomienda hacer vínculos internos en la aplicación.

## Listando las rutas

Si quieres ver una lista de todas las rutas de la aplicación puedes utilizar el comando `rails routes` desde la consola (o `rake routes` si estás utilizando una versión de Rails menor a la 5):

```
$ rails routes
    Prefix   Verb   URI Pattern             Controller#Action
pages_home   GET    /pages/home(.:format)   pages#home
```

El `Prefix` es el nombre de la ruta. Por defecto Rails le pone un nombre a la ruta así no hayamos utilizado `as`.
