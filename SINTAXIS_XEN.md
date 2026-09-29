# Sintaxis inicial de XEN

> Borrador inicial. La sintaxis se ajustará al probar nuevos casos de negocio.

## Objetivo

Representar modelos y flujos de negocio de forma simple y legible, sin describir detalles técnicos de implementación.

## Organización

- Cada archivo representa un flujo de negocio completo.
- Las instrucciones del archivo se interpretan en el orden en que aparecen.
- Cada modelo vive en su propio archivo JSON.
- Los modelos están disponibles globalmente para todos los flujos.
- Un flujo referencia los modelos, pero no vuelve a definirlos.

## Reglas generales

- La sintaxis de variables, objetos, condiciones y ciclos sigue el estilo de JavaScript.
- Los modelos se escriben en mayúsculas y en plural.
- El lenguaje no incluye asincronismo, promesas, `async` ni `await`.
- Las instrucciones representan reglas o acciones de negocio.
- Las interacciones son obligatorias por defecto.
- Una interacción opcional debe indicarse expresamente con `.optional()`.

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

El modelo relacionado se escribe en plural. El nombre de la propiedad determina la cantidad:

```js
VENTAS = {
  cliente: CLIENTES,
  libros: LIBROS
}
```

- Propiedad singular: relación con un elemento.
- Propiedad plural: relación con varios elementos.

## Kit básico

Las interacciones generales no pertenecen a ningún modelo:

```js
modal(MODELO)
drawer(MODELO)
select(MODELO)
confirm("Pregunta")
```

### `input`

Solicita al usuario el valor de un campo definido en un modelo:

```js
valor = input(MODELO.campo)
```

El campo permite identificar qué dato debe solicitarse y aplicar su tipo y sus reglas sin repetir esa información en el flujo.

### `alert`

Muestra un mensaje informativo al usuario y no solicita una respuesta:

```js
alert("Mensaje")
```

### `select`

Selecciona uno o varios elementos de un modelo.

La selección es obligatoria por defecto. Si se cancela, el flujo no continúa:

```js
elemento = select(MODELO)
```

Puede configurarse mediante propiedades encadenadas:

```js
elementos = select(MODELO)
  .min(2)
  .max(5)
```

Cuando la ausencia de selección forma parte del flujo:

```js
elemento = select(MODELO).optional()
```

### `confirm`

Representa una decisión humana obligatoria de sí o no y devuelve `true` o `false`:

```js
respuesta = confirm("¿Desea continuar?")
```

### `modal`

Muestra un elemento o resultado relacionado con un modelo:

```js
modal(MODELO)
```

### `drawer`

Muestra un elemento o resultado relacionado con un modelo dentro de un panel lateral:

```js
drawer(MODELO)
```

## CRUD

Todos los modelos disponen de las operaciones básicas:

```js
MODELO.create(...)
MODELO.read(...)
MODELO.update(...)
MODELO.delete(...)
```

Los parámetros se pasan directamente y sin estructuras adicionales innecesarias:

```js
MODELO.create(parametro1, parametro2)
```

## Variables

Las variables siguen el estilo de JavaScript:

```js
variable = valor
```

## Condiciones

Las condiciones siguen el estilo de JavaScript:

```js
if (condicion) {

}

else {

}
```

## Ciclos

Los ciclos siguen el estilo de JavaScript:

```js
while (condicion) {

}
```

## Selección múltiple de caminos

```js
switch (valor) {
  case opcion:
    break

  default:
    break
}
```

## Métodos de negocio

Los modelos pueden incorporar métodos propios para representar reglas de negocio:

```js
resultado = MODELO.metodo(parametros)
```

La sintaxis definitiva para declarar la descripción, los parámetros y el resultado de estos métodos todavía debe definirse.

## Comentarios

Los comentarios utilizan doble barra:

```js
// Descripción o aclaración
```

## Puntos pendientes

- Declaración y descripción de métodos de negocio.
- Parámetros y resultados de los métodos.
- Validaciones de campos.
- Cálculos y valores derivados.
- Estados y transiciones.
- Manejo de errores de negocio.
- Operaciones que modifican varios modelos.
