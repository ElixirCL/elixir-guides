---
section: getting-started
layout: getting-started
title: Introducción
---


¡Bienvenid@!

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

## Modo interactivo

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

Algunos detalles, como el número de versión, pueden ser distintos. Despreocúpate, por ahora usaremos `iex` solo para probar código. Para salir de `iex` presiona `Ctrl+C` dos veces.

¡Estamos listos! Usaremos la consola interactiva bastante seguido en los próximos capítulos para familiarizarnos con la estructura del lenguaje y los tipos de datos básicos.

> Nota: Si estás en Windows, puedes probar usando `iex --werl` (o `iex.bat --werl` en PowerShell) que podría entregarte una mejor experiencia dependiendo de la consola que utilices.

## Corriendo scripts

Luego de familiarizate con la base del lenguaje, probablemente quieras escribir un programa. Puedes lograrlo escribiendo el siguiente código Elixir en un archivo:

```elixir
IO.puts("Hello world from Elixir")
```

Guárdalo como `simple.exs` y ejecútalo con `elixir`:

```console
$ elixir simple.exs
Hello world from Elixir
```

Más adelante aprenderás cómo compilar código en Elixir (en el [Capítulo 8]($TODO)) y cómo usar la herramienta de construcción Mix 'Mix build tool' (en [Mix y OTP]($TODO)). Por ahora, continuemos con el [Capítulo 2]($TODO)

## ¿Tienes dudas?

Al avanzar por esta guía, es normal que tengas dudas, ¡eso es parte del proceso de aprendizaje! Hay varios lugares donde puedes hacer preguntas, estos son algunos:

  * [#elixir en irc.libera.chat](irc://irc.libera.chat/elixir)
  * [Elixir Forum](http://elixirforum.com)
  * [Elixir en Slack](https://elixir-slackin.herokuapp.com/)
  * [Elixir en Discord](https://discord.gg/elixir)
  * [elixir tag en StackOverflow](https://stackoverflow.com/questions/tagged/elixir)

Cuando hagas preguntas, recuerda estos consejos:

  * En vez de preguntar "¿cómo hago X en Elixir?", pregunta "¿cómo puedo resolver Y en Elixir?", o dicho de otra manera, no preguntes como implementar una solución ya definida. Partir por el problema entrega más contexto y menos sesgo para responder correctamente.

  * En caso que estés teniendo un problema, por favor incluye toda la información que puedas en el reporte, por ejemplo: tu versión de Elixir, el segmento de código involucrado, el mensaje de error y la traza de ejecución (stacktrace). Puedes usar sitions como [Gist](https://gist.github.com/) para pegar todo el detalle.
