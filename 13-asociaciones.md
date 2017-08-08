# ActiveRecord - Asociaciones

Las asociaciones se utilizan para definir relaciones entre tablas de una base de datos. Existen dos tipos de asociaciones que se pueden modelar en una base de datos relacional:

* One to many (uno a muchos)
* Many to many (muchos a muchos)

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

Una asociación muchos a muchos es un tipo de asociación en donde **un registro de una tabla puede estar relacionada a muchos registros de otra otra tabla**.

Por ejemplo, un libro puede tener muchos autores y un autor puede tener muchos libros. **Es una relación muchos a muchos**.

Para definir una relación muchos a muchos se debe **crear una tabla intermedia** que relacione las dos tablas.

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

## Asociación polimórfica

La asociación polimórfica es un tipo de asociación uno a muchos que se puede implementar en Ruby on Rails (en la base de datos no se puede implementar directamente) en donde una tabla puede estar asociada a muchas otras tablas.

Imagina que en nuestra aplicación de libros se pueden dejar comentarios tanto en los libros como en los autores. En vez de crear una tabla para los comentarios de los libros y otra para los comentarios de las respuestas, podemos crear una tabla única `comments` que tenga los comentarios tanto de los libros como de los autores.

La tabla de preguntas (`questions`) tendría la siguiente estructura:

| id | comentable_type | commentable_id | text                    |
|----|-----------------|----------------|-------------------------|
| 1  | Author        | 1              | Comentario al Autor 1 |
| 2  | Book          | 1              | Comentario a Book 1   |

La tabla `comments` utiliza dos columnas para identificar la tabla y el id del registro al que va a estar relacionado cada registro.

Para implementar esta asociación empecemos por crear el modelo `Comment`:

```
$ rails g model Comment commentable:reference{polymorphic} text:text
```

Fíjate que agregamos una referencia en `Comment` a `commentable`, que no es un modelo existente, es simplemente un nombre que le debemos dar a la relación.

Ahora agreguemos la relación en los modelos:

```ruby
class Author < ApplicationRecord
  has_many :comments, as: :commentable
end

class Book < ApplicationRecord
  has_many :comments, as: :commentable
end

class Comment < ApplicationRecord
  belongs_to :commentable, polymorphic: true
end
```

Si queremos agregar comentarios a un libro lo podemos hacer de la siguiente forma:

```ruby
book = Book.first
book.comments.create(text: "Este es un comentario")

# O de esta forma más larga
Comment.create(commentable: book, text: "Otro comentario")
```