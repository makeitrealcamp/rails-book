# Poblando la base de datos

Sería útil contar con un mecanismo para poblar la base de datos con algunos registros iniciales cuando hagamos una nueva instalación de la aplicación. De esa forma, cuando publiquemos nuestra aplicación en Internet, o algún amigo/colega nos quiera colaborar a desarrollarla, sea posible cargar esa información inicial.

Rails nos ofrece ese mecanismo a través del archivo `db/seeds.rb`, que nos permite escribir código Ruby y utilizar los modelos de nuestra aplicación para poblar la base de datos.

Debido a los cambios que hicimos aplicando Bootstrap, la recomendación es que clones el proyecto y te ubiques en la rama de este capítulo ejecutando los siguientes comandos:

```
$ git clone https://github.com/makeitrealcamp/books-app.git
$ cd books-app
$ git checkout step-7
``` 

Abre el archivo `db/seeds.rb` y transcribe el siguiente código:

```ruby
Book.create(title: "The Pragmatic Programmer:", author: "Andrew Hunt, Dave Thomas", description: "Illustrates the best practices and major pitfalls of many different aspects of software development", image_url: "https://images-na.ssl-images-amazon.com/images/I/41BKx1AxQWL._SX396_BO1,204,203,200_.jpg", publication_date: "10-30-1999", price: 30.96)

Book.create(title: "The Lean Startup", author: "Eric Ries", description: "Most startups fail. But many of those failures are preventable.", image_url: "https://images-na.ssl-images-amazon.com/images/I/51N-u8AsmdL.jpg", publication_date: "09-13-2011", price: 13.99)
```

Para ejecutar este código utiliza el siguiente comando en la consola:

```
$ rails db:seed
```

Ten en cuenta que si ejecutas este código varias veces, se crearán varias veces los mismos registros. Si quieres limpiar la base de datos antes de poblarla utiliza el siguiente comando:

```
$ rails db:reset
```

En el archivo `db/seed.rb` podemos escribir cualquier código Ruby válido, así que podemos hacer cosas más complejas como generar información aleatoria:

```ruby
100.times do |i|
  Book.create(title: "Book #{i}", author: "Author #{i}", description: "Description #{i}", image_url: "http://lorempixel.com/320/460/", publication_date: "01-01-2000", price: 1)
end
```