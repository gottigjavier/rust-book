## Packages y Crates

Las primeras partes del *module system* (sistema de módulos) que cubriremos son packages y crates.

Un *crate* es la cantidad más pequeña de código que el compilador de Rust considera a la vez. Incluso si ejecuta `rustc` en lugar de `cargo` y pasa un solo archivo de código fuente (como hicimos en la sección "Escribir y Ejecutar un Programa Rust" del Capítulo 1), el compilador considera que ese archivo es un crate. Los crates pueden contener módulos, y los módulos se pueden definir en otros archivos, que son compilados con el crate, como veremos en las próximas secciones.

Un crate puede presentarse en una de dos formas: un *crate binario* o un *crate librería*. Los *crates binarios* son programas que se puede compilar a un ejecutable que se puede ejecutar, como un programa de línea de comandos o un servidor. Cada uno debe tener una función llamada `main` que defina lo que sucede cuando se ejecuta el ejecutable. Todos los crates que hemos creado hasta ahora han sido crates binarios.

Los *crates librerías* no tienen una función `main` y no se compilan a un ejecutable. En su lugar, definen la funcionalidad destinada a ser compartida con múltiples proyectos. Por ejemplo, el crate `rand` que usamos en el [Capítulo 2](ch02-00-guessing-game-tutorial.html#generando-un-n%C3%BAmero-aleatorio) proporciona una funcionalidad que genera números aleatorios. La mayoría de las veces, cuando los Rustáceos dicen "crate", se refieren a crate librería, y usan "crate" de manera intercambiable con el concepto general de programación "librería".

El *crate raíz* es un archivo fuente desde el cual el compilador de Rust comienza y constituye el módulo raíz de un crate (explicaremos los módulos en profundidad en la sección ["Definiendo Módulos para Controlar el Scope y la Privacidad"](ch07-02-defining-modules-to-control-scope-and-privacy.html#definiendo-m%C3%B3dulos-para-controlar-el-scope-y-la-privacidad)).

Un *package* es un paquete de uno o más crates que proporciona un conjunto de funcionalidades. Un package contiene un archivo *Cargo.toml* que describe cómo construir esos crates. Cargo es en realidad un package que contiene el crate binario de la *herramienta de línea de comandos* que ha estado usando para construir su código. El package Cargo también contiene un crate librería del que depende el crate binario. Otros proyectos pueden depender del crate librería de Cargo para usar la misma lógica que usa la *herramienta de línea de comandos* de Cargo.

Un package puede contener tantos crates binarios como desee, pero como máximo solo un crate librería. Un package debe contener al menos un crate, ya sea un crate librería o binario.

Veamos qué sucede cuando creamos un package. Primero, ingresamos el comando `cargo new`:

```text
$ cargo new my-project
     Created binary (application) `my-project` package
$ ls my-project
Cargo.toml
src
$ ls my-project/src
main.rs
```

Después de ejecutar `cargo new`, usamos `ls` para ver qué crea Cargo. En el directorio del proyecto hay un archivo *Cargo.toml*, que nos da un package. También hay un directorio *src* que contiene *main.rs*. Abra *Cargo.toml* en su editor de texto y note que no se menciona *src/main.rs*. Cargo sigue una convención de que *src/main.rs* es *crate raíz* de un *crate binario* con el mismo nombre que el package. Del mismo modo, Cargo sabe que si el directorio del package contiene *src/lib.rs*, el package contiene un crate librería con el mismo nombre que el package, y *src/lib.rs* es su crate raíz. Cargo pasa los archivos del crate raíz a `rustc` para construir la librería o el binario.

Aquí, tenemos un package que solo contiene *src/main.rs*, lo que significa que solo contiene un crate binario llamado `my-project`. Si un package contiene *src/main.rs* y *src/lib.rs*, tiene dos crates: uno binario y uno librería, ambos con el mismo nombre que el package. Un package puede tener varios crates binarios colocando archivos en el directorio *src/bin*: cada archivo será un crate binario separado.