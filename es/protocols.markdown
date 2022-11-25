---
section: getting-started
layout: getting-started
title: Protocolos
---

Los protocolos son un mecanismo para llegar al polimorfismo en Elixir cuando se desea que el comportamiento varíe según el tipo de datos. Ya estamos familiarizados con una manera de resolver este tipo de problema: utilizando _pattern matching_ o utilizando clausulas _guard_. Considere un módulo utilitario simple el cual nos diría el tipo de variable de entrada:

```elixir
defmodule Utility do
  def type(value) when is_binary(value), do: "string"
  def type(value) when is_integer(value), do: "integer"
  # ... other implementations ...
end
```

Si el uso de este módulo se limitara a su propio proyecto, podría seguir definiendo nuevas funciones `type/1` para cada nuevo tipo de datos. Sin embargo, este código podría ser problemático si varias aplicaciones lo compartieran como una dependencia porque no habría una manera fácil de extender su funcionalidad.

Aquí es donde los protocolos pueden ayudarnos: los protocolos nos permiten extender el comportamiento original para tantos tipos de datos como sea necesario. Esto se debe a que **el envío en un protocolo está disponible para cualquier tipo de datos que haya implementado el protocolo** y un protocolo puede ser implementado por cualquier, en cualquier momento.

Así es como podríamos escribir la misma funcionalidad `Utility.type/1` como un protocolo:

```elixir
defprotocol Utility do
  @spec type(t) :: String.t()
  def type(value)
end

defimpl Utility, for: BitString do
  def type(_value), do: "string"
end

defimpl Utility, for: Integer do
  def type(_value), do: "integer"
end
```

Definimos el protocolo utilizando `defprotocol` - sus funciones y especificaciones pueden parecer similares a las interfaces o clases base abstractas en otros lenguajes. Podemos agregar tantas implementaciones como queramos usando `defimpl`. La salida es exactamente la misma que si tuviéramos un solo módulo con múltiples funciones:

```elixir
iex> Utility.type("foo")
"string"
iex> Utility.type(123)
"integer"
```

Con los protocolos, sin embargo, ya no estamos obligados a modificar continuamente el mismo módulo para admitir más y más tipos de datos. Por ejemplo, podríamos obtener las llamadas `defimpl` anteriores y distribuirlas en varios archivos y Elixir enviariá la ejecución a la implementación adecuada según el tipo de datos. Las funciones definidas en un protocolo pueden tener más de una entrada, pero la **ejecución siempre se basará en el tipo de datos de la primera entrada**.

Uno de los protocolos más comunes que se puede encontrar es el protocolo [`String.Chars`](https://hexdocs.pm/elixir/String.Chars.html): implementando su función `to_string/1` para estructuras personalizadas dirá al núcleo de Elixir cómo representarlos como cadenas. Exploraremos todos los protocolos integrados más adelante. Por ahora, implementemos el nuestro.

## Ejemplo

Ahora que hemos visto un ejemplo del tipo de problemas que los protocolos ayudan a resolver y cómo los resuelven, veamos un ejemplo más detallado.

En Elixir, tenemos dos modismos para comprobar cuántos elementos hay en una estructura de datos: `length` y `size`. `length` significa que la información debe ser computada. Por ejemplo, `length(list)` necesita recorrer toda la lista para calcular su longitud. Por otro lado, `tuple_size(tuple)` y `byte_size(binary)` no dependen de la tupla y tamaño binario ya que la información de tamaño se calcula previamente en la estructura de datos.

Incluso si tenemos funciones específicas de tipo para obtener el tamaño integrado en Elixir (como `tuple_size/1`), podríamos implementar un protocolo genérico de `Size` que implementarían todas las estructuras de datos para las que el tamaño se calculó previamente.

La definición del protocolo se vería así:

```elixir
defprotocol Size do
  @doc "Calculates the size (and not the length!) of a data structure"
  def size(data)
end
```

El protocolo `Size` espera que se implemente una función llamada `tamaño` que recibe un argumento (la estructura de datos de la que queremos saber el tamaño). Ahora podemos implementar este protocolo para las estructuras de datos que tendrían una implementación compatible:

```elixir
defimpl Size, for: BitString do
  def size(string), do: byte_size(string)
end

defimpl Size, for: Map do
  def size(map), do: map_size(map)
end

defimpl Size, for: Tuple do
  def size(tuple), do: tuple_size(tuple)
end
```

No implementamos el protocolo `Size` para las listas ya que no hay información de "tamaño" calculado previamente para las listas, y la longitud de una lista debe ser calculada (con `length/1`).

Ahora, con el protocolo definido y las implementaciones disponibles, podemos comenzar a utilizarlo:

```elixir
iex> Size.size("foo")
3
iex> Size.size({:ok, "hello"})
2
iex> Size.size(%{label: "some label"})
1
```

Pasar un tipo de datos que no implementa el protocolo genera un error:

```elixir
iex> Size.size([1, 2, 3])
** (Protocol.UndefinedError) protocol Size not implemented for [1, 2, 3] of type List
```

Es posible implementar protocolos para todos los tipos de datos de Elixir:

* `Atom`
* `BitString`
* `Float`
* `Function`
* `Integer`
* `List`
* `Map`
* `PID`
* `Port`
* `Reference`
* `Tuple`

## Protocolos and estructuras

El poder de la extensibilidad de Elixir surge cuando los protocolos y las estructuras se utlizan de manera conjunta.

En el [capítulo anterior](/getting-started/structs.html), aprendimos que aunque las estructuras son mapas, no comparten implementaciones de protocolo con los mapas. Por ejemplo, [`MapSet`](https://hexdocs.pm/elixir/MapSet.html)s (conjuntos basados en mapas) se implementan como estructuras. Intentemos utilizar el protocolo `Size` con un `MapSet`:

```elixir
iex> Size.size(%{})
0
iex> set = %MapSet{} = MapSet.new
MapSet.new([])
iex> Size.size(set)
** (Protocol.UndefinedError) protocol Size not implemented for MapSet.new([]) of type MapSet (a struct)
```

En lugar de compartir la implementación del protocolo con mapas, las estructuras requieren su propia implementación de protocolo. Dado que un `MapSet` tiene su tamaño precalculado y accesible a través de `MapSet.size/1`, podemos definir una implementación de 'Tamaño' para él:

```elixir
defimpl Size, for: MapSet do
  def size(set), do: MapSet.size(set)
end
```

Si lo desea, puede crear su propia semántica para el tamaño de su estructura. No solo eso, podría usar estructuras para crear tipos de datos más sólidos, como colas, e implementar todos los protocolos relevantes, como `Enumerable` y posiblemente `Size`, para este tipo de datos.

```elixir
defmodule User do
  defstruct [:name, :age]
end

defimpl Size, for: User do
  def size(_user), do: 2
end
```

## Implementar `Any`

La implementación manual de protocolos para todos los tipos puede volverse rápidamente repetitiva y tediosa. En tales casos, Elixir ofrece dos opciones: podemos derivar explícitamente la implementación del protocolo para nuestros tipos o implementar automáticamente el protocolo para todos los tipos. En ambos casos, necesitamos implementar el protocolo para `Any`.

### Derivando

Elixir nos permite derivar una implementación de protocolo basada en la implementación `Any`. Primero implementemos `Any` de la siguiente manera:

```elixir
defimpl Size, for: Any do
  def size(_), do: 0
end
```

Podría decirse que la implementación anterior no es razonable. Por ejemplo, no tiene sentido decir que el tamaño de un `PID` o un `Integer` es `0`.

Sin embargo, si estamos de acuerdo con la implementación de `Any`, para usar dicha implementación, necesitaríamos decirle a nuestra estructura que derive explícitamente el protocolo `Size`:

```elixir
defmodule OtherUser do
  @derive [Size]
  defstruct [:name, :age]
end
```

Al derivar, Elixir implementará el protocolo `Size` para `OtherUser` en función de la implementación proporcionada para `Any`.

### Alternativa a `Any`

Otra alternativa a `@derive` es decirle explícitamente al protocolo que recurra a `Any` cuando no se pueda encontrar una implementación. Esto se puede lograr asignando a `@fallback_to_any` el valor `true` en la definición del protocolo:

```elixir
defprotocol Size do
  @fallback_to_any true
  def size(data)
end
```

Como dijimos en la sección anterior, la implementación de `Size` para `Any` no se puede aplicar a ningún tipo de datos. Esa es una de las razones por las que `@fallback_to_any` es un comportamiento de aceptación. Para la mayoría de los protocolos, generar un error cuando un protocolo no está implementado es el comportamiento adecuado. Dicho esto, suponiendo que hayamos implementado `Any` como en la sección anterior:

```elixir
defimpl Size, for: Any do
  def size(_), do: 0
end
```

Ahora se considerará que todos los tipos de datos (incluidas las estructuras) que no hayan implementado el protocolo `Size` tienen un tamaño `0`.

La mejor técnica entre derivar y recurrir a `Any` depende del caso de uso pero, dado que los desarrolladores de Elixir prefieren lo explícito a lo implícito, es posible que vea muchas librerías incentivando el enfoque `@derive`.

## Protocolos incorporados

Elixir cuenta con algunos protocolos incorporados. En capítulos anteriores, hemos discutido el módulo `Enum` que proporciona muchas funciones que permiten trabajar con cualquier estructura de datos que implemente el protocolo `Enumerable`:

```elixir
iex> Enum.map([1, 2, 3], fn x -> x * 2 end)
[2, 4, 6]
iex> Enum.reduce(1..3, 0, fn x, acc -> x + acc end)
6
```

Otro ejemplo útil es el protocolo `String.Chars`, que especifica cómo convertir una estructura de datos a su representación humana como una cadena. Está expuesto a través de la función `to_string`:

```elixir
iex> to_string :hello
"hello"
```

Observe que la interpolación de cadenas en Elixir llama a la función `to_string`:

```elixir
iex> "age: #{25}"
"age: 25"
```

El fragmento anterior solo funciona porque los números implementan el protocolo `String.Chars`. Pasar una tupla, por ejemplo, producirá un error:

```elixir
iex> tuple = {1, 2, 3}
{1, 2, 3}
iex> "tuple: #{tuple}"
** (Protocol.UndefinedError) protocol String.Chars not implemented for {1, 2, 3} of type Tuple
```

Cuando existe la necesidad de "imprimir" una estructura de datos más compleja, se puede utilizar la función `inspect`, basada en el protocolo `Inspect`:

```elixir
iex> "tuple: #{inspect tuple}"
"tuple: {1, 2, 3}"
```

El protocolo `Inspect` es el protocolo utilizado para transformar cualquier estructura de datos en una representación textual legible. Esto es lo que usan herramientas como IEx para imprimir resultados:

```elixir
iex> {1, 2, 3}
{1, 2, 3}
iex> %User{}
%User{name: "john", age: 27}
```

Tenga en cuenta que, por convención, cada vez que el valor inspeccionado comienza con `#`, representa una estructura de datos en una sintaxis Elixir no válida. Esto significa que el protocolo de inspección no es reversible ya que se puede perder información en el camino:

```elixir
iex> inspect &(&1+2)
"#Function<6.71889879/1 in :erl_eval.expr/5>"
```

Hay otros protocolos en Elixir pero esto abarca los más comunes. Podrá obtener más información sobre protocolos e implementaciones en el módulo [`Protocol`](https://hexdocs.pm/elixir/Protocol.html).
