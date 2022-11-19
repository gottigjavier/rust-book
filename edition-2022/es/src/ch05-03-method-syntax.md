## Sintaxis del Método *Method Syntax*

Los *métodos* son similares a las funciones: los declaramos con la palabra clave `fn` y un nombre, pueden tener parámetros y un valor de retorno, y contienen algún código que se ejecuta cuando el método es llamado desde otro lugar. A diferencia de las finciones, los métodos se definen dentro del contexto de un *struct* (o en un *enum* o un objeto *trait*, que cubrimos en los Capítulos 6 y 17, respectivamente), y su primer parámetro es siempre `self`, que representa la instancia del *struct* de la cual el método es llamado.

### Definición de Métodos

Cambiemos la función `area` que tiene una instancia `Rectangle` como parámetro y, en su lugar, hagamos que un método `area` se defina en el *struct* `Rectangle`, como se muestra en el Listado 5-13.

<span class="filename">Filename: src/main.rs</span>

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!(
        "El área del rectángulo es {} [píxeles cuadrados].",
        rect1.area()
    );
}
```

<span class="caption">Listing 5-13: Definir un método `area` en el *struct* `Rectangle`</span>

Para definir la función dentro del contexto de `Rectangle`, comenzamos un bloque `impl` (implementación) para `Rectangle`. Todo lo que se encuentre dentro de este bloque `impl` se asociará con el tipo `Rectangle`. Luego movemos la función `area` dentro de las llaves de `impl` y cambiamos el primer (y en este caso, el único) parámetro para que sea `self` en la firma y en todas partes dentro del cuerpo. En `main`, donde llamamos a la función `area` y pasamos `rect1` como un argumento, podemos en su lugar usar la *sintaxis del método* para llamar al método `area` en nuestra instancia `Rectangle`. La *sintaxis del método* va después de una instancia: añadimos un punto, seguido del nombre del método, paréntesis y cualquier argumento.

En la firma de `area`, usamos `&self` en lugar de `rectangle: &Rectangle`. El `&self` es en realidad la abreviatura de `self: &Self`. Dentro de un bloque `impl`, el tipo `Self` es un *alias* del tipo al cual se asocia el bloque `impl`. Los métodos deben tener un parámetro llamado `self` de tipo `Self` como su primer parámetro, así que Rust le permite abreviar esto solo con el nombre `self`. Tenga en cuenta que aún necesitamos usar `&` delante de la abreviatura `self` para indicar que este método toma prestada la instancia `Self`, tal como lo hicimos en `rectangle: &Rectangle`. Los métodos pueden tomar la propiedad (*ownership*) de `self`, tomar prestado `self` inmutable como lo hemos hecho aquí, o tomar prestado `self` mutable, tal como pueden hacerlo con cualquier otro parámetro.

Hemos elegido `&self` aquí por la misma razón que usamos `&Rectangle` en versión de función: no queremos tener la propiedad, y solo queremos leer los datos en el *struct*, no escribir en él. Si quisiéramos cambiar la instancia en la que llamamos al método como parte de lo que hace el método, usaríamos `&mut self` como el primer parámetro. Tener un método que tome posesión de la
instancia usando solo `self` como el primer parámetro es raro; esta técnica se suele usar
cuando el método transforma `self` en otra cosa y quiere evitar que quien llama utilice la instancia original después de la transformación.

La principal razón para usar métodos en lugar de funciones, además de proporcionar la *sintaxis del método* y no tener que repetir el tipo de `self` en la firma de cada método, es por motivos de organización. Hemos puesto todas las cosas que podemos hacer con una instancia de un tipo en un bloque `impl`, en lugar de hacer que los futuros usuarios de nuestro código busquen capacidades de `Rectangle` en varios lugares de la biblioteca que proporcionamos.

Tenga en cuenta que podemos optar por dar a un método el mismo nombre que uno de los campos del *struct*. Por ejemplo, podemos definir un método en `Rectangle` también llamado `width`:

<span class="filename">Filename: src/main.rs</span>

```rust
# #[derive(Debug)]
# struct Rectangle {
#     width: u32,
#     height: u32,
# }
#
impl Rectangle {
    fn width(&self) -> bool {
        self.width > 0
    }
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    if rect1.width() {
        println!("The rectangle has a nonzero width; it is {}", rect1.width);
    }
}
```
Aquí, elegimos hacer que el método `width` devuelva `true` si el valor en el campo `width` de la instancia es mayor que 0, y `false` si el valor es 0: podemos usar un campo dentro de un método del mismo nombre para cualquier propósito. En general, cuando seguimos `rect1.width` de paréntesis, Rust sabe que nos referimos al método `width`. Cuando no usamos paréntesis, Rust sabe que nos referimos al campo `width`.

A menudo, pero no siempre, cuando proporcionamos métodos con el mismo nombre que un campo, queremos que solo devuelva el valor en ese campo y no haga nada más. Los métodos como este se denominan *getters*, y Rust no los implementa automáticamente para los campos de *structs* como lo hacen otros lenguajes. Los *getters* son útiles porque se puede hacer que el campo sea privado pero el método sea público y, por lo tanto, permitir el acceso de "solo lectura" a ese campo como parte de la API pública del tipo. Discutiremos qué son "público" y "privado" y cómo designar un campo o método como "público" o "privado" en el capítulo 7.

> ### ¿Dónde está el operador `->`?
>
>En C y C++, se utilizan dos operadores diferentes para llamar a los métodos: se utiliza `.` si está llamando directamente a un método en el objeto y `->` si está llamando al método en un puntero al objeto y necesita *desreferenciar* (eliminar la referencia) el puntero primero. En otras palabras, si `object` es un puntero, `object->algo()` es similar a `(*object).algo()`.
>
> Rust no tiene un equivalente al operador `->`; en cambio, Rust tiene una característica llamada *automatic referencing and dereferencing* (*referenciado y desreferenciado automáticos*). La llamada a métodos es uno de los pocos lugares en Rust que tienen este comportamiento.
>
> Así es como funciona: cuando llama a un método con `object.algo()`, Rust automáticamente agrega `&`, `&mut`, o `*` para que `object` coincida con la firma del método. En otras palabras, los siguientes son los mismos:
>
>
> ```rust
> # #![allow(unused)]
> # fn main() {
> # #[derive(Debug,Copy,Clone)]
> # struct Point {
> #     x: f64,
> #     y: f64,
> # }
> #
> # impl Point {
> #    fn distance(&self, other: &Point) -> f64 {
> #        let x_squared = f64::powi(other.x - self.x, 2);
> #        let y_squared = f64::powi(other.y - self.y, 2);
> #
> #        f64::sqrt(x_squared + y_squared)
> #    }
> # }
> # let p1 = Point { x: 0.0, y: 0.0 };
> # let p2 = Point { x: 5.0, y: 6.5 };
> p1.distance(&p2);
> (&p1).distance(&p2);
> # }
> ```
>
> El primero se ve mucho más limpio. Este comportamiento de referencia automática funciona porque los métodos tienen un receptor claro: el tipo de `self`. Dado el receptor y el nombre de un método, Rust puede determinar definitivamente si el método está leyendo (`&self`), mutando (`&mut self`), o consumiendo (`self`). El hecho de que Rust haga que el préstamo sea implícito para los receptores de métodos es una gran parte de hacer que la propiedad sea ergonómica en la práctica.

### Métodos con Más Parámetros

Practiquemos el uso de métodos implementando un segundo método en el *struct* `Rectangle`. Esta vez, queremos que una instancia de `Rectangle` tome otra instancia de `Rectangle` y devuelva `true` si el segundo `Rectangle` puede caber completamente dentro de `self` (el primer `Rectangle`); de lo contrario, debería devolver `false`. Es decir, una vez que hayamos definido el método `can_hold`, queremos poder escribir el programa que se muestra en el Listado 5-14.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };
    let rect2 = Rectangle {
        width: 10,
        height: 40,
    };
    let rect3 = Rectangle {
        width: 60,
        height: 45,
    };

    println!("Can rect1 hold rect2? {}", rect1.can_hold(&rect2));
    println!("Can rect1 hold rect3? {}", rect1.can_hold(&rect3));
}
```

<span class="caption">Listing 5-14: Usando el método aún no escrito `can_hold`</span>

Y la salida esperada se vería como la siguiente, porque ambas dimensiones de `rect2`
son más pequeñas que las dimensiones de` rect1` pero `rect3` es más ancha que` rect1`:

```text
Can rect1 hold rect2? true
Can rect1 hold rect3? false
```

Sabemos que queremos definir un método, por lo que estará dentro del bloque `impl Rectangle`.
El nombre del método será `can_hold`, y tomará un préstamo inmutable de otro `Rectangle` como parámetro.
Podemos decir cuál será el tipo de parámetro mirando el código que llama al método:
`rect1.can_hold(&rect2)` le pasa `&rect2`, que es un préstamo inmutable de `rect2`,
una instancia de `Rectangle`. Esto tiene sentido porque solo necesitamos leer `rect2`
(en lugar de escribir, lo que significaría que necesitaríamos un préstamo mutable),
y queremos que `main` conserve la propiedad de `rect2` para poder usarlo
nuevamente después de llamar el método `can_hold`. El valor de retorno de `can_hold`
será un Booleano, y la implementación comprobará si el ancho y la altura de `self`
son mayores que el ancho y la altura del otro `Rectangle`, respectivamente.
Agreguemos el nuevo método `can_hold` al bloque `impl` del Listado 5-13,
que se muestra en el Listado 5-15.

<span class="filename">Filename: src/main.rs</span>

```rust
# #[derive(Debug)]
# struct Rectangle {
#     width: u32,
#     height: u32,
# }
#
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }

    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}
#
# fn main() {
#     let rect1 = Rectangle {
#         width: 30,
#         height: 50,
#     };
#     let rect2 = Rectangle {
#         width: 10,
#         height: 40,
#     };
#     let rect3 = Rectangle {
#         width: 60,
#         height: 45,
#     };
#
#     println!("Can rect1 hold rect2? {}", rect1.can_hold(&rect2));
#     println!("Can rect1 hold rect3? {}", rect1.can_hold(&rect3));
# }
```

<span class="caption">Listing 5-15: Implementando el método `can_hold` en `Rectangle`
que toma otra instancia `Rectangle` como parámetro</span>

Cuando ejecutamos este código con la función `main` en el listado 5-14,
obtendremos nuestro resultado deseado. Los métodos pueden tomar múltiples
parámetros, que agregamos a la firma después del parámetro `self`, y esos parámetros
funcionan igual que los parámetros en las funciones.

### Funciones Asociadas

Todas las funciones definidas dentro de un bloque `impl` se denominan *funciones asociadas* porque están asociadas con el tipo que lleva el nombre de `impl`. Podemos definir funciones asociadas que no tienen `self` como su primer parámetro (y por lo tanto no son métodos) porque no necesitan una instancia del tipo para trabajar. Ya hemos usado una función como esta: la función `String::from` que está definida en el tipo `String`.

Las funciones asociadas que no son métodos a menudo se usan para constructores que devolverán una nueva instancia del *struct*. Estas a menudo se nombran `new`, pero `new` no es un nombre especial y no está integrado en el lenguaje. Por ejemplo, podríamos optar por proporcionar una función asociada llamada `square` que tendría un parámetro de dimensión y usarlo tanto como ancho y como altura, lo que facilitaría la creación de un `Rectangle` cuadrado en lugar de tener que especificar el mismo valor dos veces:

<span class="filename">Filename: src/main.rs</span>

```rust
# #[derive(Debug)]
# struct Rectangle {
#     width: u32,
#     height: u32,
# }
#
impl Rectangle {
    fn square(size: u32) -> Self {
        Self {
            width: size,
            height: size,
        }
    }
}
#
# fn main() {
#     let sq = Rectangle::square(3);
# }
```

Las palabras clave `Self` en el tipo de devolución y en el cuerpo de la función son alias para el tipo que aparece después de la palabra clave `impl`, que en este caso es `Rectangle`.

Para llamar a esta función asociada, usamos la sintaxis `::` con el nombre del *struct*; `let sq = Rectangle::square(3);` es un ejemplo. Esta función se nombró en el espacio de nombres ([namespaced](https://doc.rust-lang.org/reference/names/namespaces.html)) del *struct*: la sintaxis `::` se usa tanto para las funciones asociadas y como para los espacios de nombres creados por los módulos. Discutiremos los módulos en el Capítulo 7.

### Múltiples Bloques `impl`

Cada estructura puede tener múltiples bloques `impl`. Por ejemplo, el Listado 5-15 es equivalente al código que se muestra en el Listado 5-16, que tiene cada método en su propio bloque `impl`.

```rust
# #[derive(Debug)]
# struct Rectangle {
#     width: u32,
#     height: u32,
# }
#
#
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

impl Rectangle {
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}
#
# fn main() {
#     let rect1 = Rectangle {
#         width: 30,
#         height: 50,
#     };
#     let rect2 = Rectangle {
#         width: 10,
#         height: 40,
#     };
#     let rect3 = Rectangle {
#         width: 60,
#         height: 45,
#     };
#
#     println!("Can rect1 hold rect2? {}", rect1.can_hold(&rect2));
#     println!("Can rect1 hold rect3? {}", rect1.can_hold(&rect3));
# }
```

<span class="caption">Listing 5-16: Reescribiendo el Listado 5-15 usando múltiples bloques `impl`</span>

No hay ninguna razón para separar estos métodos en múltiples bloques `impl` aquí, pero esta es una sintaxis válida. Veremos un caso en el que varios bloques `impl` son útiles en el Capítulo 10, donde discutimos tipos genéricos y *traits*.

## Resumen

Los *structs* le permiten crear tipos personalizados que son significativos para su dominio. Mediante el uso de *structs*, puede mantener partes de datos asociadas conectadas entre sí y nombrar cada parte para que su código quede claro. En bloques `impl`, puede definir funciones que están asociadas con su tipo, y los métodos son un tipo de función asociada que le permite especificar el comportamiento que tienen las instancias de sus *structs*.

Pero los *structs* no son la única forma en que puede crear tipos personalizados: pasemos a la característica *enum* de Rust para agregar otra herramienta a su caja de herramientas.