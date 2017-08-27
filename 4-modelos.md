# ActiveRecord - Modelos

**ActiveRecord** es la capa que nos permite acceder y manipular la información de la base de datos sin necesidad de escribir [SQL (Structured Query Language)](SQL (Structured Query Language)).

El concepto más importante de **ActiveRecord** es el **modelo**. Un **modelo** es una clase de Ruby que representa una tabla en la base de datos:

```ruby
class Book < ActiveRecord::Base
end
```

Los modelos se encuentran en la carpeta `app/models`.

Por convención, el nombre de la tabla es el mismo nombre del **modelo** pero sin capitalizar y en plural (`books` en este caso).

En el **modelo** no se definen las columnas de la tabla explícitamente, **ActiveRecord** las toma de la tabla directamente.

## Nuestro primer modelo

Vamos a continuar con la aplicación de libros que iniciamos en los dos primeros capítulos. Si seguiste los pasos puedes continuar con esa misma aplicación. De lo contrario, puedes clonar el proyecto e iniciar en la rama de este capítulo ejecutando los siguientes comandos:

```
$ git clone https://github.com/makeitrealcamp/books-app.git
$ cd books-app
$ git checkout step-2
```

Aunque es posible crear la tabla y el modelo manualmente, es más fácil utilizar el generador de modelos de la aplicación de la línea de comandos que incluye Rails.

Vamos a crear nuestro primer modelo `Book` con los campos `title`, `author`, `description`, `publication_date` y `price`. Ejecuta el siguiente comando en la consola:

```
$ rails generate model Book title author description:text publication_date:date price:decimal
```

Varias cosas que debes tener en cuenta:

* Puedes escribir `generate` ó, más corto, `g`.

* El nombre del modelo puede estar en minúscula o capitalizado. Si tiene varias palabras como **BookComment** puedes escribir `book_comment` o `BookComment`.

* Los campos se separan por **espacio**.

* Puedes definir el tipo del campo utilizando `:` seguido del tipo de datos (sin espacios!).

* Los tipos más comunes son: `string`, `text`, `integer`, `decimal`, `date`, `time`, `datetime`, `boolean` y `references`.

* Si el tipo es `string` puedes definir la longitud máxima con llaves al final: `string{10}`

* Si omites el tipo del campo se asume que es `string{255}`.

* Los campos `id` (llave primaria), `created_at` y `updated_at` se crean de forma automática, no hay necesidad de especificarlos en el comando.

El **comando anterior** va a crear varios archivos, entre esos:

* El modelo (La clase `Book`) en `app/models/book.rb`.

* Un archivo en `db/migrate` con las instrucciones para crear la tabla. A esto se le conoce como una **migración**.

Para crear la tabla debes ejecutar:

```
$ rails db:migrate
```

Nuestro primer modelo ha sido creado!

## Utilizando nuestro modelo

Con un modelo podemos listar, crear, modificar y eliminar registros de la tabla que representa. A esto se le conoce como el **CRUD** por las siglas (**C**reate, **R**ead, **U**pdate, **D**elete).

Más adelante vamos a utilizar nuestro modelo desde los controladores, pero primero lo vamos a hacer desde la consola de Rails.

Para abrir la consola de Rails ejecuta el siguiente comando en la consola:

```
$ rails console
```

La consola de Rails es un ambiente similar a IRB pero en el que podemos acceder a las clases de nuestra aplicación.

### Creando un registro

Para crear un nuevo registro en la base de datos debemos instanciar el modelo `Book` y utilizar el método `save`:

```
> book1 = Book.new(title: "The Pragmatic Programmer", author: "Andrew Hunt, David Thomas", publication_date: "1999-10-20", price: 31.19)
> book1.save
```

Existe una forma equivalente pero más corta:

```ruby
book1 = Book.create(title: "The Pragmatic Programmer", author: "Andrew Hunt, David Thomas", publication_date: "1999-10-20", price: 31.19)
```

### Encontrando registros específicos

Cuando creas un registro la base de datos automáticamente le asigna un `id` único que puedes utilizar para encontrar ese registro a través del método `find`.

```ruby
book = Book.find(1)
book.title # => The Pragmatic Programmer
book.author # => Andrew Hunt y David Thomas
book.price # => 31.19
```

### Actualizando registros

Para actualizar la información de un registro puedes utilizar el método `save` nuevamente:

```ruby
book = Book.find(1)
book.title = "The Pragmatic Programmer: From Journeyman to Master"
book.save
```

Una forma equivalente pero más corta es con el método `update`:

```ruby
book = Book.find(1)
book.update(title: "The Pragmatic Programmer: From Journeyman to Master")
```

También es posible actualizar varios registros a la vez utilizando `update_all`:

```ruby
# actualiza el precio de todos los libros a 1000
Book.update_all(price: 1000)
```

### Eliminando registros

Para eliminar registros utiliza el método `destroy`:

```ruby
book = Book.find(1)
book.destroy
```

### Listando registros

```ruby
# retorna una colección con todos los libros
books = Book.all
```

```ruby
# retorna el primer usuario, también existe el método .last
book = Book.first
```

```ruby
# retorna el libro con id 1
book = Book.find(1)
```

```ruby
# retorna el primer libro con nombre Team Geek
book = Book.find_by(title: "Team Geek")
```

Otra forma equivalente de hacer lo anterior es:

```ruby
# retorna el primer libro con nombre Team Geek
book = Book.where(title: "Team Geek").take
```

El método `where` se utiliza para hacer consultas complejas y retorna una colección de registros. (Por eso nos toca hacer el `take` en el ejemplo anterior, `take` obtiene el primer elemento de la colección.

`where` también puede recibir una cadena para crear consultas más complejas:

```ruby
expensive_books = Book.where("price > 20")
```

Por temas de seguridad (especialmente cuando las condiciones vienen de los usuarios) y por rendimiento se recomienda cambiar el ejemplo anterior por lo siguiente:

```ruby
expensive_books = Book.where("price > ?", 20)
```

Los signos de interrogación se van a reemplazar por los siguientes argumentos del `where` en el orden en el que aparecen.

### Ordenando registros

Puedes utilizar el método `order` para ordenar los registros por alguna columna.

Por ejemplo, para ordenar todos los libros por fecha de publicación:

```ruby
books = Book.all.order(:publication_date)
```

Si los queremos de forma descendente:

```ruby
books = Book.all.order(publication: :desc)
```

### Limitar el número de registros

Puedes utilizar el método `limit` y `offset` para limitar el número de registros y saltar algunos respectivamente.

Por ejemplo, para traer máximo 5 libros:

```ruby
books = Book.limit(5)
```

Para traer máximo 5 libros pero saltar los primeros 30:

```ruby
books = Book.limit(5).offset(30)
```

### Iterando sobre los registros

```ruby
books = Book.all
books.each do |book|
  puts book.title # o lo que quieras hacer con cada libro
end
```

## Errores comunes

Quizá el error más común es utilizar `Book` (capitalizado) cuando debes utilizar `book` (sin capitalizar) y viceversa.

En general, `book` sin capitalizar sólo lo utilizas si has creado una variable con ese nombre. Por ejemplo:

```ruby
book = Book.first # eso guarda el primer producto en la variable product
```

Si quieres acceder a la información que está en `book` o modificarlo utilizarías `book` sin capitalizar:

```ruby
puts book.title
book.update(title: "Another title")
```

### Métodos que invocas sobre la clase

Los siguientes son los métodos más comunes que invocas directamente sobre la clase, algunos retornan un único registro, otros una colección de registros:

```ruby
Book.all # retorna una colección
Book.first # retorna un único registro
Book.last # retorna un único registro
Book.find(3) # retorna un único registro
Book.find_by(title: "algo") # retorna un único registro
Book.where(title: "algo") # retorna una colección
```

### Métodos que invocas sobre un registro

Asumiendo que primero ejecutamos:

```ruby
book = Book.first
```

Los métodos que generalmente vas a invocar sobre una instancia (que representa un registro) son:

```ruby
book.title # los campos de la tabla
book.title = "algo"
book.save
book.update(title: "algo")
book.destroy
```
