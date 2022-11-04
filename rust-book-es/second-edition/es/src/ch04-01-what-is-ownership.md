## ¿Qué es la propiedad (Ownership)?

La *propiedad* es un conjunto de reglas que rigen cómo un programa Rust administra la memoria. Todos los programas tienen que administrar la forma en que usan la memoria de una
computadora mientras se ejecutan. Algunos lenguajes tienen un *recolector de basura*, que constantemente busca memoria que ya no se usa mientras el programa se ejecuta. En otros lenguajes, el programador debe explícitamente asignar y liberar la memoria. Rust utiliza un tercer enfoque: la memoria se gestiona a través de un sistema de propiedad con un conjunto de reglas que el compilador verifica en tiempo de compilación. Ninguna de las características de propiedad ralentizará su programa mientras se está ejecutando.

Como la propiedad es un concepto nuevo para muchos programadores, lleva tiempo
acostumbrarse a ella. La buena noticia es que cuanto más experimentado se vuelva con Rust
y las reglas del sistema de propiedad (*ownership*), más podrá, naturalmente,
desarrollar código que sea seguro y eficiente. ¡Sígalo!

Cuando comprenda la *propiedad*, tendrá una base sólida para la comprensión
las características que hacen que Rust sea único. En este capítulo, aprenderá sobre *propiedad* a través de algunos ejemplos que se centran en una estructura de datos muy común: *strings*.

> ### La pila (*Stack*) y el montículo (*Heap*).
>
> En muchos lenguajes de programación, no tiene que pensar muy a menudo en *Stack* y *Heap*, pero en un lenguaje de programación de sistemas como Rust, que un valor esté en el *Stack* o el *Heap* tiene más de un efecto sobre cómo el lenguaje se comporta y por qué tiene que tomar ciertas decisiones. Partes de la *propiedad* se describirán en relación con *Stack* y *Heap* más adelante en este capítulo, por lo que aquí hay una breve explicación a modo de preparación.
>
> Tanto la pila como el montículo son partes de la memoria que está disponible para usar en su código en tiempo de ejecución, pero están estructurados de diferentes maneras. El *stack* almacena los valores en el orden en que los obtiene y elimina los valores en el orden opuesto. Esto se conoce como *last in, first out*, *LIFO*, (último en entrar, primero en salir). Piense en una pila de platos: cuando agrega más platos, los pone encima de la pila, y cuando necesita un plato, toma uno de la parte superior. Agregar o quitar platos del medio o abajo ¡no funcionaría tan bien! Agregar datos se llama *pushing onto the stack* (poniendo sobre la pila), y eliminar datos se llama *popping off the stack* (sacando de arriba de la pila).
>
> La pila es rápida debido a la forma en que accede a los datos: nunca tiene que buscar un lugar para poner nuevos datos o un lugar para obtener datos porque el lugar siempre es la parte superior de la pila. Otra característica que hace que la pila sea rápida es que todos los datos en la pila deben tener un tamaño conocido y fijo.
>
> Los datos con un tamaño desconocido en el momento de la compilación o un tamaño que podría cambiar deben ser almacenados en el *Heap*. El *Heap* está menos organizado: cuando pone datos en el *Heap*, pide una cierta cantidad de espacio. El asignador de memoria encuentra un lugar vacío en algún lugar del *Heap* que sea lo suficientemente grande, lo marca como que esta en uso, y devuelve un *puntero*, que es la dirección de esa ubicación. Este proceso se llama *allocating on the heap* (*asignando en el montículo*), a veces abreviado solo como “allocating” (insertar valores en el *stak* no se considera asignación). Debido a que el puntero es de un tamaño conocido y fijo, se puede almacenar el puntero en el *stack*, pero cuando quiere los datos reales, debe seguir el puntero.
>
> Piense en sentarse en un restaurante. Cuando ingresa, indica la cantidad de personas de su grupo, y el personal encuentra una mesa vacía que se adapta a todos y le lleva allí. Si alguien en su grupo llega tarde, pueden preguntar dónde ha estado sentado para encontrarle.
>
> Acceder a los datos en el *Heap* es más lento que acceder a los datos en el *stack* porque tiene que seguir un puntero para llegar allí. Los procesadores contemporáneos son más rápidos si saltan menos en la memoria. Continuando con la analogía, considere un camarero en un restaurante que recibe pedidos de muchas mesas. Es más eficiente obtener todos los pedidos en una mesa antes de pasar a la siguiente. Tomando una orden de la mesa A, luego una orden de la mesa B, luego  otra vez una de la mesa A, y entonces una más de la mesa B, sería un proceso mucho más lento. Por la misma razón, un procesador puede hacer su trabajo mejor si funciona con datos que están cerca de otros datos (como en el *stack*) en lugar de estar más lejos (como puede ser en el *heap*). Asignar una gran cantidad de espacio en el *heap* también puede llevar tiempo.
>
> Cuando su código llama a una función, los valores pasados a la función (incluidos, potencialmente, punteros a datos en el montículo) y las variables locales de la función se colocan en la pila. Cuando la función termina, esos valores se eliminan de la pila.
>
> Hacer un seguimiento de qué partes del código están usando qué datos en el montículo, minimizar la cantidad de datos duplicados en el montículo, limpiar datos sin usar en el montículo para que no se quede sin espacio, son todos problemas que aborda la *propiedad* (*ownership*). Una vez que comprenda la *propiedad*, no tendrá que pensar en la pila y el montículo con frecuencia, pero saber que el objetivo principal de la *propiedad* es administrar los datos del montículo puede ayudar a explicar por qué funciona de la manera en que lo hace.
>
### Reglas de propiedad (*Ownership*)

Primero, echemos un vistazo a las reglas de propiedad. Tenga en cuenta estas reglas cuando trabaje a través de los ejemplos que los ilustran:

* Cada valor en Rust tiene un propietario.
* Solo puede haber un propietario a la vez.
* Cuando el propietario queda fuera del ámbito, el valor se eliminará.

### Ámbito de la variable (*Scope*)

Ahora que hemos pasado la sintaxis básica, no incluiremos todo el código `fn main () {` en ejemplos, así que tendrá que poner los siguientes ejemplos dentro de una función `main` de forma manual. Como resultado, nuestros ejemplos serán un poco más concisos, dejándonos enfocarnos en los detalles reales en lugar de código repetitivo.

Como primer ejemplo de *propiedad*, veremos el ámbito o alcance (*scope*) de algunas variables. Un *scope* es el rango dentro de un programa para el cual un elemento es válido. Digamos que tenemos una variable que se ve así:

```rust
let s = "hello";
```

La variable `s` se refiere a un *string* literal, donde el valor del *string* es
codificado en el texto de nuestro programa. La variable es válida desde el punto en el
que se declara hasta el final del *scope* actual. El listado 4-1 tiene comentarios
anotando donde la variable `s` es válida.

```rust
{                      // s no es válida aquí, aún no está declarada
    let s = "hello";   // s es válida a partir de este punto

    // hacer cosas con s, sigue siendo válida
}                      // este ámbito ha terminado, y s ya no es válida
```

<span class="caption">Listing 4-1: Una variable y el ámbito en el que es válida</span>

En otras palabras, hay dos puntos importantes en el tiempo aquí:

* Cuando `s` entra en el *scope*, es válida.
* Sigue siendo válida hasta que se sale del *scope*.

En este punto, la relación entre los ámbitos y cuándo las variables son válidas es similar a la de otros lenguajes de programación. Ahora construiremos sobre esta comprensión introduciendo el tipo `String`.

### El tipo `String`

Para ilustrar las reglas de propiedad (*ownership*), necesitamos un tipo de datos que sea más complejo que los que cubrimos en la sección ["Tipos de datos"](ch03-02-data-types.html#tipos-de-datos) del Capítulo 3. Los tipos cubiertos anteriormente son todos de un tamaño conocido, pueden ser almacenados en la pila (*stack*) y sacarse de la pila cuando su alcance ha terminado, pueden ser rápida y trivialmente copiados para hacer una nueva instancia independiente si otra parte del código necesita usar el mismo valor en un ámbito diferente. Pero queremos observar los datos que se almacenan en el montículo (*heap*) y explorar cómo Rust sabe cuándo limpiar esos datos, y el tipo `String` es un gran ejemplo.

Nos concentraremos en las partes de `String` que se relacionan con la *propiedad*. Estos aspectos también se aplican a otros tipos de datos complejos provistos por la biblioteca estándar (*std*) o creados por usted. Estudiaremos el tipo `String` con más profundidad en el [Capítulo 8](ch08-02-strings.html).

Ya vimos *string* literales, donde un valor de *string* está codificado en nuestro programa. Los literales *string* son convenientes, pero no son adecuados para toda situación en la cual podríamos querer usar texto. Una razón es que son inmutables. Otra es que no todos los valores de *string* se pueden conocer cuando escribimos nuestro código: por ejemplo, ¿qué sucede si queremos tomar la entrada del usuario y almacenarla? por estas situaciones, Rust tiene un segundo tipo de *string*, `String`. Este tipo maneja datos asignados en el montículo (*heap*) y como tal, es capaz de almacenar una cantidad de texto que es desconocida para nosotros en tiempo de compilación. Puede crear un `String` a partir de un literal *string* usando la *función asociada* `from`, así:

```rust
let s = String::from("hello");
```

El operador dos puntos (`::`) nos permite asignar un espacio de nombres a esta función `from` particular bajo el tipo `String` en lugar de usar algún tipo de nombre como `string_from`. Discutiremos más esta sintaxis en la sección ["Sintaxis del método"](ch05-03-method-syntax.html) del Capítulo 5 y cuando hablemos sobre el espacio de nombres con módulos en ["Rutas para hacer Referencia a un Elemento en el Árbol de Módulos"](ch07-03-importing-names-with-use.html) en el Capítulo 7.

Este tipo de *string* puede mutar:

```rust
let mut s = String::from("hello");

s.push_str(", world!"); // push_str() agrega un literal a un String

println!("{}", s);      // Esto imprimirá `hello, world!`
```

Entonces, ¿cuál es la diferencia aquí? ¿Por qué `String` puede mutarse pero los literales no?
La diferencia es cómo estos dos tipos lidian con la memoria

### Memoria y Asignación

En el caso de un *string* literal, conocemos el contenido en tiempo de compilación, por lo que el texto está codificado directamente en el ejecutable final. Esta es la razón por la que los *string* literales son rápidos y eficientes. Pero estas propiedades solo provienen de la inmutabilidad del literal del *string*. Desafortunadamente, no podemos poner una burbuja de memoria en el binario por cada fragmento de texto cuyo tamaño se desconoce en el momento de la compilación y cuyo tamaño podría cambiar mientras se ejecuta el programa.

Con el tipo `String`, para admitir un fragmento de texto mutable y ampliable,
necesitamos asignar una cantidad de memoria en el montículo, desconocida en tiempo de compilación, para almacenar el contenidos. Esto significa:

* La memoria debe solicitarse al asignador de memoria en tiempo de ejecución.
* Necesitamos una forma de devolver esta memoria al asignador cuando hayamos terminado con nuestro `String`.

Esa primera parte la hacemos nosotros: cuando llamamos a `String::from`, su implementación solicita la memoria que necesita. Esto es bastante universal en los lenguajes de programación.

Sin embargo, la segunda parte es diferente. En lenguajes con un *recolector de basura (GC)*, el GC realiza un seguimiento y limpia la memoria que ya no se usa, y no necesitamos pensar en ello. En la mayoría de los lenguajes sin un GC, es nuestra responsabilidad identificar cuándo ya no se usa la memoria y llamar al código para liberarla explícitamente, tal como lo hicimos para solicitarla. Hacer esto correctamente ha sido históricamente un problema de programación difícil. Si lo olvidamos, desperdiciamos memoria. Si lo hacemos demasiado pronto, tendremos una variable inválida. Si lo hacemos dos veces, también es un error. Necesitamos emparejar exactamente un `allocate` con exactamente un `free`.

Rust toma una ruta diferente: la memoria se devuelve automáticamente una vez que la variable que lo posee queda fuera del ámbito. Aquí hay una versión de nuestro ejemplo de ámbito del Listado 4-1 usando un `String` en lugar de un literal de *string*:

```rust
{
    let s = String::from("hello"); // s es válida a partir de este punto

    // hacer cosas con s
}                                  // este ámbito ha terminado, y s ya no es válida
```

Aquí hay un punto natural en el que podemos devolver la memoria que nuestro `String` necesita al asignador cuando `s` sale del ámbito. Cuando una variable sale del ámbito, Rust llama a una función especial para nosotros. Esta función se llama `drop`, y es donde el autor de `String` podría poner el código para devolver la memoria. Rust llama a `drop` automáticamente al cierre de la llave.

> Nota: En C ++, este patrón de desasignación de recursos al final de la vida útil de un elemento se denomina a veces *Resource Acquisition Is Initialization (RAII)* (*Inicialización de adquisición de recursos (RAII)*).
> La función `drop` en Rust le resultará familiar si ha utilizado patrones RAII.

Este patrón tiene un profundo impacto en la forma en que se escribe el código de Rust. Puede parecer simple en este momento, pero el comportamiento del código puede ser
inesperado en situaciones más complicadas cuando queremos que múltiples variables usen
los datos que hemos asignado en el montículo (*heap*). Exploremos algunas de esas
situaciones ahora.

#### Formas en que Interactúan las Variables y los Datos: Mover

Múltiples variables pueden interactuar con los mismos datos de diferentes maneras en Rust.
Veamos un ejemplo usando un número entero en el Listado 4-2.

```rust
let x = 5;
let y = x;
```

<span class="caption">Listing 4-2: Asignando el valor entero de la variable `x` a `y`</span>

Probablemente podamos adivinar qué está haciendo esto: “vincula el valor `5` a `x`; luego haga una copia del valor en `x` y agréguela a `y`.” Ahora tenemos dos variables,`x` e `y`, y ambas son `5`. De hecho, esto es lo que está sucediendo, porque los enteros son valores simples con un tamaño conocido y fijo, y estos dos valores `5` se colocan en la pila (*stack*).

Ahora veamos la versión `String`:

```rust
let s1 = String::from("hello");
let s2 = s1;
```

Esto se ve muy similar al código anterior, por lo que podemos suponer que la forma en que funciona sería la misma: es decir, la segunda línea haría una copia del valor en `s1` y lo vincularía a `s2`. Pero esto no es exactamente lo que sucede.

Eche un vistazo a la Figura 4-1 para ver lo que está sucediendo con `String` debajo de la superficie. Un `String` se compone de tres partes, que se muestran a la izquierda: un puntero a la memoria que contiene el contenido del *string*, una longitud y una capacidad. Este grupo de datos se almacena en la pila (*stack*). A la derecha está la memoria en el montículo *(heap*) que contiene los contenidos.

<img alt="String in memory" src="img/trpl04-01.svg" class="center" style="width: 50%;" />

<span class="caption">Figure 4-1: Representación en memoria de un `String`
conteniendo el valor `"hello"` vinculado a `s1`</span>

La longitud es la cantidad de memoria, en bytes, que está usando actualmente el contenido de `String`. La capacidad es la cantidad total de memoria, en bytes, que el `String` recibió del asignador. La diferencia entre la logitud y la capacidad importa, pero no en este contexto,
por lo que, por ahora, está bien ignorar la capacidad.

Cuando asignamos `s1` a `s2`, los datos de `String` se copian, lo que significa que copiamos
el puntero, la longitud y la capacidad que están en la pila (*stack*). No copiamos los datos
en el montículo (*heap*) al que hace referencia el puntero. En otras palabras, la representación de datos en la memoria se parece a la Figura 4-2.

<img alt="s1 and s2 pointing to the same value" src="img/trpl04-02.svg" class="center" style="width: 50%;" />

<span class="caption">Figure 4-2: Representación en memoria de la variable `s2`
que tiene una copia del puntero, longitud y capacidad de `s1`</span>

La representación *NO* se parece a la Figura 4-3, que es como se vería la memoria
si Rust copiara también los datos del montículo. Si Rust hiciera esto, la operación `s2 = s1`
podría ser muy costosa en términos de rendimiento en tiempo de ejecución si los datos
en el montículo fueran grandes.

<img alt="s1 and s2 to two places" src="img/trpl04-03.svg" class="center" style="width: 50%;" />

<span class="caption">Figure 4-3: Otra posibilidad para qué `s2 = s1`
podría hacer si Rust copiara los datos del montículo (*heap*) también</span>

Anteriormente, dijimos que cuando una variable queda fuera del alcance, Rust
llama automáticamente a la función `drop` y limpia la memoria del montículo para esa variable.
Pero la Figura 4-2 muestra ambos punteros de datos apuntando a la misma ubicación.
Esto es un problema: cuando `s2` y `s1` salen del ámbito, ambos intentarán liberar la misma memoria.
Esto se conoce como error de *doble liberación* (double free) y es uno de los errores de seguridad de la memoria que mencionamos anteriormente. Liberar memoria dos veces puede provocar daños en la memoria, lo que puede generar vulnerabilidades de seguridad.

Para garantizar la seguridad de la memoria, después de la línea `let s2 = s1`, Rust ya considera `s1` como no válido. Por lo tanto, Rust no necesita liberar nada cuando `s1` queda fuera del ámbito. Mire lo que sucede cuando intenta usar `s1` después que `s2` es creado; no funcionará: 

```rust,ignore
let s1 = String::from("hello");
let s2 = s1;

println!("{}, world!", s1);
```

Recibirá un error como este porque Rust le impide usar la referencia invalidada:

```text
$ cargo run
   Compiling ownership v0.1.0 (file:///projects/ownership)
error[E0382]: borrow of moved value: `s1`
 --> src/main.rs:5:28
  |
2 |     let s1 = String::from("hello");
  |         -- move occurs because `s1` has type `String`, which does not implement the `Copy` trait
3 |     let s2 = s1;
  |              -- value moved here
4 | 
5 |     println!("{}, world!", s1);
  |                            ^^ value borrowed here after move
  |
  = note: this error originates in the macro `$crate::format_args_nl` (in Nightly builds, run with -Z macro-backtrace for more info)

For more information about this error, try `rustc --explain E0382`.
error: could not compile `ownership` due to previous error
```

Si ha escuchado los términos *shallow copy* (*copia superficial*) y *deep copy* (*copia profunda*) mientras trabaja con otros lenguajes, el concepto de copiar el puntero, la longitud y la capacidad sin copiar los datos probablemente suene como una copia superficial. Pero debido a que Rust también invalida la primera variable, en lugar de llamarse copia superficial, se la
conoce como *mover*. En este ejemplo, diríamos que `s1` se *movió* a `s2`. Entonces,
lo que realmente sucede se muestra en la Figura 4-4.

<img alt="s1 moved to s2" src="img/trpl04-04.svg" class="center" style="width: 50%;" />

<span class="caption">Figure 4-4: La representación en memoria después de `s1` ha sido invalidada</span>

¡Eso resuelve nuestro problema! Con solo `s2` válida, cuando salga del ámbito, ella sola
liberará la memoria y terminaremos.

Además, hay una opción de diseño implícita: Rust nunca creará automáticamente copias “profundas”
de sus datos. Por lo tanto, se puede suponer que cualquier copiado *automático* es económico en
términos de rendimiento en tiempo de ejecución.

#### Formas en que Interactúan las Variables y los Datos: Clonar

Si queremos hacer una *copia profunda* de los datos del montículo (*heap*) de `String` y
no sólo los datos de la pila (*stack*), podemos usar un método común llamado `clone`.
Estudiaremos la sintaxis del método en el Capítulo 5, pero dado que los métodos son
una característica común en muchos lenguajes de programación, probablemente ya los haya visto antes.

Aquí hay un ejemplo del método `clone` en acción:

```rust
let s1 = String::from("hello");
let s2 = s1.clone();

println!("s1 = {}, s2 = {}", s1, s2);
```

Esto funciona correctamente y produce explícitamente el comportamiento que se muestra en
la Figura 4-3, donde los datos del montículo se copian.

Cuando ve una llamada a `clone`, sabe que se está ejecutando algún código
arbitrario y que ese código puede ser costoso. Es un indicador visual de que
algo diferente está sucediendo.

#### Datos de Sólo-Pila: Copiar

Hay otro pliegue del que aún no hemos hablado. Este código que usa números enteros,
parte del cual se mostró en el Listado 4-2, funciona y es válido:

```rust
let x = 5;
let y = x;

println!("x = {}, y = {}", x, y);
```

Pero este código parece contradecir lo que acabamos de aprender: no tenemos una llamada a
`clone`, pero` x` sigue siendo válida y no se movió a `y`.

La razón es que los tipos como los enteros, que tienen un tamaño conocido en el momento
de la compilación, se almacenan completamente en la pila, por lo que las copias de los
valores actuales son rápidos de realizar. Eso significa que no hay ninguna razón por la que
quisiéramos evitar que `x` sea válida después de que creamos la variable` y`. En otras
palabras, aquí no hay diferencia entre la copia profunda y la poco profunda, por lo que
llamar a `clone` no haría nada diferente de la copia superficial normal y podemos omitirlo.

Rust tiene una anotación especial llamada el *trait* `Copy` que podemos colocar en
tipos como enteros que están almacenados en la pila (hablaremos más sobre los rasgos en el [Capítulo 10](ch10-02-traits.html)). Si un tipo implementa el *trait* `Copy`, las variables que lo usan no son movidas, sino que se copian trivialmente, haciéndolas aún válidas después de la asignación a otra variable.

Rust no nos permitirá anotar un tipo con `Copy` si el tipo, o cualquiera de sus partes, ha implementado el *trait* `Drop`. Si el tipo necesita que ocurra algo especial cuando el
valor se sale del ámbito y agregamos la anotación `Copy` a ese tipo, obtendremos un error
en tiempo de compilación. Para obtener información sobre cómo agregar la anotación `Copy`
a su tipo para implementar el *trait*, consulte [“Rasgos derivables”](appendix-03-derivable-traits.html) en el Apéndice C.

Entonces, ¿qué tipos implementan el *trait* `Copy`? Puede verificar la documentación del tipo dado para asegurarse, pero como regla general, cualquier grupo de valores escalares simples puede ser `Copy`, y nada que requiera asignación o es alguna forma de recurso es `Copy`. Estos son algunos de los tipos que implementan `Copy`:

* Todos los tipos de enteros, como `u32`.
* El tipo booleano, `bool`, con los valores `true` y `false`.
* Todos los tipos de punto flotante, como `f64`.
* El tipo de carácter, `char`.
* Tuplas, pero solo si contienen tipos que también son `Copy`. Por ejemplo,
   `(i32, i32)` implementa `Copy`, pero `(i32, String)` no lo hace.

### Propiedad y funciones

La mecánica para pasar un valor a una función es similar a la de asignar
un valor a una variable. Pasar una variable a una función la moverá o la copiará,
tal como lo hace la asignación. El listado 4-3 tiene un ejemplo con algunas anotaciones
que muestran dónde las variables entran y salen del ámbito (*scope*).

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let s = String::from("hello");  // s entra en el alcance

    takes_ownership(s);             // s's el valor se mueve a la función ...
                                    // ... y entonces ya no es válido aquí

    let x = 5;                      // x entra en el alcance

    makes_copy(x);                  // x se movería a la función,
                                    // pero i32 es Copy, así que está bien
                                    // todavía puede usarse x después

} // Aquí, x sale del alcance, luego s. Pero como el valor de s se movió, nada
  // sucede de manera especial.

fn takes_ownership(some_string: String) { // some_string entra en el alcance
    println!("{}", some_string);
} // Aquí, some_string sale del alcance y se llama `drop`. La memoria de
  // respaldo se libera.

fn makes_copy(some_integer: i32) { // some_integer entra en el alcance
    println!("{}", some_integer);
} // Aquí, some_integer sale del alcance. Nada especial sucede.
```

<span class="caption">Listing 4-3: Funciones con propiedad y alcance anotado</span>

Si intentamos usar `s` después de la llamada a` takes_ownership`, Rust arrojaría
un error en tiempo de compilación. Estas comprobaciones estáticas nos protegen de
los errores. Intente agregar código a `main` que use` s` y `x` para ver dónde puede
usarlos y dónde las reglas de propiedad le impiden hacerlo.

### Valores de retorno y alcance

Los valores devueltos también puede transferir la propiedad (*ownership*).
El listado 4-4 es un ejemplo con anotaciones similares a las del Listado 4-3.

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let s1 = gives_ownership();         // gives_ownership mueve su valor de 
                                        // retorno a s1

    let s2 = String::from("hello");     // s2 entra en el alcance

    let s3 = takes_and_gives_back(s2);  // s2 se mueve a takes_and_gives_back, 
                                        // que también mueve su valor 
                                        // de retorno a s3
} // Aquí, s3 sale del alcance y se descarta. s2 sale del alcance pero se movió,
  // por lo que no sucede nada. s1 sale del alcance y se descarta.

fn gives_ownership() -> String {             // gives_ownership moverá su 
                                             // valor de retorno a la función 
                                             // que lo llama.

    let some_string = String::from("hello"); // some_string entra en el alcance

    some_string                              // some_string se devuelve y 
                                             // se mueve a la función de llamada.
}

// takes_and_gives_back tomará un *string* y devolverá uno
fn takes_and_gives_back(a_string: String) -> String { // a_string entra en el alcance

    a_string  // a_string se devuelve y se mueve a la función de llamada
}
```

<span class="caption">Listing 4-4: Transferir la propiedad (*ownership*) de los valores devueltos</span>

La propiedad (*ownership*) de una variable sigue el mismo patrón cada vez: la asignación de un valor a otra variable mueve la propiedad. Cuando una variable que incluye datos en el montículo queda fuera del alcance, el valor se limpiará mediante `drop` a menos que los datos hayan
sido movidos para ser propiedad (*ownership*) de otra variable.

Tomar posesión y luego devolver la propiedad (*ownership*) con cada función es un poco tedioso.
¿Qué sucede si queremos permitir que una función use un valor pero no tome posesión?
Es bastante molesto que cualquier cosa que pasemos también deba devolverse si queremos
volver a usarla, además de cualquier dato que provenga del cuerpo de la función que también
deseemos devolver.

Rust nos permite devolver múltiples valores usando una tupla, como se muestra en el Listado 4-5.

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let s1 = String::from("hello");

    let (s2, len) = calculate_length(s1);

    println!("The length of '{}' is {}.", s2, len);
}

fn calculate_length(s: String) -> (String, usize) {
    let length = s.len(); // len() returns the length of a String

    (s, length)
}
```

<span class="caption">Listing 4-5: Devolución de la propiedad (*ownership*) de los parámetros</span>

Pero esto es demasiada ceremonia y mucho trabajo para un concepto que debería ser común.
Afortunadamente para nosotros, Rust tiene una característica usar un valor sin transferir la propiedad, llamada *referencias* (*references*).
