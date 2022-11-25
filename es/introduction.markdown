---
section: getting-started
layout: getting-started
title: Introducción
---

¡Bienvenido(a)!

En este tutorial vamos a enseñarte sobre los fundamentos de Elixir; su sintaxis, cómo definir módulos, como manipular las estructuras de datos más comunes, sus características y también vamos a aseguranos que puedas instalar Elixir y correr su consola interactiva, llamada IEx.

Los requerimientos son (puedes revisarlos con `elixir -v`)

  * Elixir 1.11.0 o superior
  * Erlang/OTP 22 o superior

¡Vamos!

> Si encuentras algún error en este tutorial o en el sitio. $TODO

> Las guías también se pueden encontrar en formato EPUB: $TODO
>
> $TODO
> $TODO
> $TODO

## Instalación

Si aún no has instalado Elixir, visita el sitio de [instalación]($TODO). Luego, puedes correr elixir --version` en la consola para revisar la versión disponible.

## Interactive mode

Una vez que instales Elixir, dispondrás de tres ejecutables: `iex`, `elixir` y `elixirc`. Si lo instalaste desde las fuentes o unsando una versión empaquetada, los encontrarás en el directorio `bin`.

Corramos Interactive Elixir (elixir interactivo) con `iex` (o `iex.bat` si estás en Windows PowerShell ya que `iex` ya es usado por PowerShell). En el modo interactivo podemos tipear cualquier expresión de Elixir y obtener su resultado. Ensayemos con algunas expresiones.

Inicia `iex` en tu consola y tipea lo siguiente:

```elixir
Erlang/OTP {{ stable.minimum_otp }} [64-bit] [smp:2:2] [...]
$TODO revisar interpolación
Interactive Elixir ({{ stable.version }}) - press Ctrl+C to exit
iex> 40 + 2
42
iex> "hello" <> " world"
"hello world"
```

Algunos detalles, como el número de versión puede ser distinto, pero no te preocupes. Por ahora usaremos `iex` solo para probar código. Para salir de `iex` presiona `Ctrl+C` dos veces.

