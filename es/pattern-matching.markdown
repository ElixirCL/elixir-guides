---
section: getting-started
layout: getting-started
title: Pattern matching
---

**Nota del traductor:** el término _pattern matching_ en inglés no tiene una traducción sensible en castellano, o de uso común en el ambiente informático. Debido a esto se decidió dejar la frase en inglés y además usar la palabra del inglés _match_, para indicar cuando se produce la coincidencia de patrones.

En este capítulo mostraremos como el operador `=` en Elixir es, de hecho, un operador de _match_ o emparejamiento de patrones, y como usar _pattern match_ dentro de estructuras de datos. Finalmente, aprenderemos sobre el operador de fijado `^`, usado para acceder valores ligados previamente a una variable.

## El operador de _match_

Hemos usado el operador `=` un par de veces antes para asignar variables en Elixir:

```elixir
iex> x = 1
1
iex> x
1
```

En Elixir, el operador `=` se denomina el *operador de _match_*. Veamos por qué:

```elixir
iex> x = 1
1
iex> 1 = x
1
iex> 2 = x
** (MatchError) no match of right hand side value: 1
```

Nota que `1 = x` es una expresión válida, y que no produjo error porque tanto el lado izquierdo como el derecho del operador son iguales a 1. Cuando los operandos no coinciden, se dispara una excepción `MatchError`, como se muestra en la tercer línea.

Una variable sólo puede ser asignada si se encuentra en el lado izquierdo del operador `=`:

```elixir
iex> 1 = unknown
** (CompileError) iex:1: undefined function unknown/0
```

Dado que no se definió previamente la variable `unknown`, Elixir asume que estabas tratando de llamar a la función `unknown/0`, pero dicha función no existe.

## _Pattern matching_

El operador de _match_ no sólo se usa para emparejar contra valores simples, sino que también es útil para desestructurar tipos de datos más complejos. Por ejemplo, podemos hacer _pattern matching_ en tuplas:

```elixir
iex> {a, b, c} = {:hello, "world", 42}
{:hello, "world", 42}
iex> a
:hello
iex> b
"world"
```

Un error de _match_ ocurrirá si los lados no pueden ser emparejados, por ejemplo, si las tuplas tienen tamaños diferentes:

```elixir
iex> {a, b, c} = {:hello, "world"}
** (MatchError) no match of right hand side value: {:hello, "world"}
```

También si se comparan tipos de datos diferentes, por ejemplo, si se intenta emparejar una tupla en el lado izquierdo con una lista en el derecho:

```elixir
iex> {a, b, c} = [:hello, "world", 42]
** (MatchError) no match of right hand side value: [:hello, "world", 42]
```

Curiosamente, podemos hacer _match_ en valores específicos. El ejemplo de código siguiente se asegura que el lado izquierdo hará _match_ sólo cuando el lado derecho sea una tupla que comience con el átomo `:ok`:

```elixir
iex> {:ok, result} = {:ok, 13}
{:ok, 13}
iex> result
13

iex> {:ok, result} = {:error, :oops}
** (MatchError) no match of right hand side value: {:error, :oops}
```

Podemos hacer _pattern matching_ en listas:

```elixir
iex> [a, b, c] = [1, 2, 3]
[1, 2, 3]
iex> a
1
```

Una lista además soporta hacer _matching_ en su cabeza y su cola:

```elixir
iex> [cabeza | cola] = [1, 2, 3]
[1, 2, 3]
iex> cabeza
1
iex> cola
[2, 3]
```

De manera similar a las funciones `hd/1` y `tl/1`, no podemos hacer _match_ con el patrón `[cabeza | cola]` en listas vacías:

```elixir
iex> [cabeza | cola] = []
** (MatchError) no match of right hand side value: []
```

El formato `[cabeza | cola]` no sólo se usa en _pattern matching_, sino que también es útil para agregar elementos al comienzo de una lista:

```elixir
iex> lista = [1, 2, 3]
[1, 2, 3]
iex> [0 | lista]
[0, 1, 2, 3]
```

La técnica de _pattern matching_ permite a los desarrolladores desestructurar fácilmente tipos de datos complejos como tuplas y listas. Como veremos en los próximos capítulos, es uno de los fundamentos usados para realizar recursión en Elixir, y aplica a otros tipos de datos, como mapas (_maps_) y binarios (_binaries_).

## El operador de fijado

En Elixir, las variables pueden ser ligadas a nuevos valores:

```elixir
iex> x = 1
1
iex> x = 2
2
```

Sin embargo, hay ocasiones en que no queremos que esto ocurra.

Usa el operador de fijado `^` cuando desees hacer _pattern matching_ contra el valor ligado a la variable anteriormente, en vez de ligar un nuevo valor.

```elixir
iex> x = 1
1
iex> ^x = 2
** (MatchError) no match of right hand side value: 2
```

Dado que hemos fijado el valor de `x` a 1, la segunda línea es equivalente a lo siguiente:

```elixir
iex> 1 = 2
** (MatchError) no match of right hand side value: 2
```

Nota que vemos el mismo mensaje de error.

Podemos usar el operador de fijado dentro de desestructuraciones, en tuplas y listas:

```elixir
iex> x = 1
1
iex> [^x, 2, 3] = [1, 2, 3]
[1, 2, 3]
iex> {y, ^x} = {2, 1}
{2, 1}
iex> y
2
iex> {y, ^x} = {2, 2}
** (MatchError) no match of right hand side value: {2, 2}
```

Debido a que `x` fue ligado al valor 1 al fijarlo, el último ejemplo se podría haber escrito de la siguiente manera:

```elixir
iex> {y, 1} = {2, 2}
** (MatchError) no match of right hand side value: {2, 2}
```

Si una variable se usa más de una vez en un patrón, todas las referencias estarán ligadas al mismo valor:

```elixir
iex> {x, x} = {1, 1}
{1, 1}
iex> {x, x} = {1, 2}
** (MatchError) no match of right hand side value: {1, 2}
```

En algunas ocasiones no te importará un valor particular en un patrón. Es práctica común ligar esos valores que no te interesan a la variable `_` o a variables que comiencen con este caracter. Por ejemplo, si sólo nos interesa la cabeza de una lista, podemos asignar la cola a `_`:

```elixir
iex> [cabeza | _] = [1, 2, 3]
[1, 2, 3]
iex> cabeza
1
```

La variable `_` es especial: no se puede leer su contenido. Si se intenta leer su contenido Elixir lanza una excepción de error de compilación (`CompileError`):

```elixir
iex> _
** (CompileError) iex:1: invalid use of _. "_" represents a value to be ignored in a pattern and cannot be used in expressions
```

Aunque el _pattern matching_ nos permite realizar construcciones poderosas, su uso es limitado. Por ejemplo, no se pueden hacer llamadas a funciones en el lado izquierdo del _match_. El siguiente ejemplo es inválido:

```elixir
iex> length([1, [2], 3]) = 3
** (CompileError) iex:1: cannot invoke remote function :erlang.length/1 inside match
```

Esto concluye la introducción a _pattern matching_. Como veremos en el próximo capítulo, _pattern matching_ es muy común en muchas construcciones del lenguaje.