# Desplegando en Heroku

Ahora vamos a desplegar nuestra aplicación en Internet. Para hacerlo vamos a utilizar un servicio llamado [Heroku](https://heroku.com/), que nos va a permitir desplegar aplicaciones fácilmente a través de Git sin necesidad de preocuparnos por configurar el servidor, la base de datos, etc.

[Heroku](https://heroku.com/) es gratuito para aplicaciones con poco tráfico y sólo empiezas a pagar cuando necesitas un mayor rendimiento.

Si quieres clonar nuevamente el proyecto y ubicarte la rama de este capítulo ejecuta los siguientes comandos:

```
$ git clone https://github.com/makeitrealcamp/books-app.git
$ cd books-app
$ git checkout step-8
``` 

Ahora vas a borrar la carpeta `.git` con el siguiente comando:

```
$ rm -rf .git
```

Eso va a eliminar el repositorio local.

## Requisitos

El primer paso es asegurarte que tienes una cuenta en [Heroku](https://heroku.com/). Si aún no la tienes debes crear una antes de continuar.

Una vez tengas tu cuenta en [Heroku](https://heroku.com/), el siguiente paso es descargar el [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli). En [este enlace](https://devcenter.heroku.com/articles/heroku-cli) encontrarás las instrucciones para diferentes sistemas operativos.

[Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) es una aplicación para la línea de comandos que nos va a permitir crear y administrar nuestras aplicaciones en [Heroku](https://heroku.com/).

Una vez tengas instalado [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) vas a ejecutar el siguiente comando en la consola:

```
$ heroku login
```

Sigue las instrucciones e ingresa tus credenciales de [Heroku](https://heroku.com/).

## Configurando nuestro repositorio de Git

Debes crear el repositorio local de Git ejecutando los siguiente comandos:

```
$ git init
$ git add .
$ git commit -m 'Initial import'
```

## Configurando nuestra aplicación para Heroku

Para que nuestra aplicación funcione en [Heroku](https://heroku.com/) debemos realizar algunos cambios a nuestra aplicación.

Por defecto, en desarrollo, **Rails** utiliza una base de datos llamada SQLite, pero para [Heroku](https://heroku.com/) necesitamos configurar PostgreSQL.

El primer paso es agregar la gema de PostgreSQL al archivo `Gemfile`, pero para eso debemos primero mover la línea `gem 'sqlite3'` al grupo de development y test:

1. Corta la línea `gem sqlite3`

2. Pégala después de la línea `group :development, :test do`:

    ```ruby
    group :development, :test do
      gem 'sqlite3'
      ...
    ````

Ahora agrega lo siguiente al final del archivo:

```ruby
group :production do
  gem 'pg'
end
```

Guarda y ejecuta `bundle install` en la consola.

El siguiente paso es configurar el archivo `config/database.yml`. Ábrelo y modifica la sección `production` para que quede de la siguiente forma:

```yml
production:
  adapter: postgresql
  encoding: unicode
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  url: <%= ENV['DATABASE_URL'] %>
```

Eso es todo. Ahora vamos a hacer el commit ejecutando los siguientes comandos:

```
$ git add .
$ git commit -m 'Configure PostgreSQL for production'
```

## Creando y desplegando nuestra aplicación

Para crear una aplicación en [Heroku](https://heroku.com/) vas a ejecutar el siguiente comando en la consola:

```
$ heroku create
```

Para desplegar la aplicación vas a ejecutar los siguiente comandos:


```
$ git push heroku master
$ heroku run rails db:schema:load
```

Estos comandos toman algún tiempo en ejecutar. El primero es el más lento y el que despliega la aplicación. El segundo carga las tablas en la base de datos de Heroku.

Para abrir la aplicación en un navegador ejecuta:

```
$ heroku open
```

Regístrate y crea algunos libros para empezar.

## Redesplegando la aplicación

Cuando realices cambios sobre la aplicación y desees redesplegar a Heroku realiza los siguientes pasos:

1. Haz commit de tus cambios.

    ````
    $ git add .
    $ git commit -m "Mensaje de cambios"
    ```

2. Redespliega a Heroku

   ```
   $ git push heroku master
   ```

3. Si hiciste nuevas migraciones córrelas en Heroku:

   ```
   $ heroku run rails db:migrate
   $ heroku restart
   ```

