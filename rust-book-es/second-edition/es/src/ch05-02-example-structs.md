## Un Programa de Ejemplo usando Structs

Para entender cuándo podríamos querer usar *structs*, escribamos un programa que calcule el área de un rectángulo. Comenzaremos usando variables individuales y luego refactorizaremos el programa hasta que usemos *structs* en su lugar.

Hagamos un nuevo proyecto binario con Cargo llamado *rectangles*  que tomará el ancho y la altura de un rectángulo especificado en píxeles y calculará el área del rectángulo. El listado 5-8 muestra un programa corto con una forma de hacer exactamente eso en *src/main.rs* de nuestro proyecto.

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let width1 = 30;
    let height1 = 50;

    // [píxeles cuadrados] es una unidad de área como lo es, por ejemplo, [metros cuadrados]
    println!(
        "El área del rectángulo es {} [píxeles cuadrados].",
        area(width1, height1)
    );
}

fn area(width: u32, height: u32) -> u32 {
    width * height
}
```

<span class="caption">Listing 5-8: Cálculo del área de un rectángulo especificado por variables de ancho y altura separadas</span>

Ahora, ejecute este programa usando `cargo run`:

```text
$ cargo run
   Compiling rectangles v0.1.0 (file:///projects/rectangles)
    Finished dev [unoptimized + debuginfo] target(s) in 0.42s
     Running `target/debug/rectangles`
El área del rectángulo es 1500 [píxeles cuadrados].
```

Este código logra calcular el área del rectángulo llamando a la función `area` con cada dimensión, pero podemos hacer más para que este código sea claro y legible.

El problema con este código es evidente en la firma de `area`:

```rust,ignore
# fn main() {
#     let width1 = 30;
#     let height1 = 50;
#
#     println!(
#         "El área del rectángulo es {} [píxeles cuadrados].",
#         area(width1, height1)
#     );
# }
#
fn area(width: u32, height: u32) -> u32 {
#     width * height
# }
```

Se supone que la función `area` calcula el área de un rectángulo, pero la función que escribimos tiene dos parámetros, y no está claro en ninguna parte de nuestro programa que los parámetros estén relacionados. Sería más legible y manejable agrupar el ancho y el alto juntos. Ya hemos discutido una forma en que podríamos hacer eso en la sección ["El Tipo Tupla"](ch03-02-data-types.html#el-tipo-tupla-tuple) del Capítulo 3: mediante el uso de tuplas.

### Refactorización con Tuplas

Listing 5-9  muestra otra versión de nuestro programa que usa tuplas.

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let rect1 = (30, 50);

    println!(
        "El área del rectángulo es {} [píxeles cuadrados].",
        area(rect1)
    );
}

fn area(dimensions: (u32, u32)) -> u32 {
    dimensions.0 * dimensions.1
}
```

<span class="caption">Listing 5-9: Especificando el ancho y alto del rectángulo con una tupla</span>

En cierto modo, este programa es mejor. Las tuplas nos permiten agregar un poco de estructura y ahora estamos pasando solo un argumento. Pero en otro sentido, esta versión es menos clara: las tuplas no nombran sus elementos, por lo que tenemos que indexar las partes de la tupla, lo que hace que nuestro cálculo sea menos obvio.

Mezclar el ancho y la altura no importaría para el cálculo del área, pero si queremos dibujar el rectángulo en la pantalla, ¡sería importante! Tendríamos que tener en cuenta que el *ancho* es el índice `0` y la *altura* es el índice `1` de la tupla. Esto sería aún más difícil de entender y de tener en cuenta para otra persona si tuviera que usar nuestro código. Debido a que no hemos transmitido el significado de nuestros datos en nuestro código, ahora es más fácil introducir errores.

### Refactorización con Structs: Agregando más Significado

Usamos *structs* para agregar significado al etiquetar los datos. Podemos transformar la tupla que estamos usando en un *struct* con un nombre para el todo y nombres para las partes, como se muestra en el Listado 5-10.

<span class="filename">Filename: src/main.rs</span>

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle { 
        width: 30,
        height: 50,
    };

    println!(
        "El área del rectángulo es {} [píxeles cuadrados].",
        area(&rect1)
    );
}

fn area(rectangle: &Rectangle) -> u32 {
    rectangle.width * rectangle.height
}
```

<span class="caption">Listing 5-10: Definiendo un struct `Rectangle`</span>

Aquí hemos definido una estructura y la hemos llamado `Rectangle`. Dentro de las llaves, definimos los campos como `width` y `height` (ancho y altura), ambos tienen el tipo `u32`. Luego, en `main`, creamos una instancia particular de `Rectangle` que tiene un ancho de 30 y una altura de 50.

Nuestra función `area` ahora se define con un parámetro, que hemos llamado `rectangle`, cuyo tipo es un préstamo inmutable de una instancia *struct* `Rectangle`. Como se mencionó en el Capítulo 4, queremos tomar prestado el *struct* en lugar de tomar posesión de él. De esta manera, `main` conserva su propiedad[^1] y puede continuar usando `rect1`, que es la razón por la que usamos `&` en la firma de la función y donde llamamos a la función.

La función de `area` accede a los campos `width` y `height` de la instancia `Rectangle` (tenga en cuenta que acceder a los campos de una instancia *struct* prestada no mueve los valores de los campos, razón por la cual a menudo ve préstamos de *structs*). Nuestra firma de función para `area` ahora dice exactamente lo que queremos decir: calcule el área de `Rectangle`, usando sus campos `width` y `height`. Esto transmite que el ancho y el alto están relacionados entre sí, y da nombres descriptivos a los valores en lugar de usar los valores de índice de tupla de `0` y `1`. Esta es una victoria para la claridad.

### Añadiendo Funcionalidad Útil con *Traits* Derivados

Sería útil poder imprimir una instancia de `Rectangle` mientras depuramos nuestro programa y ver los valores para todos sus campos. El listado 5-11 intenta usar la macro `println!` como hemos hecho en capítulos anteriores. Sin embargo, esto no funcionará.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };

    println!("rect1 is {}", rect1);
}
```

<span class="caption">Listing 5-11: Intentando imprimir una instancia de `Rectangle`</span>

Cuando ejecutamos este código, obtenemos un error con este mensaje central:

```text
error[E0277]: `Rectangle` doesn't implement `std::fmt::Display`
```

La macro `println!` Puede hacer muchos tipos de formateo, y de forma predeterminada, las llaves indican a `println!` que use el formato conocido como `Display`: salida destinada para el consumo directo del usuario final. Los tipos primitivos que hemos visto hasta ahora implementan `Display` de forma predeterminada, ya que solo hay una forma en la que querría mostrar un `1` o cualquier otro tipo primitivo a un usuario. Pero con structs, la forma en que `println!` debería formatear la salida es menos clara porque hay más posibilidades de visualización: ¿Quiere comas o no? ¿Quiere imprimir las llaves? ¿Deberían mostrarse todos los campos? Debido a esta ambigüedad, Rust no intenta adivinar lo que queremos, y los *structs* no tienen una implementación provista por `Display` para usar con `println!` y el marcador `{}`.

Si continuamos leyendo los errores, encontraremos esta nota útil:

```text
   = help: the trait `std::fmt::Display` is not implemented for `Rectangle`
   = note: in format strings you may be able to use `{:?}` (or {:#?} for pretty-print) instead
```

¡Vamos a intentarlo! La llamada a la macro `println!` se verá como `println ("rect1 es {:?} ", rect1);`. Poner el especificador `:?` dentro de las llaves le dice a `println!` que queremos usar un formato de salida llamado `Debug`. El *trait* `Debug` nos permite imprimir nuestro estructuro *struct* de una manera que es útil para los desarrolladores, para que podamos ver su
valor mientras depuramos nuestro código.

Ejecuta el código con este cambio. ¡Arrg! Todavía recibimos un error:

```text
error[E0277]: `Rectangle` doesn't implement `Debug`
```

Pero, de nuevo, el compilador nos da una nota útil:

```text
   = help: the trait `Debug` is not implemented for `Rectangle`
   = note: add `#[derive(Debug)]` to `Rectangle` or manually `impl Debug for Rectangle`
```

Rust *no* incluye funcionalidad para imprimir información de depuración,
pero tenemos que optar explícitamente para que esa funcionalidad esté disponible
para nuestro *struct*. Para hacer eso, agregamos el atributo `#[derive(Debug)]`
justo antes de la definición del *struct*, como se muestra en el Listado 5-12.

<span class="filename">Filename: src/main.rs</span>

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!("rect1 is {:?}", rect1);
}
```

<span class="caption">Listing 5-12: Añadiendo el atributo para derivar el *trait* `Debug`
e imprimir la instancia `Rectangle` utilizando el formato de depuración</span>

Ahora cuando ejecutamos el programa, no obtendremos ningún error, y veremos el siguiente resultado:

```text
$ cargo run
   Compiling rectangles v0.1.0 (file:///projects/rectangles)
    Finished dev [unoptimized + debuginfo] target(s) in 0.48s
     Running `target/debug/rectangles`
rect1 is Rectangle { width: 30, height: 50 }
```

¡Bien!, no es el resultado más bonito, pero muestra los valores de todos los
campos para esta instancia, lo que definitivamente ayudaría durante la depuración.
Cuando tenemos estructuras más grandes, es útil tener una salida que sea un poco
más fácil de leer; en esos casos, podemos usar `{:#?}` en lugar de `{:?}` en el
*string* `println!`. Cuando usamos el estilo `{:#?}` En el ejemplo, la salida se verá así:


```text
$ cargo run
   Compiling rectangles v0.1.0 (file:///projects/rectangles)
    Finished dev [unoptimized + debuginfo] target(s) in 0.48s
     Running `target/debug/rectangles`
rect1 is Rectangle {
    width: 30,
    height: 50,
}
```

Otra forma de imprimir un valor usando el formato `Debug` es usar la [macro dbg!](https://doc.rust-lang.org/std/macro.dbg.html), que se apropia de una expresión (a diferencia de println! que toma una referencia), imprime el archivo y el número de línea donde se esa llamada a la macro `dbg!` se produce en su código, junto con la expresión, el valor resultante de esa expresión y retorna la propiedad del valor.

> Nota: Llamar a la macro `dbg!` imprime en el flujo de la consola de *errores estándar* (`stderr`), a diferencia de `println!` que imprime en el flujo de consola de *salida estándar* (`stdout`). Hablaremos más sobre `stderr` y `stdout` en la sección ["Escribiendo Mensajes de Error en "Standard Error" en lugar de "Standard Output" en el Capítulo 12](ch12-06-writing-to-stderr-instead-of-stdout.html#escribiendo-mensajes-de-error-en-standard-error-en-lugar-de-standard-output).

Aquí hay un ejemplo en el que estamos interesados en el valor que se asigna al campo `width`, así como el valor de todo el *struct* en `rect1`:

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let scale = 2;
    let rect1 = Rectangle {
        width: dbg!(30 * scale),
        height: 50,
    };

    dbg!(&rect1);
}
```

Podemos poner `dbg!` encerrando la expresión `30 * scale` y, porque `dbg!` devuelve la propiedad del valor de la expresión, el campo `width` obtendrá el mismo valor que si no tuviéramos la llamada a `dbg!` allí. En la siguiente llamada, no queremos `dbg!` tome la posesión de `rect1`, por lo que usamos una referencia a `rect1`. Así es como se ve el resultado de este ejemplo:

```text
$ cargo run
   Compiling rectangles v0.1.0 (file:///projects/rectangles)
    Finished dev [unoptimized + debuginfo] target(s) in 0.61s
     Running `target/debug/rectangles`
[src/main.rs:10] 30 * scale = 60
[src/main.rs:14] &rect1 = Rectangle {
    width: 60,
    height: 50,
}
```

Podemos ver que la primera parte de la salida proviene de la línea 10 de *src/main.rs*, donde estamos depurando la expresión `30 * scale`, y su valor resultante es 60 (el formato `Debug` implementado para enteros es imprimir solo su valor). La llamada a `dbg!` en la línea 14 de *src/main.rs* genera el valor de `&rect1`, que es el *struct* `Rectangle`. Esta salida utiliza el bonito formato `Debug` del tipo `Rectangle` ¡La macro `dbg!` puede ser realmente útil cuando intenta averiguar qué está haciendo su código!

Además del *trait* `Debug`, Rust ha proporcionado una serie de *traits* para que los usemos con el atributo `derive`, que puede agregar un comportamiento útil a nuestros tipos personalizados. Esos *traits* y sus comportamientos están listados en el [Apéndice C](appendix-03-derivable-traits.html). Cubriremos cómo implementar estos *traits* con comportamiento personalizado y cómo crear sus propios *traits* en el Capítulo 10. También hay muchos atributos además de `derive`. Para obtener más información, consulte la [sección "Atributos" de la Referencia de Rust](https://doc.rust-lang.org/reference/attributes.html#attributes).

Nuestra función `area` es muy específica: solo calcula el área de los rectángulos. Sería útil relacionar este comportamiento más estrechamente con nuestro *struct* `Rectangle`, porque no funcionará con ningún otro tipo. Veamos cómo podemos continuar refactorizando este código convirtiendo la función `area` en un *método* `area` definido en nuestro tipo `Rectangle`.

 </br></br></br></br>

<small>[^1] Nota del traductor: se tradujo el texto conservando su sentido en forma literal. El texto *(en inglés)* infiere que la propiedad del contenido de `rect1` también es propiedad de la función `main`. Difiero en este concepto. La función `main` provee un *scope* (ámbito) donde `rect1` es propietaria del contenido, pero la función `main` no es propietaria del contenido en sí. Cuando se transfiere la propiedad a través de los parámetros de una función, no es la función la que recibe la propiedad, sino que ésta provee un *scope* donde son los parámetros los que adquieren la propiedad.</small>