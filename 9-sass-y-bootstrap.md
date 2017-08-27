# Sass y Bootstrap 3

[Sass](http://sass-lang.com/) es un preprocesador de CSS.

[Sass](http://sass-lang.com/) ofrece varias ventajas sobre CSS como:

* Variables
* Reglas anidadas
* Importación de archivos y parciales
* Reutilización de código con mixins y herencia
* Operadores matemáticos y lógicos
* Condicionales e iteraciones

Para aprender más sobre [Sass](http://sass-lang.com/) te recomendamos [este video de Make it Real](https://youtu.be/OaX0Jg88t8s).

## Bootstrap Sass

[Bootstrap Sass](https://github.com/twbs/bootstrap-rubygem) es la implementación oficial de Bootstrap utilizando [Sass](http://sass-lang.com/).

Es una gema que se va a integrar tus aplicaciones para que puedas personalizar [Bootstrap](http://getbootstrap.com/) fácilmente en tus aplicaciones.

Vamos a continuar trabajando en nuestra aplicación de libros. Puedes utilizar la aplicación que has venido construyendo o clonar el proyecto y descargar la rama `step-6`:

```
$ git clone https://github.com/makeitrealcamp/books-app.git
$ cd books-app
$ git checkout step-6
```

### Configuración

Lo que generalmente recomiendo es ingresar a la [documentación de la gema para Ruby on Rails](https://github.com/twbs/bootstrap-rubygem#a-ruby-on-rails) y seguir las instrucciones. Sin embargo, las copiamos acá como referencia.

**Nota:** Las instrucciones acá presentadas son para Bootstrap 4, si quieres trabajar con Bootstrap 3 debes seguir las instrucciones en [este enlace](https://github.com/twbs/bootstrap-sass#a-ruby-on-rails).

1. Incluye la gema al final del `Gemfile`:

    ```
    gem 'bootstrap', '~> 4.0.0.beta'
    ```

2. Ejecuta `bundle install`.

3. Cambia la extensión del archivo `app/assets/stylesheets/application.css` por `.scss`.

4. Reemplaza el contenido de `app/assets/stylesheets/application.scss` por lo siguiente:

    ```scss
    @import "theme";
    @import "bootstrap";
    ```

5. Crea el archivo `app/assets/stylesheets/theme.scss` en donde vas a poder sobrescribir las variables de Bootstrap, por ejemplo:

    ```scss
    $text-color:                       #303030;
    ````

Para ver la lista de variables que puedes sobrescribir sigue [este enlace](https://github.com/twbs/bootstrap-sass/blob/master/assets/stylesheets/bootstrap/_variables.scss).

Eso es todo lo que necesitas para empezar a trabajar con Bootstrap en tus proyectos.

El siguiente paso es aplicar Bootstrap al proyecto. Esto está por fuera del alcance de este libro pero puedes revisar [este commit](https://github.com/makeitrealcamp/books-app/commit/b36d61fb2ec926eb74dfc7a820dbe64026a358ac) para ver y estudiar los cambios que se hicieron.