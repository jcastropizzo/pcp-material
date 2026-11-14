# Modelo de actores y Erlang — transcripción

- Fuente: [teorica-08-modelo-de-actores.pdf](teorica-08-modelo-de-actores.pdf).
- Páginas: 77.
- SHA-256: `ea01d4aae0362b734cfe59371caccab33b4c199df0fe84f5e27e562deac3269c`.

> Texto por página con disposición aproximada. La extracción puede unir palabras o desplazar símbolos. Cotejar fórmulas, código y diagramas en el PDF.

## Página 1

[Ver página original](teorica-08-modelo-de-actores.pdf#page=1)

```text
    Programación Concurrente y Paralela

    Modelo   de Actores
```

## Página 2

[Ver página original](teorica-08-modelo-de-actores.pdf#page=2)

```text
   ¿Qué es un Actor?



      • Entidad computacional básica que interactúa intercambiando

       mensajes.
         • Funciona como un objeto dotado de un hilo de ejecución independiente.
      • Cuando un actor recibe un mensaje puede:
         • Enviar un número finito de mensajes a otros actores.
         • Crear un número finito de nuevos actores.
         • Recibir selectivamente el mensaje y designar el comportamiento para
          próximo.

      • Sin secuencia fija: Las acciones pueden ejecutarse en paralelo.
```

## Página 3

[Ver página original](teorica-08-modelo-de-actores.pdf#page=3)

```text
   Ejemplo



                  Cliente 1

                           increment
                                         buzón
                  Cliente 2 increment   {c3,valor}
                                  Contador
                                        increment
                                        increment
                         {c3, valor}
                  Cliente 3
                            2
      • Cada actor es un bucle: tomar un mensaje → computar → (enviar /
       crear actores / cambiar de comportamiento).
      • El único canal de interacción son mensajes: no usa estados compartidos.
```

## Página 4

[Ver página original](teorica-08-modelo-de-actores.pdf#page=4)

```text
   Granularidad en el Modelo de Actores






      • Modelo Teórico: Todo es un actor (incluyendo enteros y cadenas).
      • Enfoque práctico: El actor como un componente especial del sistema.
         • Más grande que un tipo primitivo.
         • Lo suficientemente pequeño para mantener la simplicidad.
```

## Página 5

[Ver página original](teorica-08-modelo-de-actores.pdf#page=5)

```text
   Modelo de comunicación Asíncrono




      • Comunicación directa: El actor A1 envía un mensaje a A2 si conoce su

       dirección.
      • Procesamiento asíncrono: A2 recibe y procesa el mensaje en un hilo de
       ejecución separado.
      • Mailbox: Funciona como un buffer asociado a cada actor. El actor A1

       coloca un mensaje al final del buzón de A2. A2 procesa el mensaje cuando
       está listo.
```

## Página 6

[Ver página original](teorica-08-modelo-de-actores.pdf#page=6)

```text
   Propiedades del envío



      • Es no bloqueante: el emisor continúa su ejecución de inmediato; no espera

       a que el mensaje sea recibido ni procesado.
      • No hay acuse de recibo ni señal de falla: si el destinatario no existe (o
       ya murió), el mensaje se descarta en silencio.
      • Entrega a lo sumo una vez: no hace reintentos ni duplicados automáticos.

      • Sobre el orden de entrega:
         • Mismo par emisor-receptor: FIFO; si A envía m1 y luego m2 a B, B los
          recibe en ese orden.
         • Emisores distintos: no hay garantía de orden.
```

## Página 7

[Ver página original](teorica-08-modelo-de-actores.pdf#page=7)

```text
   Estado y Concurrencia




      • Máquinas de estado finitas: Al cambiar de estado, el actor modifica su
       manejador de mensajes (message handler) para procesar los subsiguientes
       mensajes.
      • Aislamiento: Los actores no comparten su estado con ningún otro

       componente.
      • Concurrencia libre de locks:
         • Procesan un solo mensaje a la vez sobre su propio estado aislado.
         • Elimina por completo la necesidad de usar locks.
```

## Página 8

[Ver página original](teorica-08-modelo-de-actores.pdf#page=8)

```text
   Memoria compartida, mensajes y actores


                Compartida    Mensajes      Actores
     Estado     compartido entre privado; viaja aislado: solo el actor
                todos los hilos copiado por el canal lo toca
     Sincronización locks, monitores, bloqueos del canal, ninguna: un mensaje
                condiciones   select        por vez

     Recepción  cualquiera lee lo FIFO: llega lo que selectiva: el actor
                compartido    llega         elige qué procesar
     Riesgos    carreras e    deadlocks por deadlocks por
                incoherencias, esperas cruzadas esperas cruzadas
                deadlocks por
                esperas cruzadas

      • En actores, el mismo modelo funciona dentro de una máquina o entre
       máquinas: una de las razones de su uso en sistemas distribuidos.
```

## Página 9

[Ver página original](teorica-08-modelo-de-actores.pdf#page=9)

```text
                             Erlang
```

## Página 10

[Ver página original](teorica-08-modelo-de-actores.pdf#page=10)

```text
   Erlang: Introducción




      • Desarrollado en la década de los ’80 en Ericsson para uso en telefonía.
      • Su nombre tiene doble significado: en honor al matemático Agner Erlang, y
       también por “Ericsson language”

      • El objetivo era tener un lenguaje de alta concurrencia y tolerancia a fallos,
       funcionando 24/7.
      • Su diseño toma inspiración de lenguajes como Lisp y Prolog (originalmente
       estaba implementado sobre Prolog).
      • Usa una máquina virtual llamada BEAM, con scheduler propio.
```

## Página 11

[Ver página original](teorica-08-modelo-de-actores.pdf#page=11)

```text
   Erlang: Introducción





      • Open source desde 1998.

      • No fue diseñado para implementar el modelo de actores (que ya había sido
       formalizado), pero se amoldó al mismo cómodamente.
      • De uso en la industria hoy en día: La infraestructura de Whatsapp está
       escrita en Erlang.
```

## Página 12

[Ver página original](teorica-08-modelo-de-actores.pdf#page=12)

```text
   El Shell de Erlang






      • El comando erl inicia el EShell, el REPL de BEAM.
      • Nos presenta un prompt en la forma de n> (n número).
      • Podemos escribir expresiones, que terminan con un punto (.).

      • Se sale del shell con q().
```

## Página 13

[Ver página original](teorica-08-modelo-de-actores.pdf#page=13)

```text
   Tipos numéricos


    Números Enteros
      • Pueden expresarse en bases distintas a 10.

         • Notación Base#Value.
         • Base: entero entre 2 y 16.
            • 2#1010: 10 en base 2.
            • -16#EA: −234 en base 16.
      • No hay límite de representación:
         • Se pueden usar números enteros arbitrariamente grandes
         • Si es necesario se convierten internamente en bignums.
         • bignums: permiten cálculos precisos, pero son menos eficientes.
    Números de Punto Flotante

      • Admiten notación científica: 2.37E-10 es 2,37×10−10.
```

## Página 14

[Ver página original](teorica-08-modelo-de-actores.pdf#page=14)

```text
   Átomos en Erlang



      • Los átomos son literales constantes que se representan a sí mismos.
      • Son como los valores de un enum.

      • Las únicas operaciones son las comparaciones.
      • Dos variedades:
         • Comienzan con una letra minúscula: pueden contener letras (incluye
          acentos), dígitos, @ y .
         • Delimitados por comillas simples: pueden contener cualquier carácter.
      • Ejemplos:

         • fooBar, miércoles, alfa21, empieza_en_minuscula, ’signo pe$os’
          ’con espacio’
```

## Página 15

[Ver página original](teorica-08-modelo-de-actores.pdf#page=15)

```text
   Booleanos


      • Se utilizan los átomos true y false
      • Desempeñan el rol de booleanos al ser los resultados de comparaciones
              Expresión         Resultado

              1 == 2.           false
              1 < 2.            true

              a > z.            false
      • Los átomos están ordenados en orden lexicográfico.
      • Funciones incorporadas (Built-In Functions BIF).

              Expresión         Resultado
              is_boolean(9+6).  false
              is_boolean(true). true
```

## Página 16

[Ver página original](teorica-08-modelo-de-actores.pdf#page=16)

```text
   Operadores booleanos





         Operador Descripción
         and      Devuelve true solo si ambos argumentos son true.
         andalso  and en cortocircuito.

         or       Devuelve true si alguno de los argumentos es true.
         orelse   or en cortocircuito
              También contamos con los operadores not y xor.
```

## Página 17

[Ver página original](teorica-08-modelo-de-actores.pdf#page=17)

```text
   Tuplas




      • Tipo de dato compuesto, con elementos de distinto tipo
         • {123, def, abc}
         • {abc, {def, 123}, ghi}
         • {}
         • {persona, ’Juan’, ’Pérez’}

      • Si el primer elemento es un átomo, se le llama etiqueta (tag).
      • Convención para representar diferentes tipos de datos (se considera una
       buena práctica).
```

## Página 18

[Ver página original](teorica-08-modelo-de-actores.pdf#page=18)

```text
   Funciones sobre tuplas




     Expresión                              Resultado
     tuple_size({abc, {def, 123}, ghi}).    3

     element(2, {abc, {def, 123}, ghi}).    {def,123}
     setelement(2, {abc, {def, 123}, ghi}, def). {abc,def,ghi}

      • Los elementos se indexan desde 1.

      • setelement no modifica la tupla, devuelve una nueva.
```

## Página 19

[Ver página original](teorica-08-modelo-de-actores.pdf#page=19)

```text
   Listas




      • Delimitadas por [...] y elementos separados por ,.
      • Los elementos pueden ser de distinto tipo.

      • Ejemplos:
         • [123, def, abc]
         • [a, [b, [c, d, e], f], g]
         • []
         • [{persona, ’Juan’, ’Pérez’}, {persona, ’Carolina’, ’Gómez’},
          {persona, ’Gerardo’, ’Rodriguez’}]
```

## Página 20

[Ver página original](teorica-08-modelo-de-actores.pdf#page=20)

```text
   Construcción y Procesamiento de Listas en Erlang



      • Constructor: [Cabeza|Cola],
                           Expresión

                           [1,2,3]
                           [1|[2,3]]
                           [1|[2|[3]]]

                           [1|[2|[3|[]]]]
                           [1,2|[3|[]]]
      • Listas no adecuadas (improper) : [1,2|3]

      • Admitidas pero mejor evitar (a menos que se necesiten)
```

## Página 21

[Ver página original](teorica-08-modelo-de-actores.pdf#page=21)

```text
   Construcción y Procesamiento de Listas en Erlang



    Operador ++ (Concatenación)

    Toma dos listas y las une en una nueva lista.
      • [1,2] ++ [3,4] Resultado: [1,2,3,4]

    Operador – (Resta de Listas)
    Elimina cada elemento de la lista del lado derecho de la lista del lado izquierdo.

      • [1,1] -- [1]     Resultado: [1]
      • [1,2,3,4] -- [1,4] Resultado: [2,3]

      • Son asociativos a derecha.
```

## Página 22

[Ver página original](teorica-08-modelo-de-actores.pdf#page=22)

```text
   Funciones sobre Listas






      • Varias funciones el módulo de la biblioteca lists.
      • No son Funciones Básicas Incorporadas (BIFs).
      • Se invocan calculando nombre del módulo separados por dos puntos (:)

      • Algunas funciones provistas son member, nth, sort, zip, etc.
```

## Página 23

[Ver página original](teorica-08-modelo-de-actores.pdf#page=23)

```text
   Caracteres y Cadenas en Erlang




      • Los caracteres se representan mediante enteros,
         • La representación entera de un carácter se obtiene precediendo el carácter
          con el símbolo $:
            • 1> $A. → 65
            • 2> $a. → 97
      • las cadenas (de caracteres) se representan mediante listas de enteros.
         • la cadena "Hello World" es la lista
          [72,101,108,108,111,32,87,111,114,108,100].
            • 1> [65,66,67]. → "ABC"
```

## Página 24

[Ver página original](teorica-08-modelo-de-actores.pdf#page=24)

```text
   Comparación de Términos en Erlang


      • Operadores de igualdad (==) y desigualdad (/=) comparan valores sin tener
       en cuenta los tipos de datos.
                        Expresión Resultado

                        1 == one  false
                        one == one true
                        1 == 1.0  true

                        1 /= 1.0  false
      • =:= y =/= comparan valores y tipos.

                         Expresión Resultado
                         1 =:= 1.0 false
                         1 =/= 1.0 true
```

## Página 25

[Ver página original](teorica-08-modelo-de-actores.pdf#page=25)

```text
   Variables



      • Imperativas: almacenan valores de tipos de datos simples y compuestos.

      • Su nombre comienza con mayúscula, seguida de letras mayúsculas y
       minúsculas, enteros y guiones bajos.
      • Son de asignación única: una vez que se liga a un valor, no se puede
       cambiar (sin desligarla primero).
      • No se declaran.

      • Todas las variables son locales a la función en la que están ligadas.
         • No existen variables globales.
      • Sistema de tipos dinámico: Los tipos se determinan en tiempo de ejecución.
```

## Página 26

[Ver página original](teorica-08-modelo-de-actores.pdf#page=26)

```text
   Variables y la shell





      • En la shell, se pueden ver las variables ligadas actualmente con b().
      • También se pueden desligar variables:
         • f(Variable) desliga la variable Variable.

         • Usar f() desliga todas las variables.
         • f() no se puede usar en programas (es un comando comandos del REPL, no
          una función de Erlang).
```

## Página 27

[Ver página original](teorica-08-modelo-de-actores.pdf#page=27)

```text
   Pasaje de parámetros






      • Por valor: los argumentos de una llamada a función se evalúan, se copian en
       los parámetros formales antes de evalúar el cuerpo de la función.

      • No existen llamadas por referencia.
```

## Página 28

[Ver página original](teorica-08-modelo-de-actores.pdf#page=28)

```text
   Estructuras de Datos Complejas en Erlang



      • Se crean anidando tipos de datos compuestos entre sí.

     {persona, "Juan", "Perez",
       [
        {numerocalzado, 42},
        {mascotas, [{gato, micho}, {gato, dulce}]},
        {hijos, [{tomas, 21}, {clara, 17}]}
       ]
     },
     {persona, "Carolina", "Gómez",
       [
        {numerocalzado, 41},
        {gustos, [botes, vino]}
       ]
     }
```

## Página 29

[Ver página original](teorica-08-modelo-de-actores.pdf#page=29)

```text
   Pattern Matching






      • Se utiliza para:
         • Asignar valores a variables
         • Controlar el flujo de ejecución de programas
         • Extraer valores de tipos de datos compuestos
```

## Página 30

[Ver página original](teorica-08-modelo-de-actores.pdf#page=30)

```text
   Ejecución Pattern Matching


      • Puede tener éxito, lo que liga variables no asignadas y devuelve el valor de
       la expresión.
      • Puede fallar, sin que ninguna variable se asigne.

    Ejecución
      • Se evalúa la Expresión en el lado derecho del operador =.

      • Se compara su valor con el Patrón.
      • Criterios de Éxito:
         • La expresión y el patrón deben tener la misma forma.
         • Los literales en el patrón deben ser iguales a los valores en el lugar
          correspondiente en el valor de la expresión.
         • Las variables vinculadas deben tener el mismo valor que el lugar
          correspondiente en la expresión.
```

## Página 31

[Ver página original](teorica-08-modelo-de-actores.pdf#page=31)

```text
   Ejemplo: Pattern matching





     1> List = [1,2,3,4].
     [1,2,3,4]
     2> [Head|Tail] = List.
     [1,2,3,4]
     3> Head.
     1
     4> Tail.
     [2,3,4]
```

## Página 32

[Ver página original](teorica-08-modelo-de-actores.pdf#page=32)

```text
   Ejemplo: Pattern matching





     5> [Head|Tail] = [1].
     ** exception error: no match of right hand side value [1]
     6> [Head|Tail] = [1,2,3,4].
     [1,2,3,4]
     7> [Head1|Tail1] = [1].
     [1]
     8> Tail1.
     []
```

## Página 33

[Ver página original](teorica-08-modelo-de-actores.pdf#page=33)

```text
   Ejemplo: Pattern matching





     9> {Element, Element, X} = {1,1,2}.
     {1,1,2}
     10> {Element, Element, X} = {1,2,3}.
     ** exception error: no match of right hand side value {1,2,3}
     11> {Element, Element, _} = {1,1,2}.
     {1,1,2}
      • El símbolo de comodín (_) coincide con cualquier valor y no liga variables.
```

## Página 34

[Ver página original](teorica-08-modelo-de-actores.pdf#page=34)

```text
   Definición de funciones


      • Las funciones tienen un atomo por nombre.
      • Son una colección de cláusulas separadas por punto y coma y terminadas
       con un punto.
      • Cada cláusula tiene una cabecera que especifica un patrón.

      • Las cláusulas se evalúan sucesivamente, y el valor de retorno de una función
       es el resultado de la última expresión ejecutada.

     area({square, Side}) ->
       Side * Side ;
     area({circle, Radius}) ->
       math:pi() * Radius * Radius;
     area({triangle, A, B, C}) ->
       S = (A + B + C)/2,
       math:sqrt(S*(S-A)*(S-B)*(S-C));
     area(_) ->
       {error, invalid_object}.
```

## Página 35

[Ver página original](teorica-08-modelo-de-actores.pdf#page=35)

```text
   Guardas



      • Son restricciones adicionales en una cláusula: when seguida de una expresión

       de guarda (como el | de Haskell).
      • Se colocan antes de la flecha (->).
      • Una cláusula se selecciona solo si coincide el patrón coincide y la guarda
       evalúa true.

      • Ejemplo de la función factorial:
     factorial(N) when N > 0 ->
       N * factorial(N - 1);
     factorial(0) -> 1.
```

## Página 36

[Ver página original](teorica-08-modelo-de-actores.pdf#page=36)

```text
   Módulos


      • Las funciones se agrupan en módulos.

      • Los módulos consisten en archivos con la extensión .erl, donde los nombres
       de archivo y de módulo deben ser iguales.
      • Los módulos se nombran con la directiva -module(Name).
      • La directiva export contiene una lista de funciones exportadas en el

       formato funcion/aridad.
      • Identificación Única de Funciones: Las funciones se identifican de
       manera única por su nombre, su aridad y el módulo en el que están definidas.
      • Las funciones se pueden llamar desde fuera del módulo, agregando el
       nombre del módulo.

      • Para usarlas desde el shell, se compila el módulo usando c(modulo).
```

## Página 37

[Ver página original](teorica-08-modelo-de-actores.pdf#page=37)

```text
   Módulos


     -module(demo).
     -export([double/1,area/1]).
     -import(math, [sqrt/1]).
     double(Value) ->
       times(Value, 2).
     times(X,Y) ->
       X*Y.
     area({square, Side}) ->
       Side * Side ;
     area({circle, Radius}) ->
       math:pi() * Radius * Radius;
     area({triangle, A, B, C}) ->
       S = (A + B + C)/2,
       math:sqrt(S*(S-A)*(S-B)*(S-C));
     area(_) ->
       {error, invalid_object}.
```

## Página 38

[Ver página original](teorica-08-modelo-de-actores.pdf#page=38)

```text
   Condicionales

      • Tres formas de evaluación condicional:
         • Elección de una cláusula de función de pattern matching.
         • case (también basado en pattern matching)

         • if existe, pero es limitado (no admite funciones propias), se suele preferir
          usar case.
     case expresion-condicional of
       Patron_1 -> expresion1, expresion2, .. ;
       Patron_2 -> expresion1, expresion2, .. ;
       ... ;
       Patron_n -> expresion1, expresion2, .. %no lleva punto y coma
     end %algún patron tiene que matchear o da error
     if
       Guarda_1 -> expresion1, expresion2, .. ;
       Guarda_2 -> expresion1, expresion2, .. ;
       ... ;
       Guarda_n -> expresion1, expresion2, .. %no lleva punto y coma
     end %alguna guarda debe dar true o da error
```

## Página 39

[Ver página original](teorica-08-modelo-de-actores.pdf#page=39)

```text
   Funciones y Expresiones case



      • Como ambas se basan en pattern matching, las definiciones de funciones y

       las expresiones case son similares.
         listlen([]) -> 0;
         listlen([_|Xs]) -> 1 + listlen(Xs).
      • Puede reescribirse utilizando una expresión case:

         listlen(Y) ->
          case Y of
           []   -> 0;
           [_|Xs] -> 1 + listlen(Xs)
          end.
```

## Página 40

[Ver página original](teorica-08-modelo-de-actores.pdf#page=40)

```text
   Funciones y Expresiones case


      • Se puede usar pattern matching en varios parámetros.

         index(0,[X|_])   -> X;
         index(N,[_|Xs]) when N>0 -> index(N-1,Xs).
      • Una expresión case compara con una única expresión.

      • Se pueden agrupar en una tupla:
         index(X,Y) ->
           index({X,Y}).
         index(Z) ->
          case Z of
           {0,[X|_]}      -> X;
           {N,[_|Xs]} when N>0 -> index(N-1,Xs)
          end.
```

## Página 41

[Ver página original](teorica-08-modelo-de-actores.pdf#page=41)

```text
   Contexto (scope) de Variables



      • En Erlang, es dentro de la misma cláusula de la definición de una función.
         f(X)   -> Y=X+1,Y*X.
         g([0|Xs]) -> g(Xs);
         g([Y|Xs]) -> Y+g(Xs);
         g([])  -> 0.
      • Si se liga dentro de una rama de un if, hay que ligar en todas las ramas, si
       no da error.
         unsafe(X) ->
          case X of
           one -> Y = true;
            _ -> Z = two
          end,
          Y. %error: variable Y unsafe in ’case’
```

## Página 42

[Ver página original](teorica-08-modelo-de-actores.pdf#page=42)

```text
   Alto orden





    Erlang admite funciones de alto orden que reciben otras funciones como
    parámetro.
      • apply/3: ejecuta la función nombrada en los argumentos especificados y
       devuelve su resultado.
         • apply(Module, Function, Arguments).

            apply(demo, area, [{triangle, 2 , 3, 4}]).
```

## Página 43

[Ver página original](teorica-08-modelo-de-actores.pdf#page=43)

```text
                        Actores en Erlang
```

## Página 44

[Ver página original](teorica-08-modelo-de-actores.pdf#page=44)

```text
   Modelo de Actores en Erlang




      • La manera en Erlang de realizar las 3 acciones del modelo de Actores es:
         • Enviar: !, asíncrono, sin acuses ni señales de falla.
         • Crear: spawn, nos devuelve la identidad del nuevo actor.
         • Recibir selectivamente: receive, compara con patrones y elige qué

          procesar. El designar el comportamiento para el próximo mensaje se hace por
          medio de recursión.
      • Veamos cómo funcionan; de aquí en adelante nos basamos principalmente
       en estas primitivas.
```

## Página 45

[Ver página original](teorica-08-modelo-de-actores.pdf#page=45)

```text
   Direccionamiento para mensajes




      • Cada actor tiene una identidad que no se puede falsificar: se copia y viaja
       dentro de los mensajes.
      • Esa identidad puede ser enviada como parte de un mensaje: la topología de
       comunicación se arma en ejecución.

      • Además de la identidad, es posible registrar nombres para direcciones
       públicas bien conocidas de servicios.
      • En Erlang, crear un proceso devuelve la identidad (Pid) del proceso recién
       creado, y self() da la propia. register permite publicar un alias.
```

## Página 46

[Ver página original](teorica-08-modelo-de-actores.pdf#page=46)

```text
   Comunicación mediante Mensajes en Erlang


      • Para enviar se usa la construcción Pid ! Mensaje:
         • Pid es un identificador de proceso válido;
         • Mensaje es un valor de cualquier tipo de datos.










      • Cada proceso tiene un buzón (mailbox) en el que se almacenan los mensajes
       entrantes.
```

## Página 47

[Ver página original](teorica-08-modelo-de-actores.pdf#page=47)

```text
   Comunicación mediante Mensajes en Erlang





      • Cuando se envía un mensaje, se copia del proceso emisor al buzón del
       proceso receptor.
      • Los mensajes se almacenan en el orden en que se entregan.
      • Si se envían dos mensajes de un mismo proceso a otro, se garantiza que los

       mensajes se recibirán en el mismo orden en que se enviaron.
      • Esto no aplica a mensajes enviados desde diferentes procesos.
```

## Página 48

[Ver página original](teorica-08-modelo-de-actores.pdf#page=48)

```text
   Sobre el Envío de Mensajes





      • La comunicación es asíncrona
         • un proceso emisor no se suspende al enviar un mensaje;
         • continúa inmediatamente ejecutando la siguiente expresión en su código.
         • el Mailbox de los procesos es un buffer dinámico sin cota de tamaño fija.
      • El envío de un mensaje nunca fallará.

         • Si el receptor no existe, el mensaje se descarta sin generar un error.
```

## Página 49

[Ver página original](teorica-08-modelo-de-actores.pdf#page=49)

```text
   Comunicación mediante Mensajes en Erlang


      • La shell es un proceso : loop de lectura, evaluación y escritura.
         • self/0: devuelve el identificador del proceso en el que se evalúa.
      • flush/0 permite recuperar y eliminar los mensajes que están en el buzón de

       la shell.
     16> Shell = self().
     <0.1081.0>
     17> flush().
     ok
     18> Shell!hola.
     hola
     19> Shell!mundo.
     mundo
     20> flush().
     Shell got hola
     Shell got mundo
     ok
```

## Página 50

[Ver página original](teorica-08-modelo-de-actores.pdf#page=50)

```text
   Envío de Mensajes en Erlang





      • Como toda expresión, Pid ! Mensaje devuelve un valor.
         • El mensaje enviado.
      • Se puede aprovechar para enviar el mismo mensaje a varios procesos:

       Pid3 ! Pid2 ! Pid1 ! Mensaje
         • ! es asociativo a derecha:
            • Pid3 ! (Pid2 ! (Pid1 ! Mensaje))
```

## Página 51

[Ver página original](teorica-08-modelo-de-actores.pdf#page=51)

```text
   Creacion de Procesos en Erlang


      • Para crear un proceso se usa la BIF
       spawn(Module, Function, Arguments).
         • Crea un nuevo proceso que evalúa la función Function exportada del
          módulo Module con la lista Arguments como parámetros.
      • Devuelve un identificador de proceso: pid (process identifier).







      • Esta llamada crea un nuevo proceso con pid Pid2.

      • El pid Pid2 se devuelve como resultado del spawn
```

## Página 52

[Ver página original](teorica-08-modelo-de-actores.pdf#page=52)

```text
   Terminación de Procesos en Erlang




      • Una vez creado, un proceso continuará ejecutándose y permanecerá activo
       hasta que termine.
         • Si no hay más código para ejecutar, el proceso termina de manera normal.
         • Si ocurre un error, el proceso termina de manera anormal.
      • La creación de un proceso nunca falla

         • Si se crea con una función no existente,
            • el proceso se crea,
            • spawn/3 devuelve el pid,
            • el proceso recién creado termina con un error.
```

## Página 53

[Ver página original](teorica-08-modelo-de-actores.pdf#page=53)

```text
   Información sobre Procesos

      • La BIF processes() lista de todos los procesos en ejecución.

      • El comando de la shell i() muestra los procesos que están ejecutando.
     1> i().
     Pid           Initial Call            Heap Reds Msgs
     Registered    Current Function        Stack
     <0.0.0>       erl_init:start/2         987 4852 0
     init          init:loop/1               2
     <0.1.0>       erts_code_purger:start/0  4   27  0
     erts_code_purger erlang:hibernate/3     1
     <0.2.0>       erts_literal_area_collector:start 4 11 0
                   erlang:hibernate/3        1
     <0.3.0>       erts_dirty_process_signal_handler 233 134 0
                   erts_dirty_process_signal_handler 2
     <0.4.0>       erts_dirty_process_signal_handler 233 10 0
                   erts_dirty_process_signal_handler 2
     <0.5.0>       erts_dirty_process_signal_handler 233 10 0
                   erts_dirty_process_signal_handler 2
     <0.6.0>       prim_file:start/0        233  9   0
```

## Página 54

[Ver página original](teorica-08-modelo-de-actores.pdf#page=54)

```text
   Recepción selectiva de mensajes


      • El actor recorre su buzón en orden de llegada, comparando cada mensaje
       con sus patrones.
      • Los mensajes que no matchean quedan en el buzón, pero se continúa con
       los siguientes.

      • Si ningún mensaje matchea, receive se bloquea esperando.

    • De esta manera, podemos:
        • tolerar desorden entre distintos emisores
         (pues no sabemos en qué orden llegarán
         sus mensajes),
        • esperar una respuesta específica, o
        • ignorar mensajes que no son de interés
         (por ahora).
```

## Página 55

[Ver página original](teorica-08-modelo-de-actores.pdf#page=55)

```text
   Recepción de Mensajes en Erlang

      • Los mensajes se recuperan del buzón del proceso utilizando la cláusula

       receive.
         • Está delimitada por las palabras reservadas receive y end,
         • contiene cláusulas similares a las cláusulas case.
         receive
          {reset, Board} -> reset(Board);
          _Other     -> {error, unknown_msg}
         end
      • Al ejecutar un receive:
         • Se compara al primer mensaje del buzón con cada patrón en el receive.
         • Si hay coincidencia, se consume al mensaje del buzón, se ligan las variables
          del patrón y se ejecuta el cuerpo de la cláusula.
         • Si ninguna de las cláusulas coincide, se consideran los mensajes siguientes.

      • La instrucción receive devolverá la última expresión evaluada
```

## Página 56

[Ver página original](teorica-08-modelo-de-actores.pdf#page=56)

```text
   Ejemplo



     - module(echo).
     - export([loop/0]).
     loop() ->
       receive
        Msg -> io: format ("Echo:~p\n" ,[Msg]),
             loop()
       end.
     42> c(echo).
     {ok,echo}
     43> Echo = spawn(echo,loop,[]).
     <0.1130.0>
     44> Echo!hola.
     Echo:hola
     hola
```

## Página 57

[Ver página original](teorica-08-modelo-de-actores.pdf#page=57)

```text
   Pedidos con respuesta en mensajería asíncrona



      • El envío es unidireccional, pero frecuentemente necesitamos preguntar y
       obtener respuesta.

      • Solución: el pedido lleva la identidad del emisor {self(), pregunta}
      • El servidor responde a esa identidad; el emisor se bloquea esperando su
       respuesta.

      • Usamos un tag de correlación para distinguir la respuesta del resto de los
       mensajes en el mailbox del emisor
      • Problema: Si la respuesta nunca llega, el emisor se bloquea para siempre (se
       puede detectar con un timeout)
```

## Página 58

[Ver página original](teorica-08-modelo-de-actores.pdf#page=58)

```text
   Ejemplo


     - module(echo2).
     - export([loop/0]).
     loop() ->
       receive
        {Msg, Other} -> io: format ("Received ~p\n", [Msg]),
                   Other ! Msg,
                   loop ()
       end.
     58> c(echo2).
     {ok,echo2}
     59> Echo = spawn(echo2,loop,[]).
     <0.1166.0>
     60> Echo!{hola, self()}.
     Received hola
     {hola,<0.1158.0>}
     61> flush().
     Shell got hola
     ok
```

## Página 59

[Ver página original](teorica-08-modelo-de-actores.pdf#page=59)

```text
   Recepción no selectiva

      • Cuando no es necesario distinguir los mensajes, se vinculan a una variable.













      • El primer mensaje que llega al Pid3 se procesa, independientemente del
       orden en que se hayan enviado.
```

## Página 60

[Ver página original](teorica-08-modelo-de-actores.pdf#page=60)

```text
   Movilidad en la comunicación












      • El proceso PidA envia una tupla etiquetada con su propio identificador
      • PidB lo recibirá, y lo envía a PidC

      • PidC usa el valor recibido para enviar el mensaje foo a PidA.
```

## Página 61

[Ver página original](teorica-08-modelo-de-actores.pdf#page=61)

```text
   Ejemplo


     -module(echo3).
     -export([go/0, loop/0]).
     go() ->
       Pid = spawn(echo3, loop, []),
       Pid ! {self(), hello},
       receive
         {Pid, Msg} ->
          io:format("~w~n",[Msg])
       end,
       Pid ! stop.
     loop() ->
       receive
         {From, Msg} ->
           From ! {self(), Msg},
           loop();
         stop ->
          true
       end.
```

## Página 62

[Ver página original](teorica-08-modelo-de-actores.pdf#page=62)

```text
   Procesos Registrados en Erlang





      • Se pueden registrar procesos que ofrecen servicios específicos con un alias o
       nombre que puede usarse en lugar del pid.
      • Se pueden registrar con register(Alias, Pid)
         • El Alias es un átomo.

      • Despues de registrado se pueden enviar mensajes usando el alias:
       Alias ! Mensaje.
```

## Página 63

[Ver página original](teorica-08-modelo-de-actores.pdf#page=63)

```text
   Ejemplo


     -module(echo4).
     -export([go/0, loop/0]).
     go() ->
       register(echo, spawn(echo4, loop, [])),
       echo ! {self(), hello},
       receive
        {_Pid, Msg} ->
          io:format("~w~n",[Msg])
       end.
     loop() ->
       receive
        {From, Msg} ->
        From ! {self(), Msg},
           loop();
        stop ->
          true
       end.
```

## Página 64

[Ver página original](teorica-08-modelo-de-actores.pdf#page=64)

```text
   Ejemplo



     4> c(echo4).
     {ok,echo4}
     5> echo4:go().
     hello
     ok
     6> whereis(echo).
     <0.99.0>
     7> echo!{self(), hey}.
     {<0.84.0>,hey}
     8> flush().
     Shell got {<0.99.0>,hey}
     ok
     9> echo!stop.
     stop
     10> whereis(echo).
     undefined
    regs() : permite obtener los procesos registrados.
```

## Página 65

[Ver página original](teorica-08-modelo-de-actores.pdf#page=65)

```text
   Ejemplo





      • Un envío de mensaje a un proceso que no existe causa un error badarg.
      • Es distinto a enviar a un Pid inexistente (que se ignoran).

     17> pepe!asfd.
     ** exception error: bad argument
         in operator !/2
           called as pepe ! asfd
```

## Página 66

[Ver página original](teorica-08-modelo-de-actores.pdf#page=66)

```text
   Receive con Timeouts



     receive
       Pattern1 when Guard1 -> exp11, .., exp1n;
       Pattern2 when Guard2 -> exp21, .., exp2n;
       ...
       Other         -> expn1, .., expnn
     after
       Timeout -> exp1, .., expn
     end
      • Timeout es un entero que denota el tiempo en milisegundos .
      • Cuando un proceso alcanza un receive y ninguno de los mensajes coincide,
       espera durante Timeout milisegundos.
      • Si después de ese lapso no llega ningún mensaje, se ejecutarán las
       expresiones en el cuerpo de la cláusula after.
```

## Página 67

[Ver página original](teorica-08-modelo-de-actores.pdf#page=67)

```text
   Envío diferido con sleep



      • Se puede usar para suspender un proceso durante un período en

       milisegundos o enviar mensajes con retraso.
     sleep(Time) ->
       receive
        after Time -> ok
       end.
     send_after(Time, Pid, Message) ->
       receive
        after Time ->
           Pid ! Message
       end.
```

## Página 68

[Ver página original](teorica-08-modelo-de-actores.pdf#page=68)

```text
   Patrón común de los procesos




      • Crear el proceso.
      • Registrar el alias.
      • Inicializar el estado (del loop)

         • Muchas veces los datos que se pasan al spawn.
         • En general se almacenan en una variable (o estado del proceso).
      • Una función de recepción-evaluación toma el estado:
         • recibe un mensaje, lo maneja, actualiza el estado y lo pasa de nuevo como
          argumento a una llamada recursiva.
```

## Página 69

[Ver página original](teorica-08-modelo-de-actores.pdf#page=69)

```text
   Ejemplos de Actores en Erlang







    Veamos algunos ejemplos de programas hechos con el modelo de Actores en
    Erlang.
```

## Página 70

[Ver página original](teorica-08-modelo-de-actores.pdf#page=70)

```text
   Echo Server




     - module(echo).
     - export([loop/0]).
     loop () ->
       receive
         Msg -> io:format("Echo: ~p\n",[Msg]),
        loop ()
       end
     spawn(echo,loop,[]) ! 1.
     Echo: 1
     1
```

## Página 71

[Ver página original](teorica-08-modelo-de-actores.pdf#page=71)

```text
   Contador


     -module(counter).
     -export([new/0, inc/1, value/2, stop/1, init/0]).

     new() -> spawn (counter , init , []).
     stop(Counter) -> Counter ! stop.

     init() -> loop(0).
     inc(Counter) ->
         Counter ! increment.
     value(Counter, From) -> Counter ! {From, value}.
     loop(Val) -> receive
                increment -> loop(Val + 1);
                {From , value} -> From ! Val , loop(Val);
                stop -> true
              end.
```

## Página 72

[Ver página original](teorica-08-modelo-de-actores.pdf#page=72)

```text
   Cliente de Contador





     1> c(counter).
     {ok,counter}
     2> C = counter:new().
     <0.121.0>
     3> counter:value(C,self()).
     {<0.123.0>,value}
     4> flush().
     Shell got 0
     ok
```

## Página 73

[Ver página original](teorica-08-modelo-de-actores.pdf#page=73)

```text
   Contador (funcional)


     -module(counter2).
     -export([start/0, inc/0, value/0, stop/0, init/0]).
     start() -> register(counter, spawn(counter2 , init , [])).
     stop() -> counter ! stop.
     init() -> loop(0).
     inc() ->
         counter ! {self(), increment},
         receive
            Val -> Val
         end.
     value() ->
         counter ! {self(), value},
         receive
            Val -> Val
         end.
     loop(Val) -> receive
                {From , increment} -> From!Val+1, loop(Val + 1);
                {From , value} -> From!Val , loop(Val);
                stop -> true
              end.
```

## Página 74

[Ver página original](teorica-08-modelo-de-actores.pdf#page=74)

```text
   Cliente de Contador Funcional




     1> c(counter2).
     {ok,counter2}
     2> counter2:start().
     true
     3> counter2:value().
     0
     4> counter2:inc().
     1
     5> counter2:inc().
     2
     6> counter2:value().
     2
```

## Página 75

[Ver página original](teorica-08-modelo-de-actores.pdf#page=75)

```text
   El actor como máquina de estados




      • Un actor con memoria es una FSM: cada estado es el comportamiento para
       procesar el próximo mensaje.
      • Cada mensaje recibido puede disparar una transición: las acciones + la
       elección del próximo comportamiento.

      • El estado vive en los argumentos del loop; cambiar de estado es llamar al
       loop siguiente.
      • Ejemplo: un semáforo (sus estados son libre/ocupado, sus eventos
       acquire/release).
```

## Página 76

[Ver página original](teorica-08-modelo-de-actores.pdf#page=76)

```text
   Semáforo


     -module(sem).
     -export([new/0, acquire/1, release/1]).
     new() -> spawn(fun()-> free() end).
     acquire(S) ->
       S ! {acquire, self()},
       receive
        _ -> true
       end.
     release(S) ->
        S ! release.
     free() ->
        receive
           {acquire, From} -> From ! true, busy()
        end.
     busy() ->
        receive
           release -> free()
        end.
```

## Página 77

[Ver página original](teorica-08-modelo-de-actores.pdf#page=77)

```text
   Todo junto



                 Cliente 1 increment
                         1   counter (registrado)
                                      buzón
                                          3
                                     increment
                      4{c2, valor}
                               Contador {c2,valor}
                 Cliente 2           5
                             2
                                        acqu6ire
                           1
                        acquire        free busy
                 Cliente 3     Semáforo
                                         signal
                         true
   1 envío asíncrono: sin acuses 4 la identidad viaja en los mensajes
   2 request-reply: tag de correlación 5 recepción selectiva
   3 orden por par emisor-receptor 6 FSM:estado=próximocomportamiento
```
