# Sintaxis inicial de XEN

> Borrador inicial. Esta sintaxis se irá ajustando al probar casos reales de negocio.

## Objetivo

Representar modelos y flujos de negocio de forma simple y legible, sin describir detalles técnicos de implementación.

## Organización

- Cada archivo de flujo representa un flujo de negocio completo.
- Los pasos del archivo se interpretan en el orden en que aparecen.
- Los modelos viven en archivos JSON independientes y están disponibles globalmente para los flujos.
- Un flujo referencia los modelos, pero no vuelve a definirlos.

## Reglas generales

- La sintaxis básica de variables, objetos, condiciones y ciclos sigue el estilo de JavaScript.
- Los modelos se escriben en mayúsculas y en plural.
- El lenguaje no incluye asincronismo, promesas, `async` ni `await`.
- Las instrucciones representan reglas o acciones de negocio.
- Los componentes y operaciones son obligatorios por defecto.
- Una operación opcional debe indicarse expresamente con `.optional()`.

## Modelos

Cada modelo se guarda como un objeto plano en su propio archivo JSON:

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

## Kit básico

El kit inicial contiene interacciones generales que no pertenecen a un modelo:

```js
modal(PERSONAS)
drawer(PERSONAS)
select(PERSONAS)
confirm("¿Desea continuar?")
```

### Confirmación humana

`confirm` representa una decisión obligatoria de sí o no y devuelve `true` o `false`:

```js
reservar = confirm("¿Desea reservar los libros sin stock?")

if (reservar) {
  RESERVAS.create(cliente, libros)
}
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

Ejemplos de uso:

```js
persona = PERSONAS.mayor(edad)
hay_stock = LIBROS.stock_suficiente(libros)
```

## Ejemplo: venta parcial con reserva opcional

Archivo de flujo `venta_libros.xen`:

```js
cliente = select(CLIENTES).optional()

if (!cliente) {
  cliente = CLIENTES.create()
}

libros = select(LIBROS)
  .min(1)
  .max(3)

if (LIBROS.stock_suficiente(libros)) {
  venta = VENTAS.create(cliente, libros)
  comprobante = COMPROBANTES.create(venta)
}

else {
  disponibles = LIBROS.disponibles(libros)
  faltantes = LIBROS.faltantes(libros)

  reservar = confirm("¿Desea reservar los libros sin stock?")

  venta = VENTAS.create(cliente, disponibles)

  if (reservar) {
    reserva = RESERVAS.create(cliente, faltantes)
    comprobante = COMPROBANTES.create(venta, reserva)
  }

  else {
    comprobante = COMPROBANTES.create(venta)
  }
}

modal(COMPROBANTES)
```

Este flujo representa:

1. Buscar o crear el cliente.
2. Seleccionar los libros.
3. Comprobar el stock.
4. Vender todos los libros cuando hay stock suficiente.
5. Cuando falta stock, vender los disponibles.
6. Preguntar al cliente si desea reservar los faltantes.
7. Crear la reserva solamente si el cliente la confirma.
8. Generar un comprobante y mostrarlo.

## Puntos pendientes

- Sintaxis para declarar y describir métodos de negocio.
- Validaciones de campos.
- Cálculos, descuentos y totales.
- Estados y transiciones.
- Manejo de errores de negocio.
- Operaciones que modifican varios modelos, como confirmar una venta y descontar stock.
