# Recursos REST

REST es una forma particular de **definir las rutas HTTP** para hacer el CRUD de los **modelos** en nuestra aplicación Web.

A los **modelos** también se les conoce como **recursos**.

Por ejemplo, las rutas para hacer el CRUD de un **modelo** `Book` serían las siguientes:

| Verbo | Path | Controlador#Acción | Descripción |
| --- | --- | --- | --- |
| GET | /books | books#index | muestra la lista de libros |
| GET | /books/new | books#new | muestra el formulario para crear un libro |
| POST | /books | books#create | crea un nuevo libro |
| GET | /books/:id | books#show | muestra los detalles de un libro |
| GET | /books/:id/edit | books#edit | muestra el formulario para editar un libro |
| PATCH/PUT | /books/:id | books#update | actualiza un libro |
| DELETE | /books/:id | books#destroy | eliminar un libro |

## Definiendo las rutas de un recurso

Vamos a continuar trabajando en nuestra aplicación de libros. Puedes continuar con la aplicación que has venido construyendo o clonar el proyecto y descargar la rama `rest`:

```
$ git clone https://github.com/makeitrealcamp/books-app.git
$ cd books-app
$ git checkout step-4
```

Para definir las rutas de un recurso en `config/routes.rb` puedes definirlas una a una o utilizar la palabra clave `resources` seguido del nombre del recurso:

```ruby
resources :books
```

Asegúrate de que el archivo `config/routes.rb` quede de la siguiente forma:

```ruby
Rails.application.routes.draw do
  root 'books#index'

  resources :books
end
```

La línea `resources :books` es equivalente a las siguientes 8 líneas:

```ruby
get "/books", to: "books#index"
get "/books/new", to: "books#new"
post "/books", to: "books#create"
get "/books/:id", to: "books#show"
get "/books/:id/edit", to: "books#edit"
put "/books/:id", to: "books#update"
patch "/books/:id", to: "books#update"
delete "/books/:id", to: "books#destroy"
```

Puedes comprobarlo ejecutando el comando `rails routes` en la consola:

```
$ rails routes
   Prefix Verb   URI Pattern               Controller#Action
     root GET    /                         books#index
    books GET    /books(.:format)          books#index
          POST   /books(.:format)          books#create
 new_book GET    /books/new(.:format)      books#new
edit_book GET    /books/:id/edit(.:format) books#edit
     book GET    /books/:id(.:format)      books#show
          PATCH  /books/:id(.:format)      books#update
          PUT    /books/:id(.:format)      books#update
          DELETE /books/:id(.:format)      books#destroy
```

Fíjate que para actualizar un recurso podemos utilizar el verbo `PUT` o `PATCH`.

## Utilizando el generador de recursos

Así como desde la consola podemos generar un modelo y un controlador, existe un generador que nos permite generar el modelo y el controlador al mismo tiempo.

Por ejemplo, en vez de haber creado el modelo `Book` y el controlador `BooksController` por separado, pudimos haber utilizado el siguiente comando:

```
$ rails g resources book title author description:text image_url publication_date:date price:decimal
```

Ese comando genera, entre otros, lo siguiente:

* El modelo `Book` en `app/models/book.rb`
* La migración que se va a utilizar para crear la tabla.
* El controlador en `app/controllers/books_controller.rb`
* El archivo `books.scss` en `app/assets/stylesheets/`.
* El archivo `books.coffee` en `app/assets/javascripts/`.
* La línea `resources :books` en `config/routes.rb`.

Al final debes correr la migración:

```
$ rails db:migrate
```

## El controlador y las vistas de un recurso

El controlador de un recurso está compuesto de las siguientes acciones (métodos):

* `index` para mostrar la lista de registros.
* `show` para mostrar los detalles de un registro.
* `new` para mostrar el formulario de creación.
* `create` para crear un registro.
* `edit` para mostrar el formulario de edición.
* `update` para actualizar un registro.
* `destroy` para eliminar un registro.

Implementemos cada una de estas acciones para el modelo `Book`.

### Listando registros

El método `index` se utiliza para listar los registros. Quedaría de la siguiente forma:

```ruby
def index
  @books = Book.all
end
```

Y la vista en `app/views/books/index.html.erb`:

```erb
<h1>Libros</h1>
<%= link_to "Nuevo Libro", new_book_path %>
<table border="1">
  <thead>
    <tr>
      <th>Título</th>
      <th>Descripción</th>
      <th>Fecha de Publicación</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <% @books.each do |book| %>
      <tr>
        <td><%= book.title %></td>
        <td><%= book.description %></td>
        <td><%= book.publication_date %></td>
        <td>
          <%= link_to "Mostrar", book %>
          <%= link_to "Editar", edit_book_path(book) %>
          <%= link_to "Eliminar", book, method: :delete, data: { confirm: "¿Estás seguro de eliminar este libro?" } %>
        </td>
      </tr>
    <% end %>
  </tbody>
</table>
```

Si ingresas a http://localhost:3000/ o http://localhost:3000/books/ vas a ver una tabla vacía o con los libros que creaste en el capítulo anterior.

### Mostrando los detalles de un registro

El método `show` se utiliza para mostrar los detalles de un registro. Quedaría de la siguiente forma:

```ruby
def show
  @book = Book.find(params[:id])
end
```

Y la vista en `app/views/books/show.html.erb` quedaría de la siguiente forma:

```erb
<h1>Detalles del Libro</h1>
<div>Nombre: <%= @book.title %></div>
<div>Autor: <%= @book.author %></div>
<div>Descripción: <%= @book.description %></div>
<div>Fecha de publicación: <%= @book.publication_date %></div>
<div>Precio: <%= @book.price %></div>
```

### Mostrando el formulario de creación

La creación de un registro se divide en dos: la ruta que muestra el formulario y la ruta que inserta el registro en la base de datos cuando alguien llena el formulario.

Para mostrar el formulario se utiliza el método `new` que se implementaría de la siguiente forma:

```ruby
def new
  @book = Book.new
end
```

Y la vista en `app/views/books/new.html.erb` sería la siguiente:

```erb
<h1>Nuevo Libro</h1>
<%= form_for @book do |f| %>
  <div>
    <%= f.label :title %>
    <%= f.text_field :title %>
  </div>
  <div>
    <%= f.label :author %>
    <%= f.text_field :author %>
  </div>
  <div>
    <%= f.label :description %>
    <%= f.text_area :description %>
  </div>
  <div>
    <%= f.label :image_url %>
    <%= f.text_field :image_url %>
  </div>
  <div>
    <%= f.label :publication_date %>
    <%= f.date_field :publication_date %>
  </div>
  <div>
    <%= f.label :price %>
    <%= f.number_field :price, step: :any %>
  </div>

  <div>
    <%= f.submit %>
  </div>
<% end %>
```

### Creando un registro

Para crear el registro necesitamos implementar el método `create` y un método de ayuda `book_params`:

```ruby
def create
  @book = Book.new(book_params)
  if @book.save
    redirect_to @book
  else
    render :new
  end
end

private
  def book_params
    params.require(:book).permit(:title, :author, :description, :image_url, :publication_date, :price)
  end
```

La razón por la que tuvimos que crear ese método de ayuda es por un tema de seguridad. Ese método está filtrando la información que queremos que se pueda cambiar directamente. A esto se le conoce como **strong parameters**.

No necesitamos una vista para esta acción.

### Mostrando el formulario de edición

Para mostrar el formulario de editar se utiliza el método `edit` que se implementaría de la siguiente forma:

```ruby
def edit
  @book = Book.find(params[:id])
end
```

Y la vista en `app/views/books/edit.html.erb` sería la siguiente:

```erb
<h1>Editar Libro</h1>
<%= form_for @book do |f| %>
  <div>
    <%= f.label :title %>
    <%= f.text_field :title %>
  </div>
  <div>
    <%= f.label :author %>
    <%= f.text_field :author %>
  </div>
  <div>
    <%= f.label :description %>
    <%= f.text_area :description %>
  </div>
  <div>
    <%= f.label :image_url %>
    <%= f.text_field :image_url %>
  </div>
  <div>
    <%= f.label :publication_date %>
    <%= f.date_field :publication_date %>
  </div>
  <div>
    <%= f.label :price %>
    <%= f.number_field :price, step: :any %>
  </div>

  <div>
    <%= f.submit %>
  </div>
<% end %>
```

Fíjate que el formulario de edición es igual al de creación, más adelante veremos cómo podemos reutilizar el código para no tener que duplicarlo en los dos archivos, pero por ahora déjalo así.

### Actualizando un registro

Para actualizar el registro necesitamos implementar el método `update` y el método de ayuda `books_params` que ya creamos previamente:

```ruby
def update
  @book = Book.find(params[:id])
  if @book.update(book_params)
    redirect_to @book
  else
    render :edit
  end
end
```

No necesitamos una vista para esta acción.

### Eliminando un registro

Para eliminar un registro necesitamos implementar el método `destroy`:

```ruby
def destroy
  book = Book.find(params[:id])
  book.destroy

  redirect_to books_path
end
```

## Limitando las acciones

Para limitar las rutas y acciones de un recurso utiliza la opción `only` o `except` de `resources` en `config/routes.rb`:

```ruby
resources books, only: [:index, :new, :create]
resources authors, except: [:destroy]
```

En este caso, para **books** sólo se generarían 3 rutas: la de listar, la de mostrar el formulario y la de crear.

Para **authors** se generarían todas excepto la de eliminar.

## Notificaciones

Para mostrar notificaciones de éxito al crear, editar y eliminar registros utilizando el `flash`.

Por ejemplo, para mostrar una notificación de éxito al eliminar un producto:

```ruby
def destroy
  book = Book.find(params[:id])
  book.destroy

  flash[:notice] = "El libro ha sido eliminado exitosamente"
  redirect_to books_path
end
```

El `notice` también se puede agregar directamente sobre el `redirect_to`:

```ruby
redirect_to books_path, notice: "El libro ha sido eliminado exitosamente"
```

### Mostrando las notificaciones

Puedes agregar el siguiente código donde quieres que aparezcan los mensajes de flash en el layout `app/views/layouts/application.html.erb`:

```erb
<% flash.each do |name, msg| -%>
  <%= content_tag :div, msg, class: name %>
<% end -%>
```

## Filtros

Puedes ejecutar métodos que se ejecutan **antes** o **después** de una acción de un controlador utilizando `before_action` y `after_action`.

Por ejemplo, imagina que queremos imprimir algo antes de cada acción. Podemos utilizar un `before_action`:

```ruby
class ApplicationController < ActionController::Base
  before_action :log_request

  private
    def log_request
      puts "Ha iniciado una petición"
    end
  end
```

Si haces un `render` o `redirect_to` en un `before_action` la acción no se ejecuta.

Los filtros son heredados, así que si defines el filtro en `ApplicationController` se va a ejecutar en todos los controladores de la aplicación.

### Limitando el alcance de los filtros

Puedes limitar las acciones a las que aplica un filtro utilizando las opciones `only` o `except`.

Por ejemplo:

```ruby
before_action :log_request, only: [:new, :create]
before_action :log_request, except: [:index]
```
