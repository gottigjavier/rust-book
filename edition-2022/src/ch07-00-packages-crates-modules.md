# Gestión de Proyectos en Crecimiento con Packages, Crates y Modules

A medida que escriba programas grandes, la organización de su código será cada vez más importante. Al agrupar funcionalidad relacionada y separar código con características distintas, aclarará dónde encontrar el código que implementa una característica particular y dónde ir para cambiar el funcionamiento de una característica.

Los programas que hemos escrito hasta ahora han estado en un módulo *(module)* en un archivo. A medida que crece un proyecto, debe organizar el código dividiéndolo en múltiples módulos y luego en múltiples archivos. Un *package* (paquete) puede contener varios *crates*[^1] binarios y, opcionalmente, un *crate* librería. A medida que crece un package, puede extraer partes en *crates* separados que se convierten en dependencias externas. Este capítulo cubre todas estas técnicas. Para proyectos muy grandes que comprenden un conjunto de packages interrelacionados que evolucionan juntos, Cargo proporciona *workspaces* (espacios de trabajo), que cubriremos en la sección ["Espacios de Trabajo de Cargo"](ch14-03-cargo-workspaces.html) en el Capítulo 14.

También hablaremos sobre encapsulación de detalles de implementación, lo que le permite reutilizar código a un nivel superior: una vez que haya implementado una operación, otro código puede llamar a su código a través de su interfaz pública sin tener que saber cómo funciona la implementación. La forma en que escribe el código define qué partes son públicas para que las use otro código y qué partes son detalles de implementación privados que se reserva el derecho de cambiar. Esta es otra forma de limitar la cantidad de detalles que debe tener en mente.

Un concepto relacionado es *scope*: el contexto anidado en el cual se escribe el código tiene un conjunto de nombres que son definidos como *"in scope"* ("dentro del ámbito"). Al leer, escribir y compilar código, los programadores y compiladores necesitan saber si un nombre en particular en un lugar en particular se refiere a una variable, función, struct, enum, módulo, constante u otro elemento y qué significa ese elemento. Puede crear scopes y cambiar qué nombres están dentro o fuera del scope. No puede tener dos elementos con el mismo nombre en el mismo scope. Hay herramientas disponibles para resolver conflictos de nombres.

Rust tiene una serie de características que le permiten administrar la organización de su código, que incluyen cuáles detalles se exponen, cuáles detalles son privados y qué nombres se encuentran en cada ámbito de sus programas. Estas características, a veces denominadas colectivamente como *module system* (sistema de módulos), incluyen:

* **Packages:** Una característica de Cargo que le permite construir, testear y compartir *crates*
* **Crates:**  Un árbol de módulos el cual produce una librería o ejecutable
* **Modules** y **use:** Le permite controlar la organización, el scope y la privacidad de las rutas (paths)
* **Paths:** Una forma de nombrar un elemento, como un struct, función o módulo

En este capítulo, cubriremos todas estas características, discutiremos cómo interactúan y explicaremos cómo usarlas para administrar el scope. Al final, debe tener una comprensión sólida del *module system* y poder ¡trabajar con scopes como un profesional!

</br></br></br>

[^1] <small>*crate* puede traducirse como "caja", también como "contenedor, caja o cajón de embalaje"</small>