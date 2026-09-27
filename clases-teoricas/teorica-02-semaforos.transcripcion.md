# teorica-02-semaforos — transcripción

- Fuente: [teorica-02-semaforos.pdf](teorica-02-semaforos.pdf)
- Páginas del PDF: 115.
- SHA-256 del PDF: `c8e2ebc4f8784f6d9e7572075dbbefe4bd7920ff509a68cd72669f4e8c01eacc`.
- Tipo: transcripción textual por página, con disposición espacial conservada y referencias al original para esquemas.

> Se conserva el contenido del original, incluidos ejemplos incorrectos, erratas y diapositivas progresivas. Los bloques `text` preservan columnas y sangría; no son código listo para compilar. En código repartido en columnas, seguir las indicaciones «continúa a la derecha». Las notas editoriales se distinguen del original. Para símbolos cuya extracción sea ambigua, consultar la página enlazada.

## Página 1

[Ver página original](teorica-02-semaforos.pdf#page=1)

```text
Semáforos
```

## Página 2

[Ver página original](teorica-02-semaforos.pdf#page=2)

```text
Sincronización de más alto nivel


  Limitación soluciones previas
    • Los algoritmos de la clase anterior asumen lectura y escritura atómica
      (máquina básica).
    • Soluciones ineficientes para un uso práctico.
```

## Página 3

[Ver página original](teorica-02-semaforos.pdf#page=3)

```text
Sincronización de más alto nivel


  Limitación soluciones previas
    • Los algoritmos de la clase anterior asumen lectura y escritura atómica
      (máquina básica).
    • Soluciones ineficientes para un uso práctico.


 Más alto nivel: Semáforos
 Tipo de datos para programación concurrente:
   • Implementado generalmente por el Sistema Operativo.
   • Concepto “simple” y ampliamente utilizado
   • Son todavía de bajo nivel: propenso a errores.
```

## Página 4

[Ver página original](teorica-02-semaforos.pdf#page=4)

```text
Multiprocesamiento, Multitarea




 Asignación de Hardware
   • Multiprocesamiento Puro: Procesadores ≥ Procesos. Cada proceso
     dispone de una CPU dedicada de forma permanente.
   • Multitarea (Multiplexación): Procesos > Procesadores. Los procesos
     deben competir y compartir el tiempo de CPU.
```

## Página 5

[Ver página original](teorica-02-semaforos.pdf#page=5)

```text
El Planificador y el Modelo de Concurrencia


 El Planificador (Scheduler)
 Programa del sistema encargado de:
   • Decidir qué proceso listo pasa a ejecución.
   • Realizar el cambio de contexto (context switch).
```

## Página 6

[Ver página original](teorica-02-semaforos.pdf#page=6)

```text
El Planificador y el Modelo de Concurrencia


 El Planificador (Scheduler)
 Programa del sistema encargado de:
   • Decidir qué proceso listo pasa a ejecución.
   • Realizar el cambio de contexto (context switch).

 Nuestro Modelo de Entrelazado
  • Para simplificar el modelo, no mencionamos explícitamente al scheduler.
  • Asume un entrelazado arbitrario de sentencias atómicas.
  • El scheduler puede hacer un cambio de contexto en cualquier momento
    (incluso tras una sola instrucción).
```

## Página 7

[Ver página original](teorica-02-semaforos.pdf#page=7)

```text
Estados de un proceso


 Estados
   • Ejecución (running): El proceso tiene asignada una CPU y está ejecutando
     activamente sus instrucciones.
   • Listo (ready): El proceso posee todos los recursos necesarios para ejecutar,
     pero espera por la CPU.
   • Bloqueado / Espera (blocked/waiting): El proceso no puede ejecutar
     porque está esperando un evento externo (operación de E/S).
   • Inactivo (inactive): No está cargado en memoria principal.
   • Terminado (completed): Terminó su ejecución.
```

## Página 8

[Ver página original](teorica-02-semaforos.pdf#page=8)

```text
Ciclo de vida


     inactive             ready             running           completed


                                            blocked

  Notación
    • Dado un proceso p, p.state indica su estado actual.
    • Usamos asignación para representar cambios de estado:
      p.state := ready
      p.state := running
```

**Información gráfica:** [consultar el diagrama o tabla de la página 8](teorica-02-semaforos.pdf#page=8). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 9

[Ver página original](teorica-02-semaforos.pdf#page=9)

```text
Semáforo: definición



  Semáforo
```

## Página 10

[Ver página original](teorica-02-semaforos.pdf#page=10)

```text
Semáforo: definición



  Semáforo
  Un tipo de dato abstracto que representa enteros no negativos.
```

## Página 11

[Ver página original](teorica-02-semaforos.pdf#page=11)

```text
Semáforo: definición



  Semáforo
  Un tipo de dato abstracto que representa enteros no negativos.

  Operaciones
    • wait (originalmente P): Decrementa el valor. Si es cero, la operación se
      bloquea hasta que el valor sea positivo. .
    • signal (originalmente V): Incrementa el valor. Si operaciones wait
      bloqueads se desbloquea una de ella.
```

## Página 12

[Ver página original](teorica-02-semaforos.pdf#page=12)

```text
Semáforo: Representación



  Representación
   int V ; // Valor
   conjunto < procesos > L ; // procesos esperando ejecutar un wait
```

## Página 13

[Ver página original](teorica-02-semaforos.pdf#page=13)

```text
Semáforo: Representación



  Representación
   int V ; // Valor
   conjunto < procesos > L ; // procesos esperando ejecutar un wait
```

## Página 14

[Ver página original](teorica-02-semaforos.pdf#page=14)

```text
Semáforo: Representación



  Representación
   int V ; // Valor
   conjunto < procesos > L ; // procesos esperando ejecutar un wait


  Invariante de Representación
    • V ≥ 0.
    • V > 0 =⇒ L = ∅.
  Como corolario, tenemos que L ̸= ∅ =⇒ V = 0.
```

## Página 15

[Ver página original](teorica-02-semaforos.pdf#page=15)

```text
Semáforo: Implementación de Operaciones
```

## Página 16

[Ver página original](teorica-02-semaforos.pdf#page=16)

```text
Semáforo: Implementación de Operaciones



 wait                                      signal

   wait () {                                 signal () {
     if this . V > 0 then                      if this . L = {} then
        this . V := this . V - 1                 this . V := this . V + 1
     else                                      else
        this . L := this . L U { p }             let p in this . L
        p . state := bloqueado                   this . L := this . L - { p }
   }                                             p . state := ready
                                             }

   • p denota al proceso que invoca a la operación wait.
   • Las dos operaciones son atómicas.
```

## Página 17

[Ver página original](teorica-02-semaforos.pdf#page=17)

```text
Semáforo: Constructor




  Constructor

   Sem á foro ( int k ) {
     // pre : k >= 0
     this . V := k
     this . L := {}
   }
```

## Página 18

[Ver página original](teorica-02-semaforos.pdf#page=18)

```text
Invariantes de semáforo



  Teorema S = Semáforo(k)
   a. S.V ≥ 0
   b. S.V = k + #signal − #wait

   • k: Valor inicial de S.V.
   • #signal y #wait: Cantidad de operaciones S.signal() y S.wait()
     completadas.
   • Se considera que un proceso bloqueado en un wait no realizó la operación
     wait.
```

## Página 19

[Ver página original](teorica-02-semaforos.pdf#page=19)

```text
Demostración: Invariantes de Semáforo

 Demostración por Inducción estructural
  1. Base(Constructor):
```

## Página 20

[Ver página original](teorica-02-semaforos.pdf#page=20)

```text
Demostración: Invariantes de Semáforo

 Demostración por Inducción estructural
  1. Base(Constructor): S = Semáforo(k) con k ≥ 0.
```

## Página 21

[Ver página original](teorica-02-semaforos.pdf#page=21)

```text
Demostración: Invariantes de Semáforo

 Demostración por Inducción estructural
  1. Base(Constructor): S = Semáforo(k) con k ≥ 0.
       • #wait = 0, #signal = 0
       • S.V = k ≥ 0 ✓(a)
       • S.V = k + 0 − 0 ✓(b)
```

## Página 22

[Ver página original](teorica-02-semaforos.pdf#page=22)

```text
Demostración: Invariantes de Semáforo

 Demostración por Inducción estructural
  1. Base(Constructor): S = Semáforo(k) con k ≥ 0.
       • #wait = 0, #signal = 0
       • S.V = k ≥ 0 ✓(a)
       • S.V = k + 0 − 0 ✓(b)
  2. Caso inductivo:
```

## Página 23

[Ver página original](teorica-02-semaforos.pdf#page=23)

```text
Demostración: Invariantes de Semáforo

 Demostración por Inducción estructural
  1. Base(Constructor): S = Semáforo(k) con k ≥ 0.
       • #wait = 0, #signal = 0
       • S.V = k ≥ 0 ✓(a)
       • S.V = k + 0 − 0 ✓(b)
  2. Caso inductivo: Assumimos S.V ≥ 0 y S.V = k + #signal − #wait.
     Caso signal(S):
       • Siempre incrementa #signal en 1.
       • Si S.L = ∅: S.V aumenta en 1. Ambos lados de (b) se incrementan. S.V ≥ 0
         se mantiene.
       • Si S.L ̸= ∅: S.V no cambia, pero un proceso sale de S.L (pasa a ready).
         Nota: Por definición, esto equivale a que se complete un wait previo,
         compensando la ecuación (b).
                                                                       (continua)
```

## Página 24

[Ver página original](teorica-02-semaforos.pdf#page=24)

```text
Demostración: Invariantes de Semáforo

 Demostración por Inducción estructural (cont)
  2. Caso inductivo: Assumimos S.V ≥ 0 y S.V = k + #signal − #wait.
     ...
     Caso wait(S):
       • Si S.V = 0,
            • El proceso pasa a bloqueado ingresando a S.L.
            • Efecto: No cambia S.V (sigue siendo 0) y no se incrementa #wait.
            • No modifica ninguna variable de las ecuaciones, los invariantes (a) y (b) valen
              por hipótesis inductiva.
       • Si S.V ≥ 0
            • Decrementa S.V en 1 e incrementa #wait en 1.
            • Al restar 1 a ambos lados, (b) se mantiene. Como S.V > 0, al restar 1 queda
              S.V ≥ 0 (a).
```

## Página 25

[Ver página original](teorica-02-semaforos.pdf#page=25)

```text
Semáforo binario (o mutex)

  Semáforo Binario
  Un semáforo donde V puede tomar valores 0 o 1 (true, false).
```

## Página 26

[Ver página original](teorica-02-semaforos.pdf#page=26)

```text
Semáforo binario (o mutex)

  Semáforo Binario
  Un semáforo donde V puede tomar valores 0 o 1 (true, false).

   • El constructor tiene como precondición k ∈ {0, 1}.
   • wait no cambia y signal se redefine como sigue:
```

## Página 27

[Ver página original](teorica-02-semaforos.pdf#page=27)

```text
Semáforo binario (o mutex)

  Semáforo Binario
  Un semáforo donde V puede tomar valores 0 o 1 (true, false).

   • El constructor tiene como precondición k ∈ {0, 1}.
   • wait no cambia y signal se redefine como sigue:

      signal (semáforo binario)
        signal () {
             if this.V = 1 then undefined
            if this . L = {} then
                 this . V := this . V + 1
            else
                 let p in this . L
                 this . L := this . L - { p }
                 p . state := ready
        }
```

## Página 28

[Ver página original](teorica-02-semaforos.pdf#page=28)

```text
Exclusión mutua con mutex



 Usar un semáforo binario S = SemáforoBinario(1) (inicializado en 1)

ThreadId = 0                              ThreadId = 1
 while ( true ) {                           while ( true ) {
     // secci ó n no cr í tica                  // secci ó n no cr í tica
     S . wait ()                                S . wait ()
     // secci ó n cr í tica                     // secci ó n cr í tica
     S . signal ()                              S . signal ()
 }                                          }
```

## Página 29

[Ver página original](teorica-02-semaforos.pdf#page=29)

```text
Exclusión mutua con mutex (simplificación)




ThreadId = 0                       ThreadId = 1
        while ( true ) {                    while ( true ) {
 p1 :       S . wait ()              q1 ;       S . wait ()
 p2 :       S . signal ()            q2 :       S . signal ()
        }                                   }
```

## Página 30

[Ver página original](teorica-02-semaforos.pdf#page=30)

```text
Grafo de Estados del Semáforo



       p1: S.wait(),       p2: S.signal(),   p2: S.signal(),
       q1: S.wait(),        q1: S.wait(),     q1: blocked,
          (1, ∅)               (0, ∅)           (0, {q})



                            p1: S.wait(),     p1: blocked,
                           q2: S.signal(),   q2: S.signal(),
                               (0, ∅)           (0, {p})
```

**Información gráfica:** [consultar el diagrama o tabla de la página 30](teorica-02-semaforos.pdf#page=30). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 31

[Ver página original](teorica-02-semaforos.pdf#page=31)

```text
Propiedades del Grafo de Estados

 Exclusión Mutua
```

## Página 32

[Ver página original](teorica-02-semaforos.pdf#page=32)

```text
Propiedades del Grafo de Estados

 Exclusión Mutua
   • Una violación requiere un estado de la forma (p2: S.signal(), q2:
     S.signal(), ...).
   • Ningún estado cumple esa condición.
```

## Página 33

[Ver página original](teorica-02-semaforos.pdf#page=33)

```text
Propiedades del Grafo de Estados

 Exclusión Mutua
   • Una violación requiere un estado de la forma (p2: S.signal(), q2:
     S.signal(), ...).
   • Ningún estado cumple esa condición.

 Ausencia de Deadlock
```

## Página 34

[Ver página original](teorica-02-semaforos.pdf#page=34)

```text
Propiedades del Grafo de Estados

 Exclusión Mutua
   • Una violación requiere un estado de la forma (p2: S.signal(), q2:
     S.signal(), ...).
   • Ningún estado cumple esa condición.

 Ausencia de Deadlock
  • No existen estados en los que ambos procesos están bloqueados
     simultáneamente.
```

## Página 35

[Ver página original](teorica-02-semaforos.pdf#page=35)

```text
Propiedades del Grafo de Estados

 Exclusión Mutua
   • Una violación requiere un estado de la forma (p2: S.signal(), q2:
     S.signal(), ...).
   • Ningún estado cumple esa condición.

 Ausencia de Deadlock
  • No existen estados en los que ambos procesos están bloqueados
     simultáneamente.
 Garantía de entrada
```

## Página 36

[Ver página original](teorica-02-semaforos.pdf#page=36)

```text
Propiedades del Grafo de Estados

 Exclusión Mutua
   • Una violación requiere un estado de la forma (p2: S.signal(), q2:
     S.signal(), ...).
   • Ningún estado cumple esa condición.

 Ausencia de Deadlock
  • No existen estados en los que ambos procesos están bloqueados
     simultáneamente.
 Garantía de entrada
  • Al salir de la sección no crítica (wait), un proceso va al estado signal
     (sección crítica) o se bloquea.
  • La única transición de salida de un estado blocked va al estado donde el
     proceso entra a la sección crítica (signal).
```

## Página 37

[Ver página original](teorica-02-semaforos.pdf#page=37)

```text
Correctitud de la solución (2 procesos)
 Invariantes:
   a. #CS = #wait(S) − #signal(S)
   b. #CS + S.V = 1
    • #CS: Cantidad de procesos ejecutando su región crítica.
```

## Página 38

[Ver página original](teorica-02-semaforos.pdf#page=38)

```text
Correctitud de la solución (2 procesos)
 Invariantes:
   a. #CS = #wait(S) − #signal(S)
   b. #CS + S.V = 1
    • #CS: Cantidad de procesos ejecutando su región crítica.
 Prueba:
  a. Por inducción en la longitud de la traza de ejecución.
```

## Página 39

[Ver página original](teorica-02-semaforos.pdf#page=39)

```text
Correctitud de la solución (2 procesos)
 Invariantes:
   a. #CS = #wait(S) − #signal(S)
   b. #CS + S.V = 1
    • #CS: Cantidad de procesos ejecutando su región crítica.
 Prueba:
  a. Por inducción en la longitud de la traza de ejecución.
  b. Usando el invariante propio del semáforo
     (S.V = k + #signal(S) − #wait(S)):
```

## Página 40

[Ver página original](teorica-02-semaforos.pdf#page=40)

```text
Correctitud de la solución (2 procesos)
 Invariantes:
   a. #CS = #wait(S) − #signal(S)
   b. #CS + S.V = 1
    • #CS: Cantidad de procesos ejecutando su región crítica.
 Prueba:
  a. Por inducción en la longitud de la traza de ejecución.
  b. Usando el invariante propio del semáforo
     (S.V = k + #signal(S) − #wait(S)): Como el valor inicial es k = 1:
                          S.V = 1 + #signal(S) − #wait(S)
     Sustituyendo por el invariante (a):
                       S.V = 1 − #CS =⇒ #CS + S.V = 1
```

## Página 41

[Ver página original](teorica-02-semaforos.pdf#page=41)

```text
Propiedades de la solución


 Notar que #CS + S.V = 1:
   • Garantiza exclusión mutua:
```

## Página 42

[Ver página original](teorica-02-semaforos.pdf#page=42)

```text
Propiedades de la solución


 Notar que #CS + S.V = 1:
   • Garantiza exclusión mutua: (#CS ≤ 1).
```

## Página 43

[Ver página original](teorica-02-semaforos.pdf#page=43)

```text
Propiedades de la solución


 Notar que #CS + S.V = 1:
   • Garantiza exclusión mutua: (#CS ≤ 1).
   • Garantiza ausencia de deadlocks:
```

## Página 44

[Ver página original](teorica-02-semaforos.pdf#page=44)

```text
Propiedades de la solución


 Notar que #CS + S.V = 1:
   • Garantiza exclusión mutua: (#CS ≤ 1).
   • Garantiza ausencia de deadlocks: Dos procesos bloqueados en wait
     implican S.V = 0 y #CS = 0 (Contradicción: 0 + 0 ̸= 1).
```

## Página 45

[Ver página original](teorica-02-semaforos.pdf#page=45)

```text
Propiedades de la solución


 Notar que #CS + S.V = 1:
   • Garantiza exclusión mutua: (#CS ≤ 1).
   • Garantiza ausencia de deadlocks: Dos procesos bloqueados en wait
     implican S.V = 0 y #CS = 0 (Contradicción: 0 + 0 ̸= 1).
   • Garantiza entrada: Por absurdo. Suponer que un proceso p está bloqueado
     (quiere entrar) indefinidamente. Por invariantes,
     #CS = 1 − S.V = 1 − 0 = 1, por lo tanto, el otro proceso q se encuentra
     en la sección crítica. Como la sección crítica termina, q completará la
     sección crítica y ejecutará signal(S). Dado que solo hay dos procesos en el
     programa, S.L debe ser igual al conjunto {p}. Luego p se desbloqueará e
     ingresará a su sección crítica.
```

## Página 46

[Ver página original](teorica-02-semaforos.pdf#page=46)

```text
Sección crítica para N procesos

 Es posible usar la misma solución: un semáforo binario S = Semáforo(1)
 (inicializado en 1)

                    ThreadId = i (i ∈ {0, . . . , N − 1})
                      while ( true ) {
                          // seccion no critica
                          S . wait ()
                          // seccion critica
                          S . signal ()
                      }
```

## Página 47

[Ver página original](teorica-02-semaforos.pdf#page=47)

```text
Sección crítica para N procesos

 Es posible usar la misma solución: un semáforo binario S = Semáforo(1)
 (inicializado en 1)

                    ThreadId = i (i ∈ {0, . . . , N − 1})
                      while ( true ) {
                          // seccion no critica
                          S . wait ()
                          // seccion critica
                          S . signal ()
                      }

 Invariantes:
   a. #CS = #wait(S) − #signal(S)
   b. #CS + S.V = 1
 Prueba: Análoga a la solución binaria.
```

## Página 48

[Ver página original](teorica-02-semaforos.pdf#page=48)

```text
Propiedades solución basada en semáforos para N procesos


 Notar que #CS + S.V = 1:
   • Garantiza exclusión mutua: (#CS ≤ 1).
   • Garantiza ausencia de deadlocks: Dos procesos bloqueados en wait
     implican S.V = 0 y #CS = 0 (Contradicción: 0 + 0 ̸= 1).
   • No Garantiza entrada:
```

## Página 49

[Ver página original](teorica-02-semaforos.pdf#page=49)

```text
Propiedades solución basada en semáforos para N procesos


 Notar que #CS + S.V = 1:
   • Garantiza exclusión mutua: (#CS ≤ 1).
   • Garantiza ausencia de deadlocks: Dos procesos bloqueados en wait
     implican S.V = 0 y #CS = 0 (Contradicción: 0 + 0 ̸= 1).
   • No Garantiza entrada: Por absurdo. Suponer que un proceso p está
     bloqueado (quiere entrar) indefinidamente. Por invariantes,
     #CS = 1 − S.V = 1 − 0 = 1, por lo tanto, el otro proceso q se encuentra
     en la sección crítica. Como la sección crítica termina, q completará la
     sección crítica y ejecutará signal(S). El conjunto de procesos en espera no
     necesariamente es un singleton. Luego puede despertarse otro proceso.
```

## Página 50

[Ver página original](teorica-02-semaforos.pdf#page=50)

```text
Traza de Ejecución para 3 Procesos

           n   Proceso p         Proceso q      Proceso r             S
           1   p1: S.wait()      q1: S.wait()   r1: S.wait()        (1, ∅)
           2   p2: S.signal()    q1: S.wait()   r1: S.wait()        (0, ∅)
           3   p2: S.signal()    q1: blocked    r1: S.wait()      (0, {q})
           4   p1: S.signal()    q1: blocked    r1: blocked      (0, {q, r })
           5   p1: S.wait()      q1: blocked    r2: S.signal()    (0, {q})
           6   p1: blocked       q1: blocked    r2: S.signal()   (0, {p, q})
           7   p2: S.signal()    q1: blocked    r1: S.wait()      (0, {q})
   • Las columnas 3 y 7 coinciden, luego se puede construir una traza infinita en la
     cual q no puede entrar.
   • El problema es la elección del proceso en el conjunto
```

**Información gráfica:** [consultar el diagrama o tabla de la página 50](teorica-02-semaforos.pdf#page=50). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 51

[Ver página original](teorica-02-semaforos.pdf#page=51)

```text
Propiedades solución basada en semáforos para N procesos

  Semáforos fuertes (fair):
    • El conjunto de procesos bloqueados se implementa sobre una cola
      (FIFO).
```

## Página 52

[Ver página original](teorica-02-semaforos.pdf#page=52)

```text
Propiedades solución basada en semáforos para N procesos

  Semáforos fuertes (fair):
    • El conjunto de procesos bloqueados se implementa sobre una cola
      (FIFO).


 wait                                     signal

   wait () {                                signal () {
     if S . V > 0 then                        if S . L = {} then
        S . V := S . V - 1                      S . V := S . V + 1
     else                                     else
        S . L := append ( S .L , p )            q := head ( S . L )
        p . state := bloqueado                  S . L := tail ( S . L )
   }                                            q . state := ready
                                            }
```

## Página 53

[Ver página original](teorica-02-semaforos.pdf#page=53)

```text
Inanición y Semáforos Fuertes

 Semáforos Débiles vs. Fuertes
   • Con más de dos procesos, la solución estándar puede sufrir de inanición.
   • Con un semáforo fuerte, la inanición es imposible para cualquier número
     N de procesos.
```

## Página 54

[Ver página original](teorica-02-semaforos.pdf#page=54)

```text
Inanición y Semáforos Fuertes

 Semáforos Débiles vs. Fuertes
   • Con más de dos procesos, la solución estándar puede sufrir de inanición.
   • Con un semáforo fuerte, la inanición es imposible para cualquier número
     N de procesos.
  Prueba de Ausencia de Inanición
  Supongamos que un proceso p está bloqueado en S (es decir, p ∈ S.L):
   1. Como hay N procesos, la cola S.L puede tener a lo sumo N − 1 procesos.
   2. Por lo tanto, hay a lo sumo N − 2 procesos por delante de p en la cola.
   3. Después de un máximo de N − 2 operaciones S.signal(), p llegará a la
      cabeza de la cola.
   4. En consecuencia, se desbloqueará en la siguiente operación S.signal(), y
      entrará a su sección crítica.
```

## Página 55

[Ver página original](teorica-02-semaforos.pdf#page=55)

```text
Otros problemas de sincronización clásicos




   • Orden de ejecución
   • Filosofos comensales
   • Productor/Consumidor
   • Lectores/Escritores
   • Varios más: Fumadores, barbero, santa Clauss, ...
```

## Página 56

[Ver página original](teorica-02-semaforos.pdf#page=56)

```text
Orden de ejecución
 La sincronización se usa para coordinar el orden de ejecución entre procesos
 independientes.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 56](teorica-02-semaforos.pdf#page=56). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 57

[Ver página original](teorica-02-semaforos.pdf#page=57)

```text
Orden de ejecución
 La sincronización se usa para coordinar el orden de ejecución entre procesos
 independientes.
  Ejemplo: Mergesort Concurrente
  Para ordenar el arreglo [5, 1, 10, 7, 4, 3, 12, 8], dividimos el trabajo en tres proce-
  sos:
    Distribución de Procesos:
       • Proceso 1 (Sort Izquierdo): Ordena
         [5, 1, 10, 7] para obtener [1, 5, 7, 10].
       • Proceso 2 (Sort Derecho): Ordena
         [4, 3, 12, 8] para obtener [3, 4, 8, 12].
       • Proceso 3 (Merge): Combina
         resultados para obtener el arreglo final.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 57](teorica-02-semaforos.pdf#page=57). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 58

[Ver página original](teorica-02-semaforos.pdf#page=58)

```text
Orden de ejecución
 La sincronización se usa para coordinar el orden de ejecución entre procesos
 independientes.
  Ejemplo: Mergesort Concurrente
  Para ordenar el arreglo [5, 1, 10, 7, 4, 3, 12, 8], dividimos el trabajo en tres proce-
  sos:
    Distribución de Procesos:                           Requisito de Sincronización:
       • Proceso 1 (Sort Izquierdo): Ordena                • Los procesos de
         [5, 1, 10, 7] para obtener [1, 5, 7, 10].           ordenamiento son
       • Proceso 2 (Sort Derecho): Ordena                    independientes.
         [4, 3, 12, 8] para obtener [3, 4, 8, 12].         • El proceso Merge no debe
       • Proceso 3 (Merge): Combina                          ejecutarse hasta que los
         resultados para obtener el arreglo final.           otros dos hayan terminado
                                                             completamente.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 58](teorica-02-semaforos.pdf#page=58). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 59

[Ver página original](teorica-02-semaforos.pdf#page=59)

```text
Algoritmo: Mergesort Concurrente



  Variables Globales:
   int [] A
   Sem á foro S1 , S2
   S1 = Sem á foroBinario (0)
   S2 = Sem á foroBinario (0)



  sortIzq                       sortDer                merge
    p1 : sort izquierda          q1 : sort derecha      r1 : S1 . wait ()
    p2 : S1 . signal ()          q2 : S2 . signal ()    r2 : S2 . wait ()
                                                        r3 : merge
```

## Página 60

[Ver página original](teorica-02-semaforos.pdf#page=60)

```text
El Problema del Productor-Consumidor


   • Un problema de coordinación de orden de ejecución con dos tipos de
     procesos que interactúan a través de un buffer compartido:
       • Productores: Ejecutan la acción produce para crear un elemento de datos
         y enviarlo.
       • Consumidores: Reciben el elemento y ejecutan consume(dato) utilizándolo
         como parámetro.
```

## Página 61

[Ver página original](teorica-02-semaforos.pdf#page=61)

```text
El Problema del Productor-Consumidor


   • Un problema de coordinación de orden de ejecución con dos tipos de
     procesos que interactúan a través de un buffer compartido:
       • Productores: Ejecutan la acción produce para crear un elemento de datos
         y enviarlo.
       • Consumidores: Reciben el elemento y ejecutan consume(dato) utilizándolo
         como parámetro.

  Problemas de Sincronización
   1. Buffer Vacío: Un consumidor no puede extraer un elemento si no hay
      nada guardado.
   2. Buffer Lleno: Un productor no puede agregar elementos si está lleno.
```

## Página 62

[Ver página original](teorica-02-semaforos.pdf#page=62)

```text
Productor / Consumidor



  Tipos de Buffer
    • Buffer acotado: El productor tiene que esperar cuando el buffer está
      lleno; el consumidor espera cuando está vacío.
    • Buffer no acotado: El productor puede trabajar libremente; el
      consumidor debe esperar al productor.
```

## Página 63

[Ver página original](teorica-02-semaforos.pdf#page=63)

```text
Productor / Consumidor



  Tipos de Buffer
    • Buffer acotado: El productor tiene que esperar cuando el buffer está
      lleno; el consumidor espera cuando está vacío.
    • Buffer no acotado: El productor puede trabajar libremente; el
      consumidor debe esperar al productor.

   • No existen buffers infinitos. Son una abstracción: si el buffer es muy grande
     en relación a la tasa de producción, se omite el control de buffer lleno para
     evitar overhead asumiendo el riesgo de pérdida u sobreescritura de datos.
```

## Página 64

[Ver página original](teorica-02-semaforos.pdf#page=64)

```text
Productor-Consumidor: Buffer Infinito

 Sincronizar una interacción: el consumidor no debe extraer un elemento si
 el buffer está vacío.
  Variables globales

    ColaInfinita < < Typo > > buffer := new ... // crea cola vac í a .
    Sem á foro notEmpty = Semaforo (0)
```

## Página 65

[Ver página original](teorica-02-semaforos.pdf#page=65)

```text
Productor-Consumidor: Buffer Infinito

 Sincronizar una interacción: el consumidor no debe extraer un elemento si
 el buffer está vacío.
  Variables globales

    ColaInfinita < < Typo > > buffer := new ... // crea cola vac í a .
    Sem á foro notEmpty = Semaforo (0)




  Productor                                      Consumidor
   Dato d                                          Dato d
   while ( true ) {                                while ( true ) {
     p1 : d := produce ()                            q1 : notEmpty . wait ()
     p2 : append (d , buffer )                       q2 : d := take ( buffer )
     p3 : notEmpty . signal ()                       q3 : consume ( d )
   }                                               }
```

## Página 66

[Ver página original](teorica-02-semaforos.pdf#page=66)

```text
Productor-Consumidor: Buffer Infinito (Abreviado)

 Versión abreviada del algoritmo omitiendo las operaciones locales no críticas
 (produce y consume).
  Variables globales

    ColaInfinita < < Dato > > buffer := new ... // crea cola vac í a .
    Sem á foro notEmpty = Semaforo (0)




  Productor                                      Consumidor
   while ( true ) {                                Dato d
     p1 : append (d , buffer )                     while ( true ) {
     p2 : notEmpty . signal ()                       q1 : notEmpty . wait ()
   }                                                 q2 : d := take ( buffer )
                                                   }
```

## Página 67

[Ver página original](teorica-02-semaforos.pdf#page=67)

```text
Grafo de Estados Parcial (Buffer Infinito)



            p1: append,        p2: notEmpty.signal(),       p1: append,
        q1: notEmpty.wait(),    q1: notEmpty.wait(),    q1: notEmpty.wait(),
             (0, ∅), [ ]             (0, ∅), [x ]            (1, ∅), [x ]



            p1: append,        p2: notEmpty.signal(),       p1: append,
            q1: blocked,            q1: blocked,              q2: take,
           (0, {con}), [ ]         (0, {con}), [x ]          (0, ∅), [x ]
```

**Información gráfica:** [consultar el diagrama o tabla de la página 67](teorica-02-semaforos.pdf#page=67). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 68

[Ver página original](teorica-02-semaforos.pdf#page=68)

```text
Correctitud: Invariante del Buffer Infinito
 Suposición: Cada proceso ejecuta sus dos instrucciones de forma atómica (un
 único paso atómico por ciclo).
 Invariante de Conteo
                             notEmpty.V = #buffer
 Donde #buffer representa la cantidad de elementos presentes en la cola.
```

## Página 69

[Ver página original](teorica-02-semaforos.pdf#page=69)

```text
Correctitud: Invariante del Buffer Infinito
 Suposición: Cada proceso ejecuta sus dos instrucciones de forma atómica (un
 único paso atómico por ciclo).
 Invariante de Conteo
                             notEmpty.V = #buffer
 Donde #buffer representa la cantidad de elementos presentes en la cola.
 Prueba por Inducción Estructural:
   • Base Inductiva: Inicialmente, el buffer está vacío (#buffer = 0) y el
     semáforo se inicializa en cero (notEmpty.V = 0).
```

## Página 70

[Ver página original](teorica-02-semaforos.pdf#page=70)

```text
Correctitud: Invariante del Buffer Infinito
 Suposición: Cada proceso ejecuta sus dos instrucciones de forma atómica (un
 único paso atómico por ciclo).
 Invariante de Conteo
                             notEmpty.V = #buffer
 Donde #buffer representa la cantidad de elementos presentes en la cola.
 Prueba por Inducción Estructural:
   • Base Inductiva: Inicialmente, el buffer está vacío (#buffer = 0) y el
     semáforo se inicializa en cero (notEmpty.V = 0).
   • Paso Inductivo (Transiciones):
        • Acción del Productor: Agrega un elemento a la cola (#buffer + 1) y
          ejecuta notEmpty.signal() (notEmpty.V + 1).
        • Acción del Consumidor: Extrae un elemento de la cola (#buffer − 1) al
          completar un notEmpty.wait() exitoso (notEmpty.V − 1).
```

## Página 71

[Ver página original](teorica-02-semaforos.pdf#page=71)

```text
Propiedades de la Solución Abreviada

 Ausencia de Deadlock
  • Mientras el productor continúe generando elementos de datos de forma
     activa, ejecutará operaciones notEmpty.signal().
  • Estas señales incrementan el valor del semáforo o desbloquean al
     consumidor si este se encontraba suspendido, garantizando que el sistema
     nunca quede estancado globalmente.
```

## Página 72

[Ver página original](teorica-02-semaforos.pdf#page=72)

```text
Propiedades de la Solución Abreviada

 Ausencia de Deadlock
  • Mientras el productor continúe generando elementos de datos de forma
     activa, ejecutará operaciones notEmpty.signal().
  • Estas señales incrementan el valor del semáforo o desbloquean al
     consumidor si este se encontraba suspendido, garantizando que el sistema
     nunca quede estancado globalmente.

 Ausencia de Inanición
  • El consumidor es el único proceso capaz de ingresar a la cola de espera del
     semáforo (notEmpty.L).
  • Al no haber competencia con otros procesos de consumo, la inanición es
     trivialmente imposible.
```

## Página 73

[Ver página original](teorica-02-semaforos.pdf#page=73)

```text
¿Por qué funciona si no es atómico?




 Si la producción/consumición y las operaciones del semáforo ocurren por
 separado, el invariante notEmpty.V = #buffer deja de cumplirse transitoriamente.
 Invariante Relajado
                             notEmpty.V ≤ #buffer
```

## Página 74

[Ver página original](teorica-02-semaforos.pdf#page=74)

```text
¿Por qué funciona si no es atómico?


 Análisis de los Estados Intermedios:
  • En el Productor: Primero se ejecuta append (#buffer aumenta) y luego
     notEmpty.signal().
       • En el instante intermedio, hay un elemento real disponible en el buffer que el
         semáforo aún no contabiliza (notEmpty.V < #buffer).
       • Consecuencia: Esto es seguro. Lo único que ocurre es que el consumidor
         podría tener que esperar un instante más.
   • En el Consumidor: El semáforo decrementa su valor en el wait() antes de
     ejecutar la operación take.
       • El semáforo reserva el elemento por adelantado. Si el consumidor pasa el
         wait, tiene garantizado que el elemento ya existe en el buffer.
```

## Página 75

[Ver página original](teorica-02-semaforos.pdf#page=75)

```text
Buffer Finito


 Se puede extender el algoritmo para el caso buffer finito:
   • El productor extrae lugares vacíos del buffer, tal como el consumidor
     extrae elementos de datos del mismo.
   • Usamos un semáforo notFull que se inicializa en N, el número de lugares
     (inicialmente vacíos) en el buffer finito.
```

## Página 76

[Ver página original](teorica-02-semaforos.pdf#page=76)

```text
Buffer Finito


 Se puede extender el algoritmo para el caso buffer finito:
   • El productor extrae lugares vacíos del buffer, tal como el consumidor
     extrae elementos de datos del mismo.
   • Usamos un semáforo notFull que se inicializa en N, el número de lugares
     (inicialmente vacíos) en el buffer finito.

 Dualidad de los Semáforos
  • notEmpty cuenta la cantidad de elementos de datos disponibles para el
    consumidor.
  • notFull cuenta la cantidad de espacios libres disponibles para el productor.
```

## Página 77

[Ver página original](teorica-02-semaforos.pdf#page=77)

```text
Técnica: Semáforos Partidos (Split Semaphores)



 Definición Conceptual
 No es un tipo nuevo de semáforo, sino un término de diseño para describir un
 mecanismo de sincronización específico.
   • Consiste en un grupo de dos o más semáforos que satisfacen un
     invariante global.
   • La suma de sus componentes enteros es, a lo sumo, igual a un número N.

 Semáforos Binarios Partidos (Split Binary Semaphores)
 Cuando N = 1, la estructura se denomina semáforo binario partido.
```

## Página 78

[Ver página original](teorica-02-semaforos.pdf#page=78)

```text
Productor-Consumidor: Buffer de Capacidad 1


  Variables globales
   Dato buffer                        // Variable compartida unica
   Sem á foro notEmpty = Semaforo (0) // Indica elemento listo
   Sem á foro notFull = Semaforo (1) // Indica espacio libre

                       Invariante: notEmpty.V + notFull.V = 1


  Productor                                  Consumidor
    Dato d                                     Dato d
    while ( true ) {                           while ( true ) {
      d := produce ()                            notEmpty . wait ()
      notFull . wait ()                          d := buffer
      buffer := d                                notFull . signal ()
      notEmpty . signal ()                       consume ( d )
    }                                          }
```

## Página 79

[Ver página original](teorica-02-semaforos.pdf#page=79)

```text
Buffer de tamaño N (Semáforos Generales)


   • Implementamos con una cola circular: Un arreglo (buffer) y dos índices
     (inicio y fin)
   • Los productores agregan al frente e incrementan inicio.
   • Los consumidores toman de atrás e incrementan fin.

  Variables globales

   Dato [ N ] buffer
   int inicio := 0 , fin := 0
   Sem á foro notFull = Semaforo ( N ) // N espacios vacios al inicio
   Sem á foro notEmpty = Semaforo (0) // 0 espacios llenos al inicio



                    Invariante: notFull.V + notEmpty.V ≤ N
```

## Página 80

[Ver página original](teorica-02-semaforos.pdf#page=80)

```text
Buffer acotado de dimensión N

  Variables globales

   Dato [ N ] buffer
   int inicio := 0 , fin := 0
   Sem á foro notFull = Semaforo ( N ) // N espacios vacios al inicio
   Sem á foro notEmpty = Semaforo (0) // 0 espacios llenos al inicio




 Productor                                  Consumidor
   Dato d                                      Dato d
   while ( true ) {                            while ( true ) {
     d := produce ()                             notEmpty . wait ()
     notFull . wait ()                           d := buffer [ fin ]
     buffer [ inicio ] := d                      fin := ( fin + 1) % N
     inicio := ( inicio + 1) % N                 notFull . signal ()
     notEmpty . signal ()                        consume ( d )
   }                                           }
```

## Página 81

[Ver página original](teorica-02-semaforos.pdf#page=81)

```text
Falla con Múltiples Consumidores

 Asumir quedos consumidores en paralelo acceder a un buffer que contiene dos
 elementos.
     Paso   Consumidor q1              Consumidor q2              fin     Buffer
      1     notEmpty.wait()            notEmpty.wait()             0    [A, B, . . . ]
      2     d := buffer[fin] (lee A)   notEmpty.wait()             0    [A, B, . . . ]
      3     fin := (fin + 1) % N       notEmpty.wait()             0    [A, B, . . . ]
      4     fin := (fin + 1) % N       d := buffer[fin] (lee A)    0    [A, B, . . . ]
      5     fin := (fin + 1) % N       fin := (fin + 1) % N        1    [A, B, . . . ]
      6     ...                        fin := (fin + 1) % N        2    [A, B, . . . ]

 Consecuencias
   • Duplicación: q1 y q2 consumieron el mismo elemento (A).

   • Inconsistencia / Pérdida: El B (ubicado en el índice 1) no se procesará.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 81](teorica-02-semaforos.pdf#page=81). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 82

[Ver página original](teorica-02-semaforos.pdf#page=82)

```text
Único Productor y Múltiples Consumidores

   • Se utiliza un semáforo binario mutexC para secuenciar accesos al buffer por
     parte de los consumidores.
   • Valor inicial 1: mutexC = SemáforoBinario(1)
```

## Página 83

[Ver página original](teorica-02-semaforos.pdf#page=83)

```text
Único Productor y Múltiples Consumidores

   • Se utiliza un semáforo binario mutexC para secuenciar accesos al buffer por
     parte de los consumidores.
   • Valor inicial 1: mutexC = SemáforoBinario(1)


Productor                                  Consumidor
  Dato d                                     Dato d
  while ( true ) {                           while ( true ) {
    d := produce ()                            notEmpty . wait ()
    notFull . wait ()                             mutexC.wait()
    buffer [ inicio ] := d
                                                 d := buffer [ fin ]
    inicio := ( inicio + 1) % N
                                                 fin := ( fin + 1) % N
    notEmpty . signal ()
  }                                              mutexC.signal()
                                                 notFull . signal ()
                                                 consume ( d )
                                             }
```

## Página 84

[Ver página original](teorica-02-semaforos.pdf#page=84)

```text
Multiples Productores y Múltiples Consumidores
```

## Página 85

[Ver página original](teorica-02-semaforos.pdf#page=85)

```text
Multiples Productores y Múltiples Consumidores

      • Se utiliza un semáforo binario mutexP para secuenciar accesos al buffer por
        parte de los consumidores.
      • Valor inicial 1: mutexP = SemáforoBinario(1)


Productor                                     Consumidor
  Dato d                                        Dato d
  while ( true ) {                              while ( true ) {
    d := produce ()                               notEmpty . wait ()
    notFull . wait ()                                mutexC.wait()
       mutexP.wait()                                d := buffer [ fin ]
       buffer [ inicio ] := d                       fin := ( fin + 1) % N
       inicio := ( inicio + 1) % N                  mutexC.signal()
       mutexP.signal()                              notFull . signal ()
       notEmpty . signal ()                         consume ( d )
  }                                             }
```

## Página 86

[Ver página original](teorica-02-semaforos.pdf#page=86)

```text
El Problema de los Filósofos Comensales


   • Problema clásico usado para comparar formalismos de sincronización.
   • Formulación simple, pero solución desafiante.
   • 5 filósofos que tienen dos actividades: pensar y comer.


                     Dinámica del Filósofo
                       while ( true ) {
                         p1 : pensar
                         p2 : preprotocolo
                         p3 : comer
                         p4 : postprotocolo
                       }
```

## Página 87

[Ver página original](teorica-02-semaforos.pdf#page=87)

```text
El Problema de los Filósofos Comensales



                                   El Entorno:
                                     • Mesa con 5 platos, 5 tenedores y
                                       una fuente central de spaghetti.
                                     • Para comer, un filósofo necesita
                                       dos tenedores (el de su izquierda
                                       y su derecha).
                                     • Solo puede levantar un tenedor a
                                       la vez.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 87](teorica-02-semaforos.pdf#page=87). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 88

[Ver página original](teorica-02-semaforos.pdf#page=88)

```text
Requisitos de Correctitud del Algoritmo


 El diseño de los protocolos (pre y post) debe garantizar:
   • Un filósofo come solo si tiene ambos tenedores en su poder.
   • Exclusión Mutua: No es posible que dos filósofos sostengan el mismo
      tenedor simultáneamente.
   • Ausencia de deadlock: El sistema global nunca debe quedar bloqueado o
      estancado.
   • Ausencia de inanición: Ningún filósofo debe morir de hambre esperando
      sus recursos.
   • Eficiencia: Comportamiento óptimo y rápido en ausencia de contención por
      los tenedores.
```

## Página 89

[Ver página original](teorica-02-semaforos.pdf#page=89)

```text
Filósofos comensales: Intento con Array de Semáforos

  Variables globales
   Sem á foro tenedores [5] // inicializados en 1


 Filosofo(i)
   while ( true ) {
     // pensar
     izq := i
     der := ( i + 1) % 5
     tenedores [ izq ]. wait ()
     tenedores [ der ]. wait ()
     // comer
     tenedores [ izq ]. signal ()
     tenedores [ der ]. signal ()
   }
```

## Página 90

[Ver página original](teorica-02-semaforos.pdf#page=90)

```text
Filósofos comensales: Intento con Array de Semáforos

  Variables globales
   Sem á foro tenedores [5] // inicializados en 1


 Filosofo(i)                          No es libre de Deadlock
   while ( true ) {
     // pensar
                                      Puede generar deadlock:
     izq := i                          1. Todos levantan primero su tenedor
     der := ( i + 1) % 5
     tenedores [ izq ]. wait ()           izquierdo
     tenedores [ der ]. wait ()
     // comer
                                          (tenedores[izq].wait()).
     tenedores [ izq ]. signal ()      2. Quedan esperando indefinidamente
     tenedores [ der ]. signal ()
   }                                      por su tenedor derecho.
                                       3. Ningún proceso llegará jamás a
                                          ejecutar una operación signal.
```

## Página 91

[Ver página original](teorica-02-semaforos.pdf#page=91)

```text
Solución (A): Reducir la Concurrencia
```

## Página 92

[Ver página original](teorica-02-semaforos.pdf#page=92)

```text
Solución (A): Reducir la Concurrencia

   • No permitir que todos los filósofos intenten tomar tenedores a la vez.
   • Usar un semáforo contador sillas con N − 1 permisos.
```

## Página 93

[Ver página original](teorica-02-semaforos.pdf#page=93)

```text
Solución (A): Reducir la Concurrencia

   • No permitir que todos los filósofos intenten tomar tenedores a la vez.
   • Usar un semáforo contador sillas con N − 1 permisos.

Variables globales                       Filosofo(i)
                                           while ( true ) {
  Sem á foro tenedores [5]                   // pensar
  // inicializados en 1                      izq := i
                                             der := ( i + 1) % 5
  Sem á foro sillas = Semaforo (4)
                                              sillas.wait()
  // N -1 permisos
                                             tenedores [ izq ]. wait ()
                                             tenedores [ der ]. wait ()
                                             // comer
                                             tenedores [ izq ]. signal ()
                                             tenedores [ der ]. signal ()
                                              sillas.signal()
                                             }
```

## Página 94

[Ver página original](teorica-02-semaforos.pdf#page=94)

```text
Correctitud: Exclusión Mutua de Tenedores

 Exclusión Mutua
 Ningún tenedor es sostenido por dos filósofos simultáneamente.
```

## Página 95

[Ver página original](teorica-02-semaforos.pdf#page=95)

```text
Correctitud: Exclusión Mutua de Tenedores

 Exclusión Mutua
 Ningún tenedor es sostenido por dos filósofos simultáneamente.
 Demostración:
  • #Pi : cantidad de filósofos que sostienen el tenedor i en un instante dado:
```

## Página 96

[Ver página original](teorica-02-semaforos.pdf#page=96)

```text
Correctitud: Exclusión Mutua de Tenedores

 Exclusión Mutua
 Ningún tenedores sostenido por dos filósofos simultáneamente.
 Demostración:
  • #Pi : cantidad de filósofos que sostienen el tenedor i en un instante dado:
                  #Pi = #wait(tenedores[i]) − #signal(tenedores[i])
```

## Página 97

[Ver página original](teorica-02-semaforos.pdf#page=97)

```text
Correctitud: Exclusión Mutua de Tenedores

 Exclusión Mutua
 Ningún tenedores sostenido por dos filósofos simultáneamente.
 Demostración:
  • #Pi : cantidad de filósofos que sostienen el tenedor i en un instante dado:
                  #Pi = #wait(tenedores[i]) − #signal(tenedores[i])
   • Por el invariante propio de los semáforos:
           tenedores[i].V = k + #signal(tenedores[i]) − #wait(tenedores[i])
```

## Página 98

[Ver página original](teorica-02-semaforos.pdf#page=98)

```text
Correctitud: Exclusión Mutua de Tenedores

 Exclusión Mutua
 Ningún tenedores sostenido por dos filósofos simultáneamente.
 Demostración:
  • #Pi : cantidad de filósofos que sostienen el tenedor i en un instante dado:
                  #Pi = #wait(tenedores[i]) − #signal(tenedores[i])
   • Por el invariante propio de los semáforos:
           tenedores[i].V = k + #signal(tenedores[i]) − #wait(tenedores[i])
   • Como el valor de inicialización para cada tenedores k = 1:
              tenedores[i].V = 1 − #Pi =⇒ #Pi + tenedores[i].V = 1
```

## Página 99

[Ver página original](teorica-02-semaforos.pdf#page=99)

```text
Correctitud: Exclusión Mutua de Tenedores

 Exclusión Mutua
 Ningún tenedores sostenido por dos filósofos simultáneamente.
 Demostración:
  • #Pi : cantidad de filósofos que sostienen el tenedor i en un instante dado:
                  #Pi = #wait(tenedores[i]) − #signal(tenedores[i])
   • Por el invariante propio de los semáforos:
           tenedores[i].V = k + #signal(tenedores[i]) − #wait(tenedores[i])
   • Como el valor de inicialización para cada tenedores k = 1:
              tenedores[i].V = 1 − #Pi =⇒ #Pi + tenedores[i].V = 1
   • Por definición de semáforo tenedores[i].V ≥ 0, se deduce directamente que:
                                       #Pi ≤ 1
```

## Página 100

[Ver página original](teorica-02-semaforos.pdf#page=100)

```text
Correctitud: Ausencia de Deadlock

 Ausencia de deadlock
 El algoritmo es libre de deadlock.
```

## Página 101

[Ver página original](teorica-02-semaforos.pdf#page=101)

```text
Correctitud: Ausencia de Deadlock

 Ausencia de deadlock
 El algoritmo es libre de deadlock.
 Demostración por Absurdo:
```

## Página 102

[Ver página original](teorica-02-semaforos.pdf#page=102)

```text
Correctitud: Ausencia de Deadlock

 Ausencia de deadlock
 El algoritmo es libre de deadlock.
 Demostración por Absurdo:
  • Suponemos un deadlock : Todos bloqueados.
  • Análisis de los Recursos (Tenedores):
       • Por el invariante de sillas, a lo sumo hay 4 filósofos sentados a la mesa.
       • Cada uno logró ejecutar con éxito su primer tenedores[izq].wait():
         cada uno sostiene exactamente 1 tenedor.
       • En total, los filósofos retienen en sus manos 4 tenedores.
   • Contradicción:
       • En la mesa hay 5 tenedores disponibles.
       • Como solo se están reteniendo 4, queda 1 tenedor libre en la mesa.
       • El filósofo que tenga ese tenedor libre a su derecha podrá tomarlo y romperá
         el ciclo de espera.
```

## Página 103

[Ver página original](teorica-02-semaforos.pdf#page=103)

```text
Correctitud: Ausencia de Inanición (Parte 1)


 Ausencia de Inanición
 El algoritmo con restricción de comensales está libre de inanición.
```

## Página 104

[Ver página original](teorica-02-semaforos.pdf#page=104)

```text
Correctitud: Ausencia de Inanición (Parte 1)


 Ausencia de Inanición
 El algoritmo con restricción de comensales está libre de inanición.
 Premisa: El semáforo sillas es fuerte (cola FIFO), por lo que cualquier filósofo
 que espere para entrar eventualmente lo logrará.
 Demostración por Absurdo: Suponemos que el filósofo i está bloqueado para
 siempre. Por análisis de casos.
    • Caso 1: Bloqueado en su tenedor izquierdo (tenedores[izq]).
        • Esto implica que el vecino de su izquierda (i − 1) retiene el recurso como su
          tenedor derecho.
        • Por la propiedad de progreso, el filósofo i − 1 eventualmente terminará de
          comer y ejecutará el signal, liberando al filósofo i. ✓
```

## Página 105

[Ver página original](teorica-02-semaforos.pdf#page=105)

```text
Correctitud: Ausencia de Inanición (Parte 2)

   • Caso 2: Bloqueado en su tenedor derecho (tenedores[der]).
       • Significa que su vecino derecho (i + 1) tomó ese tenedor y nunca lo soltará,
         quedando a su vez bloqueado eternamente en su propio tenedor derecho.
       • Por inducción, todos los filósofos de la mesa tendrían que estar bloqueados
         en su tenedor derecho simultáneamente.
       • Contradicción: Por el invariante de sillas, a lo sumo hay 4 filósofos en la
         sala. El filósofo ausente no puede estar reteniendo ningún tenedor. ✓
```

## Página 106

[Ver página original](teorica-02-semaforos.pdf#page=106)

```text
Correctitud: Ausencia de Inanición (Parte 2)

   • Caso 2: Bloqueado en su tenedor derecho (tenedores[der]).
       • Significa que su vecino derecho (i + 1) tomó ese tenedor y nunca lo soltará,
         quedando a su vez bloqueado eternamente en su propio tenedor derecho.
       • Por inducción, todos los filósofos de la mesa tendrían que estar bloqueados
         en su tenedor derecho simultáneamente.
       • Contradicción: Por el invariante de sillas, a lo sumo hay 4 filósofos en la
         sala. El filósofo ausente no puede estar reteniendo ningún tenedor. ✓

   • Caso 3: Bloqueado esperando una silla (sillas.wait()).
       • Esto solo ocurriría si el valor de sillas.V fuese cero indefinidamente (sala
         permanentemente llena con 4 filósofos bloqueados).
       • Sin embargo, los Casos 1 y 2 demostraron que los filósofos que logran
         ingresar a la sala nunca se bloquean para siempre.
       • Eventualmente, uno terminará de comer y ejecutará sillas.signal(),
         permitiendo el ingreso. ✓
```

## Página 107

[Ver página original](teorica-02-semaforos.pdf#page=107)

```text
Solución (B): Romper la simetría

   • Uno de los filósofos se debe comportar distinto.
   • Toma primero el tenedor a su derecha y luego el de su izquierda.
```

## Página 108

[Ver página original](teorica-02-semaforos.pdf#page=108)

```text
Solución (B): Romper la simetría

   • Uno de los filósofos se debe comportar distinto.
   • Toma primero el tenedor a su derecha y luego el de su izquierda.

 Filosofo(i)
   if (i == 0) izq = 1; der = 0
   else { izq = i ; der = ( i +1) % N ; }
   while ( true ) {
      // pensar
      wait ( tenedores [ izq ]) ;
      wait ( tenedores [ der ]) ;
      // comer
      signal ( tenedores [ izq ]) ;
      signal ( tenedores [ der ]) ;
   }
```

## Página 109

[Ver página original](teorica-02-semaforos.pdf#page=109)

```text
El Problema de los Lectores/Escritores

 Es un modelo clásico que abstrae el acceso a una base de datos o recurso
 compartido donde los procesos tienen diferentes intenciones.
```

## Página 110

[Ver página original](teorica-02-semaforos.pdf#page=110)

```text
El Problema de los Lectores/Escritores

 Es un modelo clásico que abstrae el acceso a una base de datos o recurso
 compartido donde los procesos tienen diferentes intenciones.

Lectores (Readers)                         Escritores (Writers)
  • Solo consultan o leen la                 • Modifican o escriben nueva
    información del recurso.                   información en el recurso.
  • No modifican los datos.                  • Alteran el estado de los datos.
  • Concurrencia: Múltiples lectores         • Exclusión: Requieren acceso
    pueden acceder en simultáneo.              estrictamente exclusivo.

 Restricciones de Sincronización
  1. Escritor vs. Escritor: Dos escritores no pueden coincidir.
  2. Escritor vs. Lector: Un escritor no puede ingresar si hay lectores.
```

## Página 111

[Ver página original](teorica-02-semaforos.pdf#page=111)

```text
Lectores / Escritores (Prioridad Lectores)

  Variables globales




  Lector                            Escritor
   while ( true ) {                   while ( true ) {
     mutex . wait ()                    escribir . wait ()
       #lect := #lect + 1               // escribir
       if (#lect == 1) then             escribir . signal ()
            escribir . wait ()        }
     mutex . signal ()
     // leer
     mutex . wait ()
       #lect := #lect - 1
       if (#lect == 0) then
           escribir . signal ()
     mutex . signal ()
   }
```

## Página 112

[Ver página original](teorica-02-semaforos.pdf#page=112)

```text
Lectores / Escritores (Prioridad Lectores)

  Variables globales
    int #lect := 0
    Sem á foro mutex = SemaforoBinario (1)
    Sem á foro escribir = SemaforoBinario (1)


  Lector                                           Escritor
   while ( true ) {                                     while ( true ) {
     mutex . wait ()                                      escribir . wait ()
       #lect := #lect + 1                                 // escribir
       if (#lect == 1) then                               escribir . signal ()
            escribir . wait ()                          }
     mutex . signal ()
     // leer
     mutex . wait ()
       #lect := #lect - 1
       if (#lect == 0) then
           escribir . signal ()
     mutex . signal ()
   }
```

## Página 113

[Ver página original](teorica-02-semaforos.pdf#page=113)

```text
Lectores / Escritores (Prioridad Escritores)




  Variables globales

      Sem á foro escribir = SemaforoBinario (1)
      Sem á foro leer = SemaforoBinario (1)
      Sem á foro mutexL = SemaforoBinario (1)
      Sem á foro mutexE = SemaforoBinario (1) ,
      Sem á foro mutex = SemaforoBinario (1)
      int #lect := 0 , #esc := 0
```

## Página 114

[Ver página original](teorica-02-semaforos.pdf#page=114)

```text
Lectores / Escritores (Prioridad Escritores)


Lector                                Escritor
  while ( true ) {                      while ( true ) {
    mutex . wait ()                       mutexE . wait ()
      leer . wait ()                        #esc := #esc + 1
          mutexL . wait ()                  if (#esc == 1) then
            #lect := #lect + 1                  leer . wait ()
            if (#lect == 1) then          mutexE . signal ()
               escribir . wait ()
          mutexL . signal ()                escribir . wait ()
      leer . signal ()                      // escribir
    mutex . signal ()                       escribir . signal ()
    // leer
    mutexL . wait ()                        mutexE . wait ()
      #lect := #lect - 1                      #esc := #esc - 1
      if (#lect == 0) then                    if (#esc == 0) then
          escribir . signal ()                   leer . signal ()
    mutexL . signal ()                      mutexE . signal ()
  }                                     }
```

## Página 115

[Ver página original](teorica-02-semaforos.pdf#page=115)

```text
Semáforos de Espera Activa y Spinlocks

 Equivalencia Conceptual
 Un busy-wait semaphore se comporta exactamente como un spinlock:
   • En lugar de bloquear al proceso y enviarlo a una cola (S.L), el proceso se
     queda en un bucle infinito (girando) evaluando la condición.
Implementación (Spinlock):                  Características clave:
                                              • Consumo de CPU: Desperdicia
   S . wait () {                                ciclos de procesador de forma
       while ( S . V <= 0) {
           // skip / busy - wait
                                                constante mientras espera.
       }                                      • Sin cambio de contexto: Evita
       S . V := S . V - 1
   }                                            el costo de suspender y reactivar
                                                el hilo.
                                              • Uso ideal: Sistemas
                                                multiprocesador con esperas
```
