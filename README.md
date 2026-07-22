# Modelo Entidad-Relación — Sistema de Ventas de Empanadas

Documentación del proceso de aprendizaje seguido para construir, paso a paso, el modelo E-R de un sistema de ventas de empanadas (pedidos, estado de pago, historial de fiados/pagos), tomando como referencia real la app desarrollada en Firebase.

## Objetivo

Aprender a construir un modelo entidad-relación desde cero, aplicando la metodología a un caso real y propio, en vez de recibir el diagrama ya hecho.

## Metodología usada

El proceso se dividió en pasos, siguiendo el método clásico de modelado E-R:

1. Describir el negocio en una historia simple
2. Sacar los sustantivos candidatos a entidades
3. Clasificar cada palabra como **entidad** o **atributo**
4. Listar los atributos completos de cada entidad
5. Identificar la llave primaria de cada entidad
6. Definir las relaciones entre entidades
7. Definir la cardinalidad de cada relación
8. Traducir todo a las formas del diagrama (notación Chen y Crow's Foot)

## Prueba de las 3 preguntas (para decidir entidad vs. atributo)

Para cada palabra candidata se aplicaron estas tres preguntas:

1. ¿Se repite muchas veces con datos propios y distintos?
2. ¿Tiene sentido por sí mismo sin depender de otra cosa?
3. ¿Es solo una característica de algo más grande?

Si la respuesta apunta a "tiene datos propios y existe con independencia" → **entidad**.
Si la respuesta apunta a "es solo un dato que describe a otra cosa" → **atributo**.

## Clasificación resultante (sistema actual, análisis inicial)

| Palabra | Clasificación | Justificación |
|---|---|---|
| Pedido | Entidad | Cada uno tiene fecha, cantidad y estado propios; se repite muchas veces |
| Nombre del cliente | Atributo | No tiene ID propio; el mismo nombre mal escrito se trata como alguien distinto |
| Pastel/Empanada | Atributo | No hay catálogo de tipos; solo un precio único configurable |
| Cantidad | Atributo | Solo tiene sentido dentro de un pedido |
| Estado (pedido/pagado) | Atributo | Es un valor de dos opciones, sin datos propios adicionales |
| Método de pago | Atributo | Es un valor de dos opciones (efectivo/transferencia), sin datos propios |

**Conclusión inicial:** el sistema actual tiene una sola entidad real, **Pedido**, con todo lo demás como atributos.

## Atributos finales de Pedido (verificados contra Firebase real)

| Atributo | Tipo de dato | Notas |
|---|---|---|
| id_pedido (key de Firebase) | Texto único autogenerado | Llave primaria |
| nombreDeudor | Texto | Nombre del cliente |
| cantidad | Entero | Cantidad de empanadas |
| estado | Texto | "pagado" / "pedido" |
| metodoPago | Texto | "efectivo" / "transferencia" |
| fecha | Fecha (texto) | Fecha legible |
| timestamp | Número (milisegundos) | Usado para ordenar registros |
| total | Decimal | cantidad × precio (atributo derivado) |

## Evolución del modelo: separar Cliente como entidad

Aunque el sistema actual no maneja Cliente como entidad independiente, se decidió evolucionar el modelo (sin necesidad de que coincida con el sistema real) para practicar relaciones y cardinalidad.

**Relación definida:**

- Verbo: **Realiza**
- Cardinalidad: **Cliente (1) —— (N) Pedido**
- Lectura: un cliente puede realizar muchos pedidos, pero cada pedido pertenece a un solo cliente

## Notación Chen — formas y su caso de uso

| Forma | Uso |
|---|---|
| Rectángulo | Representa una entidad |
| Óvalo | Representa un atributo |
| Óvalo con texto subrayado | Marca la llave primaria |
| Óvalo doble | Atributo multivaluado (puede tener varios valores a la vez) |
| Óvalo con línea punteada | Atributo derivado (se calcula a partir de otros, ej: total) |
| Rombo | Representa una relación entre dos entidades |
| Rectángulo doble | Entidad débil (no existe sin depender de otra) |
| Números/letras junto al rombo (1, N, M) | Cardinalidad de la relación |

## Notación Crow's Foot (pata de gallo) — formas y su caso de uso

| Símbolo | Uso |
|---|---|
| Rectángulo con líneas divisorias | Entidad, mostrando directamente sus atributos con tipo de dato |
| Línea simple entre tablas | Relación (equivalente al rombo en Chen) |
| Rayita corta (\|) | Cardinalidad "uno" |
| Círculo (o) | Cardinalidad "cero" (opcional) |
| Pata de gallo (<) | Cardinalidad "muchos" |

## Diagrama parcial logrado (notación Chen)

```
[Cliente] ——1———< N ——(Realiza)—— [Pedido]
```

Pendiente por completar: atributos propios de la entidad Cliente (id_cliente como llave primaria, nombre, y los que se definan) y los óvalos de atributos de Pedido conectados a su rectángulo.

## Historial completo de prompts dados por el estudiante (orden cronológico, texto literal)

**Prompt 1:**
> soy estudiante de programacion y quiero aprender base de datos ,en este caso , modelo entidad-relacion
>
> tengo un proyecto en el cual es un sistema de ventas de empanadas, aqui lo he desarrollado , en este programa registro ventas, tomo el pedido de cuantos pasteles, agrego nombre y efectuo la compra o en espera si no ha pagado
>
> muestra el historial de las ventas , fiados- pagos

**Prompt 2:**
> entiendo, no quiero que me des como tal como se ve el diagrama, porque quiero crearlo yo mismo, necesito que me indiques paso a paso como podemos desarrollarlo para poder aprender, entonces teniendo en cuenta que necesitas saber de mi sistema para que tengas una idea clara

**Prompt 3:**
> en estos momentos sobre el proceso de venta:
> el cliente hace un pedido y mientras no ha pagado queda el pedido ahi en pendiente y cuando cancela se registra la venta, puede tomar varios pedidos de pasteles el en primer pedido ya que le puedo modificar la cantidad y listo, en el historial se registra la venta y la que esta pendiente en el momento
> -no guardo, los pasteles tiene un precio predeterminado
>
> sobre el cliente:
>
> - el cliente guardo el nombre y registro si es efectivo o trasnferencia (si no ha pagado queda en espera)
>
> - se guardan por nombres, de hecho tengo una grafica que clasifica las cantidades de empanadas que han pedido, ya si escribo una letra mal en el nombre se registra como uno nuevo pero si hace otro pedido con el mismo nombre crea otro producto pero si quiere pedir mas pasteles entro en el pedido y le aumento la cantidad
>
> sobre el estado /fiado
>
> - vuelvo y entro en la tarjeta donde tome el peidod y le doy pago ,(no queda como fiado si no como "PEDIDO")
>
> - en estos momentos solo registra la cantidad del precio del pasteles pero los puedo modificar desde configuracion (solo el precio)

**Prompt 4:**
> explicame muy bien el de crear la lista de sustantivos, en este caso sera las veces que se guardaran los datos, por ejemplo el nombre(id) , la venta, cantidades de pasteles, registro ...?

**Prompt 5:**
> si

**Prompt 6:**
> pedido , entidad

**Prompt 7:**
> atributo

**Prompt 8:**
> pastel- entidad, cantidad- atributo

**Prompt 9:**
> no tiene varios pasteles, solo es validar la venta , en mi sistema solo registro la venta del pastel que es el precio 3000 , no muestra mas nada

**Prompt 10:**
> estado- entidad, metodo- entidad

**Prompt 11:** (imagen adjunta: captura de pantalla de la tarjeta de pedido de "Juan" en la app)

**Prompt 12:**
> 1: como es de uso personal y pequeño cuando es asi agrego la letra del primer apellido o las iniciales del salon y ya

**Prompt 13:**
> ajusta eso, lo que pasa es error de sistema y su logica eso despues lo acomodo, en estos momentos necesito crear la base de datos y su diagrama , el sistema es un ejemplo no como tal tiene que ser igual, mas adelante lo iremos acomplando

**Prompt 14:** (imagen adjunta: captura de la estructura real en Firebase, colección `ventas` con varias keys)

**Prompt 15:** (imagen adjunta: captura del contenido de una venta expandida — cantidad, estado, fecha, nombreDeudor, timestamp, total)

**Prompt 16:**
> aqui si, solo que esos datos era antes de actualizar mi sistema , aqui si aparece
>
> (imagen adjunta: captura de otra venta expandida, incluyendo el campo metodoPago)

**Prompt 17:**
> ahora explicame que componentes se usa o formas para cada cosa, no me digas donde va si no el caso de uso de las formas

**Prompt 18:**
> bien y en el caso que puedo hacer muchos pedidos , eso no iria antes?

**Prompt 19:**
> hagamos la opcion b sin necesidad que sea igual a mi sistema, recuerda que solo era una guia

**Prompt 20:**
> a: muchos pedidos o uno como sea
> b: un pedido, un solo cliente

**Prompt 21:** (imagen adjunta: captura del diagrama dibujado por el estudiante — Cliente —1—<N— Realiza —Pedido)

**Prompt 22:**
> generame un readme, en el cual contenga lo que se hablo sobre el estudio y los prompts que yo di para la documentacion

**Prompt 23:**
> quiero que los prompt que hice salgan completo mas que todo son los promps

## Próximos pasos sugeridos

- Definir atributos propios de la entidad Cliente
- Agregar los óvalos de atributos a Pedido en el diagrama
- Evaluar si conviene separar también Producto como entidad
- Pasar el modelo final a DBML (dbdiagram.io) o script SQL para crear la base de datos
