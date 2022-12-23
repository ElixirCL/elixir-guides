---
section: getting-started
layout: getting-started
title: Basic operators
---

En el [capítulo anterior](/getting-started/basic-types.html), vimos que Elixir provee los operadores aritméticos: `+`, `-`, `*`, `/`. Además las funciones `div/2` para división entera y `rem/2`para obtener el resto de una división.

Elixir provee además `++` y `--` para manipular listas:

```elixir
iex> [1, 2, 3] ++ [4, 5, 6]
[1, 2, 3, 4, 5, 6]
iex> [1, 2, 3] -- [2]
[1, 3]
```

La concatenación de cadenas se realiza con `<>`:

```elixir
iex> "foo" <> "bar"
"foobar"
```

Elixir provee además tres operadores booleanos: `or`, `and` y `not`:

```elixir
iex> true and true
true
iex> false or is_atom(:example)
true
iex> not true
false
```

Estos operadores son estrictos en cuanto a que esperan operandos booleanos, es decir, algo que evalúe o bien a `true`, o a `false`, como primer argumento. Si se provee un operando no booleano, se emitirá una excepción:

```elixir
iex> 1 and true
** (BadBooleanError) expected a boolean on left-side of "and", got: 1
iex> 1 or false
** (BadBooleanError) expected a boolean on left-side of "or", got: 1
iex> not 1
** (ArgumentError) argument error
```

`or` y `and` son operadores que evitan realizar la evaluación del segundo argumento si el primero resulta suficiente para determinar el resultado.

```elixir
iex> false and raise("This error will never be raised")
false
iex> true or raise("This error will never be raised")
true
```

Además de estos operadores booleanos, Elixir provee además `||`, `&&` y `!`, que aceptan argumentos de cualquier tipo. Para estos operadores todos los valores, exceptuando `false` y `nil` evalúan a verdadero:

```elixir
# or
iex> 1 || true
1
iex> false || 11
11

# and
iex> nil && 13
nil
iex> true && 17
17

# not
iex> !true
false
iex> !1
false
iex> !nil
true
```

Como regla general, usa `and`, `or` y `not` cuando esperes booleanos. Y si cualquiera de los argumentos puede ser no-booleano, entonces usa `&&`, `||` y `!`.

Los operadores de comparación son: `==`, `!=`, `===`, `!==`, `<=`, `>=`, `<` y `>`:

```elixir
iex> 1 == 1
true
iex> 1 != 2
true
iex> 1 < 2
true
```

La diferencia entre `==` y `===` es que el segundo es estricto al comparar entre números enteros y flotantes:

```elixir
iex> 1 == 1.0
true
iex> 1 === 1.0
false
```

En Elixir podemos comparar dos tipos de datos diferentes:

```elixir
iex> 1 < :atom
true
```

La razón de ser de esta característica es pragmatismo. De esta manera los algoritmos de ordenamiento no necesitan preocuparse de los distintos tipos de datos al ordenar. El orden de los distintos tipos de datos es el siguiente:

    number < atom < reference < function < port < pid < tuple < map < list < bitstring

No es necesario memorizar esto, es suficiente saber que existe. Para mayor información mira la página de [operadores](https://hexdocs.pm/elixir/operators.html) en el manual de referencia.

En el próximo capítulo, discutiremos _pattern matching_ mediante el uso de `=`, que en Elixir se denomina "el operador de _match_ (emparejamiento)".