## Definiendo un *Enum*

Donde los structs le brindan una forma de agrupar campos y datos relacionados, como un `Rectangle` con su `width` y `height`, los enums le brindan una forma de decir que un valor es uno de un posible conjunto de valores. Por ejemplo, podemos querer decir que `Rectangle` es una de un conjunto de formas posibles que también incluye `Circle` y `Triangle`. Para hacer esto, Rust nos permite codificar estas posibilidades como un enum.

Veamos una situación que querríamos expresar en código y veamos por qué los enums son útiles y más apropiados que los structs en este caso. Digamos que necesitamos trabajar con direcciones IP. Actualmente, se utilizan dos estándares principales para las direcciones IP: la versión cuatro y la versión seis. Debido a que estas son las únicas posibilidades para una dirección IP con las que se encontrará nuestro programa, podemos *enumerar* todas las variantes posibles, que es donde la enumeración (*enum*) recibe su nombre.

Cualquier dirección IP puede ser una dirección versión cuatro o seis, pero no ambas al mismo tiempo. Esa propiedad de las direcciones IP hace que la estructura de datos enum sea adecuada, porque un valor de enum solo puede ser una de sus variantes. Tanto las direcciones de la versión cuatro como las de la versión seis siguen siendo fundamentalmente direcciones IP, por lo que deben tratarse como del mismo tipo cuando el código maneja situaciones que se aplican a cualquier tipo de dirección IP.


Podemos expresar este concepto en código definiendo un enum `IpAddrKind` y listando los tipos posibles que una dirección IP puede ser, `V4` y `V6`. Estas son las *variantes* del enum:

```rust
enum IpAddrKind {
    V4,
    V6,
}
#
# fn main() {
#     let four = IpAddrKind::V4;
#     let six = IpAddrKind::V6;
#
#     route(IpAddrKind::V4);
#     route(IpAddrKind::V6);
# }
#
# fn route(ip_kind: IpAddrKind) {}
```

`IpAddrKind` ahora es un tipo de datos personalizado que podemos usar en cualquier parte de nuestro código.

### Valores *Enum*

Podemos crear instancias de cada una de las dos variantes de `IpAddrKind` de esta manera:

```rust
# enum IpAddrKind {
#     V4,
#     V6,
# }
#
# fn main() {
    let four = IpAddrKind::V4;
    let six = IpAddrKind::V6;
#
#     route(IpAddrKind::V4);
#     route(IpAddrKind::V6);
# }
#
# fn route(ip_kind: IpAddrKind) {}
```

Tenga en cuenta que las variantes del enum son *espacios de nombre* bajo su identificador, y usamos dos puntos dobles para separar los dos. Esto es útil porque ahora ambos valores `IpAddrKind::V4` y `IpAddrKind::V6` son del mismo tipo: `IpAddrKind`. Entonces podemos, por ejemplo, definir una función que tome cualquier `IpAddrKind`:

```rust
# enum IpAddrKind {
#     V4,
#     V6,
# }
#
# fn main() {
#     let four = IpAddrKind::V4;
#     let six = IpAddrKind::V6;
#
#     route(IpAddrKind::V4);
#     route(IpAddrKind::V6);
# }
#
fn route(ip_kind: IpAddrKind) {}
```

Y podemos llamar a esta función con cualquiera de las variantes:

```rust
# enum IpAddrKind {
#     V4,
#     V6,
# }
#
# fn main() {
#     let four = IpAddrKind::V4;
#     let six = IpAddrKind::V6;
#
    route(IpAddrKind::V4);
    route(IpAddrKind::V6);
# }
#
# fn route(ip_kind: IpAddrKind) {}
```

El uso de enums tiene aún más ventajas. Pensando más en nuestro tipo de dirección IP, en este momento no tenemos una forma de almacenar *los datos* reales de la dirección IP; sólo sabemos de qué clase es. Dado que acaba de aprender acerca de structs en el Capítulo 5, es posible que tenga la tentación de abordar este problema con structs como se muestra en el Listado 6-1.

```rust
# fn main() {
    enum IpAddrKind {
        V4,
        V6,
    }

    struct IpAddr {
        kind: IpAddrKind,
        address: String,
    }

    let home = IpAddr {
        kind: IpAddrKind::V4,
        address: String::from("127.0.0.1"),
    };

    let loopback = IpAddr {
        kind: IpAddrKind::V6,
        address: String::from("::1"),
    };
# }
```

<span class="caption">Listing 6-1: Almacenar los datos y la variante `IpAddrKind` de una dirección IP usando un `struct`</span>

Aquí, hemos definido un struct `IpAddr` que tiene dos campos: un campo `kind` que es de tipo `IpAddrKind` (el enum que definimos previamente) y un campo `address` de tipo `String`. Tenemos dos instancias de este struct. El primero, `home`, tiene el valor `IpAddrKind::V4` como su valor `kind` con los datos de dirección asociados de `127.0.0.1`. La segunda instancia es `loopback`. Esta tiene la otra variante de `IpAddrKind` como su valor `kind`,`V6`, y tiene la dirección `::1` asociada a ella. Hemos utilizado un struct para agrupar los valores `kind` y `address` juntos,por lo que ahora la variante está asociada con el valor.

Sin embargo, representar el mismo concepto usando solo un enum es más conciso: en lugar de un enum dentro de un struct, podemos colocar los datos directamente en cada variante del *enum*. Esta nueva definición del enum `IpAddr` dice que las variantes` V4` y `V6` tendrán valores` String` asociados:

```rust
# fn main() {
    enum IpAddr {
        V4(String),
        V6(String),
    }

    let home = IpAddr::V4(String::from("127.0.0.1"));

    let loopback = IpAddr::V6(String::from("::1"));
# }
```

Adjuntamos datos a cada variante del enum directamente, por lo que no hay necesidad de un struct adicional. Aquí también es más fácil ver otro detalle de cómo funcionan los enums: el nombre de cada variante del enum que definimos también se convierte en una función que construye una instancia del enum. Es decir, `IpAddr::V4()` es una llamada de función que toma un argumento `String` y devuelve una instancia del tipo `IpAddr`. Obtenemos automáticamente esta función constructora definida como un resultado de definir el enum.

Hay otra ventaja de usar un enum en lugar de un struct: cada variante puede tener diferentes tipos y cantidades de datos asociados. Las direcciones IP de tipo versión cuatro siempre tendrán cuatro componentes numéricos que tendrán valores entre 0 y 255. Si quisiéramos almacenar direcciones `V4` como cuatro valores `u8` pero aún expresar direcciones `V6` como un valor `String`, no podríamos con un struct. Los enums manejan este caso con facilidad:

```rust
# fn main() {
    enum IpAddr {
        V4(u8, u8, u8, u8),
        V6(String),
    }

    let home = IpAddr::V4(127, 0, 0, 1);

    let loopback = IpAddr::V6(String::from("::1"));
# }
```

Hemos mostrado varias formas diferentes de definir estructuras de datos para almacenar la versión cuatro y la versión seis de las direcciones IP. Sin embargo, resulta que querer almacenar direcciones IP y codificar de qué clase son es tan común que [la biblioteca estándar tiene una definición que podemos usar!][IpAddr] <!-- ignore --> Veamos cómo la biblioteca estándar define `IpAddr`: tiene el enum exacto y las variantes que hemos definido y utilizado, pero incorpora los datos de dirección dentro de las variantes en forma de dos structs diferentes,
que se definen de manera diferente para cada variante:

[IpAddr]: https://doc.rust-lang.org/std/net/enum.IpAddr.html

```rust
# #![allow(unused)]
# fn main() {
struct Ipv4Addr {
    // --snip--
}

struct Ipv6Addr {
    // --snip--
}

enum IpAddr {
    V4(Ipv4Addr),
    V6(Ipv6Addr),
}
# }
```

Este código ilustra que puede poner cualquier tipo de datos dentro de una variante *enum*: strings, tipos numéricos o structs, por ejemplo. ¡Incluso puede incluir otro enum! Además, los tipos de la biblioteca estándar a menudo no son mucho más complicados de lo que se le podría ocurrir.

Tenga en cuenta que, aunque la biblioteca estándar contiene una definición para `IpAddr`,
aún podemos crear y usar nuestra propia definición sin conflicto porque no hemos incorporado
la definición de la biblioteca estándar a nuestro scope. Hablaremos más sobre incluir tipos en el scope en el Capítulo 7.

Veamos otro ejemplo de un enum en el Listado 6-2: este tiene una amplia variedad de tipos incrustados en sus variantes.

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
#
# fn main() {}
```

<span class="caption">Listing 6-2: El *enum* `Message` cuyas variantes almacenan diferentes cantidades y tipos de valores</span>

Este enum tiene cuatro variantes con diferentes tipos:

* `Quit` no tiene datos asociados a ella en absoluto.
* `Move` tiene campos con nombre como lo hace un struct.
* `Write` incluye un solo `String`.
* `ChangeColor` incluye tres valores `i32`.

Definir un enum con variantes como las del Listado 6-2 es similar a
definir diferentes tipos de definiciones de struct, excepto que *enum* no usa
la palabra clave `struct` y todas las variantes se agrupan bajo el tipo `Mensaje`. Los
siguientes structs podrían contener los mismos datos que contienen las variantes
*enum* anteriores:

```rust
struct QuitMessage; // unit struct
struct MoveMessage {
    x: i32,
    y: i32,
}
struct WriteMessage(String); // tuple struct
struct ChangeColorMessage(i32, i32, i32); // tuple struct
#
# fn main() {}
```

Pero si usáramos los diferentes structs, cada uno de los cuales tiene su propio tipo, no podríamos definir tan fácilmente una función para tomar cualquiera de estas clases de mensajes como lo haríamos con el enum `Message` definido en el Listado 6-2, que es un solo tipo.

Hay una similitud más entre enums y structs: del mismo modo que podemos definir métodos en los structs usando `impl`, también podemos definir métodos en los enums. Aquí hay un método llamado `call` que podríamos definir en nuestro enum `Message`:

```rust
# fn main() {
# enum Message {
#     Quit,
#     Move { x: i32, y: i32 },
#     Write(String),
#     ChangeColor(i32, i32, i32),
# }
#
impl Message {
    fn call(&self) {
        // el cuerpo del método se definiría aquí
    }
}

let m = Message::Write(String::from("hello"));
m.call();
# }
```

El cuerpo del método usaría `self` para obtener el valor que llamamos en el método. En este ejemplo, hemos creado una variable `m` que tiene el valor `Message::Write(String::from("hello"))`, y eso es lo que será `self` en el cuerpo del método `call` cuando `m.call()` se ejecuta.

Veamos otro enum en la biblioteca estándar que es muy común y útil: `Option`.

### El *Enum* `Option` y sus Ventajas Sobre Valores Nulos

Esta sección explora un caso de estudio de `Option`, que es otro enum definido por la biblioteca estándar. El tipo `Option` codifica el escenario muy común en el que un valor podría ser algo o podría no ser nada.

Por ejemplo, si solicita el primero de una lista que contiene elementos, obtendrá un valor. Si solicita el primer elemento de una lista vacía, no obtendría nada. Expresar este concepto en términos del sistema de tipos significa que el compilador puede verificar si ha manejado todos los casos que debería manejar; esta funcionalidad puede prevenir errores que son extremadamente comunes en otros lenguajes de programación.

El diseño del lenguaje de programación a menudo se piensa en términos de las características que incluye, pero las características que excluye también son importantes. Rust no tiene la característica null que tienen muchos otros lenguajes. *Null* es un valor que significa que no hay ningún valor allí. En lenguajes con null, las variables siempre pueden estar en uno de dos estados: null o not-null.

En su presentación de 2009 “Null References: The Billion Dollar Mistake”, Tony Hoare, el inventor de null, dice lo siguiente:

> Yo lo llamo mi error de mil millones de dólares. En ese momento, estaba diseñando el primer sistema completo de tipos para referencias en un lenguaje orientado a objetos. Mi objetivo era garantizar que todos los usos de las referencias fueran absolutamente seguros, con la verificación realizada automáticamente por el compilador. Pero no pude resistir la tentación de poner una referencia nula, simplemente porque era muy fácil de implementar. Esto ha llevado a innumerables errores, vulnerabilidades y bloqueos del sistema, que probablemente han causado mil millones de dólares en dolor y daños en los últimos cuarenta años.

El problema con los valores nulos es que si intenta usar un valor nulo como un valor no nulo, obtendrá algún tipo de error. Debido a que esta propiedad nula o no nula es generalizada, es extremadamente fácil cometer este tipo de error.

Sin embargo, el concepto que *null* intenta expresar sigue siendo útil: un valor nulo es un valor que actualmente no es válido o está ausente por algún motivo.

El problema no es realmente con el concepto sino con la implementación particular.
Como tal, Rust no tiene *null*, pero tiene un enum que puede codificar el
concepto de un valor presente o ausente. Este enum es `Opción<T>`, y está
[definido por la biblioteca estándar][option] <!-- ignore --> de la siguiente manera:

[option]: https://doc.rust-lang.org/std/option/enum.Option.html

```rust
# #![allow(unused)]
# fn main() {
enum Option<T> {
    None,
    Some(T),
}
# }
```

El enum `Option<T>` es tan útil que incluso está incluido en el *prelude*; no es necesario que lo incluya explícitamente en el scope. Sus variantes también se incluyen en el *prelude*: puede usar `Some` y `None` directamente sin el prefijo `Option::`. El enum `Option<T>` sigue siendo solo un enum regular, y `Some(T)` y `None` siguen siendo variantes del tipo `Option<T>`.

La sintaxis `<T>` es una característica de Rust de la que aún no hemos hablado. Es un parámetro
de tipo genérico, y trataremos los genéricos más detalladamente en el Capítulo 10. Por ahora,
todo lo que necesita saber es que `<T>` significa que la variante `Some` del enum `Option` puede contener una pieza de datos de cualquier tipo, y que cada tipo concreto que se usa en lugar de `T` hace que el tipo general `Option<T>` sea un tipo diferente. Estos son algunos ejemplos del uso de valores de `Option` para contener tipos numéricos y tipos string:

```rust
# fn main() {
    let some_number = Some(5);
    let some_char = Some('e');

    let absent_number: Option<i32> = None;
# }
```
El tipo de `some_number` es `Option<i32>`. El tipo de `some_char` es `Option<char>`, que es un tipo diferente. Rust puede inferir estos tipos porque hemos especificado un valor dentro de la variante `Some`. Para `absent_number`, Rust requiere que anotemos el tipo general de `Option`: el compilador no puede inferir el tipo que tendrá la variante `Some` correspondiente mirando solo un valor `None`. Aquí, le decimos a Rust que queremos que `absent_number` sea del tipo `Option<i32>`.

Cuando tenemos un valor `Some`, sabemos que un valor está presente y el valor se mantiene dentro de `Some`. Cuando tenemos un valor `None`, en cierto sentido, significa lo mismo que null: no tenemos un valor válido. Entonces, ¿por qué es mejor tener `Option<T>` que tener null?

En resumen, debido a que `Option<T>` y `T` (donde `T` puede ser de cualquier tipo) son tipos diferentes, el compilador no nos permitirá usar un valor de `Option<T>` como si definitivamente fuera un valor válido. Por ejemplo, este código no se compilará porque está intentando agregar un `i8` a un `Option<i8>`:

```rust,ignore
# fn main() {
    let x: i8 = 5;
    let y: Option<i8> = Some(5);

    let sum = x + y;
# }
```

Si ejecutamos este código, recibimos un mensaje de error como este:

```text
$ cargo run
   Compiling enums v0.1.0 (file:///projects/enums)
error[E0277]: cannot add `Option<i8>` to `i8`
 --> src/main.rs:5:17
  |
5 |     let sum = x + y;
  |                 ^ no implementation for `i8 + Option<i8>`
  |
  = help: the trait `Add<Option<i8>>` is not implemented for `i8`
  = help: the following other types implement trait `Add<Rhs>`:
            <&'a f32 as Add<f32>>
            <&'a f64 as Add<f64>>
            <&'a i128 as Add<i128>>
            <&'a i16 as Add<i16>>
            <&'a i32 as Add<i32>>
            <&'a i64 as Add<i64>>
            <&'a i8 as Add<i8>>
            <&'a isize as Add<isize>>
          and 48 others

For more information about this error, try `rustc --explain E0277`.
error: could not compile `enums` due to previous error
```

¡Intenso! En efecto, este mensaje de error significa que Rust no entiende cómo
sumar un `i8` y un `Opción<i8>`, porque son tipos diferentes. Cuando tenemos
un valor de un tipo como `i8` en Rust, el compilador se asegurará de que siempre tengamos
un valor válido. Podemos proceder con confianza sin tener que verificar el valor
nulo antes de usar ese valor. Solo cuando tenemos un `Opción<i8>`
(o el tipo de valor con el que estemos trabajando) tenemos que preocuparnos por
la posibilidad de no tener un valor, y el compilador se asegurará de que manejemos ese
caso antes de usar el valor.

En otras palabras, debe convertir un `Opción<T>` a un `T` antes de poder realizar operaciones `T` con él. Generalmente, esto ayuda a detectar uno de los problemas más comunes con *null*: asumir que algo no es nulo cuando en realidad lo es.

Eliminar el riesgo de asumir incorrectamente un valor no nulo lo ayuda a tener más confianza en su código. Para tener un valor que posiblemente sea nulo, debe optar explícitamente haciendo que el tipo de ese valor sea `Option<T>`. Luego, cuando usa ese valor, debe manejar explícitamente el caso cuando el valor es nulo. Siempre que un valor tenga un tipo que no sea `Option<T>`, puede suponer con seguridad que el valor no es nulo. Esta fue una decisión de diseño deliberada de Rust para limitar la omnipresencia de *null* y aumentar la seguridad del código Rust.

Entonces, ¿cómo se obtiene el valor `T` de una variante `Some` cuando tiene un valor de tipo `Opción<T>` para que pueda usar ese valor? El enum `Option<T>` tiene una gran cantidad de métodos que son útiles en una variedad de situaciones; puede verlos en [su documentación][docs] <!-- ignore -->. Familiarizarse con los métodos en `Option<T>` será extremadamente útil en su
viaje con Rust.

[docs]: https://doc.rust-lang.org/std/option/enum.Option.html

En general, para usar un valor `Option<T>`, desea tener un código que maneje cada variante. Desea un código que se ejecutará solo cuando tenga un valor `Some(T)`, y este código podrá usar el `T` interno. Desea que se ejecute algún otro código si tiene un valor `None` y ese código no tiene un valor `T` disponible. La expresión `match` es una *estructura de control* que hace justamente esto cuando se usa con *enums*: ejecutará un código diferente dependiendo de la variante del enum que tenga, y ese código puede usar los datos dentro del valor coincidente.
