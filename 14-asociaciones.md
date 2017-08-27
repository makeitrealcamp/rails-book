# ActiveRecord - Asociaciones

Las asociaciones se utilizan para definir relaciones entre tablas de una base de datos. Existen dos tipos de asociaciones que se pueden modelar en una base de datos relacional:

* One to many (uno a muchos)
* Many to many (muchos a muchos)

Recuerda, como siempre, que puedes continuar con la aplicación que estás trabajando o clonar el proyecto y ubicarte en la rama de este capítulo ejecutando los siguiente comandos:

```
$ git clone https://github.com/makeitrealcamp/books-app.git
$ cd books-app
$ git checkout step-11
``` 

## One to many (uno a muchos)

En una relación uno a muchos **cada registro de una tabla está relacionado a un registro de otra tabla**.

Por ejemplo, imagina que cada libro pertenece a una única categoría.

El primer paso es crear el modelo `Category`:

```
$ rails g model Category name
```

Ahora vamos a crear una migración para asociar el modelo `Book` a `Category`, es decir, debemos crear un campo `category_id` en `books`:

```
$ rails g migration AddCategoryToBooks category:references
````

Fíjate que en vez de crear el campo con `category_id:integer` estamos utilizando `category:references`. La razón es que de esta forma Rails nos ahorra un par de pasos:

1. Crear un índice sobre ese campo, que es importante para que las búsquedas de libros por categoría sean muy rápidas.
2. Agregar la siguiente línea al modelo `Book`:

    ```ruby
    belongs_to :category
    ```

Por último, debemos agregar la siguiente línea al modelo `Category` (esto si lo debemos hacer manualmente), :

```ruby
has_many :books
```

Veamos ahora las acciones comunes que se realizan con las relaciones uno a muchos. Primero creemos algunas categorías:

```
$ rails c
> Category.create("Art")
> Category.create("Programming")
```

Y asocia los libros que tengas con alguna de estas categorías, por ejemplo:

```ruby
book = Book.first
art = Category.first
book.update(category: art)
```

Veamos ahora otras acciones comunes que se realizan con una asociación **uno a muchos**.

### Obtener la categoría de un libro

```ruby
book = Book.first
book.category
```

### Ver los libros de una categoría

```ruby
category = Category.first
category.books
```

### Crear libros de una categoría

Existen tres formas en que puedes crear un libro relacionado con la categoría:

```ruby
category = Category.first
Book.create(category: category, title: "El super libro")
Book.create(category_id: category.id, title: "Otro super libro")
category.books.create(title: "Tercer libro")
```

## Many to many (muchos a muchos)

En nuestra aplicación de libros definimos los autores de un libro en un campo llamado `authors`. Sin embargo, sería mucho mejor tener una tabla de **autores** independiente de la tabla de **libros**. Un **libro** puede tener muchos **autores** y un **autor** puede tener muchos **libros**. Es una relación muchos a muchos.

**Nota:** No vamos a implementar esta asociación en nuestra aplicación, sólo vamos a explicar cómo se haría.

Una asociación muchos a muchos es un tipo de asociación en donde **un registro de una tabla puede estar relacionada a muchos registros de otra otra tabla**. Para definir una relación muchos a muchos se debe **crear una tabla intermedia** que relacione las dos tablas.

Imagina que la tabla `authors` tiene los siguientes registros:

| id | name       |
|----|------------|
| 1  | Pedro      |
| 2  | Pablo      |

Imagina también que la tabla `books` tiene los siguientes registros:

| id | name       |
|----|------------|
| 1  | Libro 1    |
| 2  | Libro 2    |

Para relacionar autores con libros necesitamos una tabla adicional. Para nombrarla se utiliza la combinación de los dos nombres de las tablas separados por raya al piso (`_`). En este ejemplo el nombre sería `authors_books` (también podría llamarse `books_authors` pero por notación se utiliza el orden alfabético).

La tabla `authors_books` tendría la siguiente estructura:

| author\_id | book\_id |
|-----------|------------|
| 1         | 1          |
| 1         | 2          |
| 2         | 2          |

¿Qué libros están asociados al autor Pedro? ¿Qué libro tiene dos autores?

A la tabla intermedia se le conoce como una **join table**.

Para implementar esta asociación primero debes crear el modelo `Author`:

```
$ rails g model Author name
```

Ahora creemos la tabla intermedia con una migración. Esta tabla no tiene un modelo asociado:

```
$ rails g migration create_join_table_authors_booksauthor:references book:references
```

Por último, debemos agregar `has_and_belongs_to_many` a los dos modelos:

```ruby
class Author < ApplictionRecord
  has_and_belongs_to_many :books
end

class Book < ApplicationRecord
  has_and_belongs_to_many :authors
end
```

Creemos algunos autores primero:

```
$ rails c
> Author.create(name: "Pedro")
> Author.create(name: "Juan")
```

Veamos ahora las acciones comunes que se realizan con las relaciones muchos a muchos:

### Relacionar un libro a un autor y viceversa (autores a libros)

```ruby
book = Book.first
author = Author.first
book.authors << author
```

No es necesario volver a guardar el modelo. La última línea hace el INSERT en la tabla intermedia. Lo podemos hacer al revés (relacionar el libro al autor) y tendríamos el mismo resultado.

```ruby
author = Author.first
book = Book.first
author.books << book
```

Si estamos creando un libro y queremos asociarle de una vez algunos autores podemos hacer lo siguiente:

```ruby
author = Author.first
Book.create(title: "Team Geed", author_ids: [author.id])
```

### Obtener los autores de un libro y viceversa (los libros de un autor)

```ruby
book = Book.first
book.authors
...
author = Author.first
author.books
...
```

### Desasociar un libro de un autor y viceversa

```ruby
book = Book.first
author = Author.first
book.authors.delete(author)
```

Acá estamos asumiendo que esos dos registros están asociados, aunque si no lo están no ocurre ningún error, simplemente no cambia nada en la base de datos.

## Comentarios para los libros

Vamos a implementar la posibilidad de que los usuarios puedan dejar comentarios en los libros. Para eso necesitamos un modelo `Comment` que está asociado a un usuario y a un libro. Ejecuta el siguiente comando en la consola:

```
$ rails g model Comment user:references book:references body:text
```

Ahora corre las migraciones ejecutando:

```
$ rails db:migrate
```

Abre `app/models/book.rb` y agrega la siguiente línea antes del último `end`:

```
has_many :comments
```

Haz lo mismo en `app/models/user.rb`.

Crea ahora el controlador de los comentarios ejecutando el siguiente comando:

```
$ rails g controller Comments
```

Abre `app/controllers/comments_controllers.rb` y transcribe lo siguiente **dentro** de la clase:

```ruby
before_action :authenticate_user!

def create
  book = Book.find(params[:comment][:book_id])
  comment = book.comments.build(comment_params)
  comment.user = current_user

  if comment.save
    redirect_to comment.book
  end
end

private
  def comment_params
    params.require(:comment).permit(:body)
  end
```

Lo que está haciendo el código dentro de `create` es obteniendo el libro al cuál le estamos dejando el comentario, construyendo el libro y asignándole el usuario que lo creó (el usuario actual).

Agreguemos ahora la ruta al archivo `config/routes.rb`:

```ruby
resources :comments, only: [:create]
```

Por último vamos a modificar las vistas para integrar los comentarios. Crea un archivo llamado `_comments.html.erb` en la carpeta `app/views/books/` con el siguiente contenido:

```erb
<div class="card comments mt-5 mb-5">
  <div class="card-header border-bottom-0 font-weight-bold bg-transparent">Comentarios</div>
  <ul class="list-group list-group-flush">
    <% @book.comments.each do |comment| %>
      <li class="list-group-item">
        <header class="comment-header"><strong><%= comment.user.email %></strong> wrote <%= distance_of_time_in_words(Time.current, comment.created_at) %> ago</header>
        <div class="comment-body"><%= comment.body %></div>
      </li>
    <% end %>

    <% if @book.comments.empty? %>
      <li class="list-group-item text-center">Aún no hay comentarios para este libro :(</li>
    <% end %>
  </ul>
</div>

<% if signed_in? %>
  <div class="card bg-light new-comment">
    <div class="card-body">
      <p class="font-weight-bold">Deja tu comentario:</p>
      <%= form_for @book.comments.build do |f| %>
        <%= f.hidden_field :book_id, value: @book.id %>
        <div class="form-group">
          <%= f.text_area :body, rows: 4, class: "form-control" %>
        </div>

        <div class="text-right">
          <%= f.submit "Comentar", class: "btn btn-primary" %>
        </div>
      <% end %>
    </div>
  </div>
<% else %>
  <div class="card bg-light mt-5">
    <div class="card-body">
      <p class="card-text text-center lead"><%= link_to "Regístrate", new_user_registration_path %> o <%= link_to "Ingresa", new_user_session_path %> para comentar</p>
    </div>
  </div>
<% end %>
```

Ahora abre `app/views/books/show.html.erb` y referencia el **partial** que acabamos de crear exactamente debajo del siguiente código:

```erb
<div class="text-right">
  <% if signed_in? %>
    <%= link_to "Editar", edit_book_path(@book), class: "btn btn-link" %>
    <%= link_to "Eliminar", @book, class: "btn btn-link text-danger", method: :delete, data: { confirm: "¿Estás seguro de eliminar este libro?" } %>
  <% end %>
</div>
```

La linea que debes agregar es:

```erb
<%= render "comments" >
```

Lo último que nos falta es agregar algunos estilos en `app/assets/stylesheets/books.scss`, debajo de la siguiente línea:

```scss
.book-details { padding: 0 20px 0 40px; }
```

Los estilos que vas a agregar son los siguientes:

```scss
.comments {
  .comment-header {
    color: #999;
    font-size: 0.85rem;
    margin-bottom: 5px;
  }

  .comment-body { font-size: 0.95rem; }
}
```

Prúebalo y vuélvelo a desplegar a Heroku. Asegúrate de estudiar muy bien este código y entender qué está pasando.