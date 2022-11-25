---
section: getting-started
layout: getting-started
title: Tipos de datos básicos
---

En este capítulo vamos a aprender más sobre los tipos de datos básicos de Elixir: _integers_ (números enteros), _floats_ (números flotantes), _booleans_ (booleanos), _atoms_ (símbolos), _strings_ (cadenas de texto), _lists_ (listas) y _tuples_ (tuplas). Usaremos estos términos en inglés para no confundir la nomenclatura. Algunos tipos básicos son:

```elixir
iex> 1          # integer
iex> 0x1F       # integer
iex> 1.0        # float
iex> true       # boolean
iex> :atom      # atom / symbol
iex> "elixir"   # string
iex> [1, 2, 3]  # list
iex> {1, 2, 3}  # tuple
```

## Aritmética básica

Ejecuta `iex` y tipea las siguientes expresiones:

```elixir
iex> 1 + 2
3
iex> 5 * 5
25
iex> 10 / 2
5.0
```

Puedes notar que `10 / 2` retornó un _float_ `5.0` en vez de un _integer_ `5`. Esto se debe a que el operador `/` siempre retorna un _float_. Si lo que deseas es retornar el entero o el remanente, puedes usar las funciones `div` y `rem`:

```elixir
iex> div(10, 2)
5
iex> div(10, 2)
5
iex> rem 10, 3
1
```

Habrás advertido que Elixir permite no usar paréntesis cuando se llaman funciones con al menos un argumento. Sin embargo preferimos usar siempre paréntesis.

Elixir también permite notaciones especiales para los _binary_ (binarios), _octal_ (octales) y _hexadecimal_ numbers (números hexadecimales):

```elixir
iex> 0b1010
10
iex> 0o777
511
iex> 0x1F
31
```

Los números _float_ deben tener un punto, seguido por al menos un dígito, y permiten `e` como anotación científica:

```elixir
iex> 1.0
1.0
iex> 1.0e-10
1.0e-10
```

Los _floats_ en Elixir son de doble precisión, con 64-bits.

Puedes usar la función `round` para obtener el `integer` más cercano a un `float`, o la función `trunc` para obtener el `integer` del `float`.

```elixir
iex> round(3.58)
4
iex> trunc(3.58)
3
```

## Identificación y documentación de las funciones

Las funciones en Elixir se identifican por su nombre y su aridad. La aridad de una función describe el número de argumentos que permite la función. De ahora en adelante, usaremos el nombre y la aridad para descrubur las funciones en la documentación. `trunc/1` identifica la función llamada `trunc` que permite `1` argumento, en cambio `trunc/2` describe otra función (que no existe), con el mismo nombre pero con aridad `2`.

Podemos usar esta sintaxis para acceder a la documentación. La consola de Elixir define la función `h` que permite revisar la documentación de cualquier función. Por ejemplo si escribes `h trunc/1` se imprimirá en pantalla la documentación de `trunc/1`:

```elixir
iex> h trunc/1
                             def trunc()

Returns the integer part of number.
```

`h trunc/1` funciona porque está definida en el módulo `Kernel` (`Kernel.h/1`). Todas las funciones en el módulo `Kernel` son importadas automáticamente a nuestro _namespace_. $WIP