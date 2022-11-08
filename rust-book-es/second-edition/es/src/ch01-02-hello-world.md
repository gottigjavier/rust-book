## Hola, Mundo!

Ahora que ha instalado Rust, vamos a escribir su primer programa Rust. Es
tradicional cuando se aprende un nuevo lenguaje escribir un pequeño programa
que imprime el texto `Hello, world!` en la pantalla, ¡así que haremos lo
mismo aquí!

> Nota: Este libro asume familiaridad básica con la línea de comando. Rust no
> exige nada específico acerca de su edición o herramienta, ni dónde vive su
> código, por lo que si prefiere utilizar un entorno de desarrollo integrado
> (IDE) en lugar de la línea de comandos, puede usar su IDE favorito. Muchos
> IDEs ahora tienen cierto grado de compatibilidad con Rust; revise la
> documentación del IDE para más detalles. Recientemente, el equipo de Rust se
> ha centrado en permitir un excelente soporte de IDE a través de, por ejemplo, `rust-analyzer`,
> ¡y se ha avanzado rápidamente en ese frente!

### Crear un directorio de proyectos

Comenzará haciendo un directorio para almacenar su código Rust. A Rust no le
importa dónde vive su código, pero para los ejercicios y proyectos de este
libro, le sugerimos que cree un directorio *projects* en su directorio
personal y que mantenga todos sus proyectos allí.

Abra un terminal e ingrese los siguientes comandos para crear un directorio
*projects* y un directorio para el proyecto *Hello, world!* dentro del
directorio *projects*.

Para Linux y macOS, ingrese esto:

```text
$ mkdir ~/projects
$ cd ~/projects
$ mkdir hello_world
$ cd hello_world
```

Para Windows CMD, ingrese esto:

```cmd
> mkdir "%USERPROFILE%\projects"
> cd /d "%USERPROFILE%\projects"
> mkdir hello_world
> cd hello_world
```

Para Windows PowerShell, ingrese esto:

```powershell
> mkdir $env:USERPROFILE\projects
> cd $env:USERPROFILE\projects
> mkdir hello_world
> cd hello_world
```

### Escribir y ejecutar un programa en Rust

A continuación, cree un nuevo archivo fuente y llámelo *main.rs*. Los
archivos Rust siempre terminan con la extensión *.rs*. Si usa más de una
palabra en su nombre de archivo, use un guión bajo para separarlas. Por
ejemplo, use *hello_world.rs* en lugar de *helloworld.rs*.

Ahora abra el archivo *main.rs* que acaba de crear e ingrese el código en el
Listado 1-1.

<span class="filename">Filename: main.rs</span>

```rust
fn main() {
    println!("Hello, world!");
}
```

<span class="caption">Listado 1-1: Un programa que imprime
`Hello, world!`</span>

Guarde el archivo y regrese a la ventana de su terminal. En Linux o macOS,
ingrese los siguientes comandos para compilar y ejecutar el archivo:

```text
$ rustc main.rs
$ ./main
Hello, world!
```

En Windows, ingrese el comando `.\main.exe` en lugar de `./main`:

```powershell
> rustc main.rs
> .\main.exe
Hello, world!
```

Independientemente de su sistema operativo, la cadena `Hello, world!` debe
imprimirse en la terminal. Si no ve esta salida, consulte la sección
[“Solución de problemas”](ch01-01-installation.html#soluci%C3%B3n-de-problemas) para obtener ayuda.

Si `Hello, world!` se imprimió, ¡felicitaciones! Usted ha escrito
oficialmente un programa de Rust. Eso le convierte en un programador de Rust,
¡bienvenido!

### Anatomía de un programa de Rust

Repasemos en detalle lo que acaba de suceder en su programa *Hello, world!*.
Aquí está la primera pieza del rompecabezas:

```rust
fn main() {

}
```

Estas líneas definen una función llamada `main`. La función `main` es especial:
siempre es el primer código que se ejecuta en cada programa ejecutable de
Rust. La primera línea declara una función llamada `main` que no tiene
parámetros y no devuelve nada. Si hubiera parámetros, entrarían entre
paréntesis, `()`.

El cuerpo de la función está envuelto en llaves, `{}`. Rust requiere llaves alrededor de todos los cuerpos de funciones. Es un buen estilo colocar la llave de apertura en la misma línea que la
declaración de función, agregando un espacio intermedio.

> Nota: si desea apegarse a un estilo estándar en todos los proyectos de Rust, puede usar una herramienta de formateo automático llamada `rustfmt` para formatear su código en un estilo particular (más información sobre `rustfmt` en el [Apéndice D](appendix-04-useful-development-tools.html)). El equipo de Rust ha incluido esta herramienta con la distribución estándar de Rust, como rustc, ¡así que ya debería estar instalada en su computadora!   

Dentro de la función `main` está el siguiente código:

```rust
# #![allow(unused)]
# fn main() {
    println!("Hello, world!");
# }
```

Esta línea hace todo el trabajo en este pequeño programa: imprime texto en la pantalla. Hay cuatro detalles importantes para notar aquí.

Primero, el estilo Rust es indentar con cuatro espacios, no con una *tabulación*.

Segundo, `println!` llama a una macro de Rust. Si llamara a una función en su lugar, se ingresaría como `println` (sin el `!`). Analizaremos las macros de Rust con más detalle en el Capítulo 19. Por ahora, solo necesita saber que usar `!` significa que está llamando a una macro en lugar de a una función normal, y que las macros a veces no siguen algunas reglas de las funciones.

Tercero,  el *string* `"Hello, world!"`. Pasamos este *string* como un argumento a `println!`, Y el *string* se imprime en la pantalla.

Cuarto, terminamos la línea con un punto y coma (`;`), lo que indica que esta expresión ha terminado y la siguiente está lista para comenzar. La mayoría de las líneas de código Rust terminan con un punto y coma.

### Compilar y Ejecutar son *Pasos Separados*

Acaba de ejecutar un programa recién creado, así que examinemos cada paso del proceso.

Antes de ejecutar un programa Rust, debe compilarlo usando el compilador Rust ingresando el comando `rustc` y pasándole el nombre de su archivo fuente, de esta manera:

```text
$ rustc main.rs
```

Si tiene experiencia en C o C++, notará que esto es similar a `gcc` o `clang`. Después de compilar con éxito, Rust genera un ejecutable binario.

En Linux, macOS y PowerShell en Windows, puede ver el ejecutable ingresando el comando `ls` en su shell. En Linux y macOS, verá dos archivos. Con PowerShell en Windows, verá los mismos tres archivos que vería con CMD.

```text
$ ls
main  main.rs
```

Con CMD en Windows, debe ingresar lo siguiente:

```cmd
> dir /B %= the /B option says to only show the file names =%
main.exe
main.pdb
main.rs
```

Esto muestra el archivo de código fuente con la extensión *.rs*, el archivo ejecutable (*main.exe* en Windows, pero *main* en todas las demás plataformas y, cuando se usa CMD, un archivo que contiene información de depuración con extensión *.pdb*. Desde aquí, ejecute el archivo *main* o *main.exe*, como este:

```text
$ ./main   # .\main.exe en Windows
```

Si *main.rs* fue su programa "Hello, world!", esta línea imprimiría `Hello, world!` en su terminal.

Si está más familiarizado con un lenguaje dinámico, como Ruby, Python o JavaScript, es posible que no esté acostumbrado a compilar y ejecutar un programa como pasos separados. Rust es un lenguaje *compilado con anticipación*, lo que significa que puede compilar un programa y darle el ejecutable a otras personas, y ellas pueden ejecutarlo incluso sin tener instalado Rust. Si le da a alguien un archivo *.rb*, *.py* o *.js*, debe tener instalada una implementación de Ruby, Python o JavaScript (respectivamente). Pero en esos lenguajes, solo necesita un comando para compilar y ejecutar su programa. Todo es una compensación en el diseño del lenguaje.

Compilar con `rustc` está bien para programas simples, pero a medida que crece su proyecto, querrá administrar todas las opciones y facilitar el compartir su código. A continuación, le presentaremos la herramienta *Cargo*, que le ayudará a escribir programas Rust del mundo real.