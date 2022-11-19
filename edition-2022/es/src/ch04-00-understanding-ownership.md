# Comprender la Propiedad (Ownership)

La propiedad (*Ownership*) es la característica más exclusiva de Rust y tiene profundas implicaciones para el resto del lenguaje. Permite a Rust garantizar la seguridad de la memoria sin necesidad de un recolector de basura, por lo que es importante comprender cómo funciona la propiedad. En este capítulo, hablaremos sobre la propiedad, así como sobre varias características relacionadas: préstamo (*borrowing*), segmentos (*slices*) y cómo Rust distribuye los datos en la memoria.

> Nota del traductor:
>> En la programación orientada a objetos es habitual el uso de la palabra *propiedad* como característica de un objeto. Esto puede llevar a cierta confusión debido a que en el idioma español, *propiedad* puede referirse tanto a una característica como a una posesión.
>>
>> Se debe enfatizar entonces que en este capítulo ***propiedad = posesión***.