# ActiveRecord - Validaciones

Las validaciones garantizan que sólo información válida se almacene en la base de datos.

Existen varios lugares en donde uno puede realizar validaciones: en la base de datos directamente, en el cliente utilizando JavaScript y en los modelos.

Aunque la validación en los modelos no anula la validación en la base de datos o en el cliente, los modelos ofrecen un sitio conveniente en dónde definir las validaciones.

Si quieres clonar nuevamente el proyecto y ubicarte la rama de este capítulo ejecuta los siguientes comandos:

```
$ git clone https://github.com/makeitrealcamp/books-app.git
$ cd books-app
$ git checkout step-10
``` 

## Creando nuestra primera validación

Las validaciones se definen dentro de los modelos utilizando generalmente el método `validates`:

```ruby
class Book < ApplicationRecord
  validates :title, presence: true
end
```

## ¿Cuándo ocurren las validaciones?

Las validaciones ocurren cuando invocas alguno de los siguientes métodos:

* `create`
* `create!`
* `save`
* `save!`
* `update`
* `update!`
* `valid?`
* `invalid?`

Los métodos que terminan con `!` lanzan una excepción si el registro es inválido. Los demás, excluyendo `valid?` e `invalid?` retornan el objeto si las validaciones pasan o `false` de lo contrario.

Por ejemplo, el siguiente código crea un nuevo artículo e intenta guardarlo. Si el método `save` no retorna false va a mostrar "Se guardó bien!". De lo contrario va a mostrar los errores separados por coma (`,`):

```ruby
book = Book.new(title: "Team Geek")
if book.save
  puts "Se guardó bien!"
else
  puts book.errors.full_messages.join(", ")
end
```

## Mostrando los errores en el formulario

Existen varias formas de mostrar los errores del modelo en un formulario. La más fácil es mostrar todos los errores antes de los campos. Abre el archivo `app/views/books/_form.html.erb` y transcribe el siguiente código después del `div` con clase `card-body`:

```erb
<% if @book.errors.any? %>
  <div id="card border-0">
    <p>This form contains <%= pluralize(@book.errors.count, "error") %>:</p>
    <ul>
    <% @book.errors.full_messages.each do |msg| %>
      <li class="text-danger"><%= msg %></li>
    <% end %>
    </ul>
  </div>
<% end %>
```

Cuando un campo tiene un error, Rails lo envuelve en un `div` con clase `field_with_errors`, así que abre `app/assets/stylesheets/application.scss` y agrega lo siguiente al final del archivo:

```scss
.field_with_errors .form-control {
  border: 1px solid $red;
}
```

Los errores aparecerán en Inglés. Más adelante aprenderemos cómo traducirlos.

## Validaciones incluídas

Rails incluye varias validaciones predefinidas que puedes utilizar en tus modelos.

### Presencia

Para validar que ciertos atributos no sean vacíos o `nil` utiliza la opción `presence`.

Por ejemplo, en el siguiente código estamos validando que los atributos `:name`, `:login` e `:email` no sean `nil` ni vacíos:

```ruby
class Book < ApplicationRecord
  validates :title, :author, presence: true
end
```

### Booleanos

Para validar que un atributo booleano no sea `nil` debes usar la siguiente validación en vez de `presence`:

```ruby
validates :boolean_field_name, inclusion: { in: [true, false] }
```

### Incusión y exclusión

Para validar que uno o más atributos estén incluídos o excluídos de un conjunto utiliza las opciones `inclusion` y `exclusion` respectivamente:

```ruby
validates :size, inclusion: { in: %w(small medium large),  message: "%{value} is not a valid size" }
validates :legacy_code, format: { with: /\A[a-zA-Z]+\z/, message: "only allows letters" }
```

### Números

Para verificar que uno o más atributos contienen sólo valores numéricos utiliza la opción `numericality`:

```ruby
class Player < ApplicationRecord
  validates :num_pages, numericality: true
  validates :num_pages, numericality: { only_integer: true }
end
```

Además de `only_integer` las opciones que le puedes pasar a `numericality` son:

```ruby
validates :points, numericality: { greater_than: 5 }
validates :points, numericality: { greater_than_or_equal_to: 5 }
validates :points, numericality: { equal_to: 5 }
validates :points, numericality: { less_than: 5 }
validates :points, numericality: { less_than_or_equal_to: 5 }
validates :points, numericality: { other_than: 5 }
```

### Longitud de una cadena

Utiliza la opción `length`:

```ruby
class Person < ApplicationRecord
  validates :name, length: { minimum: 2 }
  validates :bio, length: { maximum: 500 }
  validates :password, length: { in: 6..20 }
  validates :registration_number, length: { is: 6 }
end
```

Para ver la lista completa de validaciones te recomendamos la [guía oficial de Rails](http://guides.rubyonrails.org/active_record_validations.html).
