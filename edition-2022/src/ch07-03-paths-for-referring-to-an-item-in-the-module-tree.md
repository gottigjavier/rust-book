## Rutas para hecer Referencia a un Elemento en el Árbol de Módulos

Para mostrarle a Rust dónde encontrar un elemento en un árbol de módulos, usamos una ruta de la misma manera que usamos una ruta cuando navegamos por un sistema de archivos. Para llamar a una función, necesitamos conocer su ruta.

Una ruta puede tomar dos formas:
* Una *ruta absoluta* es la ruta completa a partir de un crate raíz. Para el código de un crate externo, la ruta absoluta comienza con el nombre del crate, y para el código del crate actual, comienza con el literal `crate`.
* Una *ruta relativa* comienza desde el módulo actual y usa `self`, `super` o un identificador en el módulo actual.

Tanto las rutas absolutas como las relativas van seguidas de uno o más identificadores separados por dos puntos dobles (`::`).

Volviendo al Listado 7-1, digamos que queremos llamar a la función `add_to_waitlist`. Esto es lo mismo que preguntar: ¿cuál es la ruta de la función `add_to_waitlist`? El Listado 7-3 contiene el Listado 7-1 con algunos de los módulos y funciones eliminados.

Mostraremos dos formas de llamar a la función `add_to_waitlist` desde una nueva función `eat_at_restaurant` definida en el crate raíz. Estas rutas son correctas, pero queda otro problema que evitará que este ejemplo se compile tal como está. Explicaremos por qué en un momento.

La función `eat_at_restaurant` es parte de la API pública de nuestro crate librería, por lo que la marcamos con la palabra clave `pub`. En la sección ["Exponiendo Rutas con la Palabra Clave `pub`"](ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html#exponiendo-rutas-con-la-palabra-clave-pub), entraremos en más detalles sobre `pub`.

<span class="filename">Filename: src/lib.rs</span>

```rust,ignore
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // Absolute path
    crate::front_of_house::hosting::add_to_waitlist();

    // Relative path
    front_of_house::hosting::add_to_waitlist();
}
```

<span class="caption">Listado 7-3: Llamar a la función `add_to_waitlist` usando rutas absolutas y relativas</span>

La primera vez que llamamos a la función `add_to_waitlist` en `eat_at_restaurant`, usamos una ruta absoluta. La función `add_to_waitlist` se define en el mismo crate que `eat_at_restaurant`, lo que significa que podemos usar la palabra clave `crate` para iniciar una ruta absoluta. Luego incluimos cada uno de los módulos sucesivos hasta llegar a `add_to_waitlist`. Puede imaginar un sistema de archivos con la misma estructura: especificaríamos la ruta `/front_of_house/hosting/add_to_waitlist` para ejecutar el programa `add_to_waitlist`; usar el nombre `crate` para comenzar desde el crate raíz es como usar `/` para comenzar desde la raíz del sistema de archivos en su shell.

La segunda vez que llamamos a `add_to_waitlist` en `eat_at_restaurant`, usamos una ruta relativa. La ruta comienza con `front_of_house`, el nombre del módulo definido en el mismo nivel del árbol de módulos que `eat_at_restaurant`. Aquí, el equivalente del sistema de archivos sería usar la ruta `front_of_house/hosting/add_to_waitlist`. Comenzar con un nombre de módulo significa que la ruta es relativa.

Elegir si usar una ruta relativa o absoluta es una decisión que tomará en función de su proyecto y depende de si es más probable que mueva el código de definición del elemento por separado o junto con el código que usa el elemento. Por ejemplo, si movemos el módulo `front_of_house` y la función `eat_at_restaurant` a un módulo llamado `customer_experience`, necesitaríamos actualizar la ruta absoluta a `add_to_waitlist`, pero la ruta relativa seguiría siendo válida. Sin embargo, si moviéramos la función `eat_at_restaurant` por separado a un módulo llamado `dining`, la ruta absoluta a la llamada `add_to_waitlist` permanecería igual, pero la ruta relativa tendría que actualizarse. Nuestra preferencia en general es especificar rutas absolutas porque es más probable que queramos mover las definiciones de código y las llamadas a elementos de forma independiente.

¡Intentemos compilar el Listado 7-3 y averiguar por qué no se compila todavía! El error que obtenemos se muestra en el Listado 7-4.

```text
$ cargo build
   Compiling restaurant v0.1.0 (file:///projects/restaurant)
error[E0603]: module `hosting` is private
 --> src/lib.rs:9:28
  |
9 |     crate::front_of_house::hosting::add_to_waitlist();
  |                            ^^^^^^^ private module
  |
note: the module `hosting` is defined here
 --> src/lib.rs:2:5
  |
2 |     mod hosting {
  |     ^^^^^^^^^^^

error[E0603]: module `hosting` is private
  --> src/lib.rs:12:21
   |
12 |     front_of_house::hosting::add_to_waitlist();
   |                     ^^^^^^^ private module
   |
note: the module `hosting` is defined here
  --> src/lib.rs:2:5
   |
2  |     mod hosting {
   |     ^^^^^^^^^^^

For more information about this error, try `rustc --explain E0603`.
error: could not compile `restaurant` due to 2 previous errors
```

<span class="caption">Listado 7-4: Errores del compilador al construir el código en el Listado 7-3</span>

Los mensajes de error dicen que el módulo `hosting` es privado. En otras palabras, tenemos las rutas correctas para el módulo `hosting` y la función `add_to_waitlist`, pero Rust no nos deja usarlas porque no se tiene acceso a las secciones privadas. En Rust, todos los elementos (funciones, métodos, structs, enums, módulos y constantes) son privados para los módulos *padres* de forma predeterminada. Si desea hacer privado un elemento como una función o un struct, colóquelo en un módulo.

Los elementos de un módulo padre no pueden usar los elementos privados que están dentro de los módulos hijos, pero los elementos de los módulos hijos pueden usar los elementos de sus módulos padres. Esto se debe a que los módulos hijos encapsulan y ocultan sus detalles de implementación, pero los módulos hijos pueden ver el contexto en el que están definidos. Para continuar con nuestra metáfora, piense en las reglas de privacidad como si fueran la oficina administrativa de un restaurante: lo que sucede allí es privado para los clientes del restaurante, pero los gerentes de oficina pueden ver y hacer todo en el restaurante que operan.

Rust eligió que el sistema de módulos funcionara de esta manera para que ocultar los detalles internos de implementación sea el valor predeterminado. De esa forma, sabrá qué partes del código interno puede cambiar sin romper el código externo. Sin embargo, Rust le brinda la opción de exponer partes internas del código de módulos hijos a los módulos ancestros externos mediante el uso de la palabra clave `pub` para hacer público un elemento.


### Exponiendo Rutas con la Palabra Clave `pub`

Volvamos al error del Listado 7-4 que nos decía que el módulo `hosting` es privado. Queremos que la función `eat_at_restaurant` en el módulo padre tenga acceso a la función `add_to_waitlist` en el módulo hijo, por lo que marcamos el módulo `hosting` con la palabra clave `pub`, como se muestra en el Listado 7-5.

<span class="filename">Filename: src/lib.rs</span>

```rust
mod front_of_house {
    pub mod hosting {
        fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // Absolute path
    crate::front_of_house::hosting::add_to_waitlist();

    // Relative path
    front_of_house::hosting::add_to_waitlist();
}
```

<span class="caption">Listado 7-5: Declarando el módulo `hosting` como `pub` para usarlo desde `eat_at_restaurant`</span>


Desafortunadamente, el código del Listado 7-5 aún genera un error, como se muestra en el Listado 7-6.

```text
$ cargo build
   Compiling restaurant v0.1.0 (file:///projects/restaurant)
error[E0603]: function `add_to_waitlist` is private
 --> src/lib.rs:9:37
  |
9 |     crate::front_of_house::hosting::add_to_waitlist();
  |                                     ^^^^^^^^^^^^^^^ private function
  |
note: the function `add_to_waitlist` is defined here
 --> src/lib.rs:3:9
  |
3 |         fn add_to_waitlist() {}
  |         ^^^^^^^^^^^^^^^^^^^^

error[E0603]: function `add_to_waitlist` is private
  --> src/lib.rs:12:30
   |
12 |     front_of_house::hosting::add_to_waitlist();
   |                              ^^^^^^^^^^^^^^^ private function
   |
note: the function `add_to_waitlist` is defined here
  --> src/lib.rs:3:9
   |
3  |         fn add_to_waitlist() {}
   |         ^^^^^^^^^^^^^^^^^^^^

For more information about this error, try `rustc --explain E0603`.
error: could not compile `restaurant` due to 2 previous errors
```

<span class="caption">Listado 7-6: Errores del compilador al compilar el código del Listado 7-5</span>


¿Qué sucedió? Agregar la palabra clave `pub` delante de `mod hosting` hace que el módulo sea público. Con este cambio, si podemos acceder a `front_of_house`, podemos acceder a `hosting`. Pero los *contenidos* de `hosting` siguen siendo privados; hacer público el módulo no hace público su contenido. La palabra clave `pub` en un módulo solo permite que el código en sus módulos antepasados se refiera a él, no que acceda a su código interno. Debido a que los módulos son contenedores, no hay mucho que podamos hacer si solo hacemos público el módulo; necesitamos ir más allá y elegir hacer públicos uno o más de los elementos dentro del módulo también.

Los errores en el Listado 7-6 dicen que la función `add_to_waitlist` es privada. Las reglas de privacidad se aplican a structs, enums, funciones y métodos, así como a módulos.

También hagamos pública la función `add_to_waitlist` agregando la palabra clave `pub` antes de su definición, como en el Listado 7-7.

<span class="filename">Filename: src/lib.rs</span>

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // Absolute path
    crate::front_of_house::hosting::add_to_waitlist();

    // Relative path
    front_of_house::hosting::add_to_waitlist();
}
```

<span class="caption">Listado 7-7: Agregar la palabra clave `pub` a `mod hosting` y `fn add_to_waitlist` nos permite llamar a la función desde `eat_at_restaurant`</span>

¡Ahora el código se compilará! Para ver por qué agregar la palabra clave `pub` nos permite usar estas rutas en `add_to_waitlist` con respecto a las reglas de privacidad, veamos las rutas absolutas y relativas.

En la ruta absoluta, empezamos con `crate`, la raíz del árbol de módulos de nuestro crate. El módulo `front_of_house` se define en el crate raíz. Mientras que `front_of_house` no es público, debido a que la función `eat_at_restaurant` está definida en el mismo módulo que `front_of_house` (es decir, `eat_at_restaurant` y `front_of_house` son hermanos), podemos referirnos a `front_of_house` desde `eat_at_restaurant`. El siguiente es el módulo `hosting` marcado con `pub`. Podemos acceder al módulo padre de `hosting`, por lo que podemos acceder a `hosting`. Finalmente, la función `add_to_waitlist` está marcada con `pub` y podemos acceder a su módulo padre, ¡así que esta llamada de función funciona!

En la ruta relativa, la lógica es la misma que la ruta absoluta excepto por el primer paso: en lugar de comenzar desde el crate raíz, la ruta comienza desde `front_of_house`. El módulo `front_of_house` se define dentro del mismo módulo que `eat_at_restaurant`, por lo que la ruta relativa a partir del módulo en el que se define `eat_at_restaurant` funciona. Luego, debido a que `hosting` y `add_to_waitlist` están marcados con `pub`, el resto de la ruta funciona, ¡y esta llamada de función es válida!

Si planea compartir su crate librería para que otros proyectos puedan usar su código, su API pública es su contrato con los usuarios de su crate que determina cómo pueden interactuar con su código. Hay muchas consideraciones en torno a la gestión de cambios en su API pública para hacerla más simple a las personas que dependen de su crate. Estas consideraciones están fuera del alcance de este libro; si está interesado en este tema, consulte las [Directrices de la API de Rust](https://rust-lang.github.io/api-guidelines/).

> #### Prácticas Recomendadas para Packages con un Binario y una Librería
>
> Mencionamos que un package puede contener tanto un crate raíz binario *src/main.rs* como un crate raíz librería *src/lib.rs*, y ambos crates tendrán el nombre del package de manera predeterminada. Por lo general, los packages con este patrón de contener tanto un crate librería como un crate binario tendrán sólo el suficiente código en el crate binario como para iniciar un ejecutable que llame al código con el crate librería. Esto permite que otros proyectos se beneficien de la mayor funcionalidad que proporciona el package, porque el código del crate librería se puede compartir.
>
> El árbol de módulos debe definirse en *src/lib.rs*. Luego, cualquier elemento público se puede usar en el crate binario iniciando las rutas con el nombre del package. El crate binario se convierte en un usuario del crate librería tal como un crate completamente externo usaría el crate librería: este solo puede usar la API pública. Esto le ayuda a diseñar una buena API; ¡Usted no solo es el autor, también es un cliente!
>
> En el [Capítulo 12](ch12-00-an-io-project.html), demostraremos esta práctica organizativa con un programa de línea de comandos que contendrá tanto un crate binario como un crate librería.

### Comenzando Rutas Relativas con `super`

Podemos construir rutas relativas que comiencen en el módulo padre, en lugar del módulo actual o el crate raíz, usando `super` al comienzo de la ruta. Esto es como iniciar una ruta de sistema de archivos con la sintaxis "`..`". El uso de `super` nos permite hacer referencia a un elemento que sabemos está en el módulo padre, lo que puede facilitar la reorganización del árbol de módulos cuando el módulo está estrechamente relacionado con el padre, pero el padre podría moverse a otra parte del árbol de módulos algún día.

Considere el código del Listado 7-8 que modela la situación en la que un chef corrige un pedido incorrecto y se lo presenta personalmente al cliente. La función `fix_incorrect_order` definida en el módulo `back_of_house` llama a la función `deliver_order` definida en el módulo padre especificando la ruta a `deliver_order` comenzando con `super`:

<span class="filename">Filename: src/lib.rs</span>

```rust
fn deliver_order() {}

mod back_of_house {
    fn fix_incorrect_order() {
        cook_order();
        super::deliver_order();
    }

    fn cook_order() {}
}
```

<span class="caption">Listado 7-8: llamando a una función usando una ruta relativa que comienza con `super`</span>

La función `fix_incorrect_order` está en el módulo `back_of_house`, por lo que podemos usar `super` para ir al módulo padre de `back_of_house`, que en este caso es `crate`, la raíz. A partir de ahí, buscamos `deliver_order` y lo encontramos. ¡Éxito! Creemos que es probable que el módulo `back_of_house` y la función `deliver_order` mantengan la misma relación entre sí y se muevan juntos si decidimos reorganizar el árbol de módulos del crate. Por lo tanto, usamos `super` para tener menos lugares donde actualizar código en el futuro si este código se mueve a un módulo diferente.

### Haciendo Públicos Structs y Enums

También podemos usar `pub` para designar structs y enums como públicos, pero hay algunos detalles adicionales al uso de `pub` con structs y enums. Si usamos `pub` antes de una definición de struct, hacemos público el struct, pero los campos del struct seguirán siendo privados. Podemos hacer público o no cada campo según el caso. En el Listado 7-9, hemos definido un struct `back_of_house::Breakfast` público con un campo `toast` público pero un campo `seasonal_fruit` privado. Este es un modelo del caso en un restaurante donde el cliente puede elegir el tipo de pan que viene con una comida, pero el chef decide qué fruta acompaña la comida en función de lo que está en temporada y en stock. La fruta disponible cambia rápidamente, por lo que los clientes no pueden elegir la fruta o incluso ver qué fruta obtendrán.

<span class="filename">Filename: src/lib.rs</span>

```rust
mod back_of_house {
    pub struct Breakfast {
        pub toast: String,
        seasonal_fruit: String,
    }

    impl Breakfast {
        pub fn summer(toast: &str) -> Breakfast {
            Breakfast {
                toast: String::from(toast),
                seasonal_fruit: String::from("peaches"),
            }
        }
    }
}

pub fn eat_at_restaurant() {
    // En verano, ordene un desayuno con tostada de centeno (Rye toast)
    let mut meal = back_of_house::Breakfast::summer("Rye");
    // Cambiemos de opinión sobre qué pan nos gustaría (wheat: trigo)
    meal.toast = String::from("Wheat");
    println!("I'd like {} toast please", meal.toast);

    // La siguiente línea no se compilará si la descomentamos; no se nos permite 
    // ver o modificar la fruta de temporada que viene con la comida 
    // meal.seasonal_fruit = String::from("blueberries");
}
```

<span class="caption">Listado 7-9: Un struct con algunos campos públicos y algunos campos privados</span>

Debido a que el campo `toast` en el struct `back_of_house::Breakfast` es público, en `eat_at_restaurant` podemos escribir y leer en el campo `toast` usando la notación de puntos. Tenga en cuenta que no podemos usar el campo `seasonal_fruit` en `eat_at_restaurant` porque `seasonal_fruit` es privado. ¡Intente descomentar la línea que modifica el valor del campo `season_fruit` para ver qué error obtiene!

Además, tenga en cuenta que debido a que `back_of_house::Breakfast` tiene un campo privado, el struct debe proporcionar una función asociada pública que construya una instancia de `Breakfast` (la llamamos `summer` aquí). Si `Breakfast` no tuviera esa función, no podríamos crear una instancia de `Breakfast` en `eat_at_restaurant` porque no podríamos establecer el valor del campo privado `seasonal_fruit` en `eat_at_restaurant`.

Por el contrario, si hacemos público un enum, todas sus variantes son públicas. Solo necesitamos `pub` antes de la palabra clave `enum`, como se muestra en el Listado 7-10.

<span class="filename">Filename: src/lib.rs</span>

```rust
mod back_of_house {
    pub enum Appetizer {
        Soup,
        Salad,
    }
}

pub fn eat_at_restaurant() {
    let order1 = back_of_house::Appetizer::Soup;
    let order2 = back_of_house::Appetizer::Salad;
}
```

<span class="caption">Listado 7-10: Designar un enum como público hace que todas sus variantes sean públicas</span>

Debido a que hicimos público el enum `Appetizer`, podemos usar las variantes `Soup` y `Salad` en `eat_at_restaurant`.

Los enums no son muy útiles a menos que sus variantes sean públicas. Sería molesto tener que anotar todas las variantes de enum con `pub` en todos los casos, por lo que el valor predeterminado para las variantes de enum es *público*. Los structs suelen ser útiles sin que sus campos sean públicos, por lo que los campos de struct siguen la regla general de que todo sea privado de forma predeterminada, a menos que se anote con `pub`.

Hay una situación más relacionada con `pub` que no hemos cubierto, y esa es nuestra última característica del module system: la palabra clave `use`. Primero cubriremos `use` por sí solo, y luego mostraremos cómo combinar `pub` y `use`.