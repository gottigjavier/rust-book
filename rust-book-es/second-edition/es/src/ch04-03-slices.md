## El tipo Slice (Segmento)

Los *Slices* le permiten hacer referencia a un secuencia contigua de elementos en una colección en lugar de toda la colección. Un *Slice* es una especie de referencia, por lo que no tiene la propiedad.

He aquí un pequeño problema de programación: escriba una función que tome una cadena (*string*) de palabras (separadas por espacios) y devuelva la primera palabra que encuentre en ese *string*. Si la función no encuentra un espacio en el *string*, todo el *string* debe ser una palabra, por lo que se debe devolver el *string* completo. 

Analicemos cómo escribiríamos la firma de esta función sin usar *Slices*, para entender el problema que luego resolverán los *Slices*:

```rust,ignore
fn first_word(s: &String) -> ?
```

La función `first_word` tiene un `&String` como parámetro. No queremos
la *propiedad*, así que eso está bien. Pero, ¿qué deberíamos devolver?
Realmente no tenemos una forma de hablar sobre *parte* de un *string*. Sin embargo,
podríamos devolver el índice del final de la palabra, indicado por un espacio. Probemos eso, como se muestra en el Listado 4-7.

<span class="filename">Filename: src/main.rs</span>

```rust
fn first_word(s: &String) -> usize {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return i;
        }
    }

    s.len()
}
```

<span class="caption">Listing 4-7: La función `first_word` que devuelve un valor de
índice de byte en el parámetro `String`</span>

Debido a que necesitamos recorrer el `String` elemento por elemento y comprobar si un valor es un espacio, convertiremos nuestro `String` a un array de bytes utilizando el método `as_bytes`:

```rust,ignore
let bytes = s.as_bytes();
```

A continuación, creamos un iterador sobre el array de bytes utilizando el método `iter`:

```rust,ignore
for (i, &item) in bytes.iter().enumerate() {
```

Analizaremos iteradores con más detalle en el [Capítulo 13](ch13-02-iterators.html). Por ahora, sepa que `iter` es un método que devuelve cada elemento de una colección y que `enumerate` adapta el resultado de `iter` y en su lugar devuelve cada elemento como parte de un tupla. El primer elemento de la tupla devuelta de `enumerate` es el índice, y el segundo elemento es una referencia al elemento. Esto es un poco más conveniente que calcular el índice nosotros mismos.

Debido a que el método `enumerate` devuelve una tupla, podemos usar patrones
para desestructurar esa tupla. Hablaremos más sobre los patrones en el [Capítulo 6](ch06-02-match.html). En el bucle `for`, especificamos un patrón que tiene `i` para el
índice en la tupla y `&item` para el byte simple en la tupla. Como de `.iter().enumerate()` obtenemos una referencia al elemento, usamos `&` en el patrón.

Dentro del bucle `for`, buscamos el byte que representa el espacio usando la sintaxis
literal de byte. Si encontramos un espacio, devolvemos la posición. De lo contrario,
devolvemos la longitud del *string* usando `s.len()`:

```rust,ignore
# fn first_word(s: &String) -> usize {
#    let bytes = s.as_bytes();
#
#    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return i;
        }
    }

    s.len()
# }
#
# fn main() {}
```

Ahora tenemos una forma de averiguar el índice del final de la primera palabra en el *string*,
pero hay un problema. Estamos devolviendo un `usize` por sí solo, pero es un número
significativo sólo en el contexto de `&String`. En otras palabras, como es un valor separado de
`String`, no hay garantía de que siga siendo válido en el futuro. Considere el programa en
el Listado 4-8 que usa la función `first_word` del Listado 4-7.

<span class="filename">Filename: src/main.rs</span>

```rust
# fn first_word(s: &String) -> usize {
#     let bytes = s.as_bytes();
#
#     for (i, &item) in bytes.iter().enumerate() {
#         if item == b' ' {
#             return i;
#         }
#     }
#
#     s.len()
# }
#
fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s); // word obtendrá el valor 5

    s.clear(); // esto vacía el String, haciéndolo igual a ""

    // word todavía tiene el valor 5 aquí, pero no hay más un string con el que podamos
    // usar significativamente el valor 5. ¡word ahora es totalmente inválido!
}
```

<span class="caption">Listing 4-8: Almacenar el resultado de llamar a la función `first_word`
y luego cambiar el contenido de `String`</span>

Este programa compila sin ningún error y también lo haría si usáramos `word` después de llamar a `s.clear()`. Debido a que `word` no está conectado en absoluto al estado de `s`, `word` todavía contiene el valor `5`. Podríamos usar ese valor `5` con la variable `s` para intentar extraer la primera palabra, pero esto sería un error porque el contenido de `s` ha cambiado desde que guardamos `5` en `word`.

Tener que preocuparse de que el índice en `word` esté fuera de sincronía con los
datos en `s` es tedioso y propenso a errores. La gestión de estos índices es aún
más frágil si escribimos una función `second_word`. Su firma debería verse así:

```rust,ignore
fn second_word(s: &String) -> (usize, usize) {
```

Ahora estamos rastreando un índice de inicio *y* uno final, y tenemos
aún más valores que se calcularon a partir de datos en un estado particular,
pero que no están vinculados a ese estado en absoluto. Ahora tenemos tres variables
no relacionadas flotando alrededor que deben mantenerse sincronizadas.

Afortunadamente, Rust tiene una solución a este problema: *string slices* (segmentos de cadenas).

### String Slices (Segmentos de Cadenas)

Un *string slice* es una referencia a parte de un `String`, y se ve así:

```rust
# fn main() {
    let s = String::from("hello world");

    let hello = &s[0..5];
    let world = &s[6..11];
# }
```

En lugar de una referencia a todo el `String`, `hello` es una referencia a una parte del `String`, especificado en el agregado extra [0..5]. Creamos segmentos usando un rango entre corchetes especificando `[índice_inicial..índice_final]`, donde el `índice_inicial` es la primera posición en el segmento y el `índice_final` es uno más que la última posición en el segmento. Internamente, la estructura de datos del segmento almacena la posición inicial y la longitud del segmento, que corresponde al `índice_final` menos el `índice_inicial`. Entonces, en el caso de `let world = &s[6..11];`, `world` sería un segmento que contiene un puntero al byte con índice 6 de `s`, con un valor de longitud 5.

La figura 4-6 muestra esto en un diagrama.

<img alt="world containing a pointer to the 6th byte of String s and a length 5" src="img/trpl04-06.svg" class="center" style="width: 50%;" />

<span class="caption">Figure 4-6: *string slice* refiriéndose a parte de un
`String`</span>

Con la sintaxis de rango `..` de Rust, si desea comenzar en el primer índice (cero),
puede omitir el valor antes de los dos puntos. En otras palabras, estos son iguales:

```rust
# #![allow(unused)]
# fn main() {
    let s = String::from("hello");

    let slice = &s[0..2];
    let slice = &s[..2];
# }
```

De la misma manera, si su segmento incluye el último byte del `String`, puede omitir el
número final. Eso significa que estos son iguales:

```rust
# #![allow(unused)]
# fn main() {
    let s = String::from("hello");

    let len = s.len();

    let slice = &s[3..len];
    let slice = &s[3..];
# }
```

También puede omitir ambos valores para tomar un segmento de la cadena completa.
Entonces estos son iguales:

```rust
# #![allow(unused)]
# fn main() {
    let s = String::from("hello");

    let len = s.len();

    let slice = &s[0..len];
    let slice = &s[..];
# }
```

> Nota: Los índices de rango de *string slice* deben darse en límites de caracteres UTF-8 válidos. Si intenta crear un *string slice* en el medio de un caracter multibyte, su programa se cerrará con un error. A los efectos de introducir *string slices*, estamos asumiendo sólo ASCII en esta sección. Una discusión más detallada del manejo de UTF-8 se encuentra en la sección [“Almacenamiento de texto codificado en UTF-8 con Strings”](ch08-02-strings.html) del Capítulo 8.

Con toda esta información en mente, reescribamos `first_word` para devolver un
*slice*. El tipo indicado para “string slice” se escribe como `&str`:

<span class="filename">Filename: src/main.rs</span>

```rust
fn first_word(s: &String) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}
```

Obtenemos el índice para el final de la palabra de la misma manera que lo hicimos en el Listado 4-7, buscando la primera aparición de un espacio. Cuando encontramos un espacio, devolvemos un *string slice* usando el inicio del *string* y el índice del espacio como los índices inicial y final.

Ahora, cuando llamamos a `first_word`, obtenemos un valor único que está
vinculado a los datos subyacentes. El valor se compone de una referencia al
punto inicial del segmento (*slice*) y el número de elementos en ese segmento.

Devolver un segmento también funcionaría para una función `second_word`:

```rust,ignore
fn second_word(s: &String) -> &str {
```

Ahora tenemos una API sencilla que es mucho más difícil de estropear, porque el compilador se asegurará de que las referencias a `String` sigan siendo válidas. ¿Recuerda el error en el programa del Listado 4-8, cuando obtuvimos el índice al final de la primera palabra pero luego borramos el *string*, por lo que nuestro índice no era válido? Ese código era lógicamente incorrecto pero no mostraba ningún error inmediato. Los problemas aparecerían más adelante si siguiéramos intentando usar el índice de la primera palabra con un *string* vaciado. Los *slices* hacen que este error sea imposible y nos avisan que tenemos un problema con nuestro código mucho antes. El uso de la versión *slice* de `first_word` generará un error en tiempo de compilación:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
fn first_word(s: &String) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}

fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s);

    s.clear(); // error!

    println!("the first word is: {}", word);
}
```

Aquí está el error del compilador:

```text
$ cargo run
   Compiling ownership v0.1.0 (file:///projects/ownership)
error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable
  --> src/main.rs:18:5
   |
16 |     let word = first_word(&s);
   |                           -- immutable borrow occurs here
17 | 
18 |     s.clear(); // error!
   |     ^^^^^^^^^ mutable borrow occurs here
19 | 
20 |     println!("the first word is: {}", word);
   |                                       ---- immutable borrow later used here

For more information about this error, try `rustc --explain E0502`.
error: could not compile `ownership` due to previous error
```

Recuerde de las reglas de préstamo que: si tenemos una referencia inmutable a algo, no podemos tener también una referencia mutable. Debido a que `clear` necesita truncar el `String`, necesita obtener una referencia mutable. El `println!` después de la llamada a `clear` usa la referencia en `word`, por lo que la referencia inmutable aún debe estar activa en ese punto. Rust no permite que la referencia mutable en `clear` y la referencia inmutable en `word` existan al mismo tiempo, y la compilación falla. ¡Rust no solo ha hecho que nuestra API sea más fácil de usar, sino que también ha eliminado toda una clase de errores en el momento de la compilación!

#### String Literales son Slices

Recordemos que hablamos sobre *string literales* (*cadenas literales*) que se almacenan dentro del binario. Ahora que sabemos acerca de *slices*, podemos entender correctamente los *string literales*:

```rust
let s = "Hello, world!";
```

El tipo de `s` aquí es `&str`: es un *slice* que apunta a ese punto específico del
binario. Esta es también la razón por la cual los *string literales* son inmutables;
`&str` es una referencia inmutable.

#### String Slices como Parámetros

Saber que podemos tomar segmentos de valores literales y valores `String` nos lleva
a una mejora más en `first_word`, y esa es su firma:

```rust,ignore
fn first_word(s: &String) -> &str {
```

Un *Rustáceo* más experimentado escribiría la firma que se muestra en el Listado 4-9 porque nos permite usar la misma función tanto en los valores `&String` como en los valores `&str`.

```rust,ignore
fn first_word(s: &str) -> &str {
```

<span class="caption">Listing 4-9: Mejorando la función `first_word` usando un *slice string*
para el tipo del parámetro `s`</span>

Si tenemos un *string slice*, podemos pasarlo directamente. Si tenemos un `String`, podemos pasar un *slice* de `String` o una referencia a `String`. Esta flexibilidad aprovecha la *"deref coercions"*, una característica que cubriremos en la sección ["Deref Coercions Implícitas con Funciones y Métodos"](ch15-02-deref.html#implicaciones-deref-impl%C3%ADcitas-con-funciones-y-m%C3%A9todos) del Capítulo 15. Definir una función que toma un *string slice* en lugar de una referencia a un `String` hace que nuestra API sea más general y útil sin perder ninguna funcionalidad:

<span class="filename">Filename: src/main.rs</span>

```rust
# fn first_word(s: &str) -> &str {
#     let bytes = s.as_bytes();
# 
#     for (i, &item) in bytes.iter().enumerate() {
#         if item == b' ' {
#             return &s[0..i];
#         }
#     }
# 
#     &s[..]
# }

fn main() {
    let my_string = String::from("hello world");

    // `first_word` funciona en segmentos de `String`s, ya sean parciales o completos
    let word = first_word(&my_string[0..6]);
    let word = first_word(&my_string[..]);
    
    // `first_word` también funciona en referencias a `String`s, las cuales son 
    // el equivalente a porciones completas de `String`s
    let word = first_word(&my_string);

    // Asignamos una cadena literal 
    let my_string_literal = "hello world";

    // `first_word` funciona en "segmentos" de "cadenas literales", 
    // ya sean parciales o completos
    let word = first_word(&my_string_literal[0..6]);
    let word = first_word(&my_string_literal[..]);

    // Debido a que las "cadenas literales" *ya son* "segmentos de cadena", 
    // esto también funciona, ¡sin la sintaxis de "slice [..]"!
    let word = first_word(my_string_literal);
}
```

### Otros Slices

String slices, como puedes imaginar, son específicas de los *strings*.
Pero también hay un tipo de *slice* más general. Considera este array:

```rust
let a = [1, 2, 3, 4, 5];
```

Del mismo modo que podríamos querer referirnos a una parte de un *string*,
es posible que deseemos referirnos a parte de un array. Lo haríamos así:

```rust
# #![allow(unused)]
# fn main() {
let a = [1, 2, 3, 4, 5];

let slice = &a[1..3];

assert_eq!(slice, &[2, 3]);
# }
```

Este *slice* tiene el tipo `&[i32]`. Funciona de la misma manera que los *string slices*,
almacenando una referencia al primer elemento y una longitud. Utilizará este tipo de *slices* para todo tipo de otras colecciones. Discutiremos sobre estas colecciones en detalle cuando hablemos de vectores en el Capítulo 8.

## Resumen

Los conceptos de *ownership* (propiedad), *borrowing* (préstamo) y *slices* (segmentos) garantizan la seguridad de la memoria en los programas de Rust en tiempo de compilación. El lenguaje Rust le permite controlar el uso de la memoria de la misma manera que otros lenguajes de programación de sistemas, pero hacer que el propietario de los datos limpie automáticamente esos datos cuando el propietario se sale del *scope* (alcance) significa que no tiene que escribir y depurar código adicional para obtener este control.

La propiedad afecta el funcionamiento de muchas otras partes de Rust, por lo que hablaremos
más de estos conceptos a lo largo del resto del libro. Pasemos al Capítulo 5 y veamos cómo
agrupar piezas de datos en un `struct`.
