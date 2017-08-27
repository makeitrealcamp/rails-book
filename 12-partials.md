# Partials

Si te fijas, en nuestro proyecto el formulario de crear y editar un libro tienen código que estamos duplicando. Podemos extraer el código que se repite en un archivo a parte, llamado un **partial**, y referenciarlo desde los dos formularios.

Puedes continuar con la aplicación que desplegaste en el capítulo anterior o puedes clonar nuevamente el proyecto y ubicarte en la rama de este capítulo ejecutando los siguientes comandos:

```
$ git clone https://github.com/makeitrealcamp/books-app.git
$ cd books-app
$ git checkout step-9
```

Crea un archivo llamado `_form.html.erb` (el `_` es importante para que Rails lo reconozca como un **partial**) dentro de la carpeta `app/views/books/` con el mismo contenido que tiene el archivo `new.html.rb` en la misma carpeta:

```erb
<div class="container">
  <div class="row justify-content-center">
    <div class="col-sm-6">
      <div class="card mt-5">
        <div class="card-header font-weight-bold">Agregar Libro</div>
        <%= form_for @book do |f| %>
        <div class="card-body">
          <div class="form-group">
            <%= f.label :title %>
            <%= f.text_field :title, class: "form-control", autofocus: true %>
          </div>
          <div class="form-group">
            <%= f.label :author %>
            <%= f.text_field :author, class: "form-control" %>
          </div>
          <div class="form-group">
            <%= f.label :description %>
            <%= f.text_area :description, rows: 4, class: "form-control" %>
          </div>
          <div class="form-group">
            <%= f.label :image_url %>
            <%= f.url_field :image_url, class: "form-control" %>
          </div>
          <div class="form-group">
            <%= f.label :publication_date %>
            <%= f.date_field :publication_date, class: "form-control" %>
          </div>
          <div class="form-group">
            <%= f.label :price %>
            <%= f.number_field :price, class: "form-control", step: :any %>
          </div>

        </div>
        <div class="card-footer text-right">
          <%= link_to "Cancelar", books_path, class: "btn btn-default" %>
          <%= f.submit class: "btn btn-primary" %>
        </div>
        <% end %>
      </div>
    </div>
  </div>
</div>
```

Ahora, reemplaza el contenido de `new.html.erb` y `edit.html.erb` con la siguiente línea:

```erb
<%= render "form" >
```

Eso es todo. Fíjate que, en el método `render`, utilizamos `"form"` en vez de `"_form"`, Rails ya sabe que este archivo es un **partial** y debe comenzar con `_`.

El problema es que el título de los dos formularios es ahora **Agregar Libro**. Pero al editar queremos cambiarlo por **Editar Libro**. No hay problema, podemos pasarle variables al **partial**.

Modifica la siguiente línea del **partial**:

```erb
<div class="card-header font-weight-bold">Agregar Libro</div>
```

por la siguiente:

```erb
<div class="card-header font-weight-bold"><%= title %></div>
```

En `new.html.erb` modifica la única línea que existe por:

```erb
<%= render "form", title: "Agregar Libro" >
```

En `edit.html.erb` modifica la única línea que existe por:

```erb
<%= render "form", title: "Editar Libro" >
```

Verifica que el título al crear un libro sea **Agregar Libro** y al editar sea **Editar Libro**.