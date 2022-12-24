---
section: getting-started
layout: getting-started
title: case, cond, and if
---

En este capítulo aprenderemos sobre las estructuras de control `case`, `cond` e `if`.

## `case`

`case` nos permite comparar valores contra muchos patrones hasta encontrar el que hace _match_:

```elixir
iex> case {1, 2, 3} do
...>   {4, 5, 6} ->
...>     "Esta cláusula no hará match"
...>   {1, x, 3} ->
...>     "Esta cláusula hará match y ligará x a 2"
...>   _ ->
...>     "Esta cláusula hará match con cualquier valor"
...> end
"Esta cláusula hará match y ligará x a 2"
```

Si deseas hacer _match_ de patrones contra una variable existente, es necesario usar el operador de fijado `^`:

```elixir
iex> x = 1
1
iex> case 10 do
...>   ^x -> "No hará match"
...>   _ -> "Hará match"
...> end
"Hará match"
```

Las cláusulas permiten que se especifiquen condiciones extra mediante centinelas (_guards_)

```elixir
iex> case {1, 2, 3} do
...>   {1, x, 3} when x > 0 ->
...>     "Hará match"
...>   _ ->
...>     "Haría match, si la condición del centinela no fuese satisfecha"
...> end
"Hará match"
```

La primera cláusula de arriba sólo hará _match_ cuando `x` sea positivo.

Ten presente que los errores en los centinelas no se filtran, simplemente hacen que el control falle:

```elixir
iex> hd(1)
** (ArgumentError) argument error
iex> case 1 do
...>   x when hd(x) -> "No hace match"
...>   x -> "Obtuve #{x}"
...> end
"Obtuve 1"
```

Si ninguna de las cláusulas hace _match_, se dispara una excepción:

```elixir
iex> case :ok do
...>   :error -> "No hace match"
...> end
** (CaseClauseError) no case clause matching: :ok
```

Consulta la documentación completa para [centinelas](https://hexdocs.pm/elixir/patterns-and-guards.html#guards) en el manual de referencia para mayor información sobre como usarlos y qué expresiones se permiten.

Nota que las funciones anónimas también pueden tener múltiples cláusulas y centinelas:

```elixir
iex> f = fn
...>   x, y when x > 0 -> x + y
...>   x, y -> x * y
...> end
#Function<12.71889879/2 in :erl_eval.expr/5>
iex> f.(1, 3)
4
iex> f.(-1, 3)
-3
```

El número de argumentos en cada cláusula de una función anónima debe ser el mismo, sino se dispara una excepción:

```elixir
iex> f2 = fn
...>   x, y when x > 0 -> x + y
...>   x, y, z -> x * y + z
...> end
** (CompileError) iex:1: cannot mix clauses with different arities in anonymous functions
```

## `cond`

`case` es útil cuando uno necesita comparar contra diferentes valoras. Sin embargo, en muchas circunstancias, deseamos verificar diferentes condiciones y nos interesa la primera que no evalúe a `nil` o `false`. En esos casos se usa `cond`:

```elixir
iex> cond do
...>   2 + 2 == 5 ->
...>     "Esto no será veradero"
...>   2 * 2 == 3 ->
...>     "Tampoco esto"
...>   1 + 1 == 2 ->
...>     "Esto sí"
...> end
"Esto sí"
```

Esto es equivalente a las cláusulas `else if` en otros lenguajes imperativos, aunque es usada con menor frecuencia en Elixir.

Si todas las condiciones devuelven `nil` o `false`, se dispara una excepción `CondClauseError`. Por esta razón puede ser necesario agregar una condición final igual a `true`, para establecer una opción por omisión, dado que siempre hará  _match_:

```elixir
iex> cond do
...>   2 + 2 == 5 ->
...>     "Esto nunca será verdadero"
...>   2 * 2 == 3 ->
...>     "Tampoco esto"
...>   true ->
...>     "Esto es siempre verdadero (equivalente a un else)"
...> end
"Esto es siempre verdadero (equivalente a un else)"
```

Finalmente, ten presente que `cond` considera cualquier valor distinto a `nil` o `false` como verdadero:

```elixir
iex> cond do
...>   hd([1, 2, 3]) ->
...>     "1 se considera verdadero"
...> end
"1 se considera verdadero"
```

## `if` y `unless`

Además de `case` y `cond`, Elixir provee  `if/2` y `unless/2`, que son útiles cuando se necesita verificar sólo una condición:

```elixir
iex> if true do
...>   "Funciona!"
...> end
"Funciona!"
iex> unless true do
...>   "Esto no se verá"
...> end
nil
```

Si la condición dada a `if/2` devuelve `false` o `nil`, el cuerpo de código entra el `do` y el `end` no se ejecuta, y devuelve `nil`. Lo opuesto ocurre con `unless/2`.

Ambas soportan bloques `else`:

```elixir
iex> if nil do
...>   "Esto no se verá"
...> else
...>   "Esto sí"
...> end
"Esto sí"
```

Esta es una buena oportunidad para hablar de alcance de variables en Elixir. Si alguna variable es declarada o modificada dentro de un `if`, un `case`, o construcciones similares, la declaración o modificación sólo será visible dentro de la construcción. Por ejemplo:

```elixir
iex> x = 1
1
iex> if true do
...>   x = x + 1
...> end
2
iex> x
1
```

Si por el contrario, deseas modificar el valor de la variable exterior a la construcción, debes devolver el valor desde dentro de la construcción:

```elixir
iex> x = 1
1
iex> x = if true do
...>   x + 1
...> else
...>   x
...> end
2
```

> Nota: algo curioso respecto a `if/2` y `unless/2` es que se han implementado a través de macros; no son construcciones especiales del lenguaje como ocurre en otros lenguajes. Puedes ver la documentación y el código fuente de `if/2` en la documentación del  [módulo Kernel](https://hexdocs.pm/elixir/Kernel.html). El módulo `Kernel` define además operadores, como `+/2`, y funciones, como `is_function/2`. El módulo `Kernel` es importado automáticamente en tu código por omisión, esto significa que todas las funciones que contiene están disponibles sin necesidad de llamarlas con el nombre del módulo.

Hemos concluido la introducción a las estructuras de control de flujo fundamentales en Elixir. Ahora es momento de hablar de los tipos de datos _binaries_ (binarios), _strings_ (cadenas) y listas de caracteres.