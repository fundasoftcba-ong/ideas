# Sintaxis inicial de XEN

> Borrador inicial. Esta sintaxis se irá ajustando al probar casos reales de negocio.

## Objetivo

Representar modelos y flujos de negocio de forma simple y legible, sin describir detalles técnicos de implementación.

## Reglas generales

- La sintaxis básica de variables, objetos, condiciones y ciclos sigue el estilo de JavaScript.
- Los modelos se escriben en mayúsculas y en plural.
- El lenguaje no incluye asincronismo, promesas, `async` ni `await`.
- Las instrucciones representan reglas o acciones de negocio.
- Los componentes y operaciones son obligatorios por defecto.
- Una operación opcional debe indicarse expresamente con `.optional()`.

## Modelos

Un modelo se representa mediante un objeto plano:

```js
PERSONAS = {
  id: 0,
  nombre: "",
  apellido: ""
}
```

## Relaciones

El modelo relacionado siempre se escribe en plural. El nombre de la propiedad determina la cantidad:

```js
VENTAS = {
  cliente: CLIENTES, // una relación
  libros: LIBROS     // varias relaciones
}
```

- Propiedad singular: un elemento.
- Propiedad plural: varios elementos.

## Componentes

Los componentes reciben directamente un modelo:

```js
modal(PERSONAS)
drawer(PERSONAS)
select(PERSONAS)
```

## Propiedades encadenadas

Los componentes pueden configurarse encadenando propiedades:

```js
personas = select(PERSONAS)
  .min(2)
  .max(5)
```

## Selección obligatoria y opcional

`select` exige una selección por defecto. Si se cancela, el flujo no continúa.

```js
persona = select(PERSONAS)
```

Cuando no encontrar o no elegir un elemento forma parte del negocio:

```js
persona = select(PERSONAS).optional()
```

## CRUD

Todos los modelos disponen de las operaciones básicas:

```js
PERSONAS.create(...)
PERSONAS.read(...)
PERSONAS.update(...)
PERSONAS.delete(...)
```

Los parámetros se pasan directamente, evitando objetos innecesarios:

```js
VENTAS.create(cliente, libro)
```

## Lógica

Se utilizan las condiciones y ciclos habituales de JavaScript:

```js
if (condicion) {

}

while (condicion) {

}

switch (valor) {
  case opcion:
    break

  default:
    break
}
```

## Métodos de negocio

Los modelos pueden incorporar métodos propios. La forma definitiva de declarar su descripción y sus parámetros todavía debe definirse.

Ejemplo de uso:

```js
persona = PERSONAS.mayor(edad)
```

## Ejemplo: venta de un libro

```js
cliente = select(CLIENTES).optional()

if (!cliente) {
  cliente = CLIENTES.create()
}

libro = select(LIBROS)

venta = VENTAS.create(cliente, libro)

modal(VENTAS)
```

Este flujo representa:

1. Buscar un cliente existente.
2. Crear el cliente si no existe.
3. Seleccionar un libro.
4. Crear la venta.
5. Mostrar la venta resultante.

## Puntos pendientes

- Sintaxis para declarar y describir métodos de negocio.
- Validaciones de campos.
- Cálculos, descuentos y totales.
- Estados y transiciones.
- Manejo de errores de negocio.
- Operaciones que modifican varios modelos, como confirmar una venta y descontar stock.
