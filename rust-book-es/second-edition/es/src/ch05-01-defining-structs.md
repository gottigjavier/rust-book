## Definir e Instanciar *Structs*

Los *structs* son similares a las tuplas, discutidas en la sección ["El Tipo Tupla"](ch03-02-data-types.html#el-tipo-tupla-tuple), en el sentido de que ambos contienen múltiples valores relacionados. Al igual que las tuplas, las piezas de un *struct* pueden ser de diferentes tipos. A diferencia de las tuplas, en un *struct* nombrará cada dato para que quede claro lo que significan los valores. Agregar estos nombres significa que los *structs* son más flexibles que las tuplas: no tiene que confiar en el orden de los datos para especificar o acceder a los valores de una instancia.

Para definir un *struct*, ingresamos la palabra clave `struct` y nombramos el
*struct* completo. El nombre de un *struct* debe describir el significado de
las piezas de datos que se agrupan. Luego, dentro de las llaves, definimos los nombres
y tipos de los datos, que llamamos *campos*. Por ejemplo, el Listado 5-1 muestra
un *struct* que almacena información sobre una cuenta de usuario.

```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}
#
# fn main() {}
```

<span class="caption">Listing 5-1: Definición del *struct* `User`</span>

Para usar un *struct* después de haberlo definido, creamos una *instancia* de ese *struct* especificando valores concretos para cada uno de los campos. Creamos una instancia indicando el nombre del *struct* y luego agregamos llaves que contienen pares *(clave: valor)* `key: value`, donde las claves son los nombres de los campos y los valores son los datos que queremos almacenar en esos campos. No tenemos que especificar los campos en el mismo orden en que los declaramos en el *struct*. En otras palabras, la definición de un *struct* es como una plantilla general para el tipo, y las instancias completan esa plantilla con datos particulares para crear valores de ese tipo. Por ejemplo, podemos declarar un usuario particular como se muestra en el Listado 5-2.

```rust
# struct User {
#     active: bool,
#     username: String,
#     email: String,
#     sign_in_count: u64,
# }
#
# fn main() {
    let user1 = User {
        email: String::from("someone@example.com"),
        username: String::from("someusername123"),
        active: true,
        sign_in_count: 1,
    };
# }
```

<span class="caption">Listing 5-2: Creando una instancia del *struct* `User`</span>

Para obtener un valor específico de un *struct*, usamos la notación de punto. Por ejemplo, para acceder a la dirección de correo electrónico de este usuario, usamos `user1.email`. Si la instancia es mutable, podemos cambiar un valor usando la notación de punto y la asignación en un campo particular. El listado 5-3 muestra cómo cambiar el valor en el campo `email` de una instancia `User` mutable.

```rust
# struct User {
#     active: bool,
#     username: String,
#     email: String,
#     sign_in_count: u64,
# }
#
fn main() {
    let mut user1 = User {
        email: String::from("someone@example.com"),
        username: String::from("someusername123"),
        active: true,
        sign_in_count: 1,
    };

    user1.email = String::from("anotheremail@example.com");
}
```

<span class="caption">Listing 5-3: Cambiar el valor en el campo `email` de una instancia `User`</span>

Tenga en cuenta que toda la instancia debe ser mutable; Rust no nos permite marcar solo ciertos campos como mutables. Al igual que con cualquier expresión, podemos construir una nueva instancia del *struct* como la última expresión en el cuerpo de la función para devolver implícitamente esa nueva instancia.

El listado 5-4 muestra una función `build_user` que devuelve una instancia `User` con el correo electrónico y el nombre de usuario dados. El campo `active` obtiene el valor de `true`, y `sign_in_count` obtiene un valor de `1`.

```rust
# struct User {
#     active: bool,
#     username: String,
#     email: String,
#     sign_in_count: u64,
# }
#
fn build_user(email: String, username: String) -> User {
    User {
        email: email,
        username: username,
        active: true,
        sign_in_count: 1,
    }
}
#
# fn main() {
#     let user1 = build_user(
#         String::from("someone@example.com"),
#         String::from("someusername123"),
#     );
# }
```

<span class="caption">Listing 5-4: Una función `build_user` que toma un correo electrónico y un nombre de usuario y devuelve una instancia `User`</span>

Tiene sentido nombrar los parámetros de la función con el mismo nombre que los campos del *struct*, pero tener que repetir los nombres y variables de campo `email` y` username` es un poco tedioso. Si el *struct* tuviera más campos, repetir cada nombre sería aún más molesto. Afortunadamente, ¡hay una abreviatura conveniente!

### Uso de *Field Init Shorthand*

Debido a que los nombres de los parámetros y los nombres de los campos del *struct* son
exactamente los mismos en el Listado 5-4, podemos usar la sintaxis *field init shorthand* (*abreviación de inicio de campo*) para reescribir `build_user` y así se comporte exactamente igual pero no tenga la repetición `email` y `username`, como se muestra en el Listado 5-5.

```rust
# struct User {
#     active: bool,
#     username: String,
#     email: String,
#     sign_in_count: u64,
# }
#
fn build_user(email: String, username: String) -> User {
    User {
        email,
        username,
        active: true,
        sign_in_count: 1,
    }
}
#
# fn main() {
#     let user1 = build_user(
#         String::from("someone@example.com"),
#         String::from("someusername123"),
#     );
# }
```

<span class="caption">Listing 5-5: Una función `build_user` que usa *field init shorthand* porque los parámetros` email` y `username` tienen el mismo nombre que los campos struct</span>

Aquí, estamos creando una nueva instancia del *struct* `User`, que tiene un campo llamado `email`. Queremos establecer el valor del campo `email` al valor en el parámetro `email` de la función `build_user`. Debido a que el campo `email` y el parámetro `email` tienen el mismo nombre, solo necesitamos escribir `email` en lugar de `email: email`.

### Crear Instancias desde Otras Instancias con la Sintaxis de Actualización de Struct

A menudo es útil crear una nueva instancia de un *struct* que utiliza la mayoría de los valores de una instancia anterior, pero cambia algunos. Pude hacer esto usando *struct update syntax* (sintaxis de actualización de struct).

Primero, en el Listado 5-6 mostramos cómo crear una nueva instancia `User` en `user2`
normalmente, sin la sintaxis de actualización. Establecemos un nuevo valor para `email` pero para los demás campos usamos los mismos valores de `user1` que creamos en el Listado 5-2.

```rust
# struct User {
#     active: bool,
#     username: String,
#     email: String,
#     sign_in_count: u64,
# }
#
fn main() {
    // --snip--

#     let user1 = User {
#         email: String::from("someone@example.com"),
#         username: String::from("someusername123"),
#         active: true,
#         sign_in_count: 1,
#     };
#
    let user2 = User {
        active: user1.active,
        username: user1.username,
        email: String::from("another@example.com"),
        sign_in_count: user1.sign_in_count,
    };
}
```

<span class="caption">Listing 5-6: Creando una nueva instancia de `User` usando valores de `user1`</span>

Usando *struct update syntax*, podemos lograr el mismo efecto con menos código, como se muestra en el listado 5-7. La sintaxis `..` especifica que los campos restantes no establecidos explícitamente deben tener el mismo valor que los campos en la instancia dada.

```rust
# struct User {
#     active: bool,
#     username: String,
#     email: String,
#     sign_in_count: u64,
# }
#
fn main() {
    // --snip--

#     let user1 = User {
#         email: String::from("someone@example.com"),
#         username: String::from("someusername123"),
#         active: true,
#         sign_in_count: 1,
#     };
#
    let user2 = User {
        email: String::from("another@example.com"),
        ..user1
    };
}
```

<span class="caption">Listing 5-7: Usar *struct update syntax* para establecer el nuevo
valor de `email` para una instancia `User`, pero usar el resto de los valores de los campos de la instancia desde la variable `user1`</span>

El código del Listado 5-7 también crea una instancia en `user2` que tiene un valor diferente para `email`, pero tiene los mismos valores para los campos `username`, `active` y `sign_in_count` de `user1`. La línea `..user1` debe ser la última para especificar que los campos restantes deben obtener sus valores de los campos correspondientes en `user1`, pero podemos optar por especificar valores para tantos campos como queramos en cualquier orden, independientemente del orden de los campos en la definición de la estructura.

Tenga en cuenta que *struct update syntax* usa `=` como una asignación; esto se debe a que mueve los datos, tal como vimos en la sección [“Formas en que Interactúan las Variables y los Datos: mover”](ch04-01-what-is-ownership.html#formas-en-que-interact%C3%BAan-las-variables-y-los-datos-mover). En este ejemplo, ya no podemos usar `user1` después de crear `user2` porque el `String` en el campo `username` de `user1` se movió a `user2`. Si le hubiéramos dado a `user2` nuevos valores `String` para `email` y `username` y, por lo tanto, solo usáramos los valores `active` y `sign_in_count` de `user1`, entonces `user1` seguiría siendo válido después de crear `user2`. Los tipos de `active` y `sign_in_count` son tipos que implementan el *trait* Copiar, por lo que se aplicaría el comportamiento que discutimos en la sección ["Datos de Sólo-Pila: Copiar"](ch04-01-what-is-ownership.html#datos-de-s%C3%B3lo-pila-copiar).

### Usar *Tuple Structs* sin Campos Nombrados para Crear Diferentes Tipos

Rust también admite estructuras que se parecen a las tuplas, llamadas *tuple structs* (*estructuras de tupla*). Las *tuple structs* tienen el significado adicional que proporciona el nombre del *struct*, pero no tienen nombres asociados con sus campos; más bien, solo tienen los tipos de los campos. Las *tuple structs* son útiles cuando se quiere dar un nombre a la tupla completa y hacer que la tupla sea un tipo diferente de otras tuplas, y cuando nombrar cada campo como en un *struct* regular sería detallado o redundante.

Para definir una *tuple struct*, comience con la palabra clave `struct` y el nombre del *struct* seguido de los tipos en la tupla. Por ejemplo, aquí hay definiciones y usos de dos *tuple structs* denominadas `Color` y `Point`:

```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

fn main() {
    let black = Color(0, 0, 0);
    let origin = Point(0, 0, 0);
}
```

Tenga en cuenta que los valores `black` y `origin` son de tipos diferentes, porque son instancias de diferentes *tuple structs*. Cada *struct* que define es de su propio tipo, aunque los campos dentro del *struct* tienen los mismos tipos. Por ejemplo, una función que toma un parámetro de tipo `Color` no puede tomar un `Point` como argumento, aunque ambos tipos están compuestos por tres valores `i32`. Por otro lado, las instancias de *tuple struct* son similares a las tuplas en el sentido de que puede desestructurarlas en sus piezas individuales, y puede usar un `.` seguido del índice para acceder a un valor individual.

### *Unit-Like Structs*. Structs sin Ningún Campo

¡También puede definir *structs* que no tienen ningún campo! Estos se denominan *unit-like structs* (*estructuras similares a unidades*) porque se comportan de manera similar a (), el tipo *unit* (*unidad*) que mencionamos al final de la sección ["El Tipo Tupla"](ch03-02-data-types.html#el-tipo-tupla-tuple). Los *unit-like structs* pueden ser útiles cuando necesita implementar un *trait* en algún tipo pero no tiene ningún dato que desee almacenar en el tipo en sí. Hablaremos de los *traits*[^1] en el Capítulo 10. Aquí hay un ejemplo de cómo declarar e instanciar un *unit struct* *(estructura unidad)* llamado `AlwaysEqual`:

```rust,ignore
struct AlwaysEqual;

fn main() {
    let subject = AlwaysEqual;
}
```

Para definir `AlwaysEqual`, usamos la palabra clave `struct`, el nombre que queremos, luego un punto y coma. ¡No hay necesidad de corchetes o paréntesis! Entonces podemos obtener una instancia de `AlwaysEqual` en la variable `subject` de una manera similar: usando el nombre que definimos, sin corchetes ni paréntesis. Imagine que más adelante implementaremos un comportamiento para este tipo, de modo que cada instancia de `AlwaysEqual` siempre sea igual a cada instancia de cualquier otro tipo, tal vez para tener un resultado conocido con fines de prueba. ¡No necesitaríamos ningún dato para implementar ese comportamiento! Verá en el Capítulo 10 cómo definir *traits* e implementarlos en cualquier tipo, incluidas las *unit-like structs*.

> ### Propiedad de los Datos de un Struct
>
> En la definición del *struct* `User` en el listado 5-1, utilizamos el tipo
> `String` para la propiedad en lugar del tipo *string slice* `&str`. Esta es
> una elección deliberada porque queremos que cada instancia de este
> *struct* sea propietaria de todos sus datos y que los datos sean
> válidos mientras el *struct* completo sea válido.
>
> Es posible que los *structs* almacenen referencias a datos que son propiedad
> de otra cosa, pero para hacerlo se requiere el uso de *lifetimes*, una
> característica de Rust que discutiremos en el Capítulo 10. Lifetimes
> garantiza que los datos a los que hace referencia un *struct* son
> válidos mientras el *struct* lo sea. Digamos que intenta almacenar
> una referencia en un *struct* sin especificar *lifetimes*, como esta, que
> no funcionará:
>
> <span class="filename">Filename: src/main.rs</span>
>
> ```rust,ignore
> struct User {
>     active: bool,
>     username: &str,
>     email: &str,
>     sign_in_count: u64,
> }
>
> fn main() {
>     let user1 = User {
>         email: "someone@example.com",
>         username: "someusername123",
>         active: true,
>         sign_in_count: 1,
>     };
> }
> ```
>
> El compilador se quejará de que necesita especificadores *lifetime*:
>
>
> ```text
> $ cargo run
>    Compiling structs v0.1.0 (file:///projects/structs)
> error[E0106]: missing lifetime specifier
>  --> src/main.rs:3:15
>   |
> 3 |     username: &str,
>   |               ^ expected named lifetime parameter
>   |
> help: consider introducing a named lifetime parameter
>   |
> 1 ~ struct User<'a> {
> 2 |     active: bool,
> 3 ~     username: &'a str,
>   |
>
> error[E0106]: missing lifetime specifier
>  --> src/main.rs:4:12
>   |
> 4 |     email: &str,
>   |            ^ expected named lifetime parameter
>   |
> help: consider introducing a named lifetime parameter
>   |
> 1 ~ struct User<'a> {
> 2 |     active: bool,
> 3 |     username: &str,
> 4 ~     email: &'a str,
>   |
>
> For more information about this error, try `rustc --explain E0106`.
> error: could not compile `structs` due to 2 previous errors
> ```
>
> En el Capítulo 10, discutiremos cómo solucionar estos errores para que pueda
> almacenar referencias en *structs*, pero por ahora, corregiremos errores como
> estos utilizando tipos propios como `String` en lugar de referencias como `&str`.


[^1] <small>Nota del traductor: por el momento, puede asemejar los *traits* a lo que son las *intefaces* en otros lenguajes, aunque luego se verán las diferencias.</small>