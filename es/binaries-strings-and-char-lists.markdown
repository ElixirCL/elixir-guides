---
section: getting-started
layout: getting-started
title: Binarios, `strings` y listas de caracteres
---

En "Tipos de datos básicos" aprendimos un poco sobre `strings` y usamos la
función `is_binary/1` para hacer verificaciones:

```elixir
iex> string = "hello"
"hello"
iex> is_binary(string)
true
```

En este capítulo vamos a ganar claridad respecto a qué son exactamente los
_binarios_, cómo se relacionan con los `strings` y que significan los valores
entre comillas simples como `'este'` en Elixir. Aunque los `strings` con uno de
los tipos de datos más comunes en lenguajes de programación, son sutilmente
complejos y es bastante común que no se comprenda esta complejidad. Para las
sutilezas de los `strings` en Elixir tenemos que aprender sobre
[Unicode](https://en.wikipedia.org/wiki/Unicode) y codificación de caracteres,
en particular la codificación [UTF-8](https://en.wikipedia.org/wiki/UTF-8).

## Unicode y Puntos de Código

Para facilitar la comunicación entre computadoras a través de distintos
lenguajes humanos, se requiere un estándar que permita que los cereos y unos en
una máquina configurada para usar francés, por ejemplo, signifiquen lo mismo
cuando son transmitidos a otra que está configurada en un lenguaje distinto,
por ejemplo alemán. El [Estándar
Unicode](https://unicode.org/standard/standard.html) actúa como un registro
oficial de prácticamente todos los caracteres que conocemos. Esto incluye
caracteres pertenecientes a textos clásicos e históricos, emojis y también
caracteres de formateo y control.

El estándar Unicode organiza todo el repertorio de caracteres en _mapas de
código_, donde a cada caracter se le asigna un índice numérico. Este índice se
conoce como [Punto de Código](https://en.wikipedia.org/wiki/Code_point).

En Elixir puedes usar `?` frente a un caracter para revelar su punto de código:

```elixir
iex> ?a
97
iex> ?ł
322
```

Ten presente que la mayoría de mapeos Unicode se referirán al punto de código
por su valor hexadecimal, por ejemplo, `97` es `0061` en haxadecimal. Podemos
representar caracteres Unicode en Elixir mediante un _string_ con el formato
`\uXXXX`, donde las `X` se deben reemplazar por el punto de código del caracter
en hexadecimal.

```elixir
iex> "\u0061" == "a"
true
iex> 0x0061 = 97 = ?a
97
```

La representación hexadecimal también te será útil para buscar información
sobre un punto de código en particular, por ejemplo en el siguiente URL
[https://codepoints.net/**U+0061**](https://codepoints.net/U+0061) (nótese el
código hexadecimal en negritas), puedes encontrar la ficha de datos con toda la
información del caracter `a`.

## UTF-8 y codificaciones

Ahora que sabemos de qué se trata el estándar Unicode y qué son los puntos de
código, podemos hablar de codificaciones. Mientras que el punto de código es lo
que guardamos (**qué**), una codificación es la manera (**cómo**) en que lo
guardamos. Las codificaciones son implementaciones. En otras palabras,
necesitamos un mecanismo para convertir los puntos de código en bytes, de
manera que puedan ser almacenados en memoria, escritos a disco, etc.

Elixir usa UTF-8 para codificar _string_, lo que significa que los puntos de
código son codificados como series de bytes de 8 bits. UTF-8 usa una
codificación **variable en tamaño**, entre uno y cuatro bytes, para almacenar
cada punto de código. Es capaz de codificar todos los puntos de código de
Unicode. Veamos un ejemplo:

```elixir
iex> string = "sólo"
"sólo"
iex> String.length(string)
4
iex> byte_size(string)
5
```

Aunque el _string_ del ejemplo tiene **4 caracteres**, usa **5 bytes** para
almacenarlo. Esto es debido a que se requieren 2 bytes para almacenar la "ó"
acentuada.

> Nota: si estás en Windows, existe la posibilidad de que tu terminal no esté
> configurada para usar UTF-8. Puedes cambiar la codificacion de tu sesión de
> terminal corriendo el comando `chcp 65001` antes de correr el REPL `iex`
> (`iex.bat`).

Además de definir caracteres, UTF-8 provee la noción de
[grafemas](https://dle.rae.es/grafema). Un grafema es una unidad mínima e
indivisible de la escritura de una lengua, sin embargo puede consistir de
múltiples caracteres que se perciben como un sólo grafema. Por ejemplo, el
emoji de [mujer bombero](https://emojipedia.org/woman-firefighter/) se
representa mediante la combinación de tres caracteres: el emoji de mujer (👩),
un caracter unificador no visible, y el emoji del carro de bomberos (🚒):

```elixir
iex> String.codepoints("👩‍🚒")
["👩", "‍", "🚒"]
iex> String.graphemes("👩‍🚒")
["👩‍🚒"]
```

Elixir es capaz de reconocer que se debe representar como un sólo grafema y por
lo tanto su tamaño es 1:

```elixir
iex> String.length("👩‍🚒")
1
```

> Nota: si no puedes ver los emojis de los ejemplos en tu terminal, debes
> verificar que tu terminal soporte emojis y que estés usando una tipografía
> que los pueda reproducir.

Los documentos codificados en UTF-8 se encuentran en todos lados. Esta página
está codificada en UTF-8. La información de codificación es entregada a tu
navegador y de esa manera el navegador sabe como reproducir los bytes,
caracteres y grafemas apropiadamente.

Si quieres ver qué bytes se usan en un _string_, un truco es concatenar el byte
nulo `<<0>>` al _string_:

```elixir
iex> "hełło" <> <<0>>
<<104, 101, 197, 130, 197, 130, 111, 0>>
```

Alternativamente, puedes ver la representación binaria usando la función
[IO.inspect/2](https://hexdocs.pm/elixir/IO.html#inspect/2):

```elixir
iex> IO.inspect("hełło", binaries: :as_binaries)
<<104, 101, 197, 130, 197, 130, 111>>
```

Hablemos de cadenas de bits y aprendamos qué hace el constructor `<<>>`.

## Cadenas de bits

Aunque hemos cubierto los puntos de código en la codificación UTF-8, aún
debemos adentrarnos en la manera en que se almacenan los bytes codificados,
para ello introduciremos el concepto de **cadenas de bits**. Una cadena de bits
es un tipo de dato fundamental en Elixir. Se denota con la sintaxis `<<>>`, y
es una secuencia continua de bits en memoria.

Puedes ver más información sobre el constructor binario de cadenas de
caracteres `<<>>` en el [manual de
referencia](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#%3C%3C%3E%3E/1).

Por omisión, 8 bits, que son 1 byte, se usan para almacenar cada número en una
cadena de bits. Pero puedes especificar el número de bits a través del
modificador `::n` para indicar el tamaño en `n` bits. También puedes usar la
declaración `::size(n)`:

```elixir
iex> <<42>> == <<42::8>>
true
iex> <<3::4>>
<<3::size(4)>>
```

Por ejemplo, el número decimal `3`, cuando se representa con 4 bits en base 2
será `0011`, que será equivalente a guardar cada valor por separado usando un
sólo bit:

```elixir
iex> <<0::1, 0::1, 1::1, 1::1>> == <<3::4>>
true
```

Cualquier valor que exceda lo que se puede almacenar por el número de bits
predefinidos, será truncado:

```elixir
iex> <<1>> == <<257>>
true
```

Aquí, `257` en base 2 será representado como `100000001`, pero dado que sólo
hemos reservado 8 bits para almacenarlo (por omisión), el bit de la izquierda
es ignorado y el valor almacenado es `00000001`, que es `1`.

## Binarios

**Un binario es una cadena de bits donde el número de bits es divisible por
8**. Esto significa que todo binario es una cadena de bits, pero que no
cualquier cadena de bits es un binario. Podemos usar las funciones
`is_bitstring/1` y `is_binary/1` para verificarlo:

```elixir
iex> is_bitstring(<<3::4>>)
true
iex> is_binary(<<3::4>>)
false
iex> is_bitstring(<<0, 255, 42>>)
true
iex> is_binary(<<0, 255, 42>>)
true
iex> is_binary(<<42::16>>)
true
```

Podemos hacer _pattern matching_ sobre binarios y cadenas de bits:

```elixir
iex> <<0, 1, x>> = <<0, 1, 2>>
<<0, 1, 2>>
iex> x
2
iex> <<0, 1, x>> = <<0, 1, 2, 3>>
** (MatchError) no match of right hand side value: <<0, 1, 2, 3>>
```

Observa que a menos que usemos los modificadores `::`, cada entrada en el
patrón binario hará _match_ con un byte (exactamente 8 bits). Si queremos hacer
_match_ sobre un binario de un tamaño desconocido, podemos usar el modificador
`binary` al final del patrón:

```elixir
iex> <<0, 1, x::binary>> = <<0, 1, 2, 3>>
<<0, 1, 2, 3>>
iex> x
<<2, 3>>
```

Hay un par de modificadores que son útiles al hacer _pattern matching_ sobre
binarios. El modificador `binary-size(n)` hará _match_ con `n` bytes en un
binario:

```elixir
iex> <<head::binary-size(2), rest::binary>> = <<0, 1, 2, 3>>
<<0, 1, 2, 3>>
iex> head
<<0, 1>>
iex> rest
<<2, 3>>
```

**Un _string_ es un binario codificado en UTF-8**, donde el punto de código de
cada caracter es codificado usando entre 1 y 4 bytes. Por lo tanto todo
_string_ es un binario, pero debido a las reglas de codificación del estándar
UTF-8, no todo binario es un _string_ válido.

```elixir
iex> is_binary("hello")
true
iex> is_binary(<<239, 191, 19>>)
true
iex> String.valid?(<<239, 191, 19>>)
false
```

El operador de concatenación de _string_ es, de hecho, un operador de
concatenación binaria:

```elixir
iex> "a" <> "ha"
"aha"
iex> <<0, 1>> <> <<2, 3>>
<<0, 1, 2, 3>>
```

Dado que los _string_ son binarios, podemos hacer _pattern matching_ sobre _string_:

```elixir
iex> <<head, rest::binary>> = "banana"
"banana"
iex> head == ?b
true
iex> rest
"anana"
```

Sin embargo, recuerda que al hacer _pattern matching_, estás trabajando sobre
**bytes**, por lo tanto un _string_ como "über", con caracteres multibyte, no
hará _match_ en el caracter, sino en el primer byte del caracter:

```elixir
iex> "ü" <> <<0>>
<<195, 188, 0>>
iex> <<x, rest::binary>> = "über"
"über"
iex> x == ?ü
false
iex> rest
<<188, 98, 101, 114>>
```

En el ejemplo anterior, `x` hizo _match_ sólo con el primer byte del caracter
multibyte `ü`.

Por lo tanto, cuando se realiza _pattern matching_ en _string_, es importante
usar el modificador `utf8`:

```elixir
iex> <<x::utf8, rest::binary>> = "über"
"über"
iex> x == ?ü
true
iex> rest
"ber"
```

## Listas de caracteres

Nuestra gira por las cadenas de bits, binarios y _string_ está casi completa,
pero nos queda un tipo de datos por explicar: la lista de caracteres.

**Una lista de caracteres es una lista de enteros, donde todos los enteros son
puntos de código válidos**. En la práctica, es improbable que te los cruces,
sólo en escenarios específicos, como por ejemplo al interactuar con viejas
bibliotecas de Erlang que no aceptan tipos de datos binarios como argumentos.

Mientras que las comillas dobles crean tipos de datos _string_, las comillas
simples crean listas de caracteres literales:

```elixir
iex> 'hello'
'hello'
iex> [?h, ?e, ?l, ?l, ?o]
'hello'
```

Lo que hay que observar y recordar es que `"hello"` no es lo mismo que
`'hello'`. Hablando en general, se deben usar **siempre** comillas dobles para
representar datos de tipo _string_ en Elixir. De todas maneras aprendamos como
funcionan las listas de caracteres.

En vez de contener bytes, una lista de caracteres contiene puntos de código
enteros. Sin embargo, la lista de caracteres se imprimirá entre comillas
simples si todos los puntos de código se encuentran dentro del rango ASCII,
sino es así, se imprimirán como una lista de enteros.

```elixir
iex 'hello'
'hello'
iex> 'hełło'
[104, 101, 322, 322, 111]
iex> is_list('hełło')
true
```

Intrepretar enteros como puntos de código puede llevar a comportamientos
inesperados. Por ejemplo, si estás almacenando una lista de enteros que se
encuentra dentro del rango de 0 a 127, `iex` interpreta por omisión esta lista
como una lista de caracteres, y por lo tanto mostrará los caracteres ASCII
correspondientes.

```elixir
iex> heartbeats_per_minute = [99, 97, 116]
'cat'
```

Puedes convertir una lista de caracteres en un _string_ y viceversa con las
funciones `to_string/1` y `to_charlist/1`:

```elixir
iex> to_charlist("hełło")
[104, 101, 322, 322, 111]
iex> to_string('hełło')
"hełło"
iex> to_string(:hello)
"hello"
iex> to_string(1)
"1"
```

Observa que estas funciones son polimórficas. No sólo convierten cadenas de
caracteres en _string_, también operan sobre _int_, _atom_ y otros tipos de
datos.

La concatenación de _string_ se realiza con el operador `<>`, pero las cadenas
de caracteres, al ser listas, usan el operador de concatenación de listas `++`:

```elixir
iex> 'this ' <> 'fails'
** (ArgumentError) expected binary argument in <> operator but got: 'this '
    (elixir) lib/kernel.ex:1821: Kernel.wrap_concatenation/3
    (elixir) lib/kernel.ex:1808: Kernel.extract_concatenations/2
    (elixir) expanding macro: Kernel.<>/2
    iex:1: (file)
iex> 'this ' ++ 'works'
'this works'
iex> "he" ++ "llo"
** (ArgumentError) argument error
    :erlang.++("he", "llo")
iex> "he" <> "llo"
"hello"
```

Con los binarios, _string_, y listas de caracteres aprendidos, es hora de
hablar de las estructuras de datos de tipo clave-valor.
