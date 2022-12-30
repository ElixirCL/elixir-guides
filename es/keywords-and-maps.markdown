---
section: getting-started
layout: getting-started
title: Listas de claves y diccionarios
redirect_from: /getting-started/maps-and-dicts.html
---

Ahora hablemos de estructuras de datos asociativas. Las estructuras de datos
asociativas sirven para asociar una clave con cierto valor. Los lenguajes de
programación usan distintos nombres para estas estructuras: diccionarios,
_hashes_, arreglos asociativos, etc.

En Elixir, tenemos dos estructuras de datos asociativas: listas de claves y
diccionarios.

## Listas de claves

Las listas de claves son estructuras de datos usadas para pasar opciones a
funciones. Imagina que deseas dividir una cadena de números. Podemos usar
`String.split/2`:

```elixir
iex> String.split("1 2 3", " ")
["1", "2", "3"]
```

Sin embargo... ¿Qué pasa si hay espacios adicionales entre los números?:

```elixir
iex> String.split("1  2  3", " ")
["1", "", "2", "", "3"]
```

Como puedes ver, ahora hay cadenas vacías en nuestro resultado. Por suerte la
funcion `String.split/3` nos permite usar la opción `trim` para eliminar estas
cadenas:

```elixir
iex> String.split("1  2  3", " ", [trim: true])
["1", "2", "3"]
```

`[trim: true]` es una lista de claves. Además, cuando una lista de claves es el
último argumento en una función, podemos omitir los corchetes y escribir:


```elixir
iex> String.split("1  2  3", " ", trim: true)
["1", "2", "3"]
```

Como el nombre implica, las listas de claves son simplemente listas. En
particular, las listas de claves son tuplas de 2 elementos, donde el primer
elemento, la clave, es un átomo y el segundo elemento puede ser cualquier
valor. Ambas formas de representarlas son correctas:

```elixir
iex> [{:trim, true}] == [trim: true]
true
```

Dado que las listas de claves son listas, podemos usar todas las operaciones
disponibles para listas. Por ejemplo, podemos usar `++` para agregar un nuevo
valor a la lista:

```elixir
iex> list = [a: 1, b: 2]
[a: 1, b: 2]
iex> list ++ [c: 3]
[a: 1, b: 2, c: 3]
iex> [a: 0] ++ list
[a: 0, a: 1, b: 2]
```

Puedes leer el valor de una lista de claves usando la sintaxis de corchetes:

```elixir
iex> list[:a]
1
iex> list[:b]
2
```

En caso de claves duplicadas, los valores que se encuentran primero son los que
aparecen antes. Esto es debido a que, como mencionamos antes, las listas se
recorren desde el principio para encontrar sus valores:

```elixir
iex> new_list = [a: 0] ++ list
[a: 0, a: 1, b: 2]
iex> new_list[:a]
0
```

Las listas de claves tienen las siguientes características:

  * Sus claves **deben** ser átomos.
  * Sus claves están ordenadas (orden de inserción).
  * Se pueden repetir claves.

Por ejemplo, la biblioteca [Ecto](https://github.com/elixir-lang/ecto) hace uso
de estas características para proveer de un <abbr title="Domain-Specific
Language">DSL</abbr> para escribir consultas <abbr title="Structured Query
Language">SQL</abbr>:

```elixir
query =
  from w in Weather,
    where: w.prcp > 0,
    where: w.temp < 20,
    select: w
```

Aunque podemos hacer _pattern matching_ en listas de claves, en la práctica
rara vez se usa, dado que parar poder hacer _match_ en listas se requiere tanto
la cantidad de elementos como su orden correctos para que coindidan:

```elixir
iex> [a: a] = [a: 1]
[a: 1]
iex> a
1
iex> [a: a] = [a: 1, b: 2]
** (MatchError) no match of right hand side value: [a: 1, b: 2]
iex> [b: b, a: a] = [a: 1, b: 2]
** (MatchError) no match of right hand side value: [a: 1, b: 2]
```

Para manipular listas de claves, Elixir proveee el módulo
[`Keyword`](https://hexdocs.pm/elixir/Keyword.html). Recuerda, sin embargo, que
las listas de claves son simplemente listas y, como tales, su rendimiento es
lineal: mientras más larga la lista, más tiempo tomará encontrar una clave,
contar la cantidad de elementos, etc. Por esta razón, las listas de claves se
usan principalmente para pasar parametros opcionales a las funciones. Si
necesitas almacenar muchos elementos o garantizar que las asociaciones
clave-valor sean únicas, debes usar diccionarios.

## Diccionarios

Cuando necesites almacenar valores tipo clave-valor, los diccionarios son las
estructura de datos a usar en Elixir. Un diccionario se crea usando la sintaxis
`%{}`:

```elixir
iex> map = %{:a => 1, 2 => :b}
%{2 => :b, :a => 1}
iex> map[:a]
1
iex> map[2]
:b
iex> map[:c]
nil
```

Dos diferencias surgen al compararlos con las listas de claves:

  * Los diccionarios permiten cualquier valor como clave.
  * Las claves de los diccionarios no están ordenadas.

En contraste con las listas de claves, los diccionarios son muy útiles con
_pattern matching_. Cuando se usa un diccionario en un patrón, siempre hará
_match_ en base a un subconjunto del valor dado:

```elixir
iex> %{} = %{:a => 1, 2 => :b}
%{2 => :b, :a => 1}
iex> %{:a => a} = %{:a => 1, 2 => :b}
%{2 => :b, :a => 1}
iex> a
1
iex> %{:c => c} = %{:a => 1, 2 => :b}
** (MatchError) no match of right hand side value: %{2 => :b, :a => 1}
```

Como se muestra en el ejemplo de arriba, un diccionario hace _match_ siempre
que las claves existan en el diccionario. Por lo tanto, un diccionario vacío
hace _match_ con cualquier diccionario.

Se pueden usar variables para acceder a los valores, para hacer _matching_ y
para agregar nuevas claves:

```elixir
iex> n = 1
1
iex> map = %{n => :one}
%{1 => :one}
iex> map[n]
:one
iex> %{^n => :one} = %{1 => :one, 2 => :two, 3 => :three}
%{1 => :one, 2 => :two, 3 => :three}
```

El módulo [`Map`](https://hexdocs.pm/elixir/Map.html) provee una <abbr
title="Application Programming Interface">API</abbr> muy similar a la del
módulo `Keyword`, con funciones convenientes para manipular diccionarios:

```elixir
iex> Map.get(%{:a => 1, 2 => :b}, :a)
1
iex> Map.put(%{:a => 1, 2 => :b}, :c, 3)
%{2 => :b, :a => 1, :c => 3}
iex> Map.to_list(%{:a => 1, 2 => :b})
[{2, :b}, {:a, 1}]
```

Los diccionarios usan la siguiente sintaxis para actualizar el valor de una
clave:

```elixir
iex> map = %{:a => 1, 2 => :b}
%{2 => :b, :a => 1}

iex> %{map | 2 => "two"}
%{2 => "two", :a => 1}
iex> %{map | :c => 3}
** (KeyError) key :c not found in: %{2 => :b, :a => 1}
```

La sintaxis del ejemplo anterior requiere que la clave dada exista en el
diccionario. No puede usarse para agregar nuevas claves. Véase que al usarla
con el átomo `:c`, falló, debido a que no hay clave `:c` en el diccionario.

Cuando todas las claves en el diccionario son átomos o cuando los átomos se
definen al final, puedes usar la siguiente sintaxis, que es más sencilla:

```elixir
iex> map = %{a: 1, b: 2}
%{a: 1, b: 2}
iex> map2 = %{"donas" => 3, bagels: 2, buns: 5}
%{:bagels => 2, :buns => 5, "donas" => 3}
```

Otra propiedad interesante de los diccionarios es que proveen su propia
sintaxis para acceder claves atómicas:


```elixir
iex> map = %{:a => 1, 2 => :b}
%{2 => :b, :a => 1}

iex> map.a
1
iex> map.c
** (KeyError) key :c not found in: %{2 => :b, :a => 1}
```

Esta sintaxis tiene el beneficio de que levanta excepciones si la clave no
existe en el diccionario. En ocasiones, el compilador de Elixir puede emitir un
aviso también. Esto permite tener una retroalimentación rápida y encontrar
errores de código o de tipeo tempranamente.

Los desarrolladores Elixir prefieren usar la sintaxis
`diccionario.claveAtomica` y _pattern matching_ en vez de las funciones del
módulo `Map`, porque conduce a un estilo de programación asertivo. El artículo
de José Valim (el creador de Elixir), [Writing assertive code with
Elixir](https://dashbit.co/blog/writing-assertive-code-with-elixir), enseña el
uso de esta metodología y provee ejemplos que demuestran como escribir código
más conciso y rápido mediante el uso de programación asertiva en Elixir.


## Bloques `do` y claves

Como hemos visto, las claves se usan mayormente en Elixir como parámetros
opcionales en llamadas a funciones. De hecho, ya hemos usado claves en esta
guía. Por ejemplo, vimos:

```elixir
iex> if true do
...>   "Esto se verá"
...> else
...>   "Esto no"
...> end
"Esto se verá"
```

Los bloques `do` no son más que una conveniencia sintáctica por encima del uso
de claves. Podemos reescribir el código del ejemplo anterior así:


```elixir
iex> if true, do: "Esto se verá", else: "Esto no"
"This will be seen"
```

Presta atención a ambas sintaxis. En el formato lista-de-claves, separamos cada
par clave-valor con comas, y cada clave es seguida por dos puntos (`:`). En el
bloque `do`, eliminamos los dos puntos, las comas, separamos cada clave en una
línea diferente y eliminamos el `end`. El formato lista-de-claves es útil
porque es menos verborreico. La mayor parte del tiempo es probable que uses la
sintaxis de bloques, pero es bueno saber que ambas son equivalentes.

Nota que sólo unas pocas listas de claves pueden ser convertidas en bloques:
`do`, `else`, `catch`, `rescue` y `after`. Esas son todas las claves usadas por
las estructuras de control de flujo de Elixir. Ya hemos aprendido algunas,
veremos el resto más adelante.

Con este tema terminado, veamos si podemos trabajar con estructuras anidadas.

## Estructuras de datos anidadas

Usualmente encontraremos diccionarios dentro de diccionarios, o incluso listas
de claves dentro de diccionarios, o cualquier combinación de tipos y
estructuras de datos que se te ocurra. Elixir provee las funciones `put_in/2`,
`update_in/2`, convenientes para manipular estructuras de datos anidadas,
además de otras macros que otorgan las mismas facilidades encontradas en
lenguajes imperativos, pero manteniendo la inmutabilidad de los datos.

Imagina que tienes la siguiente estructura:

```elixir
iex> users = [
  john: %{name: "John", age: 27, languages: ["Erlang", "Ruby", "Elixir"]},
  mary: %{name: "Mary", age: 29, languages: ["Elixir", "F#", "Clojure"]}
]
[
  john: %{age: 27, languages: ["Erlang", "Ruby", "Elixir"], name: "John"},
  mary: %{age: 29, languages: ["Elixir", "F#", "Clojure"], name: "Mary"}
]
```

Una lista de claves que representan usuarios, conteniendo cada una un
diccionario, que a su vez contiene el nombre del usuario, su edad y una lista
de los lenguajes de programación que le gustan. Si quisiéramos acceder a la
edad de John, podríamos escribir:

```elixir
iex> users[:john].age
27
```

Podemos usar la misma sintaxis para actualizar el valor:

```elixir
iex> users = put_in users[:john].age, 31
[
  john: %{age: 31, languages: ["Erlang", "Ruby", "Elixir"], name: "John"},
  mary: %{age: 29, languages: ["Elixir", "F#", "Clojure"], name: "Mary"}
]
```

La macro `update_in/2` es similar, pero además nos permite pasar una función
para definir cómo modificamos el valor. Por ejemplo, eliminemos "Clojure" de la
lista de lenguajes de Mary:

```elixir
iex> users = update_in users[:mary].languages, fn languages -> List.delete(languages, "Clojure") end
[
  john: %{age: 31, languages: ["Erlang", "Ruby", "Elixir"], name: "John"},
  mary: %{age: 29, languages: ["Elixir", "F#"], name: "Mary"}
]
```

Hay mucho más que aprender sobre `put_in/2` y `update_in/2`, también sobre
`get_and_update_in/2`, que nos permite extraer un valor y actualizarlo
simultáneamente. También están `put_in/3`, `update_in/3` y
`get_and_update_in/3`, que nos permiten acceder dinámicamente a la estructura.
Puedes aprender más de todas éstas y otras en la documentación del módulo
[`Kernel`](https://hexdocs.pm/elixir/Kernel.html).

Esto concluye nuestra introducción a las estructuras de datos asociativas en
Elixir. Encontrarás que, sean listas de claves o diccionarios, siempre tendrás
en Elixir la herramienta apropiada para encarar problemas que requieran
estructuras de datos asociativas.
