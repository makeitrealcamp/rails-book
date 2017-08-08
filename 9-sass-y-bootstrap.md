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

[Bootstrap Sass](https://github.com/twbs/bootstrap-sass) es la implementación oficial de Bootstrap utilizando [Sass](http://sass-lang.com/).

Es una gema que se va a integrar tus aplicaciones para que puedas personalizar [Bootstrap](http://getbootstrap.com/) fácilmente en tus aplicaciones.

### Configuración

Lo que generalmente recomiendo es ingresar a la [documentación de la gema para Ruby on Rails](https://github.com/twbs/bootstrap-sass#a-ruby-on-rails) y seguir las instrucciones. Sin embargo, las copiamos acá como referencia.

1. Incluye la gema en el `Gemfile`:

    ```
    gem 'bootstrap-sass', '~> 3.3.6'
    ```

2. Ejecuta `bundle install`.

3. Cambia la extensión del archivo `app/assets/stylesheets/application.css` por `.scss`.

4. Reemplaza el contenido de `app/assets/stylesheets/application.scss` por lo siguiente:

    ```scss
    // "bootstrap-sprockets" must be imported before "bootstrap" and "bootstrap/variables"
    @import "bootstrap-sprockets";
    @import "theme";
    @import "bootstrap";
    ```

5. Crea el archivo `app/assets/stylesheets/theme.scss` en donde vas a poder sobrescribir las variables de Bootstrap, por ejemplo:

    ```scss
    $font-size-base:                   16px;
    $text-color:                       #303030;
    ````

Para ver la lista de variables que puedes sobrescribir sigue [este enlace](https://github.com/twbs/bootstrap-sass/blob/master/assets/stylesheets/bootstrap/_variables.scss).
