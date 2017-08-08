# Desplegando en Heroku

Ahora vamos a desplegar nuestra aplicación en Internet. Para hacerlo vamos a utilizar un servicio llamado [Heroku](https://heroku.com/), que nos va a permitir desplegar aplicaciones fácilmente a través de Git, sin necesidad de preocuparnos por configurar el servidor, la base de datos, etc.

[Heroku](https://heroku.com/) es gratuito para aplicaciones con poco tráfico y sólo empiezas a pagar cuando necesitas un mayor rendimiento.

## Requisitos

El primer paso es asegurarte que tienes una cuenta en [Heroku](https://heroku.com/). Si aún no la tienes debes crear una antes de continuar.

Una vez tengas cuenta en [Heroku](https://heroku.com/), el siguiente paso es descargar el [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli). En [este enlace](https://devcenter.heroku.com/articles/heroku-cli) encontrarás las instrucciones para diferentes sistemas operativos.

[Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) es una aplicación para la línea de comandos que nos va a permitir crear y administrar nuestras aplicaciones en [Heroku](https://heroku.com/).

Una vez tengas instalado [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) vas a ejecutar el siguiente comando en la consola:

```
$ heroku login
```

Sigue las instrucciones e ingresa tus credenciales de [Heroku](https://heroku.com/).

## Configurando nuestro repositorio de Git

Si aún no lo has hecho, debes configurar el repositorio de Git ejecutando los siguiente comandos:

```
$ git init
$ git add .
$ git commit -m 'Initial import'
```

## Configurando nuestra aplicación para Heroku

Para que nuestra aplicación funcione en [Heroku](https://heroku.com/) debemos realizar algunos cambios a nuestra aplicación.

Por defecto, en desarrollo, **Rails** utiliza una base de datos llamada SQLite, pero para [Heroku](https://heroku.com/) necesitamos configurar PostgreSQL.


## Creando y desplegando nuestra aplicación

Para crear una aplicación en [Heroku](https://heroku.com/) vas a ejecutar el siguiente comando en la consola:

```
$ heroku create
```

Para desplegar la aplicación vas a ejecutar el siguiente comando:

```
$ git push heroku master
```

Para abrir la aplicación en un navegador ejecuta:

```
$ heroku open
```