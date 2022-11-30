# Introducción

> Nota: esta edición del libro es igual a
> [The Rust Programming Language][nsprust] disponible en formato de impresión
> y ebook desde [No Starch Press][nsp].

[nsprust]: https://nostarch.com/rust
[nsp]: https://nostarch.com/

Bienvenido a *El lenguaje de programación de Rust* (*The Rust Programming Language*), un libro introductorio sobre Rust. El lenguaje de programación Rust le ayuda a escribir software más rápido y confiable. La ergonomía de alto nivel y el control de bajo nivel a menudo están en desacuerdo en el diseño del lenguaje de programación; Rust desafía ese conflicto. Al equilibrar una capacidad técnica poderosa y una excelente experiencia de desarrollador, Rust le brinda la opción de controlar detalles de bajo nivel (como el uso de la memoria) sin todas las molestias asociadas tradicionalmente con dicho control.

## Para Quién es Rust

Rust es ideal para muchas personas por una variedad de razones. Veamos algunos de los grupos más importantes.

### Equipos de Desarrolladores

Rust está demostrando ser una herramienta productiva para colaborar entre grandes equipos de desarrolladores con diferentes niveles de conocimiento de programación de sistemas. El código de bajo nivel es propenso a una variedad de errores sutiles, que en la mayoría de los otros lenguajes solo se pueden atrapar mediante pruebas exhaustivas y una cuidadosa revisión del código por parte de desarrolladores experimentados. En Rust, el compilador desempeña un
rol de guardián al negarse a compilar código con estos esquivos errores, incluidos los errores de concurrencia. Al trabajar junto con el compilador, el equipo puede dedicar su tiempo a enfocarse en la lógica del programa en lugar de buscar errores.

Rust también trae herramientas de desarrollo contemporáneas al mundo de programación de sistemas:

* Cargo, el administrador de dependencias incluido y la herramienta de compilación hace que agregar, compilar y gestionar dependencias sea sencillo y consistente en todo el ecosistema de Rust.
* Rustfmt garantiza un estilo de codificación consistente entre los desarrolladores.
* Rust Language Server impulsa la integración del entorno de desarrollo integrado (IDE) para la finalización del código y mensajes de error en línea.

Al usar estas y otras herramientas en el ecosistema de Rust, los desarrolladores pueden ser productivos al escribir código a nivel de sistema.

### Estudiantes

Rust es para estudiantes y aquellos que están interesados en aprender sobre conceptos de sistemas. Con Rust, muchas personas han aprendido sobre temas como el desarrollo de sistemas operativos. La comunidad es muy acogedora y está feliz de responder las preguntas de los estudiantes. A través de esfuerzos como este libro, los equipos de Rust quieren que los conceptos de sistemas sean más accesibles para más personas, especialmente aquellos que son nuevos en la programación.

### Compañías

Cientos de empresas, grandes y pequeñas, usan Rust en producción para una variedad de tareas. Esas tareas incluyen herramientas de línea de comandos, servicios web, herramientas DevOps, dispositivos integrados, análisis y transcodificación de audio y video, criptomonedas, bioinformática, motores de búsqueda, aplicaciones de *Internet de las Cosas*, machine learning e
incluso partes importantes del navegador web *Firefox*.

### Desarrolladores de Código Abierto

Rust es para las personas que desean construir el lenguaje de programación Rust, la comunidad, las herramientas de desarrollo y las bibliotecas. Nos encantaría que contribuyera al lenguaje Rust.

### Personas que Valoran la Velocidad y la Estabilidad

Rust es para personas que buscan velocidad y estabilidad en un lenguaje. Por velocidad, nos referimos a la velocidad de los programas que puede crear con Rust y la velocidad a la que Rust le permite escribirlos. Las comprobaciones del compilador de Rust garantizan la estabilidad a través de la adición de características y la refactorización. Esto contrasta con el frágil código heredado en lenguajes sin estas comprobaciones, que los desarrolladores a menudo temen modificar. Al esforzarse por lograr abstracciones de costo cero, características de alto nivel que se compilan en código de bajo nivel tan rápido como el código escrito manualmente, Rust se esfuerza por hacer que el código seguro también sea código rápido.

El lenguaje Rust espera admitir también a muchos otros usuarios; los mencionados aquí son simplemente algunos de los principales interesados. En general, la mayor ambición de Rust es eliminar las concesiones que los programadores han aceptado durante décadas proporcionando seguridad y productividad, velocidad y ergonomía. Pruebe Rust y vea si sus opciones funcionan para usted.

## Para Quién es este Libro

Este libro asume que ha escrito código en otro lenguaje de programación, pero no hace ninguna suposición sobre cuál. Hemos tratado de hacer que el material sea ampliamente accesible para aquellos con una gran variedad de antecedentes en programación. No dedicamos mucho tiempo a hablar sobre qué es la programación o cómo pensar en ella. Si es completamente nuevo en la programación, sería mejor que leyera un libro que brinde específicamente una introducción a la programación.

## Cómo Usar Este Libro

En general, este libro asume que lo está leyendo en secuencia desde principio hacia el final. Los capítulos posteriores se basan en conceptos de capítulos anteriores, y es posible que los capítulos anteriores no profundicen en los detalles de un tema; por lo general, retomamos el tema en un capítulo posterior.

Encontrará dos tipos de capítulos en este libro: capítulos conceptuales y capítulos proyecto. En los capítulos conceptuales, aprenderá sobre un aspecto de Rust. En los capítulos del proyecto, crearemos pequeños programas juntos, aplicando lo que has aprendido hasta ahora. Los capítulos 2, 12 y 20 son capítulos de proyectos; el resto son capítulos conceptuales.

El Capítulo 1 explica cómo instalar Rust, cómo escribir un programa *Hello, world!*, y cómo usar Cargo, el administrador de paquetes y herramienta de compilación de Rust. El Capítulo 2 es una introducción práctica al lenguaje Rust. Aquí cubrimos conceptos a un alto nivel, y capítulos posteriores proporcionarán detalles adicionales. Si quiere ensuciarse las manos de inmediato, el Capítulo 2 es el lugar para eso. Al principio, usted podría incluso omitir el Capítulo 3, que cubre las características de Rust similares a las de otros lenguajes de programación, y dirirse directamente al Capítulo 4 para aprender sobre el *Sistema de Propiedad* de Rust. Sin embargo, si usted es un estudiante particularmente meticuloso que prefiere aprender cada detalle antes de pasar al siguiente, puede omitir el Capítulo 2 e ir directamente al Capítulo 3, regresando al Capítulo 2 cuando desee trabajar en un proyecto aplicando los detalles que ha aprendido.

El Capítulo 5 analiza *structs* (estructuras) y métodos, y el Capítulo 6 cubre *enums* (enumeraciones), expresiones `match` y la estructura de control `if let`. Utilizará structs y enums para crear tipos personalizados en Rust.

En el Capítulo 7, aprenderá sobre el sistema de módulos de Rust y sobre las reglas de privacidad para organizar su código y su Interfaz de Programación de Aplicaciones (API) pública. El Capítulo 8 analiza algunas colecciones de estructuras de datos comunes que proporciona la biblioteca estándar, como vectores, strings y mapas hash. El Capítulo 9 explora la filosofía y las técnicas de manejo de errores de Rust.

El Capítulo 10 explora los genéricos, los *traits* y los *lifetimes*, que le dan la capacidad de definir el código que se aplica a múltiples tipos. El Capítulo 11 trata sobre testing, que incluso con las garantías de seguridad de Rust es necesario para garantizar que la lógica de su programa sea correcta. En el Capítulo 12, construiremos nuestra propia implementación de un subconjunto de funcionalidades desde la herramienta de línea de comandos `grep` que busca texto dentro de los archivos. Para esto, usaremos muchos de los conceptos que discutimos en los capítulos anteriores.

El Capítulo 13 explora *closures* e iteradores: características de Rust que provienen de lenguajes de programación funcionales. En el Capítulo 14, examinaremos Cargo con más profundidad y hablaremos sobre las mejores prácticas para compartir sus bibliotecas con otras personas. El Capítulo 15 analiza los *puteros inteligentes* que proporciona la biblioteca estándar y los *traits* que permiten su funcionalidad.

En el Capítulo 16, analizaremos diferentes modelos de programación concurrente y hablaremos sobre cómo Rust le ayuda a programar en múltiples *hilos* (*threads*) sin miedo. El Capítulo 17 analiza cómo los modismos de Rust se comparan con los principios de programación orientada a objetos con los que puede estar familiarizado.

El Capítulo 18 es una referencia sobre patrones y concordancia de patrones, que son formas poderosas de expresar ideas a través de los programas de Rust. El Capítulo 19 contiene una mezcla heterogénea de temas de interés avanzados, incluido Rust *unsafe* y más *lifetimes*, *traits*, tipos, funciones y *closures*.

En el Capítulo 20, completaremos un proyecto en el que implementaremos un servidor web *multi-hilo* de bajo nivel.

Finalmente, algunos apéndices contienen información útil sobre el lenguaje en un formato más parecido a una referencia. El Apéndice A cubre las palabras clave de Rust, el Apéndice B cubre los operadores y símbolos de Rust, el Apéndice C cubre los *traits* derivables proporcionados por la biblioteca estándar, el Apéndice D cubre algunas herramientas de desarrollo útiles y el Apéndice E explica las ediciones de Rust. En el Apéndice F, puede encontrar traducciones del libro, y en el Apéndice G cubriremos cómo se hace Rust y qué es Rust nocturno.

No hay una manera incorrecta de leer este libro: si quiere saltarse, ¡adelante! es posible que deba volver a los capítulos anteriores si experimenta alguna confusión. Pero haga lo que sea que funcione para usted.

Una parte importante del proceso de aprendizaje de Rust es aprender a leer los mensajes de error que muestra el compilador: estos lo guiarán hacia el código que funciona. Como tal, proporcionaremos muchos ejemplos que no compilan junto con el mensaje de error que el compilador le mostrará en cada situación. Sepa que si ingresa y ejecuta un ejemplo aleatorio, ¡es posible que no se compile! Asegúrese de leer el texto que lo rodea para ver si el ejemplo que está tratando de ejecutar está destinado a un error.

En la mayoría de las situaciones, lo guiaremos a la versión correcta de cualquier código que no se compile.

> Nota del Traductor:
>
>> Algunos conceptos en el mundo de la programación están muy ligados a una palabra o expresión en el idioma inglés y/o son de uso muy habitual. En esos casos se ha decidido realizar una traducción de la mejor manera posible y sólo en los primeros usos. Esto, además de evitar hambigüedades, beneficia tanto a los lectores que ya manejan dichos conceptos, como a los que recién los descubren, dándoles la oportunidad de incorporar su utilización.

## Código fuente

*Los archivos de origen de los que se genera este libro se pueden encontrar en (versión original ingles)*:
[GitHub][book].

[book]: https://github.com/rust-lang/book/tree/main/src

Versión en español:
[GitHub][book-es].

[book-es]: https://github.com/gottigjavier/rust-book/tree/master/edition-2022/src
