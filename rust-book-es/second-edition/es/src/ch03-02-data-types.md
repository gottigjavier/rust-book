## Tipos de Datos

Cada valor en Rust es de un cierto *tipo de dato*, que le informa a Rust qué
tipo de dato se está especificando para que sepa cómo trabajar con ese dato.
Veremos dos subconjuntos de tipos de datos: escalares y compuestos.

Tenga en cuenta que Rust es un lenguaje *estáticamente tipado*, lo que
significa que debe conocer los tipos de todas las variables en tiempo de
compilación. El compilador generalmente puede inferir qué tipo queremos usar
en función del valor y cómo lo usamos. En los casos en que son posibles
muchos tipos, como cuando convertimos un `String` a un tipo numérico usando
`parse` en la sección [“Comparando la conjetura con el número secreto”](ch02-00-guessing-game-tutorial.html#comparando-la-conjetura-con-el-n%C3%BAmero-secreto) en el
Capítulo 2, debemos agregar una *anotación de tipo* (*type annotation*), como esta :

```rust
# #![allow(unused)]
# fn main() {
let guess: u32 = "42".parse().expect("Not a number!");
# }
```

Si no agregamos la anotación de tipo `:u32` anterior, Rust mostrará el siguiente error, lo que significa que el compilador necesita más información de parte nuestra para saber qué tipo queremos usar:

```text
$ cargo build
   Compiling no_type_annotations v0.1.0 (file:///projects/no_type_annotations)
error[E0282]: type annotations needed
 --> src/main.rs:2:9
  |
2 |     let guess = "42".parse().expect("Not a number!");
  |         ^^^^^ consider giving `guess` a type

For more information about this error, try `rustc --explain E0282`.
error: could not compile `no_type_annotations` due to previous error
```

Verá anotaciones de tipo diferentes para otros tipos de datos.

### Tipos Escalares

Un tipo *escalar* representa un solo valor. Rust tiene cuatro tipos escalares
básicos: enteros, números de punto flotante, booleanos y caracteres. Puede
reconocer estos de otros lenguajes de programación. Veamos cómo funcionan en
Rust.

#### Tipos *Enteros* (*Integer*)

Un *entero* es un número sin un componente fraccionario. Usamos un tipo entero
en el Capítulo 2, el tipo `u32`. Esta declaración de tipo indica que el valor
con el que está asociado debe ser un entero sin signo (los tipos enteros con
signo comienzan con `i`, en lugar de `u`) que ocupan 32 bits de espacio. La
Tabla 3-1 muestra los tipos enteros incorporados en Rust. Podemos usar cualquiera de estas variantes para declarar el tipo de un valor entero.

<span class="caption">Tabla 3-1: Tipos enteros en Rust</span>

| Length | Signed  | Unsigned |
|--------|---------|----------|
| 8-bit  | `i8`    | `u8`     |
| 16-bit | `i16`   | `u16`    |
| 32-bit | `i32`   | `u32`    |
| 64-bit | `i64`   | `u64`    |
| arch   | `isize` | `usize`  |

Cada variante puede ser "con signo" o "sin signo" y tiene un tamaño explícito. *Con signo* y *sin signo* se refieren a si es posible que el número sea negativo; en otras palabras, si el número debe tener un signo con él (*Signed*) o si solo será positivo y, por lo tanto, puede representarse sin signo (*Unsigned*). Es como escribir números en un papel: cuando el signo importa, un número se muestra con un signo más o un signo menos; sin embargo, cuando es seguro asumir que el número es positivo, se muestra *sin signo*. Los números *con signo* se almacenan usando la representación [complemento a dos](https://es.wikipedia.org/wiki/Complemento_a_dos).

Cada variante *con signo* puede almacenar números de -(2<sup>n - 1</sup>) a
2<sup>n - 1</sup> - 1 inclusive, donde *n* es el número de bits que utiliza la
variante. Entonces, un `i8` puede almacenar números de -(2<sup>7</sup>) a
2<sup>7</sup> - 1, lo que equivale de -128 a 127. Las variantes *sin signo*
pueden almacenar números de 0 al 2<sup>n</sup> - 1, por lo que un `u8` puede
almacenar números de 0 a 2<sup>8</sup> - 1, es decir, de 0 a 255.

Además, los tipos `isize` y `usize` dependen del tipo de computadora en la que
se ejecute el programa: 64 bits si está en una arquitectura de 64 bits y 32
bits si está en una arquitectura de 32 bits.

Puede escribir literales enteros en cualquiera de las formas que se muestran en la tabla 3-2. Tenga en cuenta que los literales numéricos que pueden ser varios tipos numéricos permiten un sufijo de tipo, como `57u8`, para designar el tipo. Los literales numéricos también pueden usar `_` como separador visual para que el número sea más fácil de leer, como `1_000`, que tendrá el mismo valor que si hubiera especificado `1000`.


<span class="caption">Tabla 3-2: Literales enteros en Rust</span>

| Number literals  | Example       |
|------------------|---------------|
| Decimal          | `98_222`      |
| Hex              | `0xff`        |
| Octal            | `0o77`        |
| Binary           | `0b1111_0000` |
| Byte (`u8` only) | `b'A'`        |

Entonces, ¿cómo saber qué tipo de número entero usar? Si no está seguro, los valores predeterminados de Rust generalmente son buenas opciones. El tipo predeterminado para enteros es `i32`. La situación principal en la que usaría `isize` o `usize` es al indexar algún tipo de colección.

> **Desbordamiento de Enteros**
>
> Digamos que tiene una variable de tipo `u8` que puede contener valores entre 0 y 255. 
> Si intenta cambiar la variable a un valor fuera de ese rango, como como 256, ocurrirá un *desbordamiento de enteros*, lo que puede resultar en uno de dos comportamientos.
>
> Cuando está compilando en modo de depuración, Rust incluye verificaciones de desbordamiento de enteros que hacen que su programa entre en *pánico* en tiempo de ejecución si ocurre este comportamiento. Rust usa el término entrar en pánico cuando un programa se cierra por un error. Discutiremos los *pánicos* con más profundidad en la sección ["Errores Irrecuperables con panic!”](ch09-01-unrecoverable-errors-with-panic.html) del capítulo 9.
>
> Cuando esté compilando en modo de lanzamiento con la bandera `--release`, Rust no incluye comprobaciones de desbordamiento de enteros que causen pánico. En cambio, si se produce un desbordamiento, Rust realiza el ajuste de complemento a dos. En resumen, los valores mayores que el valor máximo que el tipo puede contener "ajustan" al mínimo de los valores que el tipo puede contener. En el caso de un `u8`, el valor 256 se convierte en 0, el valor 257 se convierte en 1, y así sucesivamente. El programa no entrará en pánico, pero la variable tendrá un valor que probablemente no sea el que esperaba tener. Confiar en el comportamiento de ajuste del desbordamiento de enteros se considera un error.
>
> Para manejar explícitamente la posibilidad de desbordamiento, puede usar estas familias de métodos proporcionadas por la biblioteca estándar para tipos numéricos primitivos:
>
> - Restrinja en todos los modos con los métodos `wrapping_*`, tales como `wrapping_add`
> - Devuelva el valor `None` si hay desbordamiento con los métodos `checked_*`
> - Devuelva el valor y un booleano que indica si hubo desbordamiento con los métodos `overflowing_*`
> - Sature en los valores mínimo o máximo del valor con métodos `saturating_*`
>


#### Tipos de Punto Flotante

Rust también tiene dos tipos primitivos para *números de punto flotante*, que
son números con cifras decimales. Los tipos de punto flotante de Rust son
`f32` y `f64`, que son de 32 bits y 64 bits de tamaño, respectivamente. El
tipo predeterminado es `f64` porque en las CPU modernas se trata con más o menos la
misma velocidad que `f32`, pero es capaz de obtener más precisión. Todos los tipos de punto flotante son *con signo*.

Aquí hay un ejemplo que muestra los números de punto flotante en acción:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let x = 2.0; // f64

    let y: f32 = 3.0; // f32
}
```

Los números de punto flotante se representan según el estándar IEEE-754. El
tipo `f32` es un *float* de precisión simple, y `f64` tiene doble precisión.

#### Operaciones Numéricas

Rust soporta las operaciones matemáticas básicas que esperaría para todos los
tipos de números: adición, sustracción, multiplicación, división y residuo (resto de la división entera). La división de enteros se redondea al entero inferior más próximo. El siguiente código muestra cómo usaría cada uno en una declaración `let`:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    // addition
    let sum = 5 + 10;

    // subtraction
    let difference = 95.5 - 4.3;

    // multiplication
    let product = 4 * 30;

    // division
    let quotient = 56.7 / 32.2;

    // remainder
    let remainder = 43 % 5;
}
```

Cada expresión en estas declaraciones usa un operador matemático y la evalúa a un
solo valor, que luego se vincula a una variable. El [Apéndice B](appendix-02-operators.html) contiene una lista de todos los operadores que Rust proporciona.

#### El tipo *booleano* (*Boolean*)

Como en la mayoría de los demás lenguajes de programación, un tipo *booleano* en Rust tiene dos valores posibles: `true` y `false`. Los booleanos tienen un tamaño de un byte. El tipo *booleano* en Rust se especifica con `bool`. Por ejemplo:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let t = true;

    let f: bool = false; // con "anotación de tipo" explícita
}
```

La forma principal de usar valores *booleanos* es mediante condicionales, como una expresión `if`. Cubriremos cómo funcionan las expresiones `if` en Rust en la sección [“Estructuras de Control”](ch03-05-control-flow.html).

#### El Tipo Caracter

El tipo `char` de Rust es el tipo alfabético más primitivo del lenguaje. Aquí hay algunos ejemplos de cómo declarar valores `char`:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let c = 'z';
    let z: char = 'ℤ'; // con "anotación de tipo" explícita
    let heart_eyed_cat = '😻';
}
```

Tenga en cuenta que el literal `char` se especifica con comillas simples, a diferencia de los literales *strings*, que usan comillas dobles. El tipo `char` de Rust tiene un tamaño de cuatro bytes y representa un valor escalar Unicode, lo que significa que puede representar mucho más que solo ASCII. Letras acentuadas; Caracteres chinos, japoneses y coreanos; emoji; y los *espacios de ancho cero* son todos valores `char` válidos en Rust. Los valores escalares Unicode van desde `U + 0000` a `U + D7FF` y `U + E000` a `U + 10FFFF` inclusive. Sin embargo, un “carácter” no es realmente un concepto en Unicode, por lo que su intuición humana para lo que es un “carácter” puede no coincidir con lo que es un `char` en Rust. Discutiremos este tema en detalle en [“Almacenamiento de texto codificado en UTF-8 con Strings”](ch08-02-strings.html) en el Capítulo 8.

### Tipos Compuestos

*Los tipos compuestos* pueden agrupar múltiples valores en un tipo. Rust tiene
dos tipos de compuestos primitivos: tuplas y matrices.

#### El tipo *Tupla* (*Tuple*)

Una tupla es una forma general de agrupar un número de valores con una variedad de tipos dentro de un tipo compuesto. Las tuplas tienen una longitud fija: una vez declaradas, no pueden crecer ni encogerse de tamaño.

Creamos una tupla escribiendo una lista de valores separados por comas dentro paréntesis. Cada posición en la tupla tiene un tipo, y los tipos de los diferentes valores en la tupla no tienen que ser iguales. Agregamos anotaciones de tipo opcionales en este ejemplo:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```

La variable `tup` se vincula a la tupla completa, porque una tupla se considera un
solo elemento compuesto. Para obtener los valores individuales de una tupla,
podemos usar la coincidencia de patrones para desestructurar un valor de tupla
como este:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let tup = (500, 6.4, 1);

    let (x, y, z) = tup;

    println!("The value of y is: {}", y);
}
```

Este programa crea primero una tupla y la vincula a la variable `tup`. Luego
usa un patrón con `let` para tomar `tup` y convertirlo en tres variables
separadas, `x`, `y`, y `z`. Esto se llama *desestructuración*, porque divide
la tupla individual en tres partes. Finalmente, el programa imprime el valor
de `y`, que es `6.4`.

También podemos acceder a un elemento de tupla directamente usando un punto (`.`) seguido del índice del valor al que queremos acceder. Por ejemplo:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let x: (i32, f64, u8) = (500, 6.4, 1);

    let five_hundred = x.0;

    let six_point_four = x.1;

    let one = x.2;
}
```

Este programa crea la tupla, `x`, y luego crea nuevas variables para cada
elemento usando su índice. Como con la mayoría de los lenguajes de
programación, el primer índice en una tupla es 0.

La tupla sin ningún valor tiene un nombre especial: *unidad*. Este valor y su correspondiente tipo se escriben `()` y representan un valor vacío o un tipo de retorno vacío. Las expresiones devuelven implícitamente el valor *unidad* si no devuelven cualquier otro valor. 

#### El tipo *Array* (*Arreglo o Matriz*)

Otra forma de tener una colección de valores múltiples es con un *Array*. A diferencia de una tupla, cada elemento de un *array* debe tener el mismo tipo. A diferencia de algunos otros lenguajes, los *arrays* en Rust tienen una longitud fija: una vez declarados, no pueden crecer o reducirse de tamaño.

En Rust, los valores en un *array* se escriben como una lista separada por comas, entre corchetes:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];
}
```

Los arrays son útiles cuando quiere que sus datos se asignen en el *Stack* en
lugar de en el *Heap* (discutiremos más sobre *Stack* y *Heap* en el
[Capítulo 4](ch04-01-what-is-ownership.html#la-pila-stack-y-el-mont%C3%ADculo-heap)) o cuando quiera asegurarse de tener siempre una cantidad fija de elementos. Sin embargo, un array no es tan flexible como el tipo vector. Un vector es un tipo de colección similar provisto por la biblioteca estándar *que* puede crecer o reducir su tamaño. Si no está seguro de utilizar un
array o un vector, probablemente debería usar un vector. El [Capítulo 8](ch08-01-vectors.html)
discute los vectores con más detalle.

Sin embargo, los arrays son más útiles cuando sabe que no será necesario cambiar la cantidad de elementos. Por ejemplo, si estuviera usando los nombres de los meses en un programa, probablemente usaría un array en lugar de un vector porque sabe que siempre contendrá 12 elementos:

```rust
# #![allow(unused)]
# fn main() {
let months = ["January", "February", "March", "April", "May", "June", "July",
              "August", "September", "October", "November", "December"];
# }
```

Escriba el tipo de una matriz usando corchetes con el tipo de cada elemento, un punto y coma y luego la cantidad de elementos en la matriz, así:

```rust
# #![allow(unused)]
# fn main() {
let a: [i32; 5] = [1, 2, 3, 4, 5];
# }
```

Aquí, `i32` es el tipo de cada elemento. Después del punto y coma, el número `5` indica que el array contiene cinco elementos.

También puede inicializar un array para que contenga el mismo valor para cada elemento especificando el valor inicial, seguido de un punto y coma, y luego la longitud del array entre corchetes, como se muestra aquí:

```rust
# #![allow(unused)]
# fn main() {
let a = [3; 5];
# }
```

El array llamado `a` contendrá 5 elementos que se establecerán inicialmente en el valor `3`. Esto es lo mismo que escribir `let a = [3, 3, 3, 3, 3];` pero de una manera más concisa.

##### Acceso a los Elementos del Array

Un array es un fragmento único de memoria, de tamaño fijo conocido, que se puede asignar al *Stack*. Puede acceder a los elementos de un array mediante la indexación, así:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];

    let first = a[0];
    let second = a[1];
}
```

En este ejemplo, la variable llamada `first` obtendrá el valor `1`, porque ese
es el valor en el índice `[0]` en el array. La variable llamada `second`
obtendrá el valor `2` del índice `[1]` en el array.

##### Acceso a Elementos no válidos de un Array 

Veamos qué sucede si intenta acceder a un elemento de un array que está más allá del final del array. Supongamos que ejecuta este código, similar al juego de adivinanzas del Capítulo 2, para obtener del usuario un índice del array: 

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
use std::io;

fn main() {
    let a = [1, 2, 3, 4, 5];

    println!("Please enter an array index.");

    let mut index = String::new();

    io::stdin()
        .read_line(&mut index)
        .expect("Failed to read line");

    let index: usize = index
        .trim()
        .parse()
        .expect("Index entered was not a number");

    let element = a[index];

    println!("The value of the element at index {index} is: {element}");
}
```

Este código se compila correctamente. Si ejecuta este código usando `cargo run` e ingresa 0, 1, 2, 3 o 4, el programa imprimirá el valor correspondiente a ese índice en el array. Si, en cambio, ingresa un número después del final del array, como `10`, verá un resultado como este: 

```text
thread 'main' panicked at 'index out of bounds: the len is 5 but the index is 10', src/main.rs:19:19
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

El programa resultó en un *error en tiempo de ejecución* al momento de usar un valor no válido en la operación de indexación. El programa salió con un mensaje de error y no ejecutó la declaración final `println!`. Cuando intenta acceder a un elemento utilizando la indexación, Rust comprobará que el índice que ha especificado es menor que la longitud del array. Si el índice es mayor o igual que la longitud, Rust entrará en pánico. Esta verificación tiene que ocurrir en tiempo de ejecución, especialmente en este caso, porque el compilador no puede saber qué valor ingresará un usuario cuando se ejecute el código más tarde.

Este es un ejemplo de los principios de seguridad de la memoria de Rust en acción. En muchos lenguajes de bajo nivel, este tipo de verificación no se realiza, y cuando proporciona un índice incorrecto, se puede acceder a memoria no válida. Rust le protege contra este tipo de error al salir inmediatamente en lugar de permitir el acceso a la memoria y continuar. El Capítulo 9 analiza más sobre el manejo de errores de Rust y cómo puede escribir un código legible y seguro que no entre en pánico ni permita el acceso no válido a la memoria.