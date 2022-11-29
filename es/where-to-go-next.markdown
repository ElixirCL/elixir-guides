---
section: getting-started
layout: getting-started
title: ¿Y ahora cómo continuo?
---

¿Ansioso por aprender más? ¡Sigue leyendo!

## Crea tu primer proyecto Elixir

Para crear tu primer proyecto, Elixir cuenta con una herramienta para construr aplicaciones llamada Mix. Puedes crear tu nuevo proyecto ejecutando:

```console
$ mix new path/to/new/project
```

Hemos escrito una guía que abrca la construicción de una aplicación Elixir, con su propio árbol de supervisión, configuración, pruebas y más. La aplicación funciona como un almacén de clave-valor distribuido donde organizamos pares de clave-valor en depósitos y distribuimos esos depósitos en varios nodos:

* [Mix y OTP](/getting-started/mix-otp/introduction-to-mix.html)

Si planeas escribir tu primera biblioteca para que la usen otros desarrolladores, no olvides leer nuestras [Pautas para bibliotecas] (https://hexdocs.pm/elixir/library-guidelines.html).

## Metaprogramación

Elixir es un lenguaje de programación extensible y muy personalizable gracias a su soporte de metaprogramación. La mayor parte de la metaprogramación en Elixir se realiza a través de macros, que son muy útiles en varias situaciones, especialmente para escribir DSL. Hemos escrito una breve guía que explica los mecanismos básicos detrás de las macros, muestra cómo escribir macros y cómo usar macros para crear DSL:

* [Metaprogramación en Elixir](/getting-started/meta/quote-and-unquote.html)

## Comunidad y otros recursos

Tenemos una sección [Aprendizaje](/learning.html) que recomienda libros, screencasts y otros recursos para aprender Elixir y explorar el ecosistema. También existen varios recursos de Elixir, como conferencias, proyectos de código abierto u otros materiales de aprendizaje creado por la comunidad.

No olvides que también puede consultar el [código fuente de Elixir] (https://github.com/elixir-lang/elixir), lo cual en su mayoria se encuentra escrito principalmente en Elixir (principalmente el directorio `lib`), o [ explore la documentación de Elixir](/docs.html).

## Un poco de Erlang

Elixir se ejecuta en la máquina virtual de Erlang y, tarde o temprano, un desarrollador de Elixir querrá interactuar con bibliotecas de Erlang existentes. Aquí hay una lista de recursos en línea que cubren los fundamentos de Erlang y sus características más avanzadas:

* [Sintaxis de Erlang: un curso acelerado](/crash-course.html) proporciona una introducción concisa a la sintaxis de Erlang. Cada fragmento de código va acompañado de un código equivalente en Elixir. Esta es una oportunidad para que usted no solo se exponga un poco a la sintaxis de Erlang, sino que también revise algunas de las cosas que ha aprendido en esta guía.

* El sitio web oficial de Erlang tiene un breve [tutorial] (https://www.erlang.org/course). Hay un capítulo con imágenes que describen brevemente los principios de Erlang para [programación concurrente] (https://www.erlang.org/course/concurrent_programming.html).

* [¡Aprenda un poco de Erlang por un gran bien!] (http://learnyousomeerlang.com/) es una excelente introducción a Erlang, sus principios de diseño, biblioteca estándar, mejores prácticas y mucho más. Una vez que haya leído el curso acelerado mencionado anteriormente, podrá omitir con seguridad los primeros dos capítulos del libro que se ocupan principalmente de la sintaxis. Cuando llegue al capítulo [Guía del autoestopista para la concurrencia](http://learnyousomeerlang.com/the-hitchhikers-guide-to-concurrency), ahí es donde comienza la verdadera diversión.
