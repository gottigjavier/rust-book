## La Estructura de Control `match`

Rust tiene una estructura de control extremadamente poderosa llamada `match`que le permite comparar un valor con una serie de patrones y luego ejecutar el código según el patrón que coincida. Los patrones pueden estar formados por valores literales, nombres de variables, comodines y muchas otras cosas; El Capítulo 18 cubre todos los diferentes tipos de patrones y lo que hacen. El poder de `match` proviene de la expresividad de los patrones y del hecho de que el compilador confirma que se manejan todos los casos posibles.

Piense en una expresión de `match` como una máquina clasificadora de monedas: las monedas se deslizan por una pista con orificios de diferentes tamaños a lo largo de ella, y cada moneda cae por el primer orificio que encuentra que encaja. De la misma manera, los valores pasan por cada patrón en `match`, y en el primer patrón en el que el valor encaje, el valor cae en el bloque de código asociado para ser utilizado durante la ejecución.

Hablando sw monedas, ¡utilicémoslas como ejemplo usando `match`!
Podemos escribir una función que puede tomar una moneda desconocida de los Estados Unidos y,
de manera similar a la máquina de conteo, determinar qué moneda es y devolver su valor en
centavos, como se muestra aquí en el Listado 6-3.

```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
#
# fn main() {}
```

<span class="caption">Listing 6-3: Un enum y una expresión `match` que tiene las variantes del enum como sus patrones</span>

Vamos a desglosar el `match` en la función `value_in_cents`. Primero, listamos la palabra clave `match` seguida de una expresión, que en este caso es el valor `coin`. Esto parece muy similar a una expresión utilizada con `if`, pero hay una gran diferencia: con `if`, la expresión necesita devolver un valor booleano, pero aquí, puede ser de cualquier tipo. El tipo de `coin` en este ejemplo es el *enum* de `coin` que definimos en la línea 1.

A continuación están los brazos del `match`. Un brazo tiene dos partes: un patrón y algún código.
El primer brazo aquí tiene un patrón que es el valor `Coin::Penny` y luego el operador `=> ` que separa el patrón y el código a ejecutar. El código en este caso es solo el valor `1`. Cada brazo está separado del siguiente con una coma.

Cuando se ejecuta la expresión `match`, compara el valor resultante con el patrón de cada brazo, en orden. Si un patrón coincide con el valor, el código asociado con ese patrón se ejecuta. Si ese patrón no coincide con el valor, la ejecución continúa al siguiente brazo, al igual que en una máquina clasificadora de monedas. Podemos tener tantas brazos como necesitemos: en el Listado 6-3, nuestro `match` tiene cuatro brazos.

El código asociado con cada brazo es una expresión, y el valor resultante de la expresión en el brazo correspondiente es el valor que se devuelve para la expresión `match` completa.

Por lo general, no usamos corchetes si el código del brazo del `match` es corto, como en el Listado 6-3, donde cada brazo solo devuelve un valor. Si desea ejecutar varias líneas de código en un brazo del `match` debe usar corchetes, y la coma que sigue al brazo es opcional. Por ejemplo, el siguiente código imprimirá “Lucky penny!” cada vez que se llama al método con
un `Coin::Penny` pero aún devuelve el último valor del bloque, `1`:

```rust
# enum Coin {
#     Penny,
#     Nickel,
#     Dime,
#     Quarter,
# }
#
fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => {
            println!("Lucky penny!");
            1
        }
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
#
# fn main() {}
```

### Patrones que se Vinculan a Valores

Otra característica útil de los brazos del *match* es que pueden unirse a las partes
de los valores que coinciden con el patrón. Así es como podemos extraer valores de las variantes *enum*.

Como ejemplo, cambiemos una de nuestras variantes enum para contener datos dentro de ella. Desde 1999 hasta 2008, Estados Unidos acuñó monedas de veinticinco centavos con diferentes diseños en un lado para cada uno de los 50 estados. Ninguna otra moneda tiene diseños estatales, por lo que solo las monedas de veinticinco centavos tienen este valor adicional. Podemos agregar esta información a nuestro `enum` cambiando la variante `Quarter` para incluir un valor `UsState` almacenado dentro de él, lo que hemos hecho aquí en el Listado 6-4.

```rust
#[derive(Debug)] // so we can inspect the state in a minute
enum UsState {
    Alabama,
    Alaska,
    // --snip--
}

enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}
#
# fn main() {}
```

<span class="caption">Listing 6-4: Un enum `Coin` en el cual la variante `Quarter` también tiene un valor `UsState`</span>

Imaginemos que un amigo nuestro está tratando de coleccionar los *Quarters* (*monedas de 25 centavos*) de los 50 estados. Mientras clasificamos nuestro cambio suelto por tipo de moneda, también mencionaremos el nombre del estado asociado con cada *quarter*, de modo que si es uno que nuestro amigo no tiene, pueda agregarlo a su colección.

En la expresión match de este código, agregamos una variable llamada `state` al patrón que coincide con los valores de la variante` Coin::Quarter`. Cuando coincide un `Coin::Quarter`, la variable `state` se vinculará al valor del estado de ese *quarter*. Entonces podemos usar `state` en el código para ese brazo, así:

```rust
# #[derive(Debug)]
# enum UsState {
#     Alabama,
#     Alaska,
#     // --snip--
# }
#
# enum Coin {
#     Penny,
#     Nickel,
#     Dime,
#     Quarter(UsState),
# }
#
fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("State quarter from {:?}!", state);
            25
        }
    }
}
#
# fn main() {
#     value_in_cents(Coin::Quarter(UsState::Alaska));
# }
```

Si tuviéramos que llamar a `value_in_cents(Coin::Quarter(UsState::Alaska))`, `coin` sería `Coin::Quarter(UsState::Alaska)`. Cuando comparamos ese valor con cada uno de los brazos del *match*, ninguno de ellos coincide hasta que llegamos a `Coin::Quarter(state)`. En ese punto, el vínculo para `state` será el valor `UsState::Alaska`. Entonces podemos usar ese vínculo en la expresión `println!`, obteniendo así el valor de estado interno de la variante del *enum*
`Coin` para `Quarter`.

### Coincidencia con `Option<T>`

En la sección anterior, queríamos obtener el valor interno de `T` del caso `Some` al usar `Option<T>`; ¡también podemos manejar`Option<T>` usando `match` como lo hicimos con el enum `Coin`! En lugar de comparar monedas, compararemos las variantes de `Option<T>`, pero la forma en que funciona la expresión `match` sigue siendo la misma.

Digamos que queremos escribir una función que tome una `Opción<i32>` y, si hay un valor dentro, suma 1 a ese valor. Si no hay un valor dentro, la función debe devolver el valor `None` y no intentar realizar ninguna operación.

Esta función es muy fácil de escribir, gracias a `match`, y se verá como la del Listado 6-5.

```rust
# fn main() {
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}

let five = Some(5);
let six = plus_one(five);
let none = plus_one(None);
# }
```

<span class="caption">Listing 6-5: Una función que usa una expresión `match` en un `Option<i32>`</span>

Examinemos la primera ejecución de `plus_one` con más detalle. Cuando llamamos a `plus_one(five)`, la variable `x` en el cuerpo de `plus_one` tendrá el valor `Some(5)`. Luego lo
comparamos con cada brazo del *match*.

```rust,ignore
# fn main() {
#     fn plus_one(x: Option<i32>) -> Option<i32> {
#         match x {
            None => None,
#             Some(i) => Some(i + 1),
#         }
#     }
#
#     let five = Some(5);
#     let six = plus_one(five);
#     let none = plus_one(None);
# }
```

El valor `Some(5)` no coincide con el patrón `None`, por lo que continuamos al siguiente brazo.

```rust,ignore
# fn main() {
#     fn plus_one(x: Option<i32>) -> Option<i32> {
#         match x {
#             None => None,
            Some(i) => Some(i + 1),
#         }
#     }
#
#     let five = Some(5);
#     let six = plus_one(five);
#     let none = plus_one(None);
# }
```

¿`Some(5)` coincide con `Some(i)`? ¡Sí, lo hace! Tenemos la misma variante. El `i` se vincula al valor contenido en `Some`, entonces `i` toma el valor `5`. El código en el brazo del *match* entonces se ejecuta, por lo que sumamos 1 al valor de `i` y creamos un nuevo valor `Some` con nuestro total de `6` dentro.

Ahora consideremos la segunda llamada de `plus_one` en el Listado 6-5, donde `x` es `None`. Ingresamos al `match` y lo comparamos con el primer brazo.

```rust,ignore
# fn main() {
#     fn plus_one(x: Option<i32>) -> Option<i32> {
#         match x {
            None => None,
#             Some(i) => Some(i + 1),
#         }
#     }
#
#     let five = Some(5);
#     let six = plus_one(five);
#     let none = plus_one(None);
# }
```

¡Coincide! No hay ningún valor para sumarle, por lo que el programa se detiene y devuelve el valor `None` en el lado derecho de `=>`. Debido a que el primer brazo coincide, no se comparan otros brazos.

Combinar `match` y *enums* es útil en muchas situaciones. Verá este patrón mucho en el código Rust: haga un `match` con un enum, vincule una variable a los datos internos y luego ejecute el código en función de ello. Es un poco complicado al principio, pero una vez que se acostumbre, deseará tenerlo en todos los lenguajes. Es constantemente un favorito del usuario.

### Los *Matches* son Exhaustivos

Hay otro aspecto de `match` que debemos discutir: los patrones de los brazos deben cubrir todas las posibilidades. Considere esta versión de nuestra función `plus_one` que tiene un error y no compilará:

```rust,ignore
# fn main() {
    fn plus_one(x: Option<i32>) -> Option<i32> {
        match x {
            Some(i) => Some(i + 1),
        }
    }
#
#     let five = Some(5);
#     let six = plus_one(five);
#     let none = plus_one(None);
# }
```

No manejamos el caso `None`, por lo que este código provocará un error. Afortunadamente, es un error que Rust sabe capturar. Si tratamos de compilar este código, obtendremos este error:

```text
$ cargo run
   Compiling enums v0.1.0 (file:///projects/enums)
error[E0004]: non-exhaustive patterns: `None` not covered
   --> src/main.rs:3:15
    |
3   |         match x {
    |               ^ pattern `None` not covered
    |
note: `Option<i32>` defined here
    = note: the matched value is of type `Option<i32>`
help: ensure that all possible cases are being handled by adding a match arm with a wildcard pattern or an explicit pattern as shown
    |
4   ~             Some(i) => Some(i + 1),
5   ~             None => todo!(),
    |

For more information about this error, try `rustc --explain E0004`.
error: could not compile `enums` due to previous error
```

¡Rust sabe que no cubrimos todos los casos posibles e incluso sabe qué patrón olvidamos! Los *Matches* en Rust son *exhaustivos*: debemos agotar hasta la última posibilidad para que el código sea válido. Especialmente en el caso de `Option<T>`, cuando Rust evita que olvidemos manejar explícitamente el caso `None`, nos protege de asumir que tenemos un valor cuando podríamos tener null, lo que hace que el error de mil millones de dólares discutido anteriormente sea imposible.

### Patrones Comodín (*catch-all*) y el Marcador de Posición `_`

Al usar enums, también podemos realizar acciones especiales para algunos valores particulares, pero para todos los demás valores, realizamos una acción predeterminada. Imagine que estamos implementando un juego en el que, si saca un 3 en un lanzamiento de dados, su jugador no se mueve, sino que obtiene un nuevo sombrero elegante. Si saca un 7, su jugador pierde un sombrero elegante. Para todos los demás valores, su jugador mueve esa cantidad de espacios en el tablero de juego. Aquí hay un `match` que implementa esa lógica, con el resultado del lanzamiento de dados codificado en lugar de un valor aleatorio, y toda la otra lógica representada por funciones sin cuerpo porque implementarlas en realidad está fuera del alcance de este ejemplo:

```rust
# fn main() {
    let dice_roll = 9;
    match dice_roll {
        3 => add_fancy_hat(),
        7 => remove_fancy_hat(),
        other => move_player(other),
    }

    fn add_fancy_hat() {}
    fn remove_fancy_hat() {}
    fn move_player(num_spaces: u8) {}
# }
```

Para los dos primeros brazos, los patrones son los valores literales 3 y 7. Para el último brazo que cubre todos los demás valores posibles, el patrón es la variable que hemos elegido nombrar `other`. El código que se ejecuta para el brazo `other` usa la variable pasándola a la función `move_player`.

Este código compila, aunque no hemos enumerado todos los valores posibles que puede tener un `u8`, porque el último patrón coincidirá con todos los valores no enumerados específicamente. Este patrón catch-all (*"comodín"* o *"atrapa todo"*) cumple con el requisito de que `match` debe ser exhaustivo. Tenga en cuenta que tenemos que poner el brazo catch-all en último lugar porque los patrones se evalúan en orden. Si colocamos el brazo catch-all antes, los otros brazos nunca se ejecutarán, por lo que ¡Rust nos avisará si agregamos brazos después de un catch-all!.

Rust también tiene un patrón que podemos usar cuando queremos un catch-all pero no queremos *usar* el valor en el patrón catch-all: `_` es un patrón especial que coincide con cualquier valor y no se vincula a ese valor. Esto le dice a Rust que no vamos a usar el valor, por lo que no nos advertirá sobre una variable no utilizada.

Cambiemos las reglas del juego: ahora, si saca algo que no sea un 3 o un 7, debe tirar de nuevo. Ya no necesitamos usar el valor de catch-all, por lo que podemos cambiar nuestro código para usar `_` en lugar de la variable llamada `other`:

```rust
# fn main() {
    let dice_roll = 9;
    match dice_roll {
        3 => add_fancy_hat(),
        7 => remove_fancy_hat(),
        _ => reroll(),
    }

    fn add_fancy_hat() {}
    fn remove_fancy_hat() {}
    fn reroll() {}
# }
```

Este ejemplo también cumple con el requisito de exhaustividad porque estamos ignorando explícitamente todos los demás valores en el último brazo; no hemos olvidado nada.

Finalmente, cambiaremos las reglas del juego una vez más, para que no suceda nada más en su turno si saca algo que no sea un 3 o un 7. Podemos expresar eso usando el valor *unidad* (el tipo de tupla vacía que mencionamos en la sección [“El Tipo Tupla”](ch03-02-data-types.html#el-tipo-tupla-tuple)) como el código que va con el brazo `_`:

```rust
# fn main() {
    let dice_roll = 9;
    match dice_roll {
        3 => add_fancy_hat(),
        7 => remove_fancy_hat(),
        _ => (),
    }

    fn add_fancy_hat() {}
    fn remove_fancy_hat() {}
# }
```

Aquí, le decimos a Rust explícitamente que no vamos a usar ningún otro valor que no coincida con un patrón en un brazo anterior, y no queremos ejecutar ningún código en este caso.

En el [Capítulo 18][cap8] veremos más sobre patrones y coincidencias. Por ahora, vamos a pasar a la sintaxis `if let`, que puede ser útil en situaciones en las que la expresión `match` es un poco verbosa.

[cap8]: ch18-00-patterns.html