## Referencias y Préstamos (*References and Borrowing*)

El problema con el código de tupla en el Listado 4-5 es que tenemos que devolver el `String` a la función de llamada para que podamos seguir usando el `String` después de la llamada a `calculate_length`, porque el `String` se movió a `calculate_length`.  En su lugar, podemos proporcionar una referencia al valor del `String`. Una *referencia* es como un puntero en el sentido de que es una dirección que podemos seguir para acceder los datos almacenados en esa dirección; esos datos son propiedad de alguna otra variable. A diferencia de un puntero, se garantiza que una referencia apunte a un valor válido de un tipo particular durante la vida de esa referencia. 

Aquí se explica cómo definiría y usaría una función `calculate_length` que hace referencia a
un objeto como parámetro en lugar de tomar posesión del valor:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```

Primero, observe que todo el código de tupla en la declaración de la variable y el valor de retorno de la función han desaparecido. En segundo lugar, tenga en cuenta que pasamos `&s1` a `calculate_length` y, en su definición, tomamos `&String` en lugar de `String`. Estos *ampersands* (`&`) representan *referencias*, y le permiten referirse a algún valor sin tomar posesión de él. 
La figura 4-5 muestra un diagrama.

<img alt="&String s pointing at String s1" src="img/trpl04-05.svg" class="center" />

<span class="caption">Figure 4-5: Un diagrama de `&String s` apuntando a `String s1`</span>

> Nota: lo contrario de *referenciar* usando `&` es *desreferenciar*, que se
> logra con el operador de desreferencia, `*`. Veremos algunos usos del operador de
> desreferencia en el Capítulo 8 y discutiremos los detalles de desreferenciación en
> el Capítulo 15.

Echemos un vistazo más de cerca a la llamada de función aquí:

```rust
# fn calculate_length(s: &String) -> usize {
#     s.len()
# }
let s1 = String::from("hello");

let len = calculate_length(&s1);
```

La sintaxis `&s1` nos permite crear una *referencia* al valor de `s1` pero no lo posee. Como no es el propietario, el valor al que apunta no se eliminará cuando la referencia deje de usarse.

Del mismo modo, la firma de la función usa `&` para indicar que el tipo del parámetro `s` es una referencia. Agreguemos algunos comentarios explicativos:

```rust
fn calculate_length(s: &String) -> usize { // s es una referencia a un String
    s.len()
} // Aquí, s sale del scope. Pero como no tiene propiedad de lo que referencia,
  // no sucede nada.
```

El scope en el que la variable `s` es válida es el mismo que el scope de cualquier parámetro de función, pero el valor al que apunta la referencia no se elimina cuando `s` deja de usarse porque `s` no tiene propiedad. Cuando las funciones tienen referencias como parámetros en lugar de los valores reales, no necesitaremos devolver los valores para devolver la propiedad, porque nunca tuvimos la propiedad.

A la acción de crear una *referencia* la llamamos *préstamo* (*borrowing*). Como en la vida real, si una persona posee algo, se lo puede pedir prestado. Cuando haya terminado, debe devolverlo. No lo posee.

Entonces, ¿qué sucede si tratamos de modificar algo que estamos pidiendo prestado?
Pruebe el código en el Listado 4-6. Alerta de spoiler: ¡no funciona!

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
fn main() {
    let s = String::from("hello");

    change(&s);
}

fn change(some_string: &String) {
    some_string.push_str(", world");
}
```

<span class="caption">Listing 4-6: Intentando modificar un valor prestado</span>

Aquí está el error:

```text
$ cargo run
   Compiling ownership v0.1.0 (file:///projects/ownership)
error[E0596]: cannot borrow `*some_string` as mutable, as it is behind a `&` reference
 --> src/main.rs:8:5
  |
7 | fn change(some_string: &String) {
  |                        ------- help: consider changing this to be a mutable reference: `&mut String`
8 |     some_string.push_str(", world");
  |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ `some_string` is a `&` reference, so the data it refers to cannot be borrowed as mutable

For more information about this error, try `rustc --explain E0596`.
error: could not compile `ownership` due to previous error
```

Así como las variables son inmutables por defecto, también lo son las referencias.
No se nos permite modificar algo a lo que tenemos una referencia.

### Referencias Mutables

Podemos corregir el código del Listado 4-6 para permitirnos modificar un valor prestado con solo unos pequeños ajustes usando una referencia mutable:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

Primero, cambiamos `s` para que sea `mut`. Luego creaamos una referencia mutable con `&mut s` donde llamamos a la función `change` y actualizamos la firma de la función para aceptar una referencia mutable con `some_string: &mut String`. Esto deja muy claro que la función `change` mutará el valor que toma prestado.

Las referencias mutables tienen una gran restricción: si tiene una referencia mutable a un valor, no puede tener otras referencias a ese mismo valor. Este código que intenta crear dos referencias mutables a `s` fallará:

```rust,ignore
# fn main() {
    let mut s = String::from("hello");

    let r1 = &mut s;
    let r2 = &mut s;

    println!("{}, {}", r1, r2);
# }
```

Aquí está el error:

```text
$ cargo run
   Compiling ownership v0.1.0 (file:///projects/ownership)
error[E0499]: cannot borrow `s` as mutable more than once at a time
 --> src/main.rs:5:14
  |
4 |     let r1 = &mut s;
  |              ------ first mutable borrow occurs here
5 |     let r2 = &mut s;
  |              ^^^^^^ second mutable borrow occurs here
6 | 
7 |     println!("{}, {}", r1, r2);
  |                        -- first borrow later used here

For more information about this error, try `rustc --explain E0499`.
error: could not compile `ownership` due to previous error
```

Este error dice que este código no es válido porque no podemos tomar prestado `s` como mutable más de una vez al mismo tiempo. El primer préstamo mutable está en `r1` y debe durar hasta que se use en `println!`, pero entre la creación de esa referencia mutable y su uso, intentamos crear otra referencia mutable en `r2` que toma prestados los mismos datos que `r1`.

La restricción que impide múltiples referencias mutables a los mismos datos al mismo tiempo permite la mutación, pero de una manera muy controlada. Es algo con lo que los nuevos Rustáceos luchan, porque la mayoría de los lenguajes le permiten mutar cuando lo desee.

El beneficio de tener esta restricción es que Rust puede evitar carreras de datos en tiempo de compilación. Una *data race* es similar a una condición de carrera y ocurre cuando se producen estos tres comportamientos:

* Dos o más punteros acceden a los mismos datos al mismo tiempo.
* Al menos uno de los punteros se está utilizando para escribir en los datos.
* No se usa ningún mecanismo para sincronizar el acceso a los datos.

Las carreras de datos causan un comportamiento indefinido y pueden ser difíciles de diagnosticar y corregir cuando intenta rastrearlos en tiempo de ejecución. Rust evita que este problema ocurra porque se negará a compilar código con *data race*.

Como siempre, podemos usar llaves para crear un nuevo ámbito, permitiendo múltiples referencias mutables, pero no *simultáneas*:

```rust
# fn main() {
let mut s = String::from("hello");

{
    let r1 = &mut s;

} // r1 queda fuera del scope aquí, por lo que podemos hacer una nueva referencia sin problemas.

let r2 = &mut s;
# }
```

Rust aplica una regla similar para combinar referencias mutables e inmutables. Este código da como resultado un error:

```rust,ignore
# fn main() {
    let mut s = String::from("hello");

    let r1 = &s; // no problem
    let r2 = &s; // no problem
    let r3 = &mut s; // BIG PROBLEM

    println!("{}, {}, and {}", r1, r2, r3);
# }
```

Aquí está el error:

```text
$ cargo run
   Compiling ownership v0.1.0 (file:///projects/ownership)
error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable
 --> src/main.rs:6:14
  |
4 |     let r1 = &s; // no problem
  |              -- immutable borrow occurs here
5 |     let r2 = &s; // no problem
6 |     let r3 = &mut s; // BIG PROBLEM
  |              ^^^^^^ mutable borrow occurs here
7 | 
8 |     println!("{}, {}, and {}", r1, r2, r3);
  |                                -- immutable borrow later used here

For more information about this error, try `rustc --explain E0502`.
error: could not compile `ownership` due to previous error
```

¡Uf! *Tampoco* podemos tener una referencia mutable mientras tengamos una referencia inmutable al mismo valor.

¡Los usuarios de una referencia inmutable no esperan que el valor cambie repentinamente debajo de ellos! Sin embargo, se permiten múltiples referencias inmutables porque nadie que solo esté leyendo los datos tiene la capacidad de afectar la lectura de los datos de otra persona.

Tenga en cuenta que el scope de una referencia comienza desde donde se introdujo y continúa hasta la última vez que se usó esa referencia. Por ejemplo, este código se compilará porque el último uso de las referencias inmutables, `¡println!`, ocurre antes de que se introduzca la referencia mutable:

```rust,ignore
# fn main() {
    let mut s = String::from("hello");

    let r1 = &s; // no problem
    let r2 = &s; // no problem
    println!("{} and {}", r1, r2);
    // variables r1 y r2 no deben usarse después de este punto

    let r3 = &mut s; // no problem
    println!("{}", r3);
# }
```
Los scopes de las referencias inmutables `r1` y `r2` terminan después de `println!`, donde se usaron por última vez, que es antes de que se cree la referencia mutable `r3`. Estos scopes no se superponen, por lo que este código está permitido. La capacidad del compilador para decir que una referencia ya no se usa en un punto antes del final del scope se llama *Non-Lexical Lifetimes* (NLL para abreviar). Puede leer más sobre esto en [The Edition Guide](https://blog.rust-lang.org/2018/12/06/Rust-1.31-and-rust-2018.html#non-lexical-lifetimes).

Aunque estos errores pueden ser frustrantes a veces, recuerde que es el compilador Rust el que señala un posible error anticipadamente (en tiempo de compilación en lugar de en tiempo de ejecución) y le muestra exactamente dónde está el problema. Entonces no tiene que rastrear por qué sus datos no son lo que pensaba que eran.

### Referencias Colgantes

En lenguajes con punteros, es fácil crear erróneamente un *puntero colgante* (un puntero que hace referencia a una ubicación en la memoria que se le puede haber dado a alguien más) al liberar algo de la memoria mientras se conserva un puntero a esa memoria. En Rust, por el contrario, el compilador garantiza que las referencias nunca serán referencias colgantes: si tiene una referencia a algunos datos, el compilador se asegurará de que los datos no queden fuera del scope antes que lo haga la referencia a esos datos.

Intentemos crear una referencia colgante para ver cómo Rust las previene con un error en tiempo de compilación: 

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String {
    let s = String::from("hello");

    &s
}
```

Aquí está el error:

```text
$ cargo run
   Compiling ownership v0.1.0 (file:///projects/ownership)
error[E0106]: missing lifetime specifier
 --> src/main.rs:5:16
  |
5 | fn dangle() -> &String {
  |                ^ expected named lifetime parameter
  |
  = help: this function's return type contains a borrowed value, but there is no value for it to be borrowed from
help: consider using the `'static` lifetime
  |
5 | fn dangle() -> &'static String {
  |                ~~~~~~~~

For more information about this error, try `rustc --explain E0106`.
error: could not compile `ownership` due to previous error
```

Este mensaje de error se refiere a una función que aún no hemos cubierto: tiempos de vida.
Analizaremos los tiempos de vida en detalle en el Capítulo 10. Pero, si ignora las partes
acerca de tiempos de vida, el mensaje contiene la clave de por qué este código es un problema:

```text
en-this function's return type contains a borrowed value, but there is no value
for it to be borrowed from.

es-el tipo de devolución de esta función contiene un valor prestado, pero no hay
ningún valor para que se tome prestado.
```

Echemos un vistazo más de cerca a lo que está sucediendo exactamente en cada etapa de
nuestro código `dangle`:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
fn dangle() -> &String { // dangle devuelve una referencia a un String

    let s = String::from("hello"); // s es un nuevo String

    &s // devolvemos una referencia al String s
} // s sale del scope y se descarta. Desaparece de la memoria.
  // ¡Peligro!
```

Debido a que `s` se crea dentro de `dangle`, cuando el código de `dangle` finaliza, `s` será desasignado. Pero tratamos de devolver una referencia a `s`. Eso significa que esta referencia estaría apuntando a un `String` inválido ¡Eso no es bueno! Rust no nos dejará hacer esto.

La solución aquí es devolver el `String` directamente:

```rust
fn no_dangle() -> String {
    let s = String::from("hello");

    s
}
```

Esto funciona sin ningún problema. La propiedad se trasladó y no se desasignó nada.

### Las Reglas de las Referencias

Repasemos lo que hemos visto sobre las referencias:

* En cualquier momento dado puede tener: *o* una referencia mutable, *o* cualquier cantidad de referencias inmutables.
* Las referencias deben ser siempre válidas.

A continuación, veremos un tipo diferente de referencia: *slices*.
