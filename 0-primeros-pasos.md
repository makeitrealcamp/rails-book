# Primeros pasos

En este capítulo vamos a crear nuestra primera aplicación de [Ruby on Rails](http://rubyonrails.org/), la misma aplicación con la que trabajaremos durante todo el libro.

La aplicación que vamos a construir se va a llamar **Books** y les va a permitir a los usuarios registrarse, publicar libros y dejar reseñas. El resultado final lo puedes ver en [este enlace](https://radiant-escarpment-43957.herokuapp.com/).

## Requisitos

Para instalar [Ruby on Rails](http://rubyonrails.org/) necesitas tener instalado [Ruby](https://www.ruby-lang.org/). Verifica si lo tienes instalado abriendo la línea de comandos y ejecutando:

```
$ ruby -v
ruby 2.4.1p111 (2017-03-22 revision 58053) [x86_64-darwin16]
```

La versión puede ser diferente, cualquier versión mayor a 2.1.0 funciona.

Si aún no tienes [Ruby](https://www.ruby-lang.org/) instalado puedes seguir las instrucciones que se encuentran en [este enlace](https://github.com/makeitrealcamp/ruby-installation).

## Instalación

Para instalar **Ruby on Rails** ejecuta el siguiente comando:

```
$ gem install rails --no-ri --no-rdoc
```

Para verificar que haya quedado bien instalado deberías poder ejecutar el siguiente comando sin error:

```
$ rails -v
Rails 5.1.3
```

De nuevo, la versión puede ser diferente, pero cualquier versión mayor a 5.1.0 funciona.

## Crea tu primera aplicación

Para crear una aplicación en Ruby on Rails debes ejecutar el comando `rails new <nombre_de_la_app>`. En nuestro caso el nombre de la aplicación será **books**, así que ejecuta el siguiente comando:

```
$ rails new books
```

Esto va a crear una carpeta llamada **books** en la ubicación donde te encuentres en ese momento.

Ingresa a la carpeta ejecutando:

```
$ cd books
```

Abre la carpeta con tu editor preferido. Por ejemplo, si estás utilizando Atom como editor de texto ejecuta:

```
$ atom .
```

Deberías ver una serie de carpetas y archivos que **Rails** generó automáticamente cuando ejecutamos el comando `rails new books`.

![Books - Atom Editor](https://s3.amazonaws.com/makeitreal/images/books/atom-books.jpg)

Inicia el servidor ejecutando `rails server` o `rails s`:

```
$ rails server
```

Ingresa en tu navegador a http://localhost:3000/. Deberías ver la pantalla inicial de Ruby on Rails!

![Rails - Inicio](https://s3.amazonaws.com/makeitreal/images/books/rails-welcome.jpg)

### Nuestra primera ruta

El objetivo principal de **Ruby on Rails** es el de asociar **rutas** (p.e. `/books`, `/users`, etc.) a **métodos de Ruby** que se encarguen de responder las peticiones HTTP.

Las asociación entre rutas y métodos se define en el archivo `config/routes.rb`.

A las clases que contienen los métodos asociados a rutas se les conoce como **controladores**.

Los **controladores** se encuentra en la carpeta `app/controllers`.

Vamos a generar nuestro primer **controlador** ejecutando el siguiente comando:

```
$ rails generate controller Pages
```

Este comando va a crear varios archivos. Entre ellos el **controlador** en `app/controllers/pages_controller.rb`.

Abre el controlador y vamos ahora a crear un método `home` dentro de ese **controlador**. Te debería quedar de la siguiente forma:

```ruby
class PagesController < ApplicationController
  def home
    render html: "<h1>Hola, Rails!</h1>".html_safe
  end
end
```

Lo que estamos haciendo en este método es retornando el código HTML que queremos que aparezca en el navegador.

A los métodos dentro de un **controlador** se les conocen como **acciones**.

¿Se te ocurre alguna idea de por qué tenemos que agregarle `.html_safe` al final?

Por último, vamos a asociar el método `home` a la ruta `/home`. Abre el archivo `config/routes.rb` y asegúrate de que quede como se muestra a continuación:

```ruby
Rails.application.routes.draw do
  get '/home', to: 'pages#home'
end
```

Ingresa desde tu navegador a http://localhost:3000/home/. Te debería aparecer "Hola, Rails!".

### Utilizando una vista

Escribir todo el código HTML directamente en las **acciones** (los métodos de los **controladores**) sería bastante engorroso. La solución es utilizar **vistas**.

Las **vistas** son archivos que se encuentran en la carpeta `app/views`.

Lo interesante de las **vistas** es que podemos mezclar código HTML con código Ruby.

Para crear nuestra primera vista crea un carpeta llamada `pages` dentro de `app/views` y crea un archivo llamado `home.html.erb` dentro de esa nueva carpeta con el siguiente contenido:

```erb
<% 3.times do %>
  <p>Hola Mundo</p>
<% end %>
```

Fíjate que el código Ruby se debe embeber en las etiquetas `<%` y `%>`.

Ahora, para decirle a la **acción** `home` que renderice este archivo debes eliminar la línea que empieza con `render` y dejar el método `home` vacío como se muestra a continuación:

```ruby
class PagesController < ApplicationController
  def home
  end
end
```

Por convención Rails va a buscar un archivo que se llame igual al método (ignorando la extensión) y que se encuentre dentro de una carpeta que se llame igual al controlador. Es por eso que creamos la carpeta `pages` y llamamos a la vista `home.html.erb`.

Si refrescas la página deberías ver la frase "Hola Mundo" tres veces.

### Definiendo la página de inicio

Para asociar la raíz del proyecto (la ruta inicial `/`) con la **acción** `home` debes modificar el archivo `config/routes.rb` para que quede de la siguiente forma:

```ruby
Rails.application.routes.draw do
  root 'pages#home'
end
```

Ahora ingresa a http://localhost:3000/. Debería aparecer "Hola Mundo" tres veces nuevamente.

## Si te sientes abrumado(a)

Si te sientes abrumado(a) con tanta información no te preocupes, se pondrá peor ... no es cierto, estoy bromeando. En los próximos capítulos vamos a reforzar todo lo que hemos visto hasta ahora y todo va a empezar a tener más sentido ;)

¡Continuemos!
