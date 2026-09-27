# teorica-01-introduccion — transcripción

- Fuente: [teorica-01-introduccion.pdf](teorica-01-introduccion.pdf)
- Páginas del PDF: 90.
- SHA-256 del PDF: `ea6bfbafa8cecc07d8720146195a649dea405d51cad99cd609440d65123df03d`.
- Tipo: transcripción textual por página, con disposición espacial conservada y referencias al original para esquemas.

> Se conserva el contenido del original, incluidos ejemplos incorrectos, erratas y diapositivas progresivas. Los bloques `text` preservan columnas y sangría; no son código listo para compilar. En código repartido en columnas, seguir las indicaciones «continúa a la derecha». Las notas editoriales se distinguen del original. Para símbolos cuya extracción sea ambigua, consultar la página enlazada.

## Página 1

[Ver página original](teorica-01-introduccion.pdf#page=1)

```text
Programación concurrente y paralela

Introducción
```

## Página 2

[Ver página original](teorica-01-introduccion.pdf#page=2)

```text
Condiciones generales



   • Docentes:
       • Julián Zylber (Ay 1) - jzylber [[AT]] dc.uba.ar
       • Jorge Szabo (Ay 1) - jorgecszabo [[AT]] gmail.com
       • Tomás Chimenti (JTP) - tach.365 [[AT]] gmail.com
       • Pablo Terlisky (JTP) - terlisky [[AT]] dc.uba.ar
       • Hernán Melgratti (Profesor) - hmelgra [[AT]] dc.uba.ar
   • Horario:
       • Teóricas: Lunes de 17 a 22
       • Prácticas: Miércoles de 17 a 22
```

## Página 3

[Ver página original](teorica-01-introduccion.pdf#page=3)

```text
Recursos




   • Bibliografía:
        • Textos: no hay un texto principal. Referencias en la página web
        • Publicaciones relacionadas
        • Diapositivas de clases
   • Página web en capus: Información al día del curso,
   • Comunicación: En el campus
```

## Página 4

[Ver página original](teorica-01-introduccion.pdf#page=4)

```text
Programación: Enfoque clásico (secuencial)


  Programas = Algoritmos + Estructuras de datos (N. Wirth)
    • Datos: Representación de la información en la memoria de la
      computadora.
    • Algoritmos: Secuencia de instrucciones que describe cómo transformar
      datos (entradas en salidas).

  Desmenuzando
    • Programa: Transforma datos de entrada en datos de salida.
    • Secuencialidad: El orden de ejecución de los pasos es total.
        • La ejecución de las instrucciones no se solapa.
```

## Página 5

[Ver página original](teorica-01-introduccion.pdf#page=5)

```text
Enfoque clásico: Semántica

  Semántica denotacional de un programa secuencial
    • Un programa describe una transformación de estados.
    • El Estado asigna valores (Val) a variables (Var) como abstracción de la
      memoria:
        • Conjunto de estados: Σ = Var → Val
        • Un estado específico: σ, s ∈ Σ
    • El significado de un programa P, denotado como JPK, es una función
      parcial de estado en estado:

                                    JPK : Σ ,→ Σ
```

## Página 6

[Ver página original](teorica-01-introduccion.pdf#page=6)

```text
IMP (Pequeño lenguaje imperativo)



  Sintaxis
   x ∈ Var                                                           (Variables)
   n ∈ Int                                                           (Enteros)
   A ::= n | x | A + A | A − A | A × A                               (Expr. Arit.)
   B ::= true | false | A = A | A ≤ A | ¬B | B ∧ B                   (Expr. Bool.)
   C ::= skip | x := A | C ; C | if B then C else C | while B do C   (Comandos)
```

## Página 7

[Ver página original](teorica-01-introduccion.pdf#page=7)

```text
Semántica Denotacional: Expresiones 1

  Expresiones Aritméticas: JAK : Σ → Num

   JnK        = λσ.n                                 JA1 − A2 K = λσ.JA1 Kσ − JA2 Kσ
   Jx K       = λσ.σ x                               JA1 × A2 K = λσ.JA1 Kσ × JA2 Kσ
   JA1 + A2 K = λσ.JA1 Kσ + JA2 Kσ

  Expresiones Booleanas: JBK : Σ → {true, false}

   JtrueK          = λσ.true                        JA1 ≤ A2 K   = λσ.(JA1 Kσ ≤ JA2 Kσ)
   JfalseK         = λσ.false                       J¬BK         = λσ.¬(JBKσ)
   JA1 = A2 K      = λσ.(JA1 Kσ = JA2 Kσ)           JB1 ∧ B2 K   = λσ.(JB1 Kσ ∧ JB2 Kσ)

   1
       Asumimos cálculo lambda con enteros, booleanos y fix
```

**Información gráfica:** [consultar el diagrama o tabla de la página 7](teorica-01-introduccion.pdf#page=7). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 8

[Ver página original](teorica-01-introduccion.pdf#page=8)

```text
Semántica Denotacional: Comandos 2


  Comandos: JC K : Σ ,→ Σ

       JskipK                     = λσ.σ
       JC1 ; C2 K                 = JC2 K ◦ JC1 K
       Jx := AK                   = λσ.σ[x 7→ JAKσ]
       Jif B then C1 else C2 K    = λσ.if JBKσ then JC1 Kσ else JC2 Kσ
       Jwhile B do C K            = fix (λf .λσ.if JBKσ then f (JC Kσ) else σ)



   2
       Asumimos cálculo lambda con enteros, booleanos y fix
```

**Información gráfica:** [consultar el diagrama o tabla de la página 8](teorica-01-introduccion.pdf#page=8). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 9

[Ver página original](teorica-01-introduccion.pdf#page=9)

```text
Terminación y Correctitud
   • La denotación para expresiones son funciones totales.
          • Su evaluación siempre termina.
   • La denotación para comandos es una función parcial.
    def
  C = while true do skip

          JC K = fix(λf .λσ.if JtrueKσ then f (JskipKσ) else σ) = fix(λf .λσ.f σ)
  El punto fijo está indefinido (JC K = ⊥), lo que muestra que la ejecución no
  termina para ningún estado (entrada).

  Correctitud
  Un programa es correcto solo si su semántica es una función total sobre las
  entradas válidas (garantiza la terminación).
```

**Información gráfica:** [consultar el diagrama o tabla de la página 9](teorica-01-introduccion.pdf#page=9). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 10

[Ver página original](teorica-01-introduccion.pdf#page=10)

```text
Semántica como funciones




  Determinismo
   • El significado de un programa se modela a través de una función.
   • En consecuencia la ejecución de programas es determinística:
       • Si la ejecución termina para un estado σ, el estado resultante σ ′ es único.
       • Formalmente: ∀σ, σ1 , σ2 ∈ Σ, JC Kσ = σ1 ∧ JC Kσ = σ2 =⇒ σ1 = σ2 .
```

## Página 11

[Ver página original](teorica-01-introduccion.pdf#page=11)

```text
Enfoque clásico: Equivalencia

  Equivalencia
    • Dos programas son equivalentes si denotan a la misma función.
    • C1 y C2 son equivalentes (C1 ≃ C2 ) si JC1 K = JC2 K.

  Ejemplo

    • Considerar C1 def           def
                    = x := 1 y C2 = x := 0; x := x + 1.
    • Luego JC1 K = λσ.σ[x 7→ 1]
            JC2 K = λσ.σ[x 7→ σx + 1] ◦ λσ.σ[x 7→ 0] = λσ.σ[x 7→ 1]

    • C1 y C2 son equivalentes porque JC1 K = JC2 K
```

**Información gráfica:** [consultar el diagrama o tabla de la página 11](teorica-01-introduccion.pdf#page=11). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 12

[Ver página original](teorica-01-introduccion.pdf#page=12)

```text
Enfoque clásico: La equivalencia es una congruencia



  Principio de sustitutividad
  Si dos programas son equivalentes, se puede reemplazar uno por otro dentro de
  un programa más grande sin alterar su significado.

  P1 = x := 1 y P2 = x := 0; x := x + 1
    • Como P1 y P2 son equivalentes (P1 ≃ P2 ), para todo P valen
         • P; P1 ≃ P; P2
         • P1 ; P ≃ P 2 ; P
```

**Información gráfica:** [consultar el diagrama o tabla de la página 12](teorica-01-introduccion.pdf#page=12). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 13

[Ver página original](teorica-01-introduccion.pdf#page=13)

```text
Contextos

  Sintaxis

                C ::= [·]                   (Agujero / Hueco)
                   |   C; C | C ; C         (Secuencia)
                   |   if B then C else C   (Bifurcación Izquierda)
                   |   if B then C else C   (Bifurcación Derecha)
                   |   while B do C         (Iteración)


  Aplicación de Contexto
  C[C ] denota al comando que se obtiene al reemplazar el agujero [·] en C por C .
```

## Página 14

[Ver página original](teorica-01-introduccion.pdf#page=14)

```text
Enfoque clásico: La equivalencia es una congruencia



  Congruencia
  ≃ es una congruencia: Para todo C[·], se cumple C1 ≃ C2 =⇒ C[C1 ] ≃ C[C2 ]

  Notar
    • Se puede demostrar por inducción en el estructura de C.
    • Caso C = C ′ ; C . P; P1 ≃ P; P2 ya que JC[C 1]K = JC ′ [C 1]; C K =
      JC K ◦ JC ′ [C 1]K = JC K ◦ JC ′ [C 2]K = JC ′ [C 2]; C K = JC[C 2]K.
    • los restantes son análogos
```

## Página 15

[Ver página original](teorica-01-introduccion.pdf#page=15)

```text
Límites del modelo secuencial



  Sistemas actuales de cómputo
    • El cómputo puramente secuencial es marginal.
        • Eficiencia: Latencia en operaciones de entrada/salida y optimización del
          uso compartido de CPU.
        • Hardware: Arquitecturas de múltiples núcleos (multicore) multinivel.
        • Distribución: Componentes de software que se ejecutan
          concurrentemente en distintos dispositivos físicos.
```

## Página 16

[Ver página original](teorica-01-introduccion.pdf#page=16)

```text
Cómputo concurrente




  Orden de ejecución no secuencial
    • El orden de ejecución de instrucciones es un orden parcial.
    • El orden relativo de ejecución entre algunas instrucciones no está
      especificado:
        • pueden ser ejecutados en cualquier orden
```

## Página 17

[Ver página original](teorica-01-introduccion.pdf#page=17)

```text
IMP Concurrente

  Sintaxis
   x ∈ Var                                                             (Variables)
   n ∈ Int                                                             (Enteros)
   A ::= n | x | A + A | A − A | A × A                                 (Expr. Arit.)
   B ::= true | false | A = A | A ≤ A | ¬B | B ∧ B                     (Expr. Bool.)
   C ::= skip | x := A | C ; C | if B then C else C | while B do C     (Comandos)
           | C |C

   • Operador |: paralelo (de menor prioridad)
   • C1 |C2 : indica el orden relativo de ejecución entre las instrucciones de C1 y
     C2 no está definido.
   • Llamamos (de manera imprecisa) procesos a C1 y C2 .
```

## Página 18

[Ver página original](teorica-01-introduccion.pdf#page=18)

```text
Ejemplo: Orden Parcial

  Definición del escenario
    • Sea C def                def                   def
             = C1 | C2 con C1 = x := 1; y := 2 y C2 = z := y .
    • Las instrucciones en C1 están totalmente ordenadas: (x := 1) < (y := 2)
    • Las instrucciones en C2 están totalmente ordenadas (relación vacía).
    • Sin embargo, z := y no es comparable con ninguna instrucción de C1 .

  ¿Qué significa esto?
    • Una ejecución válida de C debe respetar las restricciones locales:
        • Ejecutar x := 1 antes de y := 2.
        • Ejecutar z := y de forma arbitraria (antes, durante o después) de la
          ejecución de las instrucciones de C1 .
```

**Información gráfica:** [consultar el diagrama o tabla de la página 18](teorica-01-introduccion.pdf#page=18). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 19

[Ver página original](teorica-01-introduccion.pdf#page=19)

```text
Tiempo físico


  Solapamiento Temporal
    • ¿Pueden las ejecuciones de x := 1 y z := y solaparse en el mismo instante
      de tiempo físico?
    • Depende de la arquitectura de hardware.a
           • Imposible: Si disponemos de un único procesador que ejecuta una sola
             instrucción a la vez (SISD).
           • Posible: Si el sistema cuenta con múltiples procesadores, ya sean núcleos
             o distribuidos (MIMD).
    a
        Taxonomía de Flynn (1966).
```

## Página 20

[Ver página original](teorica-01-introduccion.pdf#page=20)

```text
Paralelismo y Concurrencia

  Paralelismo
  Al menos dos pasos de la ejecución de un programa se solapan en el tiempo
  (requiere hardware multiprocesador).

  Concurrencia
  Paralelismo potencial.
  Un único procesador ejecutará de manera secuencial alguna linearización del
  orden parcial.

  Importante
  Ni la ejecución paralela ni la concurrente se explican con un orden total.
```

## Página 21

[Ver página original](teorica-01-introduccion.pdf#page=21)

```text
¿Por qué concurrencia?




  Concurrencia como Abstracción
  Una abstracción que permite comprender la ejecución de programas que com-
  parten recursos:
    • Evita considerar detalles específicos de su ejecución física.
    • En particular, independiza el modelo matemático de la cantidad de
       procesadores reales sobre los que se ejecuta el software.
```

## Página 22

[Ver página original](teorica-01-introduccion.pdf#page=22)

```text
¿Cómo se estudia la concurrencia?

  Modelos Semánticos
   1. Concurrencia Real (True Concurrency): Modelos basados puramente
      en órdenes parciales.
   2. Entrelazado (Interleaving): Modelos que consideran la abstracción de
      un único procesador virtual.
        • La semántica de un programa captura el conjunto de todos los posibles
          órdenes totales que constituyen las linearizaciones del orden parcial.

  En esta materia:
    • Utilizaremos mayormente el entrelazado porque es
        • el más comunmente aceptado... especialmente en la literatura tradicional.
        • “el más simple”.
```

## Página 23

[Ver página original](teorica-01-introduccion.pdf#page=23)

```text
Ejecución concurrente


  Ejecución concurrente
  La ejecución de un programa concurrente consiste en ejecutar un entrelaza-
  miento arbitrario de las instrucciones atómicas de sus procesos.

                   def
  Ejecución de C = x := 1; y := 2 | z := y
    • Asumiendo cada asignación como atómica, se puede ejecutar como:
        • z := y ; x := 1; y := 2, o
        • x := 1; z := y ; y := 2, o
        • x := 1; y := 2; z := y .
    • La elección es arbitraria, no hay garantías sobre cuál será elegida.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 23](teorica-01-introduccion.pdf#page=23). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 24

[Ver página original](teorica-01-introduccion.pdf#page=24)

```text
Entrelazados arbitrarios


  Abstracción del tiempo
  Se ignoran cuestiones de tiempo para análizar un programa:
    • La próxima instrucción a ejecutar puede pertenecer a cualquier proceso.
    • La correctitud de un programa concurrente no depende de suposiciones
       sobre los tiempos exactos de ejecución.

  Discretización del espacio de estados
  Al eliminar la variable temporal, el análisis se simplifica:
    • Sólo se debe considerar una cantidad finita o enumerable de secuencias
       posibles de ejecución.
```

## Página 25

[Ver página original](teorica-01-introduccion.pdf#page=25)

```text
No determinismo

                       def
  Ejecución de C = x := 1; y := 2 | z := y
    • Asumiendo cada asignación como atómica, se puede ejecutar como:
            • z := y ; x := 1; y := 2:              λσ.σ[x 7→ 1, y 7→ 2, z 7→ σy ]a
            • x := 1; z := y ; y := 2:               λσ.σ[x 7→ 1, y 7→ 2, z 7→ σy ]
            • x := 1; y := 2; z := y :                λσ.σ[x 7→ 1, y 7→ 2, z 7→ 2]
    • Los dos primeros producen el mismo resultado, pero el tercero es distinto.
    a
        Denota actualización de estado


  No determinismo
  Distintas ejecuciones de un mismo programa con un mismo estado inicial pueden
  arrojar resultados distintos.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 25](teorica-01-introduccion.pdf#page=25). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 26

[Ver página original](teorica-01-introduccion.pdf#page=26)

```text
Hacia una semántica denotacional


  Notación
                         L
  Usaremos el operador       para denotar no determinismo

                               λσ.σ1 ⊕ σ2 ⊕ · · · ⊕ σn

  la función puede producir cualquier σi (1 ≤ i ≤ n)

    def
  C = x := 1; y := 2 | z := y

          JC K = λσ.σ[x 7→ 1, y 7→ 2, z 7→ σy ] ⊕ σ[x 7→ 1, y 7→ 2, z 7→ 2]
```

**Información gráfica:** [consultar el diagrama o tabla de la página 26](teorica-01-introduccion.pdf#page=26). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 27

[Ver página original](teorica-01-introduccion.pdf#page=27)

```text
Limitaciones del enfoque anterior

  No composicionalidad

    • Sean C1 def            def
               = x := 1 y C2 = x := 0; x := x + 1.
    • Luego (asumiendo que cada asignación es atómica):
     JC1 K = λσ.σ[x 7→ 1].          JC1 |C1 K = λσ.σ[x 7→ 1].
     JC2 K = λσ.σ[x 7→ 1].          JC1 |C2 K = λσ.σ[x 7→ 1] ⊕ σ[x 7→ 2].
    • Problema: La equivalencia no es una congruencia:
         • ∃C = [·] | C1 tal que C1 ≃ C2 y C[C1 ] ̸≃ C[C2 ].

  Limitación
  Esta visión pierde el hecho de que el estado puede ser alterado externamente por
  otro proceso durante la ejecución.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 27](teorica-01-introduccion.pdf#page=27). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 28

[Ver página original](teorica-01-introduccion.pdf#page=28)

```text
Resultado vs comportamiento

  Mismo resultado, diferente comportamiento
 Sean los procesos de bucle infinito:    Aislados, la denotación de todos ellos
         def
   • C1 = while true do skip             es idéntica (divergencia pura):
   • C2 def
        = while true do x := x + 1           JC1 K = JC2 K = JC3 K = λσ.⊥
         def
   • C3 = while true do y := y + 1

  Sin embargo, tienen distinta interacción:
    • C1 es independiente (no altera variables).
    • C2 comunica o interactúa modificando continuamente la variable x .
    • C3 comunica o interactúa modificando continuamente la variable y .
```

**Información gráfica:** [consultar el diagrama o tabla de la página 28](teorica-01-introduccion.pdf#page=28). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 29

[Ver página original](teorica-01-introduccion.pdf#page=29)

```text
Semántica composicional

  Mecanismo de interacción
  La definición de una semántica composicional debe considerar la manera en que
  los procesos comunican o interactúan.

  Importante
  Desarrollar una semántica denotacional (composicional) para lenguajes concu-
  rrentes está fuera del alcance de este curso.

 Para interesados:
   • Resumptions: Hennessy, M. y Plotkin, G. D. (1979). Full abstraction for a
     simple parallel programming language.
   • Trace Semantics: Brookes, S. (1996). Full abstraction for a shared-variable
     parallel language.
```

## Página 30

[Ver página original](teorica-01-introduccion.pdf#page=30)

```text
Modelos de interacción: Categorías principales



   • Memoria compartida:
       • Los procesos leen y escriben en un espacio de direcciones común.
       • Requiere protocolos o primitivas para coordinar el acceso a la memoria.
   • Intercambio de mensajes:
       • Los procesos tienen memorias aisladas y no comparten datos directamente.
       • Comunican explícitamente mediante operaciones de envío y recepción.

   • Presentan muchas variantes.
```

## Página 31

[Ver página original](teorica-01-introduccion.pdf#page=31)

```text
Características salientes




   • No determinismo intrínseco.
   • El resultado final no siempre es interesante (importan los programas que no
     terminan).
   • Interacción/comunicación.
```

## Página 32

[Ver página original](teorica-01-introduccion.pdf#page=32)

```text
Atomicidad



  Atomicidad
  Una instrucción es atómica si se puede ejecutar completamente sin la posibilidad
  de ser intercalada con la ejecución de otra instrucción atómica.


 Ejecución simultánea:
   • Si dos instrucciones atómicas se ejecutan “al mismo tiempo”, su resultado
     equivale a la ejecución secuencial en algún orden arbitrario.
   • No existen estados intermedios visibles de la ejecución de una acción
     atómica.
```

## Página 33

[Ver página original](teorica-01-introduccion.pdf#page=33)

```text
Atomicidad: Alto vs. Bajo nivel

   • Al definir qué acciones son atómicas, se deben considerar las garantias de
     ejecución del procesador.
   • En general, vamos a asumir que las lecturas y escrituras de variables
     compartidas son atómicas, así como también la evaluación de
     expresiones sobre variables locales (registros).
   • La asignación x := x + 1 se descompone como la secuencia de acciones
     atómicas (x ′ es una variable local/no compartida):
       1. x ′ := x                              (Lectura de la variable compartida)
       2. x ′ := x ′ + 1                                (Modificación local interna)
       3. x := x ′                             (Escritura en la variable compartida)

   • Cada una de estas tres (sub)instrucciones es por sí misma atómica.
```

## Página 34

[Ver página original](teorica-01-introduccion.pdf#page=34)

```text
Atomicidad


  Ejemplo

   • Sea C def
           = x := x + 1.
   • Asumiendo que la asignación es atómica:
                              JC | C K = λσ.σ[x 7→ σx + 2]
   • Reescribiendo con x ′ local: C def
                                    = x ′ := x ; x ′ := x ′ + 1; x := x ′ :
        • Lectura y escritura atómica de variables compartidas:
                    JC | C K = λσ.σ[x 7→ σx + 1, . . . ] ⊕ σ[x 7→ σx + 2, . . . ]
        • Se pierde un incremento debido al entrelazamiento.
        • En general, no nos van a interesar los cambios en las variables locales (x ′ ).
```

**Información gráfica:** [consultar el diagrama o tabla de la página 34](teorica-01-introduccion.pdf#page=34). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 35

[Ver página original](teorica-01-introduccion.pdf#page=35)

```text
Independencia del tiempo

  Principio fundamental
  La semántica y la correctitud de un programa concurrente son independientes
  de los tiempos de ejecución.

  Diseño de programas
    • Sin suposiciones temporales: Ningún programa concurrente puede
      basar su lógica en el paso del tiempo o velocidad de la CPU.
    • Indeterminación de velocidad: Una instrucción puede tardar
      microsegundos o días en ejecutar; el programa debe ser válido en cualquier
      escenario.
    • Sincronización explícita: Si el orden de ejecución importa, el programa
      lo debe garantizar independiente del paso del tiempo.
```

## Página 36

[Ver página original](teorica-01-introduccion.pdf#page=36)

```text
Independencia del Tiempo


  Ejemplo
   • Considerar
       • C1 def
            = sleep(1000); x := 1
       • C2 def
            = x := 2

   • Intuitivamente, se podría pensar que C2 terminará antes.
   • Bajo el principio de independencia del tiempo resulta:

                       JC1 | C2 K = λσ.σ[x 7→ 1] ⊕ σ[x 7→ 2]
```

**Información gráfica:** [consultar el diagrama o tabla de la página 36](teorica-01-introduccion.pdf#page=36). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 37

[Ver página original](teorica-01-introduccion.pdf#page=37)

```text
Contenidos


   • Memoria compartida:
       • Exclusión Mutua, Algoritmos de exclusion mutua (Dekker & Bakery).
         Semáforos y Monitores.
       • Problemas clásicos: Filósofos comensales, Lectores/Escritores, . . .
       • Estructuras de datos concurrentes y programación libre de locks. Software
         transaction memories (STM)
   • Intercambio de Mensajes:
       • Comunicación sincrónica
       • Comunicación asincrónica (continuaciones)
   • Modelo de actores.
   • Modelos formales de concurrencia (LTS y LTL/CTL).
```

## Página 38

[Ver página original](teorica-01-introduccion.pdf#page=38)

```text
Pseudocódigo para algoritmos concurrentes

     • Usaremos un pseudocódigo estructurado para describir nuestros algoritmos.
     • Las palabras clave:
         • global : indica que una variable es compartida.
         • thread : indica que un flujo de ejecución es concurrente.


    Programa con variable compartida

1    global x               • T1 y T2 son flujos que ejecutan concurrentemente
2
3    thread T1              • Comparten a la variable x
4    x = 0
5
6    thread T2
7    x = 1
```

## Página 39

[Ver página original](teorica-01-introduccion.pdf#page=39)

```text
Pseudocódigo para algoritmos concurrentes

      • Es posible declarar variables locales (sin cualificador)

  Programa con variable compartida

 1        global x = 0              • T1 y T2 declaran la variable local temp.
 2
 3        thread T1                 • Son dos variable homónimas.
 4        temp
 5        temp = x                  • Asumimos que podemos aplicar α-renombre en
          x = temp + 1
 6
 7
                                      variables locales.
 8        thread T2
 9        temp
 10       temp = x
 11       x = temp + 1
 12
```

## Página 40

[Ver página original](teorica-01-introduccion.pdf#page=40)

```text
Pseudocódigo para algoritmos concurrentes

      • Acciones atómicas: vamos a etiquetar instrucciones cuando cáda linea de
        ejecución es atómica.

  Programa con variable compartida

 1     global x = 0                   • p1, p2, q1 y q2 son etiquetas y cada
 2
 3     thread p                         instrucción es atómica.
 4     temp
 5     p1 :  temp = x
 6     p2 :  x = temp + 1
 7
 8     thread q
 9     temp
 10    q1 :  temp = x
 11    q2 :  x = temp + 1
```

## Página 41

[Ver página original](teorica-01-introduccion.pdf#page=41)

```text
Semántica operacional (Modelo de ejecución)


  Modelo de ejecución de un programa
    • Todas las ejecuciones de un programa de N procesos se describe como un
      grafo dirigido (posiblemente infinito):
        • Estados (nodos):
             • N etiquetas de próxima instrucción, una para cada programa.
             • la asignación de valores a variables globales y locales
        • Transición (arcos): Hay una transición entre s1 y s2 si s2 se obtiene
          ejecutando una de las próximas acciones de s1
    • Una ejecución es un camino (posiblemente infinito) en el grafo.
```

## Página 42

[Ver página original](teorica-01-introduccion.pdf#page=42)

```text
Modelo de Ejecución



 Considerar los threads
 1   global x = 0
 2
 3   thread p
 4   p1 :  x = x + 1
 5
 6   thread q
 7   q1 :   x = x + 1

 ¿Cuál es el diagrama de transición de estados?
```

## Página 43

[Ver página original](teorica-01-introduccion.pdf#page=43)

```text
Grafo de Transición - Asignación Atómica Global




                                  (p1 , q1 , x = 0)
                             p1                       q1


           (−, q1 , x = 1)                                 (p1 , −, x = 1)
                             q1                       p1


                                  (−, −, x = 2)
```

**Descripción editorial del esquema:** El grafo es un rombo: desde (p1,q1,x=0), p1 lleva a (-,q1,x=1) y q1 a (p1,-,x=1); la instrucción restante de cada rama llega a (-,-,x=2). Los dos incrementos globalmente atómicos terminan en x=2.

**Información gráfica:** [consultar el diagrama o tabla de la página 43](teorica-01-introduccion.pdf#page=43). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 44

[Ver página original](teorica-01-introduccion.pdf#page=44)

```text
Modelo de Ejecución



 1   global x = 0
 2
 3 thread p
 4 temp
 5 p1 :  temp = x
 6 p2 :  x = temp + 1
 7
 8  thread q
 9  temp
 10 q1 :  temp = x
 11 q2 :  x = temp + 1

 ¿Cuál es el diagrama de transición de estados?
```

## Página 45

[Ver página original](teorica-01-introduccion.pdf#page=45)

```text
Grafo de Transición - Asignación No Atómica (Data Race)

                                                                      Formato: (PCp , PCq , x , tempp , tempq )
                                          (p1 , q1 , 0, −, −)
                                p1                                   q1



          (p2 , q1 , 0, 0, −)                                               (p1 , q2 , 0, −, 0)
                                     q1                         p1
               p2                                                                      q2


          (−, q1 , 1, −, −)               (p2 , q2 , 0, 0, 0)               (p1 , −, 1, −, −)
                                          p2              q2
               q1                                                                      p1


          (−, q2 , 1, −, 1)     (−, q2 , 1, −, 0)    (p2 , −, 1, 0, −)      (p2 , −, 1, 1, −)

               q2                                    p2   q2                p2


          (−, −, 2, −, −)                                                   (−, −, 1, −, −)

                                                                          Pérdida de actualización
```

**Información gráfica:** [consultar el diagrama o tabla de la página 45](teorica-01-introduccion.pdf#page=45). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 46

[Ver página original](teorica-01-introduccion.pdf#page=46)

```text
Correctitud de programas concurrentes



  Correctitud
    • No se analiza en términos de calcular un resultado funcional único o
      estado final.
    • Porque hay programas que no terminan.
    • Se usan propiedades de la computación:
        • Safety
        • Liveness
```

## Página 47

[Ver página original](teorica-01-introduccion.pdf#page=47)

```text
Propiedades de Safety


  Propiedad de Safety
    • Establece que algo malo nunca ocurre.
    • Debe valer en todo estado de cómputo.

  Ejemplo
    • En un sistema operativo: “El cursor del mouse siempre se muestra en
      pantalla”.
    • Si se vale esta propiedad, el cursor jamás desaparecerá,
      independientemente de qué programas se estén ejecutando.
```

## Página 48

[Ver página original](teorica-01-introduccion.pdf#page=48)

```text
Propiedades de Liveness


  Propiedad de Liveness
    • Establece que algo bueno tarde o temprano ocurrirá.
    • Vale, si para todo estado cualquier estado alcanzable siempre es posible
      alcanzar (en 0 o mas pasos) un estado que cumpla la propiedad.

  Ejemplo
    • En un sistema operativo: “Si haces clic en el botón del mouse, tarde o
      temprano el cursor del mouse cambia de forma”.
    • El sisema puede no responder inmediatamente, pero no puede postergar la
      respuesta indefinidamente.
```

## Página 49

[Ver página original](teorica-01-introduccion.pdf#page=49)

```text
Especificación de propiedades de Safety

   • Las propiedades de safety suelen tomar la forma de: Siempre, algo “malo”
     no es verdadero.
   • Estas propiedades se satisfacen trivialmente con un programa que no hace
     nada.

  Desafío
  Escribir programas que realicen tareas útiles (satisfacen propiedades de liveness
  sin violar las propiedades de safety.


   • Un sistema que solo muestra el cursor del mouse inmóvil es seguro (safe),
     pero completamente inútil por falta de progreso (liveness).
```

## Página 50

[Ver página original](teorica-01-introduccion.pdf#page=50)

```text
Definición de Correctitud Concurrente




  Correctitud de un programa concurrente
  Un programa concurrente es correcto si y sólo si satisface simultáneamente
  tanto sus propiedades de safety como sus propiedades de liveness.
```

## Página 51

[Ver página original](teorica-01-introduccion.pdf#page=51)

```text
Fairness

       • La correctitud exige considerar todas las ejecuciones.
       • ¿Tiene sentido escenarios donde las instrucciones de un proceso específico
         jamás se ejecutan..
  Ejemplo

   1           global n = 0
   2           global flag = false
   3
   4           thread p
   5           p1 :  while flag = false
   6           p2 :  n = 1 - n
   7
   8           thread q
   9           q1 :  flag = true
  10
```

## Página 52

[Ver página original](teorica-01-introduccion.pdf#page=52)

```text
Fairness

       • La correctitud exige considerar todas las ejecuciones.
       • ¿Tiene sentido escenarios donde las instrucciones de un proceso específico
         jamás se ejecutan..
  Ejemplo

   1           global n = 0
   2           global flag = false
   3
   4           thread p
   5           p1 :  while flag = false
   6           p2 :  n = 1 - n
   7
   8           thread q
   9           q1 :  flag = true
  10
```

## Página 53

[Ver página original](teorica-01-introduccion.pdf#page=53)

```text
Definición de Continuamente Habilitada en Ben-Ari



  Instrucción habilitada
  Una instrucción pj de un proceso p está habilitada en un estado s si el puntero
  de instrucción del proceso apunta a pj y puede ser ejecutada.

   • Las asignaciones (ej. x := 1) y las sentencias de control no pueden ser
     bloqueadas por el entorno.
   • Una vez que el puntero de control llega a esa instrucción, la instrucción
     permanece en estado habilitado (enabled) hasta que es ejecutada.
   • Nada que haga otro proceso la puede “deshabilitar”.
```

## Página 54

[Ver página original](teorica-01-introduccion.pdf#page=54)

```text
Fairness




  Ejecución (débilmente) fair
    • Una ejecución es (débilmente) fair si una instrucción que está
      continuamente habilitada tarde o temprano se ejecuta.
    • Dada una ejecución s0 , s1 , . . . , si , si pk está continuamente habilitada en
      si , entonces pk es ejecutada en un estado posterior sk para algún k > i.
```

## Página 55

[Ver página original](teorica-01-introduccion.pdf#page=55)

```text
Fairness

  Ejemplo

   1          global n = 0
   2          global flag = false
   3
   4          thread p
   5          p1 :  while flag = false
   6          p2 :     n = 1 - n
   7
   8          thread q
   9          q1 :  flag = true
  10




       • El programa termina en ejecuciones fair.
       • Toda ejecución fair debe incluir q1.
       • Esto garantiza que q termine.
       • Luego p termina (al máximo en dos pasos)
```

## Página 56

[Ver página original](teorica-01-introduccion.pdf#page=56)

```text
Grafo de Transición


         Formato: (PCp , PCq , flag, n)
                                                                   (p1 , q1 , false, 0)q
                                                                  p1                       1



                                                             q1
                                      (p2 , q1 , false, 0)         (p2 , −, true, 0)           (p1 , −, true, 0)
                                                p2                                                      p1
                                                                                  p2

                      p2
                                      (p1 , q1 , false, 1)         q1
                                                                                               (−, −, true, 0)
                                          p1


                           (p2 , q1 , false, 1)                         p2      (p1 , −, true, 1)
                                                                                               p1
                                               q1

                                                     (p2 , −, true, 1)          (−, −, true, 1)
```

**Información gráfica:** [consultar el diagrama o tabla de la página 56](teorica-01-introduccion.pdf#page=56). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 57

[Ver página original](teorica-01-introduccion.pdf#page=57)

```text
Grafo de Transición - Computación infinita (no terminación)


            Formato: (PCp , PCq , flag, n)
                                                                      (p1 , q1 , false, 0)q
                                                                     p1                       1



                                                                q1
                                         (p2 , q1 , false, 0)             (p2 , −, true, 0)       (p1 , −, true, 0)
                                                   p2                                                      p1
                                                                                      p2

                         p2
                                         (p1 , q1 , false, 1)         q1
                                                                                                  (−, −, true, 0)
                                             p1


                              (p2 , q1 , false, 1)                           p2      (p1 , −, true, 1)
                                                                                                  p1
                                                  q1


        c                                               (p2 , −, true, 1)            (−, −, true, 1)
```

**Información gráfica:** [consultar el diagrama o tabla de la página 57](teorica-01-introduccion.pdf#page=57). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 58

[Ver página original](teorica-01-introduccion.pdf#page=58)

```text
Grafo de Transición - Computación infinita (no terminación)

         Formato: (PCp , PCq , flag, n)
                                                                   (p1 , q1 , false, 0)q
                                                                  p1                       1



                                                             q1
                                      (p2 , q1 , false, 0)             (p2 , −, true, 0)       (p1 , −, true, 0)
                                                p2                                                      p1
                                                                                   p2

                      p2
                                      (p1 , q1 , false, 1)         q1
                                                                                               (−, −, true, 0)
                                          p1


                           (p2 , q1 , false, 1)                           p2      (p1 , −, true, 1)
                                                                                               p1
                                               q1
              ¿Es una ejecución justa? No.
                                                     (p , −, true, 1)
              Porque la instrucción q1 está continuamente2                        (−, −, true, 1)
              habilitada en el ciclo y nunca se ejecuta.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 58](teorica-01-introduccion.pdf#page=58). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 59

[Ver página original](teorica-01-introduccion.pdf#page=59)

```text
Definiciones



  Sección crítica
  Llamamos sección crítica a una parte del programa que no puede ser ejecutada
  concurrentemente con otra sección crítica del mismo programa.



  Exclusión mutua
  Llamamos exclusión mutua al problema de asegurar que dos (o mas) threads no
  ejecutan simultáneamente su sección crítica.
```

## Página 60

[Ver página original](teorica-01-introduccion.pdf#page=60)

```text
Esquema general


 Existen N procesos que tienen la siguiente estructura
 1   shared variables
 2
 3 thread id = i
 4 while ( true ) {
 5     seccion no critica
 6     pre - protocol
 7     seccion critica
 8     post - protocol
 9 }


     • No hay variables compartidas entre sección crítica y no crítica.
     • La sección crítica siempre termina.
     • La no crítica no necesariamente termina.
```

## Página 61

[Ver página original](teorica-01-introduccion.pdf#page=61)

```text
Requerimientos de la exclusión mutua



  1. Exclusión Mutua (Safety): En cualquier momento hay como máximo un
     proceso en la región crítica.

  2. Ausencia de deadlocks (Liveness): Si varios procesos intentan entrar a la
     sección crítica tarde o temprano alguno entra.

  3. Ausencia de inhanición (garantía de entrada) (Liveness): Cualquier
     proceso que intenta entrar a su sección crítica tarde o temprano entra.
```

## Página 62

[Ver página original](teorica-01-introduccion.pdf#page=62)

```text
Exclusión mutua




  Problema
  ¿Podemos resolver el problema de la exclusión mutua para dos procesos asumien-
  do que las únicas operaciones atómicas son la lectura y la escritura de variables?
```

## Página 63

[Ver página original](teorica-01-introduccion.pdf#page=63)

```text
Algoritmo I (levanto la bandera)


                    shared flag = false

        thread p                                 thread q
   p0 : while ( true )                    q0 :   while ( true )
   p1 :   seccion no critica              q1 :     seccion no critica
   p2 :   while ( flag ) ;                q2 :     while ( flag ) ;
   p3 :   flag = true                     q3 :     flag = true
   p4 :   seccion critica                 q4 :     seccion critica
   p5 :   flag = false                    q5 :     flag = false

   • ¿Cómo lo analizamos?
   • Razonamos sobre el modelo de cómputo
   • Problema: El tamaño (explosión combinacional).
   • Vamos a simplificar (para razonar manualmente)
```

## Página 64

[Ver página original](teorica-01-introduccion.pdf#page=64)

```text
Simplificación para análisis



     • Es riesgos, pero vamos a considerar la secciones críticas y no críticas como
       comentarios
     • Vamos a obviar el while (exterior).
 1                     shared flag = false
 2
 3      thread p                                     thread q
 4      while ( true )                               while ( true )
 5 p1 :   while ( flag )                        q1 :   while ( flag )
 6 p2 :   flag = true                           q2 :   flag = true
 7 p3 :   flag = false                          q3 :   flag = false
```

## Página 65

[Ver página original](teorica-01-introduccion.pdf#page=65)

```text
Descomposición y Abstracción de while(flag)

   • Representamos a while(flag) como una acción atómica (aunque no lo es)
   • Su descomposición es
      1 shared flag = false
      2 d0 :  ...
      3 d1 :  local = flag
      4 d2 :  if local then jump d1
      5 d3 :  ...



          (PC , flag, local)   No puede salir del while.       (PC , flag, local)

                               A nivel abstracto, no
     (d0 , true, ⊥)                                         (d0 , false, ⊥)
                   ...
                               puede avanzar (puede                     ...
                               representarse también
     (d1 , true, ⊥)                                         (d1 , false, ⊥)
                               con un loop)
     d2            d1                                                   d1
                                                                                    d2
    (d2 , true, true)                                      (d2 , false, false)           (d3 , false, false)
```

**Información gráfica:** [consultar el diagrama o tabla de la página 65](teorica-01-introduccion.pdf#page=65). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 66

[Ver página original](teorica-01-introduccion.pdf#page=66)

```text
Modelo de cómputo (simplificado - con self-loops)


  Formato: (PCp , PCq , flag)
                                                                 (p1 , q1 , false)
                                                                 p1                 q1
                                p3                                                                                        q3
                                            (p2 , q1 , false)                                 (p1 , q2 , false)
                                     p2                              q1             p1                             q2

          q1        (p3 , q1 , true)                  q3         (p2 , q2 , false)                p3                   (p1 , q3 , true)   p1

                                                                          p2   q2

                                             (p3 , q2 , true)                             (p2 , q3 , true)
                                                                q2                       p2

                                       q3                                                                         p3
                                                                 (p3 , q3 , true)
```

**Información gráfica:** [consultar el diagrama o tabla de la página 66](teorica-01-introduccion.pdf#page=66). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 67

[Ver página original](teorica-01-introduccion.pdf#page=67)

```text
Modelo de cómputo (simplificado) - sin self-loops


   Formato: (PCp , PCq , flag)
                                                                  (p1 , q1 , false)
                                                                  p1                 q1
                                 p3                                                                                        q3
                                             (p2 , q1 , false)                                 (p1 , q2 , false)
                                      p2                              q1             p1                             q2


                     (p3 , q1 , true)                  q3         (p2 , q2 , false)                p3                   (p1 , q3 , true)
                                                                           p2   q2

                                              (p3 , q2 , true)                             (p2 , q3 , true)
                                                                 q2                       p2

                                        q3                                                                         p3
                                                                  (p3 , q3 , true)
```

**Información gráfica:** [consultar el diagrama o tabla de la página 67](teorica-01-introduccion.pdf#page=67). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 68

[Ver página original](teorica-01-introduccion.pdf#page=68)

```text
Análisis


 1                     shared flag = false
 2
 3      thread p                                      thread q
 4      while ( true )                                while ( true )
 5 p1 :   while ( flag )                         q1 :   while ( flag )
 6 p2 :   flag = true                            q2 :   flag = true
 7 p3 :   flag = false                           q3 :   flag = false


     • Exclusión mutua (Safety): En cualquier momento hay como máximo un
       proceso en la región crítica.
     • ¿Cuándo un proceso está en la sección crítica? Si p está en p3 o q está en q3.
     • ¿Cuál es un estado de violación de exclusión mutua? Un estado (p3, q3, ?)
       donde ambos procesos coexisten en su sección crítica.
```

## Página 69

[Ver página original](teorica-01-introduccion.pdf#page=69)

```text
Modelo de cómputo (simplificado)


   Formato: (PCp , PCq , flag)
                                                                 (p1 , q1 , false)
                                                                 p1                 q1
                                 p3                                                                                       q3
                                            (p2 , q1 , false)                                 (p1 , q2 , false)
                                      p2                             q1             p1                             q2


                     (p3 , q1 , true)                 q3         (p2 , q2 , false)                p3                   (p1 , q3 , true)
                                                                          p2   q2

                                             (p3 , q2 , true)                             (p2 , q3 , true)
                                                                q2                       p2

                                       q3                                                                         p3
                                                                (p3 , q3 , true)                       Falta de Mutex
```

**Información gráfica:** [consultar el diagrama o tabla de la página 69](teorica-01-introduccion.pdf#page=69). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 70

[Ver página original](teorica-01-introduccion.pdf#page=70)

```text
Análisis




                       shared flag = false

        thread p                                  thread q
        while ( true )                            while ( true )
   p1 :   while ( flag )                     q1 :   while ( flag )
   p2 :   flag = true                        q2 :   flag = true
   p3 :   flag = false                       q3 :   flag = false
```

## Página 71

[Ver página original](teorica-01-introduccion.pdf#page=71)

```text
Algoritmo II (levanto mi bandera)



 1
 2                   global flag = { false , false }
 3
 4
 5  thread id = 0                      thread id = 1
 6    while ( true ) {                 while ( true ) {
  7    // seccion no critica            // seccion no critica
  8    otro = ( id + 1) % 2             otro = ( id + 1) % 2
  9    flag [ id ] = true               flag [ id ] = true
 10    while ( flag [ otro ]) ;         while ( flag [ otro ]) ;
 11    // seccion critica               // seccion critica
 12    flag [ id ] = false              flag [ id ] = false
 13 }                                           }
```

## Página 72

[Ver página original](teorica-01-introduccion.pdf#page=72)

```text
Algoritmo II (levanto mi bandera)




 1
 2                   global flag = { false , false }
 3
 4      thread                                thread
 5      while ( true )                        while ( true )
 6 p1 :  flag [0] = true               q1 :    flag [1] = true
 7 p2 :  while ( flag [1]) ;           q2 :    while ( flag [0]) ;
 8 p3 :  flag [0] = false              q3 :    flag [ id ] = false
```

## Página 73

[Ver página original](teorica-01-introduccion.pdf#page=73)

```text
Segundo Intento: Modelo de cómputo


 Formato: (PCp , PCq , flag0 , flag1 )
                                                                         (pp11 , q1 , false, false)
                                                                                               q1


                                         p3                                                                                      q3
                                                   (p2 , q1 , true, false)                   (p1 , q2 , false, true)
                                                        p2                                                       q2
                                                                             q1            p1


                       (p3 , q1 , true, false)                           (p2 , q2 , true, true)                   (p1 , q3 , false, true)
                                                   q1                               p2                                 p1
                                                                                    q2
                                                                                  q3 p3
                                              q2        (p3 , q2 , true, true)               (p2 , q3 , true, true)         p2
```

**Información gráfica:** [consultar el diagrama o tabla de la página 73](teorica-01-introduccion.pdf#page=73). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 74

[Ver página original](teorica-01-introduccion.pdf#page=74)

```text
Algoritmo II (levanto mi bandera)




 1
 2                   global flag = { false , false }
 3
 4      thread p                              thread q
 5      while ( true )                        while ( true )
 6 p1 :  flag [0] = true               q1 :    flag [1] = true
 7 p2 :  while ( flag [1]) ;           q2 :    while ( flag [0]) ;
 8 p3 :  flag [0] = false              q3 :    flag [ id ] = false
```

## Página 75

[Ver página original](teorica-01-introduccion.pdf#page=75)

```text
Segundo Intento: Modelo de cómputo

 Formato: (PCp , PCq , flag0 , flag1 )
                                                                         (pp11 , q1 , false, false)
                                                                                               q1


                                         p3                                                                                       q3
                                                   (p2 , q1 , true, false)                    (p1 , q2 , false, true)
                                                        p2                                                        q2
                                                                                q1           p1


                       (p3 , q1 , true, false)                               (p2 , q2 , true, true)                (p1 , q3 , false, true)
                                                   q1                                  p2                               p1
                                                                                       q2
                                                                                     q3 p3
                                              q2        (p3 , q2 , true, true)                (p2 , q3 , true, true)         p2



                      Nota: Técnicamente representa un livelock. Es un
                      estado donde los hilos continúan ejecutando instruc-
                      ciones activamente (espera activa), pero el sistema
                      global no logra avanzar.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 75](teorica-01-introduccion.pdf#page=75). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 76

[Ver página original](teorica-01-introduccion.pdf#page=76)

```text
Algoritmo III (uso un turno)



 1
 2                         global turno = 0
 3
 4
 5    thread id = 0                    thread id = 1
 6     while ( true ) {                  while ( true ) {
 7       // seccion no critica           // seccion no critica
 8       while ( turno != id ) ;         while ( turno != id ) ;
 9       // seccion critica              // seccion critica
 10      turno = ( id + 1) % 2           turno = ( id + 1) % 2
 11      // seccion no critica           // seccion no critica
 12    }                                         }
```

## Página 77

[Ver página original](teorica-01-introduccion.pdf#page=77)

```text
Algoritmo III (Simplificación)




 1
 2                     global turno = 0
 3
 4 thread p                             thread q
 5  while ( true ) {                    while ( true ) {
 6 p0 : while ( turno != 0) ;      q0 :   while ( turno != 1) ;
 7 p1 : turno = 1                  q1 :   turno = 0
```

## Página 78

[Ver página original](teorica-01-introduccion.pdf#page=78)

```text
Primer Intento: Modelo de cómputo



          Formato: (PCp , PCq , turno)
                                                     q1
                    q0          (p0 , q0 , 0)             (p0 , q1 , 1)   p0


                                    p0



                                                                 q0

                                                p1
                    q0          (p1 , q0 , 0)             (p0 , q0 , 1)   p0
```

**Información gráfica:** [consultar el diagrama o tabla de la página 78](teorica-01-introduccion.pdf#page=78). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 79

[Ver página original](teorica-01-introduccion.pdf#page=79)

```text
Algoritmo III (Simplificación)


 1
 2                      global turno = 0
 3
 4 thread p                               thread q
 5  while ( true ) {                      while ( true ) {
 6 p0 : while ( turno != 0) ;        q0 :   while ( turno != 1) ;
 7 p1 : turno = 1                    q1 :   turno = 0


     • Mutex: Sí
     • Ausencia de deadlocks: Sí
     • Ausencia de inhanición: Veamos....
         • Nuestra simplificación, tiene un problema.
         • Eliminó las secciones no críticas, que pueden no terminar.
```

## Página 80

[Ver página original](teorica-01-introduccion.pdf#page=80)

```text
Algoritmo III (Simplificación)

 1
 2                         global turno = 0
 3
 4
 5    thread id = 0                    thread id = 1
 6     while ( true ) {                  while ( true ) {
 7       // seccion no critica           // seccion no critica
 8       while ( turno != id ) ;         while ( turno != id ) ;
 9       // seccion critica              // seccion critica
 10      turno = ( id + 1) % 2           turno = ( id + 1) % 2
 11      // seccion no critica           // seccion no critica
 12    }                                         }


      • Mutex: Sí
      • Ausencia de deadlocks: Sí
      • Ausencia de inhanición: No (si el thread id 0 no termina su sección no
        crítica)
```

## Página 81

[Ver página original](teorica-01-introduccion.pdf#page=81)

```text
Algoritmo de Dekker (II + III)

 1
 2    shared turno = 0
 3    shared flag = { false , false }
 4
 5
 6    thread id = 0                     thread id = 1
 7      // seccion no critica             ...
 8      otro = ( id + 1) % 2
 9      flag [ id ] = true
 10     while ( flag [ otro ])
 11       if ( turno == otro )
 12          flag [ id ] = false
 13          while ( turno != id ) ;
 14          flag [ id ] = true
 15
 16     // seccion critica
 17
 18     turno = otro
 19     flag [ id ] = false
 20     // seccion no critica
```

## Página 82

[Ver página original](teorica-01-introduccion.pdf#page=82)

```text
Algoritmo de Peterson


 1
 2    shared turno = 0
 3    shared flag = { false , false }
 4
 5
 6    thread id = 0
 7      // seccion no critica
 8      otro = ( id + 1) % 2
 9      flag [ id ] = true
 10     turno = otro
 11     while ( flag [ otro ] && turno == otro ) ;
 12
 13     // seccion critica
 14
 15     flag [ id ] = false
 16     // seccion no critica
```

## Página 83

[Ver página original](teorica-01-introduccion.pdf#page=83)

```text
Dekker y Peterson




   • Mutex: Sí
   • Ausencia de deadlocks: Sí
   • Ausencia de inhanición: Sí

 Sólo sirven para dos procesos.
```

## Página 84

[Ver página original](teorica-01-introduccion.pdf#page=84)

```text
Algoritmo de Bakery

 1    shared entrando [ n ] = { false , ... , false }
 2    shared numero [ n ]   = {0 , ... , 0}
 3
 4
 5    thread id = 0
 6      // seccion no critica
 7      entrando [ id ] = true
 8      numero [ id ] = 1 + max ( numero [1] , ... , [ n ])
 9      entrando [ id ] = false
 10     for ( j = 1; j <= n ; ++ j )
 11       while ( entrando [ j ]) ;
 12       while ( numero [ j ] != 0 && ( numero [ j ] < numero [ id ] ||
 13         ( numero [ j ] == numero [ id ] && j < id ) ) ) ;
 14
 15     // seccion critica
 16
 17     numero [ id ] = 0
 18     // seccion no critica


 Este algoritmo resuelve el problema para n threads.
```

## Página 85

[Ver página original](teorica-01-introduccion.pdf#page=85)

```text
Pregunta




 ¿Y si uno tiene otras acciones atómicas, cómo puede resolver el problema de la
 exclusión mutua?
```

## Página 86

[Ver página original](teorica-01-introduccion.pdf#page=86)

```text
Test and set


 1    function test - and - set ( ref comp , ref local )
 2            local = comp
 3            comp = 1

 1
 2                                shared comp = 0
 3
 4
 5    thread id = 0                                thread id = 1
 6      int local                                    int local
 7      // seccion no critica                        // seccion no critica
 8      repeat                                       repeat
 9        test - and - set ( comp , local )            test - and - set ( comp , local )
 10     until ( local == 0)                          until ( local == 0)
 11     // seccion critica                           // seccion critica
 12     comp = 0                                     comp = 0
 13     // seccion no critica                        // seccion no critica
```

## Página 87

[Ver página original](teorica-01-introduccion.pdf#page=87)

```text
Exchange

 1    function exchange ( ref comp , ref local )
 2            temp = comp
 3            comp = local
 4            local = temp

 1
 2                              shared comp = 0
 3
 4
 5    thread id = 0                              thread id = 1
 6      int local = 1                              int local = 1
 7      // seccion no critica                      // seccion no critica
 8      repeat                                     repeat
 9        exchange ( comp , local )                  exchange ( comp , local )
 10     until ( local == 0)                        until ( local == 0)
 11     // seccion critica                         // seccion critica
 12     comp = 0                                   comp = 0
 13     // seccion no critica                      // seccion no critica
```

## Página 88

[Ver página original](teorica-01-introduccion.pdf#page=88)

```text
Compare-and-swap


 1    function compare - and - swap ( ref comp , ref viejo , ref nuevo )
 2            temp = comp
 3            if ( comp == viejo )
 4                  comp = nuevo
 5            return temp

 1
 2                            shared comp = false
 3
 4
 5    thread id = 0                              thread id = 1
 6      // seccion no critica                      // seccion no critica
 7      while ( compare - and - swap (             while ( compare - and - swap (
 8        comp , false , true ) ) ;                  comp , false , true ) ) ;
 9      // seccion critica                         // seccion critica
 10     comp = false                               comp = false
 11     // seccion no critica                      // seccion no critica
```

## Página 89

[Ver página original](teorica-01-introduccion.pdf#page=89)

```text
Fetch-and-add

 1
 2    function fetch - and - add ( ref comp , ref local , ref x )
 3            local = comp
 4            comp = comp + x

 1
 2                            shared ticket = 0
 3                            shared turno = 0
 4
 5
 6    thread id = 0                               thread id = 1
 7      int miTurno                                 int miTurno
 8      // seccion no critica                       // seccion no critica
 9      fetch - and - add (                         fetch - and - add (
 10       ticket , miTurno , 1)                       ticket , miTurno , 1)
 11     while ( turno != miTurno ) ;                while ( turno != miTurno ) ;
 12     // seccion critica                          // seccion critica
 13     fetch - and - add (                         fetch - and - add (
 14       turno , miTurno , 1)                        turno , miTurno , 1)
 15     // seccion no critica                       // seccion no critica
```

## Página 90

[Ver página original](teorica-01-introduccion.pdf#page=90)

```text
Busy waiting




 Todas las soluciones vistas en esta clase son ineficientes dado que consumen
 tiempo de procesador en las esperas.
 Sería deseable suspender la ejecución de un proceso que intenta acceder a la
 sección crítica hasta tanto sea posible.
```
