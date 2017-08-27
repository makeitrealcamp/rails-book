# Arquitectura

**Ruby on Rails** usa un concepto llamado [convención sobre configuración](http://es.wikipedia.org/wiki/Convenci%C3%B3n_sobre_Configuraci%C3%B3n) para que la estructura de todos los proyectos sea similar y escribamos menos código.

La desventaja de la [convención sobre configuración](http://es.wikipedia.org/wiki/Convenci%C3%B3n_sobre_Configuraci%C3%B3n) es que muchas cosas en Rails te van a parecer magia al principio. No te preocupes, es normal y con el tiempo uno aprende a entender, e incluso a apreciar, lo que está ocurriendo.

Lo importante es que no olvides el objetivo principal: ser capaces de crear nuestras primeras aplicaciones Web.

## Los componentes de Rails

Los componentes más importantes de **Ruby on Rails** y que veremos en más detalle a continuación son:

* El **enrutador**, que se configura en el archivo `config/routes.rb` y nos permite asociar las **rutas** con las **acciones**.

* Los **controladores**, que se encuentran en la carpeta `app/controllers` y almacenan las **acciones**.

* Las **vistas**, que se encuentran en `app/views`, y nos permiten definir el código HTML que se renderiza desde los **controladores**.

* Un componente que no conoces aún es **ActiveRecord**, que nos va a permitir interactuar con la base de datos.

* Por último, la **aplicación de consola**, que nos va a permitir ahorrar tiempo de desarrollo.

## El enrutador

El **enrutador** es el componente que decide qué **controlador** y qué **método** va a procesar una petición HTTP.

El **enrutador** se configura en el archivo `config/routes.rb`.

Hay varias formas de definir las rutas. Veamos la más genérica:

```ruby
get '/home', to: 'pages#home'
```

En este ejemplo estamos diciendo que cuando alguien haga una petición a `GET /home`, el método `index` del controlador `PagesController` (ubicado en `app/controllers/pages_controller.rb`) es el que se va a encargar de procesar la petición.

Otra forma equivalente es utilizar el operador `=>` (hashrocket) de la siguiente forma:

```ruby
get '/home' => 'pages#home'
```

No importa cuál forma utilices, lo importante es ser consistente en cada proyecto.

Por último, es posible omitir el controlador y el método **siempre y cuando la ruta tenga la forma `/<controlador>/<método>`**. Por ejemplo, la siguiente línea utilizará el método `home` del controlador `PagesController`.

```ruby
get '/pages/home'
```

## Los controladores

Los **controladores** son clases de Ruby con métodos que se van a encargar de procesar las peticiones HTTP.

```ruby
class PagesController < ApplicationController
  def home
    render html: "<h1>Hola Mundo</h1>".html_safe
  end
end
```

En este ejemplo estamos renderizando el HTML `<h1>Hola Mundo</h1>`.

El método `html_safe` es necesario para decirle a Rails que no **escape** el código HTML.

## Las vistas

Como vimos en el capítulo anterior podemos utilizar vistas para no tener que escribir todo el código HTML en las **acciones** (que es posible pero muy engorroso).

Por convención Rails renderiza una vista por defecto que se debe encontrar en una ubicación específica y se debe llamar de una forma específica:

* El nombre del archivo debe ser igual al método seguido de `.html.erb`.
* Se debe ubicar en la carpeta `app/views` dentro de una carpeta que se llame igual al controlador.

Por ejemplo, el método `home` del siguiente **controlador** va a intentar renderizar la vista `app/views/pages/home.html.erb`:

```ruby
class PagesController < ApplicationController
  def home
  end
end
```

### Pasando información del controlador a la vista

Cuando defines una **variable de instancia** (las que comienzan con `@`) en la **acción**, esta variable va a estar disponible en la vista. Por ejemplo:

```ruby
class PagesController < ApplicationController
  def home
    @name = "Pedro"
  end
end
```

La variable `@name` va a estar disponible en la vista (`app/views/pages/home.html.erb`) y la podemos mostrar de la siguiente forma:

```erb
<h1>Hola <%= @name %></h1>
```

La etiqueta `<%=` le indica a **Rails** que queremos mostrar la variable en la pantalla. Uno de los errores más comunes es olvidar agregar el igual `=` y no entender por qué no aparece la variable en la pantalla.

### Query String

El **query string** es el conjunto de propiedades que van después del signo de interrogación (`?`) de un URL. **Rails** automáticamente convierte las propiedades en el hash `params` que puedes acceder desde el **controlador** o la **vista**.

Por ejemplo, si queremos obtener el valor de una propiedad llamada `name` utilizaríamos `params[:name]`. Modifica `app/views/pages/home.html.erb` para que quede de la siguiente forma:

```erb
<h1>Hola <%= params[:name] %></h1>
```

Ahora ingresa a http://localhost:3000/?name=Pedro. Deberías ver en pantalla "Hola Pedro". Prueba cambiando el **query string** con otros nombres.

Los valores del **query string** siempre llegan como cadenas de texto. Si deseas otro tipo debes convertirlo manualmente. Por ejemplo:

```erb
<h1>En cinco años tendrás <%= params[:age].to_i + 5 %> años</h1>
```

En este caso estamos convirtiendo la propiedad `age` a un entero para poderlo sumar a `5`.

## ActiveRecord

**ActiveRecord** es la capa que nos permite acceder y manipular la información de la base de datos sin necesidad de escribir [SQL (Structured Query Language)](SQL (Structured Query Language)).

En los próximos capítulos hablaremos en detalle de este componente.

## La aplicación de consola

Una de las razones por las que **Ruby on Rails** es tan popular es que trae una poderosa aplicación de consola que nos permite, entre otras cosas, generar código a través de comandos llamados **generadores**.

Hasta ahora hemos visto los siguiente comandos:

* `rails new` nos permite crear una nueva aplicación.
* `rails server` nos permite iniciar el servidor.
* `rails generate controller` nos permite crear un controlador.

A medida que avancemos vamos a ver otros comandos útiles.

## Listar libros

Continuemos trabajando en nuestra aplicación. Si seguiste los pasos del capítulo anterior puedes continuar con esa aplicación. De lo contrario ejecuta los siguientes comandos para clonar el proyecto y continuar:

```
$ git clone https://github.com/makeitrealcamp/books-app.git
$ cd books-app
$ git checkout step-1
```

Vamos a crear un nuevo **controlador** llamado `books` que va a contener todas las acciones relacionadas con los libros: listar, crear, editar, eliminar, etc.

Para crear el controlador utiliza el siguiente comando:

```
$ rails generate controller books
```

Ahora ingresa a `app/controllers/books_controller.rb` y crea el método `index` como se muestra a continuación:

```ruby
class BooksController < ApplicationController
  def index
    @books = ["Pragmatic Programmer", "Eloquent Ruby", "Secrets of the JavaScript Ninja"]
  end
end
```

Aún no vamos a obtener lo libros de la base de datos, pero por ahora podemos mostrar los 3 que tenemos en el arreglo.

Crea un archivo `app/views/books/index.html.erb` y transcribe lo siguiente:

```erb
<h1>Libros</h1>

<table border="1">
  <thead>
    <tr>
      <th>Nombre</th>
    </tr>
  </thead>
  <tbody>
    <% @books.each do |book| %>
      <tr>
        <td><%= book %></td>
      </tr>
    <% end %>
  </tbody>
</table>
```

Por último tenemos que crear la ruta. Abre `config/routes.rb` y agrega la siguiente línea antes de cerrar el último `end`:

```ruby
  get '/books', to: 'books#index'
```

Abre un navegador e ingresa a http://localhost:3000/books. Deberías ver los tres libros que acabamos de crear en una tabla como se muestra en la siguiente imagen:

![Books - Atom Editor](https://s3.amazonaws.com/makeitreal/images/books/books-list.jpg)