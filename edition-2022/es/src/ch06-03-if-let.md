## Estructura de Control Concisa con `if let`

La sintaxis `if let` le permite combinar `if` y `let` en una forma menos verbosa de manejar valores que coinciden con un patrón mientras ignora el resto. Considere el programa en el Listado 6-6 que coincide con un valor `Option<u8>` en la variable `config_max` pero solo quiere ejecutar código si el valor es la variante `Some`.

```rust
# fn main() {
    let config_max = Some(3u8);
    match config_max {
        Some(max) => println!("The maximum is configured to be {}", max),
        _ => (),
    }
# }
```

<span class="caption">Listing 6-6: Un `match` que solo se preocupa por ejecutar el
código cuando el valor es `Some`</span>

Si el valor es `Some`, imprimimos el valor en la variante `Some` vinculando el valor a la variable `max` en el patrón. No queremos hacer nada con el valor `None`. Para satisfacer la expresión `match`, tenemos que agregar `_ => ()` después de procesar solo una variante, que es un código repetitivo molesto para añadir.

En cambio, podríamos escribir esto de una manera más corta usando `if let`. El siguiente código se comporta igual que el `match` en el Listado 6-6:

```rust
# fn main() {
    let config_max = Some(3u8);
    if let Some(max) = config_max {
        println!("The maximum is configured to be {}", max);
    }
# }
```

La sintaxis `if let` toma un patrón y una expresión separados por un signo igual. Funciona de la misma manera que un `match`, donde la expresión se asigna al `match` y el patrón es su primer brazo. En este caso, el patrón es `Some(max)`, y `max` se vincula al valor dentro de `Some`. Luego podemos usar `max` en el cuerpo del bloque `if let` de la misma manera que usamos `max` en el correspondiente brazo `match`. El código en el bloque `if let` no se ejecuta si el valor no coincide con el patrón.

Usar `if let` significa menos tipeo, menos indentación y menos código repetitivo. Sin embargo, pierde la verificación exhaustiva que impone `match`. Elegir entre `match` y `if let` depende de lo que esté haciendo en su situación particular y de si ganar concisión es una compensación adecuada por perder la verificación exhaustiva.

En otras palabras, puede pensar en `if let` como *azúcar sintáctico* para un `match`, que ejecuta código cuando el valor coincide con un patrón y luego ignora todos los demás valores.

Podemos incluir un `else` con un `if let`. El bloque de código que acompaña al `else` es el mismo que el bloque de código que iría con el caso `_` en la expresión `match` que es equivalente a `if let` y `else`. Recuerde la definición del enum `Coin` en el Listado 6-4, donde la variante `Quarter` también contenía un valor `UsState`. Si quisiéramos contar todas las monedas que no son de 25 centavos (*quarters*) y al mismo tiempo anunciar el estado de los *quarters*, podríamos hacerlo con una expresión `match` como esta:

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
#  fn main() {
    let coin = Coin::Penny;
    let mut count = 0;
    match coin {
        Coin::Quarter(state) => println!("State quarter from {:?}!", state),
        _ => count += 1,
    }
# }
```

O podríamos usar una expresión `if let` y `else` como esta:

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
# fn main() {
#     let coin = Coin::Penny;
    let mut count = 0;
    if let Coin::Quarter(state) = coin {
        println!("State quarter from {:?}!", state);
    } else {
        count += 1;
    }
# }
```

Si tiene una situación en la que su programa tiene una lógica que es demasiado verbosa para expresarla usando un `match`, recuerde que`if let` también está en su caja de herramientas de Rust.

## Resumen

Ahora hemos cubierto cómo usar enums para crear tipos personalizados que pueden ser uno de un conjunto de valores enumerados. Mostramos cómo el tipo `Opción<T>` de la biblioteca estándar le ayuda a usar el sistema de tipos para evitar errores. Cuando los valores *enum* tienen datos dentro de ellos, puede usar `match` o `if let` para extraer y usar esos valores, dependiendo de la cantidad de casos que necesite manejar.

Sus programas en Rust ahora pueden expresar conceptos en su dominio usando *structs*
y *enums*. La creación de tipos personalizados para usar en su API garantiza
la seguridad de tipos: el compilador se asegurará de que sus funciones obtengan solo
valores del tipo que cada función espera.

Con el fin de proporcionar a sus usuarios una API bien organizada que sea fácil de usar y solo exponga exactamente lo que necesitarán, pasemos ahora a los módulos de Rust.
