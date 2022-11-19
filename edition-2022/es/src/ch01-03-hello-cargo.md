## Hola, Cargo!

Cargo es el sistema de compilación y gestor de paquetes de Rust. La mayoría de los Rustáceos utilizan esta herramienta para administrar sus proyectos Rust porque Cargo maneja muchas tareas por usted, como construir su código, descargar las bibliotecas de las que depende su código y construir esas bibliotecas. (A las bibliotecas que su código necesita las llamamos *dependencias*).

Los programas más simples de Rust, como el que hemos escrito hasta ahora, no tienen ninguna dependencia. Entonces, si hubiéramos construido el proyecto *Hello, world!* con Cargo, solo usaría la parte de Cargo que maneja la construcción de su código. Al escribir programas de Rust más complejos, agregará dependencias, y si inicia un proyecto utilizando Cargo, agregar dependencias será mucho más fácil de hacer.

Debido a que la gran mayoría de los proyectos de Rust usan Cargo, el resto de este libro asume que también está utilizando Cargo. Cargo viene instalado con Rust si utilizó los instaladores oficiales discutidos en la sección [“Instalación”](ch01-01-installation.html#instalaci%C3%B3n). Si instaló Rust por otros medios, verifique si Cargo se instaló ingresando lo siguiente en su terminal:

```text
$ cargo --version
```

Si ve un número de versión, ¡ya lo tiene!. Si ve un error, como
`command not found`, consulte la documentación de su método de instalación
para determinar cómo instalar Cargo por separado.

### Creando un Proyecto con Cargo

Vamos a crear un nuevo proyecto utilizando Cargo y veamos cómo difiere de
nuestro proyecto original *Hello, world!*. Vuelva a su directorio
*projects* (o donde haya decidido almacenar su código). Luego, en cualquier
sistema operativo, ejecute lo siguiente:

```bash
$ cargo new hello_cargo --bin
$ cd hello_cargo
```

El primer comando crea un nuevo ejecutable binario llamado *hello_cargo*. El
argumento `--bin` pasado a `cargo new` crea una aplicación ejecutable (a
menudo simplemente llamada *binary*) en lugar de una biblioteca (`--lib`). Si omite el indicador, cargo asumirá por defecto el indicador `--bin`.
Hemos nombrado nuestro proyecto *hello_cargo*, y Cargo crea sus archivos en un
directorio del mismo nombre.

Vaya al directorio *hello_cargo* y liste los archivos. Verá que Cargo ha generado dos archivos y un directorio para nosotros: un archivo *Cargo.toml* y un directorio *src* con un archivo *main.rs* dentro. También ha inicializado un nuevo repositorio de Git junto con un archivo *.gitignore*. Los archivos Git no se generarán si ejecuta `cargo new` dentro de un repositorio Git existente; puede sobreescribir este comportamiento usando `cargo new --vcs=git`.

> Nota: Git es un sistema de control de versiones común. Puede cambiar
> `cargo new` para usar un sistema de control de versiones diferente o ningún
> sistema de control de versiones usando el indicador `--vcs`. Ejecute
> `cargo new --help` para ver las opciones disponibles.

Abra *Cargo.toml* en su editor de texto de su elección. Debería verse simila
al código en el Listado 1-2.

<span class="filename">Filename: Cargo.toml</span>

```toml
[package]
name = "hello_cargo"
version = "0.1.0"
edition = "2021"

# Vea más claves y sus definiciones en https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
```

<span class="caption">Listado 1-2: Contenido de *Cargo.toml* generado por
`cargo new`</span>

Este archivo está en formato [*TOML*][toml] <!-- ignore --> (*Tom's Obvious, Minimal Language*), que es el formato de configuración de Cargo.

[toml]: https://github.com/toml-lang/toml

La primera línea, `[package]`, es un encabezado de sección que indica que las siguientes declaraciones están configurando un paquete. A medida que agreguemos más información a este archivo, agregaremos otras secciones.

Las siguientes tres líneas establecen la información de configuración que Cargo necesita para compilar su programa: el nombre, la versión y la edición de Rust usada. Hablaremos de la clave `edición` en el [Apéndice E](appendix-05-editions.html).

La última línea, `[dependencies]`, es el comienzo de una sección para que usted liste cualquiera de las dependencias de su proyecto. En Rust, los paquetes de código se conocen como *crates*. No necesitaremos otros *crates* para este proyecto, pero lo haremos en el primer proyecto en el Capítulo 2, entonces usaremos esta sección de dependencias.

Ahora abra *src/main.rs* y eche un vistazo:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    println!("Hello, world!");
}
```

Cargo ha generado un programa *Hello, world!* para usted, ¡como el que escribimos en el Listado 1-1! Hasta ahora, las diferencias entre nuestro proyecto anterior y el proyecto que Cargo genera son que Cargo colocó el código en el directorio *src*, y tenemos un archivo de configuración
*Cargo.toml* en el directorio superior.

Cargo espera que sus archivos fuente vivan dentro del directorio *src*. El directorio del proyecto de nivel superior es solo para archivos README, información de licencia, archivos de configuración y cualquier otra cosa no relacionada con su código. Usar Cargo te ayuda a organizar tus proyectos. Hay un lugar para todo, y todo está en su lugar.

Si comenzó un proyecto que no usa Cargo, como lo hicimos con el proyecto *Hello, world!*, puede convertirlo a un proyecto que sí utiliza Cargo. Mueva el código del proyecto al directorio *src* y cree un archivo *Cargo.toml* apropiado.

### Construir y Ejecutar un Proyecto con Cargo

Ahora veamos qué es diferente cuando construimos y ejecutamos el programa *Hello, world!* con Cargo. Desde su directorio *hello_cargo*, construya su proyecto ingresando el siguiente comando:

```text
$ cargo build
   Compiling hello_cargo v0.1.0 (file:///projects/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 2.85 secs
```

Este comando crea un archivo ejecutable en *target/debug/hello_cargo* (o *target\debug\hello_cargo.exe* en Windows) en lugar de en su directorio actual. Debido a que la compilación predeterminada es una compilación de depuración, Cargo coloca el binario en un directorio llamado *debug*. Puede ejecutar el ejecutable con este comando:

```toml
$ ./target/debug/hello_cargo # .\target\debug\hello_cargo.exe en Windows
Hello, world!
```

Si todo va bien, `Hello, world!` debería imprimirse en la terminal. Ejecutar `cargo build` por primera vez también hace que Cargo cree un nuevo archivo en el nivel superior: *Cargo.lock*. Este archivo realiza un seguimiento de las versiones exactas de las dependencias en su proyecto. Este proyecto no tiene dependencias, por lo que el archivo es un poco escaso. Nunca necesitará
cambiar este archivo manualmente; Cargo maneja sus contenidos por usted.

Acabamos de crear un proyecto con `cargo build` y lo ejecutamos con `./target/debug/hello_cargo`, pero también podemos usar `cargo run` para compilar el código y luego ejecutar el ejecutable resultante en un solo comando:

```toml
$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/hello_cargo`
Hello, world!
```

Usar `cargo run` es más conveniente que tener que recordar ejecutar `cargo build` y luego usar la ruta completa al binario, por lo que la mayoría de los desarrolladores usan `cargo run`.

Tenga en cuenta que esta vez no vimos la salida que indica que Cargo estaba compilando `hello_cargo`. Cargo descubrió que los archivos no habían cambiado por lo que simplemente ejecutó el binario. Si hubiera modificado su código fuente, Cargo habría reconstruido el proyecto antes de ejecutarlo, y usted habría visto esta salida:

```toml
$ cargo run
   Compiling hello_cargo v0.1.0 (file:///projects/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 0.33 secs
     Running `target/debug/hello_cargo`
Hello, world!
```

Cargo también proporciona un comando llamado `cargo check`. Este comando verifica rápidamente su código para asegurarse de que compila pero no produce un ejecutable:

```toml
$ cargo check
   Compiling hello_cargo v0.1.0 (file:///projects/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 0.32 secs
```

¿Por qué no querría un ejecutable?. A menudo, `cargo check` es mucho más rápido que `cargo build` porque se salta el paso de producir un ejecutable. Si continuamente revisa su trabajo mientras escribe el código, ¡usar `cargo check` acelerará el proceso de informarle si su proyecto aún se está compilando! Como tal, muchos Rustáceos llevan a cabo un `cargo check` periódicamente mientras escriben su programa para asegurarse de que compila. Luego ejecutan `cargo build` cuando están listos para usar el ejecutable.

Repasemos lo que hemos aprendido hasta ahora sobre Cargo:

* Podemos crear un proyecto usando `cargo new`.
* Podemos construir un proyecto usando `cargo build`.
* Podemos construir y ejecutar un proyecto en un solo paso usando `cargo run`.
* Podemos construir un proyecto para chequear errores sin crear un binario usando `cargo check`.
* En lugar de guardar el resultado de la construcción en el mismo directorio
 que nuestro código, Cargo lo almacena en el directorio *target/debug*.

Una ventaja adicional de usar Cargo es que los comandos son los mismos sin importar en qué sistema operativo esté trabajando. Entonces, en este punto, ya no brindaremos instrucciones específicas para Linux y macOS en comparación con Windows.

### Construyendo para Lanzamiento (Producción)

Cuando su proyecto finalmente esté listo para su lanzamiento, puede usar `cargo build --release` para compilarlo con optimizaciones. Este comando creará un ejecutable en *target/release* en lugar de *target/debug*. Las optimizaciones hacen que su código Rust se ejecute más rápido, pero al activarlas, se alarga el tiempo que tarda su programa en compilarse. Esta es la razón por la cual hay dos perfiles diferentes: uno para el desarrollo, cuando desea recompilar rápidamente y con frecuencia, y otro para compilar el programa final que le dará a un usuario, que no se recmpilará repetidamente y que se ejecutará tan rápido como posible. Si está evaluando el tiempo de ejecución de su código, asegúrese de ejecutar `carga build --release` y *marcar la referencia* con el ejecutable en *target/release*.

### Cargo como Convención

Con proyectos simples, Cargo no le da mucho valor a simplemente usar `rustc`, pero demostrará su valor a medida que sus programas se vuelven más intrincados. Con proyectos complejos compuestos de múltiples *crates*, es mucho más fácil dejar que Cargo coordine la compilación.

Aunque el proyecto `hello_cargo` es simple, ahora usa muchas de las herramientas reales que usará en el resto de su carrera en Rust. De hecho, para trabajar en cualquier proyecto existente, puede usar los siguientes comandos para verificar el código usando Git, cambiar al directorio de ese proyecto y compilar:

```bash
$ git clone someurl.com/someproject
$ cd someproject
$ cargo build
```

Para obtener más información sobre Cargo, consulte [su documentación].

[su documentación]: https://doc.rust-lang.org/cargo/

## Resumen

¡Ya ha tenido un gran comienzo en su viaje a Rust! En este capítulo, ha aprendido a:

* Instalar la última versión estable de Rust usando `rustup`
* Actualizar a una versión más reciente de Rust
* Abrir documentación instalada localmente
* Escribir y ejecutar un programa *Hello, world!* que usa `rustc` directamente
* Crear y ejecutar un nuevo proyecto utilizando las convenciones de Cargo

Este es un buen momento para crear un programa más sustancial para acostumbrarse a leer y escribir el código Rust. Entonces, en el Capítulo 2, construiremos un programa de adivinanzas. Si prefiere comenzar aprendiendo cómo funcionan los conceptos de programación común en Rust, consulte el Capítulo 3 y luego regrese al Capítulo 2.
