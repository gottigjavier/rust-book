## Definiendo Módulos para Controlar el Scope y la Privacidad

En esta sección, hablaremos sobre módulos y otras partes del module system, es decir, rutas que le permiten nombrar elementos; la palabra clave `use` que trae una ruta al scope; y la palabra clave `pub` para hacer públicos los elementos. También discutiremos la palabra clave `as`, los paquetes externos y el operador global.

Primero, vamos a comenzar con una lista de reglas para una fácil referencia cuando esté organizando su código en el futuro. A continuación, explicaremos en detalle cada una de las reglas.

### Hoja de Referencia de Módulos

Aquí proporcionamos una referencia rápida sobre cómo funcionan los módulos, las rutas, la palabra clave `use` y la palabra clave `pub` en el compilador, y cómo la mayoría de los desarrolladores organizan su código. Veremos ejemplos de cada una de estas reglas a lo largo de este capítulo, pero este es un buen lugar para referirse a él como un recordatorio de cómo funcionan los módulos.

* **Comenzar desde el crate raíz:** al compilar un crate, el compilador primero busca en el archivo del crate raíz (generalmente src/lib.rs para un crate librería o src/main.rs para un crate binario) en busca de código para compilar.

* **Declaración de módulos:** en el archivo del crate raíz, puede declarar nuevos módulos; digamos, declara un módulo "garden" con `mod garden;` . El compilador buscará el código del módulo en estos lugares:
    * En la misma línea, entre corchetes que reemplazan el punto y coma que sigue a `mod garden`
    * En el archivo src/garden.rs
    * En el archivo src/garden/mod.rs

* **Declaración de submódulos:** en cualquier archivo que no sea el crate raíz, puede declarar submódulos. Por ejemplo, puede declarar `mod vegetables;` en src/garden.rs. El compilador buscará el código del submódulo dentro del directorio nombrado para el módulo principal en estos lugares:
    * En la misma línea, inmediatamente a `mod vegetables`, entre corchetes en lugar del punto y coma
    * En el archivo src/garden/vegetables.rs
    * En el archivo src/garden/vegetables/mod.rs

* **Rutas al código en los módulos:** una vez que un módulo es parte de su crate, puede hacer referencia al código de ese módulo desde cualquier otro lugar en ese mismo crate, siempre que las reglas de privacidad lo permitan, usando la ruta al código. Por ejemplo, un tipo de `Asparagus` en el módulo *garden vegetables* se encontraría en `crate::garden::vegetables::Asparagus`.

* **Privado vs. público:** el código dentro de un módulo es privado desde sus módulos padres de forma predeterminada. Para hacer público un módulo, declárelo con `pub mod` en lugar de `mod`. Para hacer que los elementos dentro de un módulo público también sean públicos, use `pub` antes de sus declaraciones.

* **La palabra clave `use`:** dentro de un scope, la palabra clave `use` crea accesos directos a elementos para reducir la repetición de rutas largas. En cualquier scope que pueda hacer referencia a `crate::garden::vegetables::Asparagus`, puede crear un acceso directo con `use crate::garden::vegetables::Asparagus;` y de ahí en adelante solo necesita escribir `Asparagus` para hacer uso de ese tipo en el scope.

Aquí creamos un crate binario llamado `backyard` que ilustra estas reglas. El directorio del crate, también llamado `backyard`, contiene estos archivos y directorios:

```text
backyard
├── Cargo.lock
├── Cargo.toml
└── src
    ├── garden
    │   └── vegetables.rs
    ├── garden.rs
    └── main.rs
```

El archivo del crate raíz en este caso es *src/main.rs* y contiene:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
use crate::garden::vegetables::Asparagus;

pub mod garden;

fn main() {
    let plant = Asparagus {};
    println!("I'm growing {:?}!", plant);
}
```

La línea `pub mod garden;` le dice al compilador que incluya el código que encuentra en *src/garden.rs*, que es:

<span class="filename">Filename: src/garden.rs</span>

```rust,ignore
pub mod vegetables;
```

Aquí, `pub mod vegetables;` significa que el código en *src/garden/vegetables.rs* también está incluido. Ese código es:

```rust,ignore
#[derive(Debug)]
pub struct Asparagus {}
```

¡Ahora entremos en los detalles de estas reglas y demostrémoslas en acción!

### Agrupando Código Relacionado en Módulos

Los *módulos* nos permiten organizar el código dentro de un crate para que sea legible y fácil de reutilizar. Los módulos también nos permiten controlar la *privacidad* de los elementos, porque el código dentro de un módulo es privado de forma predeterminada. Los elementos privados son detalles de implementación interna que no están disponibles para uso externo. Podemos elegir hacer públicos los módulos y los elementos dentro de ellos, lo que los expone para permitir que el código externo los use y dependa de ellos.

Como ejemplo, escribamos un crate librería que proporcione la funcionalidad de un restaurante. Definiremos las firmas de las funciones, pero dejaremos sus cuerpos vacíos para concentrarnos en la organización del código, en lugar de la implementación de un restaurante.

En la industria de los restaurantes, algunas partes de un restaurante se denominan *front of house* y otras como *back of house*. El *front of house* es donde están los clientes; esto abarca donde los anfitriones sientan a los clientes, los camareros toman los pedidos y los pagos, y los bartenders preparan las bebidas. El *back of house* es donde los chefs y cocineros trabajan en la cocina, los lavaplatos limpian y los gerentes hacen el trabajo administrativo.

Para estructurar nuestro crate de esta manera, podemos organizar sus funciones en módulos anidados. Cree una nueva librería llamada `restaurant` ejecutando `cargo new restaurant --lib`, luego ingrese el código del Listado 7-1 en *src/lib.rs* para definir algunos módulos y firmas de funciones. Aquí está la sección *front of house*:

<span class="filename">Filename: src/lib.rs</span>

```rust,ignore
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}

        fn seat_at_table() {}
    }

    mod serving {
        fn take_order() {}

        fn serve_order() {}

        fn take_payment() {}
    }
}
```

<span class="caption">Listado 7-1: Un módulo `front_of_house` que contiene otros módulos que a su vez contienen funciones</span>

Definimos un módulo con la palabra clave `mod` seguida del nombre del módulo (en este caso, `front_of_house`). El cuerpo del módulo luego va entre corchetes. Dentro de los módulos, podemos colocar otros módulos, como en este caso con los módulos de `hosting` y `serving`. Los módulos también pueden contener definiciones de otros elementos, como structs, enums, constantes, traits y, como en el Listado 7-1, funciones.

Mediante el uso de módulos, podemos agrupar definiciones relacionadas y nombrar por qué están relacionadas. Los programadores que utilizan este código pueden navegar por el código según los grupos en lugar de tener que leer todas las definiciones, lo que facilita encontrar las definiciones relevantes para ellos. Los programadores que agreguen nuevas funcionalidades a este código sabrían dónde colocar el código para mantener el programa organizado.

Anteriormente, mencionamos que *src/main.rs* y *src/lib.rs* se denominan crates raíz. La razón de su nombre es que el contenido de cualquiera de estos dos archivos forma un módulo llamado `crate` en la raíz de la estructura del módulo del crate, conocida como *árbol de módulos*.

<span class="filename">El Listado 7-2 muestra el árbol de módulos para la estructura del Listado 7-1.</span>

```text
crate
 └── front_of_house
     ├── hosting
     │   ├── add_to_waitlist
     │   └── seat_at_table
     └── serving
         ├── take_order
         ├── serve_order
         └── take_payment
```

<span class="caption">Listado 7-2: El árbol de módulos para el código del Listado 7-1</span>

Este árbol muestra cómo algunos de los módulos encajan unos dentro de otros; por ejemplo, `hosting` está anidado dentro de `front_of_house`. El árbol también muestra que algunos módulos son *hermanos* entre sí, lo que significa que están definidos en el mismo módulo; `hosting` y `serving` son hermanos definidos dentro de `front_of_house`. Si el módulo A está contenido dentro del módulo B, decimos que el módulo A es el *hijo* del módulo B y que el módulo B es el *padre* del módulo A. Tenga en cuenta que todo el árbol del módulo tiene su raíz bajo el módulo implícito llamado `crate`.

El árbol de módulos puede recordarle el árbol de directorios del sistema de archivos en su computadora; ¡esta es una comparación muy acertada! Al igual que los directorios en un sistema de archivos, usted utiliza módulos para organizar su código. Y al igual que los archivos en un directorio, necesitamos una forma de encontrar nuestros módulos.