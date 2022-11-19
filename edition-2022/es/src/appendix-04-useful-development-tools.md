## Apéndice D - Herramientas Útiles de Desarrollo

En este apéndice, hablamos de algunas herramientas de desarrollo útiles que el proyecto Rust proporciona. Veremos el formato automático, formas rápidas de aplicar correcciones de  advertencia, un linter e integración con IDEs. 

### Formateo Automático con *rustfmt*

La herramienta `rustfmt` reformatea su código de acuerdo con el estilo de código de la comunidad. Muchos proyectos colaborativos usan `rustfmt` para evitar discusiones sobre qué estilo usar al escribir Rust: todos formatean su código usando la herramienta.

Para instalar `rustfmt` ingrese lo siguiente:

```text
$ rustup component add rustfmt
```

Este comando le proporciona `rustfmt` y `cargo-fmt`, similar a como Rust te da tanto `rustc` como `cargo`. Para dar formato a cualquier proyecto Cargo, ingrese lo siguiente:

```text
$ cargo fmt
```

Ejecutar este comando reformatea todo el código de Rust en el *crate* actual. Esto solo debería cambiar el estilo del código, no la semántica del código. Para obtener más información sobre `rustfmt`, consulte [su documentación](https://github.com/rust-lang/rustfmt).

### Corrija tu código con rustfix

La herramienta *rustfix* se incluye con las instalaciones de Rust y puede corregir automáticamente las advertencias del compilador que tienen una forma clara de corregir el problema que probablemente sea lo que desea. Es probable que haya visto advertencias del compilador antes. Por ejemplo, considere este código:

<span class="filename">Filename: src/main.rs</span>

```rust
fn do_something() {}

fn main() {
    for i in 0..100 {
        do_something();
    }
}
```

Aquí llamamos a la función `do_something` 100 veces, pero nunca usamos la variable `i` en el cuerpo del bucle `for`. Rust nos advierte sobre eso:

```text
$ cargo build
   Compiling myprogram v0.1.0 (file:///projects/myprogram)
warning: unused variable: `i`
 --> src/main.rs:4:9
  |
4 |     for i in 0..100 {
  |         ^ help: consider using `_i` instead
  |
  = note: #[warn(unused_variables)] on by default

    Finished dev [unoptimized + debuginfo] target(s) in 0.50s
```

La advertencia sugiere que usemos `_i` en su lugar: el guión bajo indica que tenemos la intención de que esta variable no se use. Podemos aplicar automáticamente esa sugerencia usando la herramienta `rustfix` ejecutando el comando `cargo fix`:

```text
$ cargo fix
    Checking myprogram v0.1.0 (file:///projects/myprogram)
      Fixing src/main.rs (1 fix)
    Finished dev [unoptimized + debuginfo] target(s) in 0.59s
```

Cuando volvamos a mirar *src/main.rs*, veremos que `cargo fix` ha cambiado el código:

<span class="filename">Filename: src/main.rs</span>

```rust
fn do_something() {}

fn main() {
    for _i in 0..100 {
        do_something();
    }
}
```

La variable de bucle `for` ahora se llama `_i` y ya no aparece la advertencia.

También puede usar el comando `cargo fix` para hacer la transición de su código entre diferentes ediciones de Rust. Las ediciones están cubiertas en el Apéndice E.




### Más Lints con Clippy

La herramienta Clippy es una colección de lints para analizar su código, poder detectar errores comunes y mejorar su código Rust.

Para instalar Clippy, ingrese lo siguiente:

```text
$ rustup component add clippy
```

Para ejecutar los lints de Clippy en cualquier proyecto de Cargo, ingrese lo siguiente:

```text
$ cargo clippy
```

Por ejemplo, supongamos que escribe un programa que usa una aproximación de una constante matemática, como *pi*, como lo hace este programa:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let x = 3.1415;
    let r = 8.0;
    println!("the area of the circle is {}", x * r * r);
}
```

Ejecutar `cargo clippy` en este proyecto da como resultado este error:

```text
error: approximate value of `f{32, 64}::consts::PI` found. Consider using it directly
 --> src/main.rs:2:13
  |
2 |     let x = 3.1415;
  |             ^^^^^^
  |
  = note: #[deny(clippy::approx_constant)] on by default
  = help: for further information visit https://rust-lang-nursery.github.io/rust-clippy/master/index.html#approx_constant
```

Este error le permite saber que Rust ya tiene definida una constante `PI` más precisa, y que su programa sería más correcto si usara la constante en su lugar. Usted cambiaría entonces su código para usar la constante `PI`. El siguiente código no genera ningún error o advertencia de Clippy:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let x = std::f64::consts::PI;
    let r = 8.0;
    println!("the area of the circle is {}", x * r * r);
}
```

Para más información sobre Clippy, vea [esta documentación](https://github.com/rust-lang/rust-clippy).


### Integración IDE usando *rust-analyzer*

Para ayudar a la integración de IDE, la comunidad de Rust recomienda usar [`rust-analyzer`](https://rust-analyzer.github.io/). Esta herramienta es un conjunto de utilidades centradas en compiladores que habla el [Protocolo del Servidor de Idiomas](https://langserver.org/), que es una especificación para que los IDEs y los lenguajes de programación se comuniquen entre sí. Diferentes clientes pueden usar `rust-analyzer`, como el [complemento Rust Analyzer para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust-analyzer).

Visite la [página de inicio](https://rust-analyzer.github.io/) del proyecto `rust-analyzer` para obtener instrucciones de instalación, luego instale el soporte del servidor de idiomas en su IDE particular. Su IDE obtendrá capacidades como autocompletado, salto a definición y errores en línea de código.