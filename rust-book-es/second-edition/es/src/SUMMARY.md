# El Lenguaje de Programación Rust

[Prefacio](foreword.md)
[Introducción](ch00-00-introduction.md)

## Comenzando

- [Comenzando](ch01-00-getting-started.md)
    - [Instalación](ch01-01-installation.md)
    - [Hola, Mundo!](ch01-02-hello-world.md)
    - [Hola, Cargo!](ch01-03-hello-cargo.md)

- [Programando un Juego de Adivinanzas](ch02-00-guessing-game-tutorial.md)

- [Conceptos Comunes de Programación](ch03-00-common-programming-concepts.md)
    - [Variables y Mutabilidad](ch03-01-variables-and-mutability.md)
    - [Tipos de Datos](ch03-02-data-types.md)
    - [Funciones](ch03-03-how-functions-work.md)
    - [Comentarios](ch03-04-comments.md)
    - [Estructuras de Control](ch03-05-control-flow.md)

- [Comprendiendo la Propiedad (posesión)](ch04-00-understanding-ownership.md)
    - [Qué es la Propiedad?](ch04-01-what-is-ownership.md)
    - [Referencias y Préstamos](ch04-02-references-and-borrowing.md)
    - [Segmentos (Slices)](ch04-03-slices.md)

- [Usando Structs para Estructurar Datos Relacionados](ch05-00-structs.md)
    - [Definiendo e Instanciando Structs](ch05-01-defining-structs.md)
    - [Un Programa Ejemplo Usando Structs](ch05-02-example-structs.md)
    - [Sintáxis del Método](ch05-03-method-syntax.md)

- [Enums y Patrones de Concordancia](ch06-00-enums.md)
    - [Definiendo un Enum](ch06-01-defining-an-enum.md)
    - [El Operador de Estructuras de Control `match`](ch06-02-match.md)
    - [Estructura de Control concisa con `if let`](ch06-03-if-let.md)

## Basic Rust Literacy

- [Módulos](ch07-00-modules.md)
    - [`mod` y el Sistema de Archivos](ch07-01-mod-and-the-filesystem.md)
    - [Controlando la Visibilidad con `pub`](ch07-02-controlling-visibility-with-pub.md)
    - [Rutas para hacer Referencia a un Elemento en el Árbol de Módulos](ch07-03-importing-names-with-use.md)

- [Colecciones Comunes](ch08-00-common-collections.md)
    - [Almacenamiento de listas de valores con Vectores](ch08-01-vectors.md)
    - [Almacenamiento de texto codificado en UTF-8 con Strings](ch08-02-strings.md)
    - [Mapas Hash](ch08-03-hash-maps.md)

- [Manejo de Errores](ch09-00-error-handling.md)
    - [Errores Irrecuperables con `panic!`](ch09-01-unrecoverable-errors-with-panic.md)
    - [Errores Recuperables con `Result`](ch09-02-recoverable-errors-with-result.md)
    - [Con `panic!` o Sin `panic!`](ch09-03-to-panic-or-not-to-panic.md)

- [Tipos genéricos, Traits y Lifetimes](ch10-00-generics.md)
    - [Tipos de Datos Genéricos](ch10-01-syntax.md)
    - [Traits: Definición del Comportamiento Compartido](ch10-02-traits.md)
    - [Validando Referencias con Lifetimes](ch10-03-lifetime-syntax.md)

- [Testing](ch11-00-testing.md)
    - [Escribiendo Tests](ch11-01-writing-tests.md)
    - [Corriendo Tests](ch11-02-running-tests.md)
    - [Organización de los Tests](ch11-03-test-organization.md)

- [Un Proyecto I/O: Creando un Programa de Línea de Comandos](ch12-00-an-io-project.md)
    - [Aceptando Argumentos de Línea de Comandos](ch12-01-accepting-command-line-arguments.md)
    - [Leyendo un Archivo](ch12-02-reading-a-file.md)
    - [Refactorizando para mejorar la Modularidad y el Manejo de Errores](ch12-03-improving-error-handling-and-modularity.md)
    - [Desarrollando la Funcionalidad de la Biblioteca con Desarrollo Basado en Pruebas (Test-driven development (TDD))](ch12-04-testing-the-librarys-functionality.md)
    - [Trabajando con Variables de Entorno](ch12-05-working-with-environment-variables.md)
    - [Escribiendo Mensajes de Error en "Standard Error" en lugar de "Standard Output"](ch12-06-writing-to-stderr-instead-of-stdout.md)

## Pensando en Rust

- [Características del Lenguaje Funcional: Iterators y Closures](ch13-00-functional-features.md)
    - [Closures: Funciones Anónimas que pueden Capturar su Entorno](ch13-01-closures.md)
    - [Procesando una Serie de Elementos con Iteradores](ch13-02-iterators.md)
    - [Mejorando Nuestro Proyecto de I/O](ch13-03-improving-our-io-project.md)
    - [Comparación de Rendimiento: Loops vs. Iterators](ch13-04-performance.md)

- [Más Acerca de Cargo y Crates.io](ch14-00-more-about-cargo.md)
    - [Personalizando Compilaciones con Perfiles de Lanzamiento](ch14-01-release-profiles.md)
    - [Publicando un Crate en Crates.io](ch14-02-publishing-to-crates-io.md)
    - [Espacios de Trabajo de Cargo](ch14-03-cargo-workspaces.md)
    - [Instalando Binarios desde Crates.io con `cargo install`](ch14-04-installing-binaries.md)
    - [Ampliando Cargo con Comandos Personalizados](ch14-05-extending-cargo.md)

- [Punteros Inteligentes](ch15-00-smart-pointers.md)
    - [`Box<T>` apunta a datos en el Heap y tiene un tamaño conocido](ch15-01-box.md)
    - [`Tratamiento de Punteros Inteligentes como Referencias Regulares con el trait Deref`](ch15-02-deref.md)
    - [El `Drop Trait` Ejecuta el Código durante la Limpieza](ch15-03-drop.md)
    - [`Rc<T>`, Puntero Inteligente con Conteo de Referencias](ch15-04-rc.md)
    - [`RefCell<T>` y el Patrón de Mutabilidad Interior](ch15-05-interior-mutability.md)
    - [Creación de Ciclos de Referencia y la Seguridad en la Fuga de Memoria](ch15-06-reference-cycles.md)

- [Concurrencia sin Miedo](ch16-00-concurrency.md)
    - [Hilos (Threads)](ch16-01-threads.md)
    - [Paso de Mensajes](ch16-02-message-passing.md)
    - [Estado Compartido](ch16-03-shared-state.md)
    - [Concurrencia Extensible: `Sync` and `Send`](ch16-04-extensible-concurrency-sync-and-send.md)

- [Características de Programación Orientada a Objetos de Rust](ch17-00-oop.md)
    - [Características de los Lenguajes Orientados a Objetos](ch17-01-what-is-oo.md)
    - [Uso de Objetos `Trait` que permiten Valores de Diferentes Tipos](ch17-02-trait-objects.md)
    - [Implementación de un Patrón de Diseño Orientado a Objetos](ch17-03-oo-design-patterns.md)

## Temas Avanzados

- [Patrones Coinciden con la Estructura de Valores](ch18-00-patterns.md)
    - [Todos los patrones de lugares pueden ser utilizados](ch18-01-all-the-places-for-patterns.md)
    - [Refutabilidad: si un Patrón podría No Coincidir](ch18-02-refutability.md)
    - [Toda la Sintaxis de Patrones](ch18-03-pattern-syntax.md)

- [Características Avanzadas](ch19-00-advanced-features.md)
    - [Rust en Modo Inseguro](ch19-01-unsafe-rust.md)
    - [Traits Avanzados](ch19-02-advanced-traits.md)
    - [Tipos Avanzados](ch19-03-advanced-types.md)
    - [Funciones Avanzadas y Closures](ch19-04-advanced-functions-and-closures.md)
    - [Macros](ch19-05-macros.md)

- [Proyecto Final: Construcción de un Servidor web Multiprocesos](ch20-00-final-project-a-web-server.md)
    - [Un Servidor Web de un solo Proceso](ch20-01-single-threaded.md)
    - [Transformando nuestro Servidor de un solo Proceso a un Servidor Multiprocesos](ch20-02-multithreaded.md)
    - [Apagado y Limpieza Elegantes](ch20-03-graceful-shutdown-and-cleanup.md)

- [Apéndice](appendix-00.md)
    - [A - Palabras Claves](appendix-01-keywords.md)
    - [B - Operadores y Símbolos](appendix-02-operators.md)
    - [C - Traits Derivables](appendix-03-derivable-traits.md)
    - [D - Herramientas Útiles de Desarrollo](appendix-04-useful-development-tools.md)
    - [E - Ediciones](appendix-05-editions.md)
    - [F - Traducciones](appendix-06-translation.md)
    - [G - Cómo se hace Rust y “Nightly Rust”](appendix-07-nightly-rust.md)
    - [H - Lifetimes Avanzados](appendix-08-advanced-lifetimes.md)
    - [I - Características más Recientes (previous version)](appendix-09-newest-features.md)
