## Funciones

Las funciones predominan en el código Rust. Ya ha visto una de las
funciones más importantes en el lenguaje: la función `main`, que es el punto
de entrada de muchos programas. También ha visto la palabra clave `fn`, que le
permite declarar nuevas funciones.

El código de Rust usa *snake case* como estilo convencional para nombres de funciones y variables. En *snake case*, todas las letras son minúsculas y las palabras separadas se unen con un guión bajo. Aquí hay un programa que contiene una definición de función de ejemplo:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    println!("Hello, world!");

    another_function();
}

fn another_function() {
    println!("Another function.");
}
```

Las definiciones de funciones en Rust comienzan con `fn` y tienen un par de
paréntesis después del nombre de la función. Las llaves le dicen al compilador
dónde comienza y termina el cuerpo de la función.

Podemos llamar a cualquier función que hayamos definido ingresando su nombre
seguido de un conjunto de paréntesis. Debido a que `another_function` se define
en el programa, puede ser llamada desde el interior de la función `main`. Tenga
en cuenta que definimos `another_function` *después de (y fuera del cuerpo de)* la función `main` en el código fuente; podríamos haberla definido antes también. A Rust no le importa
dónde define sus funciones, solo que estén definidas en alguna parte.

Comencemos un nuevo proyecto binario llamado *functions* para explorar un poco más allá las funciones. Coloque el ejemplo `another_function` en *src/main.rs* y ejecútelo. Usted
debería ver el siguiente resultado:

```text
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished dev [unoptimized + debuginfo] target(s) in 0.28 secs
     Running `target/debug/functions`
Hello, world!
Another function.
```

Las líneas se ejecutan en el orden en que aparecen en la función `main`.
En primer lugar se imprime el mensaje: *"¡Hello, world!"*. Luego es llamada
`another_function` y se imprime su mensaje: *"Another function."*.

### Parámetros de una función

Podemos definir funciones para que tengan *parámetros*, que son
variables especiales que forman parte de la firma de una función. Cuando una
función tiene parámetros, puede proporcionarle valores concretos para esos
parámetros. Técnicamente, los valores concretos se llaman *argumentos*,
pero en una conversación informal, la gente tiende usar las palabras
*parámetro* y *argumento* indistintamente ya sea para las variables
en la definición de una función o los valores concretos pasados cuando se llama
a una función.

La siguiente versión reescrita de `another_function` muestra cómo se ven los parámetros
en Rust:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    another_function(5);
}

fn another_function(x: i32) {
    println!("The value of x is: {}", x);
}
```

Intente ejecutar este programa; debería obtener el siguiente resultado:

```text
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished dev [unoptimized + debuginfo] target(s) in 1.21 secs
     Running `target/debug/functions`
The value of x is: 5
```

La declaración de `another_function` tiene un parámetro llamado `x`. El tipo de
`x` se especifica como `i32`. Cuando `5` se pasa a `another_function`, la
macro `println!` inserta `5` donde estaban el par de llaves en formato
*string*.

En las firmas de funciones, *debe* declarar el tipo de cada parámetro. Esta es una decisión deliberada en el diseño de Rust: requerir anotaciones de tipo en las definiciones de función significa que el compilador casi nunca necesita que las use en otra parte del código para averiguar a qué tipo se refiere. El compilador también puede dar mensajes de error más útiles si sabe qué tipos espera la función.

Cuando desee que una función tenga múltiples parámetros, separe las declaraciones
de parámetros con comas, como esta:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    print_labeled_measurement(5, 'h');
}

fn print_labeled_measurement(value: i32, unit_label: char) {
    println!("The measurement is: {value}{unit_label}");
}
```

Este ejemplo crea una función llamada `print_labeled_measurement` con dos parámetros. El primer parámetro se llama `value`y es un `i32`; el segundo es nombrado `unit_labely` es tipo `char`. La función luego imprime texto que contiene ambos: `value` y `unit_label`. 

Intentemos ejecutar este código. Reemplace el programa actualmente en sus proyecto
*src/main.rs*, *functions* con el ejemplo anterior y ejecútelo usando `cargo
run`:

```text
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/functions`
The measurement is: 5h
```

Llamamos a la función con `5` como el valor para `value` y `'h'` como el valor para `unit_label`, entonces la salida del programa contiene esos valores.

### Declaraciones y expresiones

Los cuerpos de funciones están formados por una serie de declaraciones que opcionalmente
terminan en una expresión. Hasta ahora, las funciones que hemos cubierto no han incluido una expresión final, pero ya ha visto una expresión como parte de una declaración. Debido a que Rust es un lenguaje basado en expresiones, esta es una distinción importante de entender.
Otros lenguajes no tienen las mismas distinciones, así que veamos qué son
declaraciones y expresiones, y cómo sus diferencias afectan los cuerpos de las
funciones.

Las *Declaraciones* son instrucciones que realizan alguna acción y no devuelven un valor. Las *Expresiones* se evalúan y arrojan un valor resultante. Veamos algunos ejemplos.

De hecho, ya hemos usado declaraciones y expresiones. Crear una variable y asignarle un valor con la palabra clave `let` es declaración. En el Listado 3-1, `let y = 6;` es una declaración.

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let y = 6;
}
```

<span class="caption">Listing 3-1: La declaración de función `main` contiene una instrucción</span>

Las definiciones de funciones también son declaraciones; todo el ejemplo anterior es un
declaración en sí misma.

Las declaraciones no devuelven valores. Por lo tanto, no puede asignar una instrucción `let`
a otra variable, como intenta hacer el siguiente código; obtendrá un error:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
fn main() {
    let x = (let y = 6);
}
```

Cuando ejecuta este programa, el error que obtendrá es similar a este:

```text
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
error: expected expression, found statement (`let`)
 --> src/main.rs:2:14
  |
2 |     let x = (let y = 6);
  |              ^^^^^^^^^
  |
  = note: variable declaration using `let` is a statement

error[E0658]: `let` expressions in this position are unstable
 --> src/main.rs:2:14
  |
2 |     let x = (let y = 6);
  |              ^^^^^^^^^
  |
  = note: see issue #53667 <https://github.com/rust-lang/rust/issues/53667> for more information

warning: unnecessary parentheses around assigned value
 --> src/main.rs:2:13
  |
2 |     let x = (let y = 6);
  |             ^         ^
  |
  = note: `#[warn(unused_parens)]` on by default
help: remove these parentheses
  |
2 -     let x = (let y = 6);
2 +     let x = let y = 6;
  | 

For more information about this error, try `rustc --explain E0658`.
warning: `functions` (bin "functions") generated 1 warning
error: could not compile `functions` due to 2 previous errors; 1 warning emitted
```

La instrucción `let y = 6` no devuelve un valor, por lo que no hay nada para
vincular a `x`. Esto es diferente de lo que sucede en otros lenguajes, como
C y Ruby, donde la asignación devuelve el valor de esa asignación. En esos
lenguajes, puede escribir `x = y = 6` y tanto `x` como `y` tienen el valor
`6`. Ese no es el caso en Rust.

Las expresiones se evalúan como un valor y constituyen la mayor parte del resto del código que escribirá en Rust. Considere una operación matemática simple, como `5 + 6`, que
es una expresión que evalúa al valor `11`. Las expresiones pueden ser parte de
declaraciones: en el Listado 3-1, el `6` en la declaración ` let y = 6; `es una
expresión que evalúa al valor `6`. Llamar a una función es una expresión. 
Llamar a una macro es una expresión. El bloque `{}` que utilizamos para crear nuevos ámbitos, es una expresión, por ejemplo:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let x = 5;

    let y = {
        let x = 3;
        x + 1
    };

    println!("The value of y is: {}", y);
}
```

Esta expresión:

```rust,ignore
{
    let x = 3;
    x + 1
}
```

es un bloque que, en este caso, se evalúa como `4`. Ese valor se vincula a `y`
como parte de la declaración `let`. Tenga en cuenta que la línea `x + 1`
no tiene un punto y coma en el final, a diferencia de la mayoría de las líneas
que ha visto hasta ahora. Las expresiones no incluyen el punto y coma final.
Si agrega un punto y coma al final de un expresión la convierte en una declaración, entonces no devolverá un valor. Tenga esto en cuenta mientras explora los valores devueltos por las funciones y las expresiones a continuación.

### Funciones con valores de retorno

Las funciones pueden devolver valores al código que las llama. No nombramos los valores
de retorno, pero debemos declarar su tipo después de una flecha (`->`). En Rust, el retorno
del valor de la función es sinónimo del valor de la expresión final en el bloque del cuerpo de una función. Se puede adelantar la devolución de una función usando la palabra clave `return` y especificando un valor, pero la mayoría de las funciones devuelven implícitamente la última expresión. Aquí hay un ejemplo de una función que devuelve un valor:

<span class="filename">Filename: src/main.rs</span>

```rust
fn five() -> i32 {
    5
}

fn main() {
    let x = five();

    println!("The value of x is: {}", x);
}
```

No hay llamadas a función, macros, o incluso declaraciones `let` en la función `five`
solo el número `5` en si mismo. Esa es una función perfectamente válida en Rust.
Tenga en cuenta que el tipo de retorno de la función también se especifica como `-> i32`.
Trate ejecutando este código; la salida debería verse así:

```text
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished dev [unoptimized + debuginfo] target(s) in 0.30 secs
     Running `target/debug/functions`
The value of x is: 5
```

El `5` en `five` es el valor de retorno de la función, por lo que el tipo de retorno
es `i32`. Examinemos esto con más detalle. Hay dos partes importantes:

Primero, la línea `let x = five ();` muestra que estamos usando el valor de retorno de una
función para inicializar una variable. Debido a que la función `five` devuelve un `5`,
esa línea es la misma que la siguiente:

```rust
let x = 5;
```

Segundo, la función `five` no tiene parámetros y define el tipo de valor de retorno, pero el cuerpo de la función es un `5` solitario sin punto y coma porque es una expresión cuyo valor queremos devolver.

Veamos otro ejemplo:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let x = plus_one(5);

    println!("The value of x is: {}", x);
}

fn plus_one(x: i32) -> i32 {
    x + 1
}
```

Al ejecutar este código se imprimirá `The value of x is: 6`. Pero si colocamos un
punto y coma al final de la línea que contiene `x + 1`, cambiándola de una
expresión a una declaración, obtendremos un error.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
fn main() {
    let x = plus_one(5);

    println!("The value of x is: {}", x);
}

fn plus_one(x: i32) -> i32 {
    x + 1;
}
```

La ejecución de este código genera un error de la siguiente manera:

```text
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
error[E0308]: mismatched types
 --> src/main.rs:7:24
  |
7 | fn plus_one(x: i32) -> i32 {
  |    --------            ^^^ expected `i32`, found `()`
  |    |
  |    implicitly returns `()` as its body has no tail or `return` expression
8 |     x + 1;
  |          - help: remove this semicolon

For more information about this error, try `rustc --explain E0308`.
error: could not compile `functions` due to previous error
```

El mensaje de error principal, “mismatched types,” (tipos no coincidentes),
revela el problema central con este código. La definición de la función `plus_one`
dice que devolverá un `i32`, pero las declaraciones no evalúan a un valor, que
se expresa por `()`, el tipo *unit*. Por lo tanto, no se devuelve nada, lo que
contradice la definición de la función y resulta en un error. En esta salida,
Rust proporciona un mensaje que posiblemente ayude a rectificar este problema:
sugiere eliminar el punto y coma, que arreglaría el error.
