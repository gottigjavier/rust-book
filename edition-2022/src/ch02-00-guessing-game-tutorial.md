# Programando un Juego de Adivinanzas

¡Saltemos a Rust trabajando juntos en un proyecto práctico!. En este capítulo, le presentamos algunos conceptos comunes de Rust mostrándole cómo usarlos en un programa real. Aprenderá sobre `let`, `match`, métodos, funciones asociadas, *crates* externos y más. Los siguientes capítulos
explorarán estas ideas con más detalle. En este capítulo, practicará los fundamentos.

Implementaremos un problema clásico de programación para principiantes: un
juego de adivinanzas. Así es como funciona: el programa generará un entero
aleatorio entre 1 y 100. Luego pedirá al jugador que ingrese una conjetura.
Después de ingresar una conjetura, el programa indicará si la conjetura es
demasiado baja o demasiado alta. Si la conjetura es correcta, el juego
imprimirá un mensaje de felicitación y saldrá.

## Configuración de un Nuevo Proyecto

Para configurar un nuevo proyecto, vaya al directorio *projects* que creó en
el Capítulo 1 y cree un nuevo proyecto utilizando Cargo, de la siguiente
manera:

```bash
$ cargo new guessing_game
$ cd guessing_game
```

El primer comando, `cargo new`, toma el nombre del proyecto (`guessing_game`) como primer argumento. El segundo comando cambia al directorio del nuevo proyecto.

Mire el archivo generado *Cargo.toml*:

<span class="filename">Filename: Cargo.toml</span>

```toml
[package]
name = "guessing_game"
version = "0.1.0"
edition = "2021"

[dependencies]
```


Como vio en el Capítulo 1, `cargo new` genera un programa “Hello, world!” para usted. Mire el archivo *src/main.rs*:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    println!("Hello, world!");
}
```

Ahora compilemos este programa “Hello, world!” y ejecutémoslo en el mismo paso usando el comando `cargo run`:

```toml
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 1.50 secs
     Running `target/debug/guessing_game`
Hello, world!
```

El comando `run` es útil cuando necesita iterar rápidamente en un proyecto, como lo haremos en este juego, probando rápidamente cada iteración antes de pasar a la siguiente.

Vuelva a abrir el archivo *src/main.rs*. Estará escribiendo todo el código en este archivo.

## Procesando una Conjetura

La primera parte del programa del juego de adivinación pedirá la entrada del usuario, procesará esa entrada y verificará que la entrada esté en el formato esperado. Para comenzar, le permitiremos al jugador ingresar una conjetura. Ingrese el código en el listado 2-1 en *src/main.rs*.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
use std::io;

fn main() {
    println!("Guess the number!");

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {}", guess);
}
```

<span class="caption">Listado 2-1: Código que obtiene una conjetura del
usuario y la imprime</span>

Este código contiene mucha información, así que vamos a revisarlo línea por línea. Para obtener la entrada del usuario y luego imprimir el resultado como salida, tenemos que traer la biblioteca `io` (*input/output*),(*entrada/salida*) al *alcance o ámbito* (*scope*). La biblioteca `io` proviene de la biblioteca estándar (que se conoce como `std`):

```rust,ignore
use std::io;
#   
# fn main() {
#     println!("Guess the number!");
#
#     println!("Please input your guess.");
#
#     let mut guess = String::new();
#
#     io::stdin()
#         .read_line(&mut guess)
#         .expect("Failed to read line");
#
#     println!("You guessed: {guess}");
# }
```

Por defecto, Rust solo tiene un conjunto de elementos definidos en la biblioteca estándar que trae al ámbito de cada programa. Este conjunto se llama el preludio, y puede ver todo lo que contiene en la [documentación de la biblioteca estándar](https://doc.rust-lang.org/std/prelude/index.html).

Si un tipo que desea utilizar no está en el preludio, debe traer ese tipo al ámbito explícitamente con una instrucción `use`. El uso de la biblioteca `std::io` proporciona varias funciones útiles, incluida la capacidad de aceptar las entradas del usuario.

Como se vio en el Capítulo 1, la función `main` es el punto de entrada al programa:

```rust,ignore
# use std::io;
#
fn main() {
#     println!("Guess the number!");
#
#     println!("Please input your guess.");
#
#     let mut guess = String::new();
#
#     io::stdin()
#         .read_line(&mut guess)
#         .expect("Failed to read line");
#
#     println!("You guessed: {guess}");
# }
```

La sintaxis `fn` declara una nueva función, los paréntesis (vacíos) `()` indican que no hay parámetros, y la llave `{` inicia el cuerpo de la función.

Como también aprendió en el Capítulo 1, `println!` Es una macro que imprime
un *string* en la pantalla:

```rust,ignore
# use std::io;
#
# fn main() {
    println!("Guess the number!");

    println!("Please input your guess.");
#
#     let mut guess = String::new();
#
#     io::stdin()
#         .read_line(&mut guess)
#         .expect("Failed to read line");
#
#     println!("You guessed: {guess}");
# }
```

Este código está imprimiendo un mensaje indicando qué es el juego y solicitando la opción del usuario.

### Almacenando Valores con Variables

A continuación, crearemos una varable para almacenar la entrada del usuario, así:

```rust,ignore
# use std::io;
#
# fn main() {
#     println!("Guess the number!");
#
#     println!("Please input your guess.");
#
        let mut guess = String::new();
#
#     io::stdin()
#         .read_line(&mut guess)
#         .expect("Failed to read line");
#
#     println!("You guessed: {guess}");
# }
```

¡Ahora el programa se está poniendo interesante!. Están sucediendo muchas cosas en esta pequeña línea. Usamos la sentencia `let` crear la *variable*. Aquí hay otro ejemplo:

```rust,ignore
let apples = 5;
```

Esta línea crea una nueva variable llamada `apples` y la vincula al valor `5`. En Rust, las variables son inmutables de forma predeterminada, lo que significa que una vez que le damos un valor a la variable, el valor no cambiará. Discutiremos este concepto en detalle en la sección ["Variables y Mutabilidad"](ch03-01-variables-and-mutability.html#variables-y-mutabilidad) en el Capítulo 3. Para hacer que una variable sea mutable, agregamos `mut` antes del nombre de la variable:

```rust,ignore
let apples = 5; // immutable
let mut bananas = 5; // mutable
```

> Nota: La sintaxis `//` inicia un comentario que continúa hasta el final de la línea. Rust ignora todo en los comentarios, que se tratan con más detalle en el [Capítulo 3](ch03-04-comments.html#comentarios).

Regresemos al programa de adivinanzas. Ahora sabe que `let mut guess` introducirá una variable mutable llamada `guess`. A la derecha del signo igual (`=`) está el valor al que `guess` está vinculado, que es el resultado de llamar a `String::new()`, una función que devuelve una nueva instancia vacía de tipo `String`. [`String`][string] <!-- ignore --> es un tipo de *string* proporcionado por la biblioteca estándar que es un fragmento de texto codificado en UTF-8 que puede crecer.

> Nota del traductor: no pasar por alto que `String` no es un tipo de dato primitivo como podría serlo en otros lenguajes sino que forma parte de la biblioteca estandar de Rust. Para más información visite [Struct std::string::String](https://doc.rust-lang.org/std/string/struct.String.html).

[string]: ch08-02-strings.md

La sintaxis `::` en la línea `::new()` indica que `new()` es una *función asociada* del tipo `String`. Una [`función asociada`](ch05-03-method-syntax.md#funciones-asociadas) es una función que se implementa en un tipo, en este caso un `String`, y no en una instancia particular de ese tipo, como lo haría un método. Algunos lenguajes llaman a esto un *método estático*.

Esta función `new()` crea un nuevo *string* vacío. Encontrará una función `new()` en muchos tipos, porque es un nombre común para darle una función asociada que crea un nuevo valor de algún tipo.

En resumen, la línea `let mut guess = String::new();` ha creado una variable mutable que actualmente está vinculada a una nueva instancia vacía de `String`. ¡Uf!

### Recibiendo la Entrada del Usuario

Recuerde que incluimos la funcionalidad de *entrada/salida* de la biblioteca estándar con `use std::io;` en la primera línea del programa. Ahora llamaremos a la función asociada `stdin` del módulo `io`, que nos permitirá manejar la entrada del usuario:

```rust,ignore
# use std::io;
#
# fn main() {
#     println!("Guess the number!");
#
#     println!("Please input your guess.");
#
#         let mut guess = String::new();
#
    io::stdin()
        .read_line(&mut guess)
#         .expect("Failed to read line");
#
#     println!("You guessed: {guess}");
# }
```

Si no hubiésemos enumerado la línea `use std::io` al comienzo del programa, podríamos haber escrito esta llamada de función como `std::io::stdin`. La función `stdin` devuelve una instancia de [`std::io::Stdin`][iostdin] <!-- ignore -->, que es un tipo que representa un *manejador* de la entrada estándar para su terminal.

[iostdin]: https://doc.rust-lang.org/stable/std/io/struct.Stdin.html

A continuación, la línea `.read_line(&mut guess)`, llama al método [`read_line`][read_line] <!-- ignore --> en el controlador de entrada estándar para obtener la entrada del usuario. También estamos pasando `&mut guess` como argumento a `read_line` para decirle qué *string* almacenar la entrada del usuario. El trabajo completo de `read_line` es tomar lo que el usuario escriba en la entrada estándar y agregarlo a un *string* (sin sobrescribir su contenido ), por lo que pasamos ese *string* como argumento. El argumento *string* debe ser mutable para que el método pueda cambiar el contenido del *string*.

[read_line]: https://doc.rust-lang.org/stable/std/io/struct.Stdin.html#method.read_line

El `&` indica que este argumento es una *referencia*, que le permite dejar que varias partes de su código accedan a una parte de los datos sin necesidad de copiar esos datos en la memoria varias veces. Las referencias son una característica compleja, y una de las principales ventajas de Rust es la seguridad y facilidad de uso de las referencias. No necesita saber muchos de
esos detalles para finalizar este programa. Por ahora, todo lo que necesita saber es que, al igual que las variables, las referencias son inmutables por defecto. Por lo tanto, debe escribir `&mut guess`en lugar de `&guess` para que sea mutable. (El Capítulo 4 explicará las referencias más a fondo).

### Manejando la Falla Potencial con el Tipo `Result`

No hemos terminado con esta línea de código. Aunque lo que hemos discutido hasta ahora es una sola línea de texto, es solo la primera parte de la única línea lógica de código. La segunda parte es este método:

```rust,ignore
# use std::io;
#
# fn main() {
#     println!("Guess the number!");
#
#     println!("Please input your guess.");
#
#         let mut guess = String::new();
#
#     io::stdin()
#         .read_line(&mut guess)
        .expect("Failed to read line");
#
#     println!("You guessed: {guess}");
# }
```

Podríamos haber escrito este código como:

```rust,ignore
io::stdin().read_line(&mut guess).expect("Failed to read line");
```

Sin embargo, una línea larga es difícil de leer, por lo que es mejor dividirla. A menudo es aconsejable introducir una nueva línea y otros espacios en blanco para ayudar a dividir las líneas largas cuando llama a un método con la sintaxis `.method_name()`. Ahora analicemos lo que hace esta línea.

Como se mencionó anteriormente, `read_line` pone lo que el usuario ingresa en el *string* que le pasamos, pero también devuelve un valor Result. [Result](https://doc.rust-lang.org/std/result/enum.Result.html) es una [enumeración](ch06-00-enums.html), a menudo llamada *enum*, que es un tipo que puede estar en uno de varios estados posibles. Llamamos a cada estado posible una variante.

El Capítulo 6 cubrirá enumeraciones en más detalle.

Para `Result`, las variantes son `Ok` o `Err`. La variante `Ok` indica que la operación fue exitosa, y dentro de `Ok` está el valor generado exitosamente. La variante `Err` significa que la operación falló, y `Err` contiene información sobre cómo o por qué falló la operación.

Los valores de tipo `Result`, como los valores de cualquier tipo, tienen métodos definidos en ellos. Una instancia de `io::Result` tiene un [método `expect`][expect] <!-- ignore --> al que puede llamar. Si esta instancia de `io::Result` es un valor `Err`, `expect` hará que el programa se cuelgue y mostrará el mensaje que pasó como argumento para `expect`. Si el método `read_line` devuelve un `Err`, probablemente sea el resultado de un error proveniente del sistema operativo subyacente. Si esta instancia de `io::Result` es un valor `Ok`, `expect` tomará el valor de retorno que `Ok` está reteniendo y le devolverá ese valor para que pueda usarlo. En este caso, ese valor es el número de bytes en lo que el usuario ingresó en la entrada estándar.

[expect]: https://doc.rust-lang.org/std/result/enum.Result.html#method.expect

Si no llama a `expect`, el programa compilará, pero recibirá una advertencia:

```text
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
warning: unused `Result` that must be used
  --> src/main.rs:10:5
   |
10 |     io::stdin().read_line(&mut guess);
   |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   |
   = note: `#[warn(unused_must_use)]` on by default
   = note: this `Result` may be an `Err` variant, which should be handled

warning: `guessing_game` (bin "guessing_game") generated 1 warning
    Finished dev [unoptimized + debuginfo] target(s) in 0.59s
```

Rust advierte que no ha utilizado el valor `Result` devuelto por `read_line`, lo que indica que el programa no ha manejado un posible error.

La forma correcta de suprimir la advertencia es escribir el manejo de errores, pero en nuestro caso solo queremos bloquear este programa cuando ocurre un problema, por lo que podemos usar `expect`. Aprenderá sobre la recuperación de errores en el [Capítulo 9][cap9] <!-- ignore -->.

[cap9]: ch09-02-recoverable-errors-with-result.html

### Impresión de Valores con *Placeholders* (*marcadores de posición*) de  `println!`

Aparte de las llaves cierre, solo hay una línea más para discutir en el código agregado hasta el momento, que es el siguiente:

```rust,ignore
# use std::io;
#
# fn main() {
#     println!("Guess the number!");
#
#     println!("Please input your guess.");
#
#         let mut guess = String::new();
#
#     io::stdin()
#         .read_line(&mut guess)
#         .expect("Failed to read line");
#
    println!("You guessed: {guess}");
# }
```

Esta línea imprime la cadena que ahora contiene la entrada del usuario. El conjunto de corchetes `{}` es un marcador de posición: piense en `{}` como pequeñas pinzas de cangrejo que mantienen un valor en su lugar. Puede imprimir más de un valor utilizando corchetes: el primer conjunto de corchetes contiene el primer valor que aparece después de la cadena de formato, el segundo conjunto contiene el segundo valor, y así sucesivamente. Imprimiendo múltiples valores en una llamada a `println!` se vería así:

```rust
# #![allow(unused)]
# fn main() {
let x = 5;
let y = 10;

println!("x = {} e y = {}", x, y);
# }
```

Este código imprimiría `x = 5 e y = 10`.

### Probando la Primera Parte

Probemos la primera parte del juego de adivinanzas. Ejecútalo usando `cargo run`:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
     Running `target/debug/guessing_game`
Guess the number!
Please input your guess.
6
You guessed: 6
```

En este punto, la primera parte del juego está lista: recibimos información del teclado y luego la imprimimos.

## Generando un Número Secreto

A continuación, necesitamos generar un número secreto que el usuario intentará adivinar. El número secreto debe ser diferente cada vez, por lo que es divertido jugar más de una vez. Usaremos un número aleatorio entre 1 y 100 para que el juego no sea demasiado difícil. Rust aún no incluye la funcionalidad de números aleatorios en su biblioteca estándar. Sin embargo, el equipo de Rust proporciona un [crate `rand`][randcrate] con dicha funcionalidad.

[randcrate]: https://crates.io/crates/rand

### Usar un *Crate* para Obtener más Funcionalidades

Recuerde que un *crate* es un paquete de archivos de código fuente de Rust. El proyecto que hemos estado creando es un *binary crate*, que es un ejecutable. El *crate*`rand` es una *library crate*, que contiene un código que no se puede ejecutar por sí mismo sino que está destinado a ser utilizado en otros programas.

La coordinación de *crates* externos de Crago es donde Cargo realmente brilla. Antes de que podamos escribir el código que usa `rand`, necesitamos modificar el archivo *Cargo.toml* para incluir el *crate* `rand` como una dependencia. Abra ese archivo ahora y agregue la siguiente línea en la parte inferior debajo del encabezado de la sección `[dependencies]` que Cargo creó para usted. Asegúrese de especificar `rand` exactamente como lo tenemos aquí, con este número de versión, o los ejemplos de código en este tutorial pueden no funcionar.

<span class="filename">Filename: Cargo.toml</span>

```toml
[dependencies]

rand = "0.8.3"
```

En el archivo *Cargo.toml*, todo lo que sigue a un encabezado es parte de una sección que continúa hasta que se inicia otra sección. La sección `[dependencies]` es donde le dice a Cargo de qué *crates* externos depende su proyecto y qué versiones de esos *crates* necesita. En este caso, especificaremos el *crate* `rand` con el especificador de versión semántica `0.8.3`. Cargo entiende [Versión semántica][semver] <!-- ignore --> (a veces llamado *SemVer*), que es un estándar para escribir números de versión. El número `0.8.3` es en realidad una abreviatura para `^0.8.3`, que significa “cualquier versión que se al menos `0.8.3` pero inferior a `0.9.0`”.

[semver]: http://semver.org

Cargo considera que estas versiones tienen API públicas compatibles con la versión `0.8.3`, y esta especificación garantiza que obtendrá el último parche que se compilará con el código de este capítulo. No se garantiza que cualquier versión `0.9.0` o superior tenga la misma API que la que usan los siguientes ejemplos.

Ahora, sin cambiar ninguno de los códigos, construyamos el proyecto, como se muestra en el Listado 2-2.

```text
$ cargo build
    Updating crates.io index
  Downloaded rand v0.8.3
  Downloaded libc v0.2.86
  Downloaded getrandom v0.2.2
  Downloaded cfg-if v1.0.0
  Downloaded ppv-lite86 v0.2.10
  Downloaded rand_chacha v0.3.0
  Downloaded rand_core v0.6.2
   Compiling rand_core v0.6.2
   Compiling libc v0.2.86
   Compiling getrandom v0.2.2
   Compiling cfg-if v1.0.0
   Compiling ppv-lite86 v0.2.10
   Compiling rand_chacha v0.3.0
   Compiling rand v0.8.3
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53s
```

<span class="caption">Listado 2-2: El resultado de ejecutar `cargo build` después de agregar el *crate rand* como una dependencia</span>

Es posible que vea diferentes números de versión (¡pero todos serán compatibles con el código, gracias a SemVer!), y las líneas pueden estar en un orden diferente.

Cuando incluimos una dependencia externa, Cargo obtiene las últimas versiones de todo lo que necesita la dependencia desde el *registro*, que es una copia de los datos de [Crates.io][cratesio]. Crates.io es donde las personas en el ecosistema de Rust publican sus proyectos Rust de código abierto para que otros los usen.

[cratesio]: https://crates.io

Después de actualizar el registro, Cargo comprueba la sección `[dependencies]` y descarga los *crates* listados que aún no se han descargado. En este caso, aunque solo listamos `rand` como una dependencia, Cargo también tomó otros *crates* de las que depende `rand` para funcionar. Después de descargar los *crates*, Rust los compila y luego compila el proyecto con las dependencias disponibles.

Si inmediatamente ejecuta `cargo build` nuevamente sin realizar ningún cambio, no obtendrá ningún resultado aparte de la línea `Finished`. Cargo sabe que ya ha descargado y compilado las dependencias, y no ha cambiado nada sobre ellas en su archivo *Cargo.toml*. Cargo también sabe que no ha cambiado nada sobre su código, por lo que tampoco lo recompila. Sin nada que
hacer, simplemente sale.

Si abre el archivo *src/main.rs*, realiza un cambio trivial, y luego lo guarda y compila de nuevo, solo verá dos líneas de salida:

```text
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
```

Estas líneas muestran que Cargo solo actualiza la construcción con su pequeño cambio al archivo *src/main.rs *. Sus dependencias no han cambiado, por lo que Cargo sabe que puede reutilizar lo que ya ha descargado y compilado para esas.

#### Asegurar Compilaciones Reproducibles con el Archivo *Cargo.lock*

Cargo tiene un mecanismo que garantiza que pueda reconstruir el mismo artefacto cada vez que usted o cualquier otra persona construya su código: Cargo usará solo las versiones de las dependencias que especificó hasta que indique lo contrario. Por ejemplo, supongamos que la próxima semana sale la versión *0.8.4* del *crate* `rand`, y esa versión contiene una corrección de errores importante, pero también contiene una regresión que romperá su código. Para manejar esto, Rust crea el archivo *Cargo.lock* la primera vez que ejecuta `cargo build`, por lo que ahora tenemos esto en el directorio guessing_game.

Cuando crea un proyecto por primera vez, Cargo descubre todas las versiones de las dependencias que se ajustan a los criterios y luego las escribe en el archivo Cargo.lock. Cuando construya su proyecto en el futuro, Cargo verá que el archivo Cargo.lock existe y usará las versiones especificadas allí en lugar de hacer todo el trabajo de averiguar las versiones nuevamente. Esto le permite tener una compilación reproducible automáticamente. En otras palabras, su proyecto permanecerá en `0.8.3` hasta que lo actualice explícitamente, gracias al archivo *Cargo.lock*. Debido a que el archivo *Cargo.lock* es importante para compilaciones reproducibles, a menudo se verifica dentro del control de código fuente con el resto del código de su proyecto.

#### Actualización de un *crate* para Obtener una Nueva Versión

Cuando *desea* actualizar un crate, Cargo proporciona el comando `update`, que ignorará el archivo *Cargo.lock* y descubrirá todas las versiones más recientes que se ajusten a sus especificaciones en *Cargo.toml*. Cargo luego escribirá esas versiones en el archivo *Cargo.lock*. De lo contrario, por defecto, Cargo solo buscará versiones superiores a la `0.8.3` e inferiores a la `0.9.0`. Si el *crate* `rand` ha lanzado las dos nuevas versiones `0.8.4` y `0.9.0`, verá lo siguiente si ejecuta `cargo update`:

```text
$ cargo update
    Updating crates.io index
    Updating rand v0.8.3 -> v0.8.4
```

Cargo ignora la versión `0.9.0`. En este punto, también notará un cambio en su archivo *Cargo.lock* que indica que la versión del *crate* `rand` que está usando ahora es `0.8.4`. Para usar la versión `0.9.0` de `rand` o cualquier versión de la serie `0.9.x`, debe actualizar el archivo `Cargo.toml` para que tenga este aspecto:

```toml
[dependencies]
rand = "0.9.0"
```

La próxima vez que ejecute `cargo build`, Cargo actualizará el registro de *crates* disponibles y reevaluará sus requisitos de `rand` de acuerdo con la nueva versión que ha especificado.

Hay mucho más que decir sobre [Cargo][doccargo] <!-- ignore --> y [su ecosistema][doccratesio] <!-- ignore --> que veremos en el Capítulo 14, pero por ahora, esto es todo lo que necesita saber. Cargo hace que sea muy fácil reutilizar las bibliotecas, por lo que los *Rustáceos* pueden
escribir proyectos más pequeños que se ensamblan a partir de una serie de paquetes.

[doccargo]: http://doc.crates.io
[doccratesio]: http://doc.crates.io/crates-io.html

### Generando un Número Aleatorio

Ahora que ha agregado el *crate* `rand` a *Cargo.toml*, comencemos a usar
`rand`. El siguiente paso es actualizar *src/main.rs*, como se muestra en el
Listado 2-3.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    println!("The secret number is: {}", secret_number);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {}", guess);
}
```

<span class="caption">Listado 2-3: Agregando código para generar un número aleatorio</span>

Primero, agregamos la línea `use rand::Rng`. El *trait* `Rng` define los métodos que implementan los generadores de números aleatorios, y este *trait* debe estar dentro del ámbito para que podamos usar esos métodos. El capítulo 10 cubrirá los *traits* en detalle.

Además, estamos agregando dos líneas más en el medio. La función
`rand::thread_rng` nos dará el generador de números aleatorios particular
que vamos a usar: uno que es local al subproceso actual de ejecución y sembrado
por el sistema operativo. A continuación, llamamos al método `gen_range` en
el generador de números aleatorios. Este método está definido por el *trait*
`Rng` que trajimos al ámbito con la sentencia `use rand::Rng`. El método
`gen_range` toma dos números como argumentos y genera un número aleatorio
entre ellos. El tipo de expresión de rango que estamos usando aquí toma la forma `(start..=end)` y es inclusiva en los límites inferior y superior, por lo que debemos especificar `(1..=100)` para solicitar un número entre 1 y 100. Otra forma es `(start, end)`, inclusiva en el límite inferior pero exclusiva en el límite superior, por lo que deberíamos especificar `(1, 101)` para solicitar un número entre 1 y 100.

> Nota: No solo sabrá qué *traits* usar y qué métodos y funciones llamar desde
> un *crate*. Las instrucciones para usar un *crate* están en la
> documentación de cada *crate*. Otra buena característica de Cargo es que
> puede ejecutar el comando `cargo doc --open`, que construirá la
> documentación provista por todas sus dependencias localmente y la abrirá
> en su navegador. Si está interesado en otra funcionalidad del *crate*
> `rand`, por ejemplo, ejecute `cargo doc --open` y haga clic en `rand` en
> la barra lateral de la izquierda.

La segunda línea que agregamos al código imprime el número secreto. Esto es útil mientras desarrollamos el programa para poder probarlo, pero lo eliminaremos de la versión final. ¡No es gran cosa si el programa imprime la respuesta tan pronto como comienza!

Intente ejecutar el programa varias veces:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 7
Please input your guess.
4
You guessed: 4
$ cargo run
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 83
Please input your guess.
5
You guessed: 5
```

Debería obtener diferentes números aleatorios, y todos deberían ser números entre 1 y 100. ¡Buen trabajo!

## Comparando la Conjetura con el Número Secreto

Ahora que tenemos una entrada de usuario y un número aleatorio, podemos compararlos. Ese paso se muestra en el Listado 2-4. Tenga en cuenta que este código no se compilará todavía, como explicaremos.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
use rand::Rng;
use std::cmp::Ordering;
use std::io;

fn main() {
    // --snip--
#     println!("Guess the number!");
#
#     let secret_number = rand::thread_rng().gen_range(1..=100);
#
#     println!("The secret number is: {secret_number}");
#
#     println!("Please input your guess.");
#
#     let mut guess = String::new();
#
#     io::stdin()
#         .read_line(&mut guess)
#         .expect("Failed to read line");

    println!("You guessed: {guess}");

    match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }
}
```

<span class="caption">Listado 2-4: Manejo de los posibles valores de retorno de la comparación de dos números</span>

Primero añadimos otra declaración `use`, que trae un tipo
llamado `std::cmp::Ordering` al ámbito desde la biblioteca estándar. Como
`Result`, `Ordering` es otra enumeración, pero las variantes para `Ordering`
son `Less`, `Greater`, y `Equal`. Estos son los tres resultados posibles
cuando compara dos valores.

Luego agregamos cinco líneas nuevas en la parte inferior que usan el tipo
`Ordering`. El método `cmp` compara dos valores y puede invocarse sobre
cualquier cosa que se pueda comparar. Toma una referencia a lo que sea que
quiera comparar: aquí está comparando el `guess` con el `secret_number`.
Luego devuelve una variante de la enumeración `Ordering` que trajimos al
ámbito con la sentencia `use`. Usamos una expresión
[`match`][match] <!-- ignore --> para decidir qué hacer a continuación en
función de qué variante de `Ordering` se devolvió de la llamada a `cmp` con
los valores en `guess` y `secret_number`.

[match]: ch06-02-match.html

Una expresión `match` se compone de *brazos* (*arms*). Un brazo consta de un *patrón* y el código que debería ejecutarse si el valor dado al comienzo del `match` se ajusta al patrón de ese brazo. Rust toma el valor otorgado a `match` y mira a través del patrón de cada brazo por turno. La construcción `match` y los patrones son potentes funciones en Rust que te permiten
expresar una variedad de situaciones puede encontrar el código y asegúrese de manejarlas todas. Estas características se tratarán en detalle en el Capítulo 6 y el Capítulo 18, respectivamente.

Veamos un ejemplo de lo que sucedería con la expresión `match` usada aquí. Digamos que el usuario ha supuesto 50 y el número secreto generado aleatoriamente esta vez es 38. Cuando el código compara 50 a 38, el método `cmp` devolverá `Ordering::Greater`, porque 50 es mayor que 38. La expresión `match` obtiene el valor `Ordering::Greater` y comienza a verificar el patrón de cada brazo. Mira el patrón del primer brazo, `Ordering::Less`, y ve que el valor `Ordering::Greater` no coincide con `Ordering::Less`, por lo que ignora el código en ese brazo y pasa al siguiente brazo . El siguiente patrón del brazo, `Ordering::Greater`, *coincide* con `Ordering::Greater`. !El código asociado en ese brazo se ejecutará e imprimirá `Too big!` en la pantalla. La expresión `match` termina porque no es necesario mirar el último brazo en este escenario.

Sin embargo, el código en el listado 2-4 no se compilará todavía. Vamos a intentarlo:

```text
$ cargo build
   Compiling libc v0.2.86
   Compiling getrandom v0.2.2
   Compiling cfg-if v1.0.0
   Compiling ppv-lite86 v0.2.10
   Compiling rand_core v0.6.2
   Compiling rand_chacha v0.3.0
   Compiling rand v0.8.3
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
error[E0308]: mismatched types
  --> src/main.rs:22:21
   |
22 |     match guess.cmp(&secret_number) {
   |                     ^^^^^^^^^^^^^^ expected struct `String`, found integer
   |
   = note: expected reference `&String`
              found reference `&{integer}`

For more information about this error, try `rustc --explain E0308`.
error: could not compile `guessing_game` due to previous error
```

El núcleo del error indica que hay *tipos no coincidentes* (*mismatched types*). Rust tiene un fuerte sistema de tipo estático. Sin embargo, también tiene tipo de inferencia. Cuando escribimos
`let guess = String::new()`, Rust pudo inferir que `guess` debería ser `String` y no nos hizo escribir el tipo. El `secret_number`, por otro lado, es un tipo de número. Algunos tipos de números pueden tener un valor entre 1 y 100: `i32`, un número de 32 bits; `u32`, un número de 32 bits sin signo; `i64`, un número de 64 bits; así como otros. Rust tiene como valor predeterminado un `i32`, que es el tipo de `secret_number` a menos que agregue información de tipo en otro lugar que haría que Rust infiera un tipo numérico diferente. La razón del error es que Rust no puede comparar un *string* y un tipo *número*.

En última instancia, queremos convertir el `String` que el programa lee como entrada en un tipo de número para que podamos compararlo numéricamente con el número secreto. Podemos hacer eso agregando la siguiente línea al cuerpo de la función `main`:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
# use rand::Rng;
# use std::cmp::Ordering;
# use std::io;
#
# fn main() {
    // --snip--
#     println!("Guess the number!");
#
#     let secret_number = rand::thread_rng().gen_range(1..=100);
#
#     println!("The secret number is: {secret_number}");
#
#     println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");

    let guess: u32 = guess.trim().parse().expect("Please type a number!");

    println!("You guessed: {guess}");

    match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }
}
```

La nueva línea es:

```rust,ignore
let guess: u32 = guess.trim().parse().expect("Please type a number!");
```
Creamos una variable llamada `guess`. Pero espere, ¿el programa no tiene ya una variable llamada `guess`? Sí, pero Rust nos permite *sombrear* el valor anterior de `guess` con uno nuevo. El *sombreado* (shadowing) nos permite reutilizar el nombre de la variable `guess` en lugar de obligarnos a crear dos variables únicas, como `guess_str` y `guess`, por ejemplo. Cubriremos esto con más detalle en el Capítulo 3, pero por ahora sepa que esta característica se usa a menudo cuando desea convertir un valor de un tipo a otro tipo.

Vinculamos `guess` a la expresión `guess.trim().Parse()`. El `guess` en la expresión se refiere a al `guess` original que contiene la entrada como un *string*. El método `trim` en una instancia `String` eliminará cualquier espacio en blanco, salto de línea (`\n`), etc., al principio y al final para poder comparar con un `u32`, que solo puede contener caracteres numéricos. El usuario debe presionar <span class="keystroke">enter</span> para ingresar el dato y cumplir con `read_line`. Cuando el usuario presiona <span class="keystroke">enter</span>, se agrega un caracter de nueva línea al *string*. Por ejemplo, si el usuario escribe <span class="keystroke">5</span> y presiona <span class="keystroke">enter</span>, `guess` se ve así: `5\n`. El `\n` representa “nueva línea” (En Windows, al presionar <span class="keystroke">enter</span> se produce un retorno de carro y una nueva línea, \r\n). El método `trim` elimina `\n` o `\r\n`, lo que da como resultado solo `5`.

El método [`parse` en *string*][parse] <!-- ignorar --> convierte un *string* a algún otro tipo. Aquí lo utilizamos para convertir de string a número. Debemos decirle a Rust el tipo exacto de número que queremos utilizando `let guess: u32`. Los dos puntos (`:`) después de `guess` le dicen a Rust que *anotaremos* el tipo de la variable. Rust tiene algunos tipos de números incorporados; el `u32` que se ve aquí es un entero sin signo de 32 bits. Es una buena opción predeterminada para un pequeño número positivo. Aprenderá sobre otros tipos de números en el Capítulo 3. Además, la anotación `u32` en este programa de ejemplo y la comparación con `secret_number` significa que Rust inferirá que `secret_number` también debería ser `u32`. ¡Entonces la comparación será entre dos valores del mismo tipo!

[parse]: https://doc.rust-lang.org/stable/std/primitive.str.html#method.parse

El método `parse` solo funcionará en caracteres que puedan convertirse lógicamente en números y, por lo tanto, puede causar errores fácilmente. Si, por ejemplo, el *string* contiene `A👍%`, no habría forma de convertir eso en un número. Debido a que podría fallar, el método `parse` arroja un tipo `Result`, muy parecido al método `read_line` (descrito anteriormente en “Manejo de la
falla potencial con el tipo de resultado”). Trataremos este `Result` de la misma manera utilizando el método `expect` nuevamente. Si `parse` devuelve una variante `Err` de `Result` porque no pudo crear un número a partir del *string*, la llamada `expect` *colgará* el juego e imprimirá el mensaje que le damos. Si `parse` puede convertir con éxito el *string* en un número,
devolverá la variante `Ok` de `Result`, y `expect` devolverá el número que queremos del valor `Ok`.

¡Corramos el programa ahora!.

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 0.43 secs
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 58
Please input your guess.
  76
You guessed: 76
Too big!
```

¡Bien! Aunque se agregaron espacios antes de la suposición, el programa aún descubrió que el usuario adivinó 76. Ejecute el programa varias veces para verificar el comportamiento diferente con diferentes tipos de entrada: adivine el número correctamente, adivine un número que sea demasiado alto, y adivina un número que es demasiado bajo.

Tenemos la mayor parte del juego funcionando ahora, pero el usuario solo puede hacer una conjetura. ¡Cambiemos eso agregando un bucle!

## Permitir Múltiples Conjeturas con Bucle

La palabra clave `loop` crea un bucle infinito. Añadiremos eso ahora para dar a los usuarios más oportunidades de adivinar el número:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
# use rand::Rng;
# use std::cmp::Ordering;
# use std::io;
#
# fn main() {
#
    // --snip--

#     println!("Guess the number!");
#
#     let secret_number = rand::thread_rng().gen_range(1..=100);
#
    println!("The secret number is: {secret_number}");

    loop {
        println!("Please input your guess.");

        // --snip--

#         let mut guess = String::new();

#         io::stdin()
#             .read_line(&mut guess)
#             .expect("Failed to read line");
#
#         let guess: u32 = guess.trim().parse().expect("Please type a number!");
#
#         println!("You guessed: {guess}");
#
        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => println!("You win!"),
        }
    }
}
```

Como puede ver, hemos movido todo, desde el indicador de entrada de adivinanzas, a un bucle. Asegúrese de sangrar las líneas dentro del bucle otros cuatro espacios cada una y vuelva a ejecutar el programa. El programa ahora pedirá otra conjetura para siempre, lo que en realidad introduce un nuevo problema. ¡No parece que el usuario pueda salir!

El usuario siempre puede detener el programa utilizando el atajo de teclado <span class="keystroke">ctrl-c</span>. Pero hay otra forma de escapar de este monstruo insaciable, como se menciona en la discusión `parse` en [“Comparando la Conjetura con el Número Secreto”](ch02-00-guessing-game-tutorial.html#comparando-la-conjetura-con-el-n%C3%BAmero-secreto): si el usuario ingresa una respuesta sin número, el programa se bloqueará. El usuario puede
aprovechar eso para salir, como se muestra aquí:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 1.50s
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 59
Please input your guess.
45
You guessed: 45
Too small!
Please input your guess.
60
You guessed: 60
Too big!
Please input your guess.
59
You guessed: 59
You win!
Please input your guess.
quit
thread 'main' panicked at 'Please type a number!: ParseIntError { kind: InvalidDigit }', src/main.rs:28:47
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

Al escribir `quit` en realidad se cierra el juego, pero también lo hará cualquier otra entrada que no sea numérica. Sin embargo, esto es poco óptimo por decir lo menos. Queremos que el juego se detenga automáticamente cuando se adivine el número correcto.

### Saliendo Después de una Conjetura Correcta

Vamos a programar que el juego se cierre cuando el usuario gana agregando una sentencia `break`:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
# use rand::Rng;
# use std::cmp::Ordering;
# use std::io;
#
# fn main() {
#
#     println!("Guess the number!");
#
#     let secret_number = rand::thread_rng().gen_range(1..=100);
#
#     println!("The secret number is: {secret_number}");
#
#     loop {
#         println!("Please input your guess.");
#
#         let mut guess = String::new();
#
#         io::stdin()
#             .read_line(&mut guess)
#             .expect("Failed to read line");
#
#         let guess: u32 = guess.trim().parse().expect("Please type a number!");
#
#         println!("You guessed: {guess}");

        // --snip--

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```

Agregar la línea `break` después de `You win!` hace que el programa salga del ciclo `loop` cuando el usuario adivina el número secreto correctamente. Salir del bucle también significa salir del programa, porque el bucle es la última parte de `main`.

### Manejo de Entrada Iinválida

Para refinar aún más el comportamiento del juego, en lugar de bloquear el programa cuando el usuario ingresa algo que no es un número , hagamos que el juego ignore un ingreso no numérico para que el usuario pueda seguir adivinando. Podemos hacerlo alterando la línea donde `guess` se convierte de `String` a `u32`, como se muestra en el Listado 2-5.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
# use rand::Rng;
# use std::cmp::Ordering;
# use std::io;
#
# fn main() {
#
#     println!("Guess the number!");
#
#     let secret_number = rand::thread_rng().gen_range(1..=100);
#
#     println!("The secret number is: {secret_number}");
#
#     loop {
#         println!("Please input your guess.");
#
#         let mut guess = String::new();
#
        //  --snip--

        io::stdin()
            .read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = matc guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("You guessed: {guess}");

        // --snip--
#
#         match guess.cmp(&secret_number) {
#             Ordering::Less => println!("Too small!"),
#             Ordering::Greater => println!("Too big!"),
#             Ordering::Equal => {
#                 println!("You win!");
#                 break;
#             }
#         }
#     }
# }
```

<span class="caption">Listado 2-5: Ignorar una conjetura sin número y pedir otra conjetura en lugar de bloquear el programa</span>

Cambiamos de una llamada a `expect` a una expresión `match` para pasar de estrellarnos en un error a manejar el error. Recuerde que `parse` devuelve un tipo `Result` y `Result` es una enumeración que tiene las variantes `Ok` o `Err`. Estamos usando una expresión `match` aquí, como
hicimos con el resultado `Ordering` del método `cmp`.

Si `parse` puede convertir con éxito el *string* en un número, devolverá un
valor `Ok` que contiene el número resultante. Ese valor `Ok` coincidirá con
el patrón del primer brazo, y la expresión `match` simplemente devolverá el
valor `num` que `parse` produjo y lo puso dentro del valor `Ok`. Ese número
terminará justo donde lo queremos en la nueva variable `guess` que estamos
creando.

Si `parse` *no* puede convertir el *string* en un número, devolverá un valor `Err` que contiene más información sobre el error. El valor `Err` no coincide con el patrón `Ok(num)` en el primer brazo del `match`, pero sí con el patrón `Err(_)` en el segundo brazo. El guión bajo `_` es un valor comodín. En este ejemplo, estamos diciendo que queremos hacer coincidir todos los valores `Err` sin importar la información que contengan. Entonces el programa ejecutará el código del segundo brazo, `continue`, que le dice al programa que vaya a la siguiente iteración del `loop` y pida otra conjetura. ¡De manera efectiva, el programa ignora todos los errores que `parse` podría encontrar!

Ahora todo en el programa debería funcionar como se esperaba. Vamos a intentarlo:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 4.45s
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 61
Please input your guess.
10
You guessed: 10
Too small!
Please input your guess.
99
You guessed: 99
Too big!
Please input your guess.
foo
Please input your guess.
61
You guessed: 61
You win!
```

¡Increíble! Con un pequeño ajuste final, terminaremos el juego de adivinanzas. Recuerde que el programa todavía está imprimiendo el número secreto. Eso funcionó bien para las pruebas, pero arruina el juego. Vamos a eliminar el `println!` que muestra el número secreto. El listado 2-6 muestra el código final.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
use rand::Rng;
use std::cmp::Ordering;
use std::io;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin()
            .read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("You guessed: {guess}");

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```

<span class="caption">Listado 2-6: El código del juego de adivinanzas completo</span>

## Resumen

En este punto, ha construido con éxito el juego de adivinanzas. ¡Felicitaciones!

Este proyecto fue una forma práctica de presentarle muchos conceptos nuevos de Rust: `let`, `match`, métodos, funciones asociadas, uso de *crates* externos y más. En los siguientes capítulos, aprenderá sobre estos conceptos con más detalle. El Capítulo 3 cubre conceptos que tienen la mayoría de los lenguajes de programación, como variables, tipos de datos y funciones, y
muestra cómo usarlos en Rust. El Capítulo 4 explora la propiedad, una característica que hace que Rust sea diferente de otros lenguajes. El Capítulo 5 discute estructuras y sintaxis de métodos, y el Capítulo 6 explica cómo funcionan las enumeraciones.
