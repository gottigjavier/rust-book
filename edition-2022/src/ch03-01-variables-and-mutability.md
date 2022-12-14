## Variables y Mutabilidad

Como se mencionó en la sección ["Almacenando Valores con Variables"](ch02-00-guessing-game-tutorial.html#almacenando-valores-con-variables), las variables por defecto son inmutables. Este es uno de los muchos empujones que Rust le da para escribir su código de una manera que aproveche la seguridad y la fácil concurrencia que ofrece Rust. Sin embargo, todavía tiene la opción de hacer que sus variables sean mutables. Exploremos cómo y por qué Rust lo anima a elegir la inmutabilidad y por qué a veces es posible que desee no optar por ella.

Cuando una variable es inmutable, una vez que un valor está vinculado a un nombre, no puede cambiar ese valor. Para ilustrar esto, generemos un nuevo proyecto llamado *variables* en su directorio *projects* usando `cargo new variables`.

Luego, en su nuevo directorio *variables*, abra *src/main.rs* y reemplace su
código con el siguiente código que no se compilará aún:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
fn main() {
    let x = 5;
    println!("The value of x is: {}", x);
    x = 6;
    println!("The value of x is: {}", x);
}
```

Guarde y ejecute el programa usando `cargo run`. Debería recibir un mensaje
de error, como se muestra en este resultado:

```text
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
error[E0384]: cannot assign twice to immutable variable `x`
 --> src/main.rs:4:5
  |
2 |     let x = 5;
  |         -
  |         |
  |         first assignment to `x`
  |         help: consider making this binding mutable: `mut x`
3 |     println!("The value of x is: {x}");
4 |     x = 6;
  |     ^^^^^ cannot assign twice to immutable variable

For more information about this error, try `rustc --explain E0384`.
error: could not compile `variables` due to previous error
```

Este ejemplo muestra cómo el compilador le ayuda a encontrar errores en sus programas. Aunque los errores del compilador pueden ser frustrantes, solo se refieren a que su programa todavía no está haciendo lo que quiere hacer de manera segura; ello *no* significan, ¡que no es un buen programador! Los *Rustáceos* experimentados aún tienen errores de compilación.

El mensaje de error indica que la causa del error es que `no puede asignar dos veces a la variable inmutable 'x'`, porque trató de asignar un segundo valor a la variable inmutable `x`.

Es importante que obtengamos errores de tiempo de compilación cuando intentamos cambiar un valor que previamente hemos designado como inmutable porque esta misma situación puede conducir a fallas. Si una parte de nuestro código opera bajo el supuesto de que el valor nunca cambiará y otra parte de nuestro código cambia ese valor, es posible que la primera parte del código
no haga lo que estaba diseñado para hacer. La causa de este tipo de error puede ser difícil de rastrear después del hecho, especialmente cuando la segunda parte del código cambia el valor solo *algunas veces*.

En Rust, el compilador garantiza que cuando declara que un valor no cambiará, realmente no cambiará. Eso significa que cuando está leyendo y escribiendo código, no es necesario que realice un seguimiento de cómo y dónde puede cambiar un valor. Su codigo es así más fácil de razonar.

Pero la mutabilidad puede ser muy útil. Las variables son inmutables solo por
defecto. Como lo hizo en el Capítulo 2, puede hacer que sean mutables
añadiendo `mut` delante del nombre de la variable. Además de permitir que
este valor cambie, `mut` transmite intención para los futuros lectores del
código al indicar que en otras partes del código esta variable cambiará de valor.

Por ejemplo, cambiemos *src/main.rs* a lo siguiente:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let mut x = 5;
    println!("The value of x is: {}", x);
    x = 6;
    println!("The value of x is: {}", x);
}
```

Cuando ejecutamos el programa ahora, obtenemos esto:

```text
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.30 secs
     Running `target/debug/variables`
The value of x is: 5
The value of x is: 6
```

Podemos cambiar el valor vinculado a `x` de `5` a `6` cuando se usa `mut`. En última instancia, decidir si usar la mutabilidad o no depende de usted y depende de lo que crea que es más claro en esa situación particular.

### Constantes

Al igual que las variables inmutables, las *constantes* son valores que están vinculados a un nombre y no se les permite cambiar, pero existen algunas diferencias entre las constantes y las variables.

Primero, no está permitido usar `mut` con constantes. Las constantes no son
solo inmutables por defecto, siempre son inmutables. Usted declara constantes usando la palabra clave `const` en lugar de la palabra clave `let`, y el tipo del valor *debe* ser anotado. Estamos a punto de cubrir *types* (tipos) y *type annotations* (anotaciones de tipo) en la siguiente sección, ["Tipos de datos"](ch03-02-data-types.html), así que no se preocupe por los detalles en este momento. Solo debe saber que siempre debe anotar el tipo.

Las constantes se pueden declarar en cualquier ámbito, incluido el ámbito global, lo que las hace útiles para valores que muchas partes del código necesitan conocer.

La última diferencia es que las constantes pueden establecerse sólo de una expresión constante, no como el resultado de un valor que solo podría calcularse en tiempo de ejecución.

Aquí hay un ejemplo de una declaración constante:

```rust
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
```
El nombre de la constante es `THREE_HOURS_IN_SECONDS` y su valor se establece en el resultado de multiplicar 60 (el número de segundos en un minuto) por 60 (el número de minutos en una hora) por 3 (el número de horas que queremos contar en este programa). La convención de nomenclatura de Rust para las constantes es usar todo en mayúsculas con guiones bajos entre palabras. El compilador puede evaluar un conjunto limitado de operaciones en tiempo de compilación, lo que nos permite optar por escribir este valor en un manera que sea más fácil de entender y verificar, en lugar de establecer esta constante al valor 10.800. Consulte la sección de [Rust Reference sobre "Evaluación Constante" (en inglés)](https://doc.rust-lang.org/stable/reference/const_eval.html) para obtener más información sobre qué operaciones se pueden utilizar al declarar constantes. 

Las constantes son válidas durante todo el tiempo que se ejecuta un programa, dentro del alcance en el que fueron declaradas, lo que las convierte en una opción útil para los valores en su dominio de aplicación que varias partes del programa podrían necesitar conocer, como la cantidad máxima de puntos que cualquier jugador de un juego puede ganar o la velocidad de la luz.

Nombrar como constantes a los valores codificados usados a lo largo de su programa es útil para transmitir el significado de ese valor a los futuros mantenedores del código. También ayuda tener solo un lugar en el código que necesitaría cambiar si el valor codificado debe actualizarse en el futuro.

### *Sombreado* (*Shadowing*)

Como se vio en el [Capítulo 2](ch02-00-guessing-game-tutorial.html), puede declarar una nueva variable con el mismo nombre que una variable anterior. Los Rustáceos dicen que la primera variable es *sombreada* por la segunda, lo que significa que el valor de la segunda variable es lo que el compilador verá cuando se use el nombre de la variable. En efecto, la segunda variable *sombrea* a la primera, tomando cualquier uso del nombre de la variable para sí misma hasta que *es oscurecida* de nuevo o finaliza su ámbito. Podemos sombrear una variable usando el mismo nombre de variable y repitiendo el uso de la palabra clave `let` de la siguiente manera:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let x = 5;

    let x = x + 1;

    {
        let x = x * 2;
        println!("The value of x in the inner scope is: {x}");
    }

    println!("The value of x is: {x}");
}
```

Este programa primero enlaza `x` al valor `5`. Luego crea una nueva variable `x` repitiendo `let x =`, tomando el valor original y sumándole `1`, así que ahora el valor de `x` es `6`. Luego, dentro de un ámbito interno creado por las llaves, el tercer `let` también sombrea `x` y crea una nueva variable, multiplicando el valor previo por `2`, dando a `x` un valor de `12`. Cuando ese alcance termina, el sombreado interno también termina y `x` vuelve a ser `6`. Cuando ejecutamos este programa, arrojará lo siguiente: 

```text
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/variables`
The value of x in the inner scope is: 12
The value of x is: 6
```

El *sombreado* (*shadowing*) es diferente a marcar una variable como `mut`,
porque obtendremos un error en tiempo de compilación si accidentalmente
intentamos reasignar a esta variable sin usar la palabra clave `let`. Al
usar `let` podemos realizar algunas transformaciones en un valor, pero la
variable sigue siendo inmutable después de que se hayan completado esas
transformaciones.

La otra diferencia entre `mut` y *shadowing* es que, debido a que estamos
creando efectivamente una nueva variable cuando usamos nuevamente la palabra 
clave `let`, podemos cambiar el *tipo* del valor pero reutilizar el mismo
nombre. Por ejemplo, supongamos que nuestro programa le pide al usuario que
muestre cuántos espacios quiere entre algunos textos ingresando caracteres
de espacio en blanco, pero realmente queremos almacenar esa entrada como un número:

```rust
# fn main() {
    let spaces = "   ";
    let spaces = spaces.len();
# }
```

La primera variable `spaces` es un tipo *string* y la segunda variable `spaces`, que es una nueva variable que pasa a tener el mismo nombre que la primera, es un tipo *número*. El
sombreado nos ahorra tener que pensar en diferentes nombres, como `spaces_str` y `spaces_num`. En su lugar, podemos reutilizar simplemente el nombre de `spaces`. Sin embargo, si tratamos de usar `mut` para esto, como se muestra aquí, obtendremos un error en tiempo de compilación:

```rust,ignore
let mut spaces = "   ";
spaces = spaces.len();
```

El error dice que no podemos cambiar el tipo de una variable:

```text
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
error[E0308]: mismatched types
 --> src/main.rs:3:14
  |
2 |     let mut spaces = "   ";
  |                      ----- expected due to this value
3 |     spaces = spaces.len();
  |              ^^^^^^^^^^^^ expected `&str`, found `usize`

For more information about this error, try `rustc --explain E0308`.
error: could not compile `variables` due to previous error
```

Ahora que hemos explorado cómo funcionan las variables, veamos qué más tipos de
datos pueden haber.
