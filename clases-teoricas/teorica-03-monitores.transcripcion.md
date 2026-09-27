# teorica-03-monitores — transcripción

- Fuente: [teorica-03-monitores.pdf](teorica-03-monitores.pdf)
- Páginas del PDF: 86.
- SHA-256 del PDF: `ed608cf0d567820a3b71ddf670acdfb2f811fa17c4ecc647cef2ff626b7c3ae2`.
- Tipo: transcripción textual por página, con disposición espacial conservada y referencias al original para esquemas.

> Se conserva el contenido del original, incluidos ejemplos incorrectos, erratas y diapositivas progresivas. Los bloques `text` preservan columnas y sangría; no son código listo para compilar. En código repartido en columnas, seguir las indicaciones «continúa a la derecha». Las notas editoriales se distinguen del original. Para símbolos cuya extracción sea ambigua, consultar la página enlazada.

## Página 1

[Ver página original](teorica-03-monitores.pdf#page=1)

```text
Monitores
```

## Página 2

[Ver página original](teorica-03-monitores.pdf#page=2)

```text
Semáforos


  Ventajas
    • Simples.
    • Eficientes (no hay busy-waiting).

  Desventajas (Muy bajo nivel)
    • Si olvidamos el signal: deadlock.
    • Si olvidamos el wait: no garantizamos exclusión.
    • La sincronización no está vinculada a los datos.
        • Las instrucciones e encargadas de la sincronización pueden aparecer en
          cualquier parte.
```

## Página 3

[Ver página original](teorica-03-monitores.pdf#page=3)

```text
Monitores




   • Combina tipo de datos abstractos y exclusión mutua.
       • Propuesto por Tony Hoare [1974].
   • Empleados masivamente en lenguajes modernos:
       • Pthreads
       • Java
       • C#
```

## Página 4

[Ver página original](teorica-03-monitores.pdf#page=4)

```text
Monitores : ADT + Sincronización

 Definición y Encapsulamiento
  • La responsabilidad sincronizar correctamente está alocada en módulos
     aislados.
  • Un monitor para cada objeto o grupo de objetos relacionados (tipo de
     datos).
  • Todos los campos de datos de un monitor son privados.

 Exclusión Mutua y Concurrencia
   • Mismo monitor: La abstracción garantiza que las operaciones se ejecutan
     en exclusión mutua.
   • Distintos monitores: Las ejecuciones de sus operaciones pueden
     entrelazarse libremente.
```

## Página 5

[Ver página original](teorica-03-monitores.pdf#page=5)

```text
Monitores y Objetos




 Generalización de la POO
  • Es una evolución de las clases de la Programación Orientada a Objetos.
  • Añade la restricción de que solo un proceso a la vez puede operar sobre
    un objeto.
```

## Página 6

[Ver página original](teorica-03-monitores.pdf#page=6)

```text
Ejemplo

Example                           • Encapsulamiento: La variable
 Monitor Contador {                 contador es privada y no se puede
   private int contador = 0         acceder desde afuera del monitor
   public void incrementar () {     CS.
     contador ++                  • Exclusión mutua: Solo un proceso
   }                                a la vez puede ejecutar una
 }                                  operación del monitor.
 thread p
                                  • Atomicidad garantizada: Aunque
 p1 : CS . incrementar ()           incrementar tiene varias
                                    instrucciones, los procesos p y q no
 thread q                           pueden entrelazarse.
 q1 : CS . incrementar ()         • Resultado: Se garantiza que el
                                    valor final de contador será 2.
```

## Página 7

[Ver página original](teorica-03-monitores.pdf#page=7)

```text
Simulación Gráfica de un Monitor


                      p   q
                                   Estado Inicial
                                     • El monitor está vacío y
Contador                               la puerta abierta.
                                     • Los procesos p y q
                                       quieren entrar.
  incrementar                        • La elección depende de
                                       la implementación. Por
                      n = 0            ahora asumimos no
                                       determinismo.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 7](teorica-03-monitores.pdf#page=7). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 8

[Ver página original](teorica-03-monitores.pdf#page=8)

```text
Simulación Gráfica de un Monitor


                      q
                                   Exclusión Mutua
                                    • p es elegido e ingresa a
Contador                              incrementar.
                                    • La puerta se cierra
                                      automáticamente.
  incrementar                       • q queda bloqueado fuera.
                p
                      n = 0
```

**Información gráfica:** [consultar el diagrama o tabla de la página 8](teorica-03-monitores.pdf#page=8). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 9

[Ver página original](teorica-03-monitores.pdf#page=9)

```text
Simulación Gráfica de un Monitor


                      q
                                   Modificación de n
                                     • p termina su bloque de
Contador                               operaciones.
                                     • El valor de la variable
                                       cambia a n = 1.
  incrementar                        • El lock se abre para el
                                       siguiente proceso.
                      n = 1
```

**Información gráfica:** [consultar el diagrama o tabla de la página 9](teorica-03-monitores.pdf#page=9). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 10

[Ver página original](teorica-03-monitores.pdf#page=10)

```text
Simulación Gráfica de un Monitor



                                   Resultado Final
                                     • p se retira. q toma su
Contador                               turno de manera
                                       exclusiva.
                                     • Al finalizar q, el valor
  incrementar                          definitivo es n = 2.
                q                    • Atomicidad total
                      n = 2            garantizada.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 10](teorica-03-monitores.pdf#page=10). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 11

[Ver página original](teorica-03-monitores.pdf#page=11)

```text
Variables de Condición

   • El monitor garantiza exclusión mutua implícita sobre las variables privadas.
   • Muchos problemas requieren sincronización adicional y explícita.
```

## Página 12

[Ver página original](teorica-03-monitores.pdf#page=12)

```text
Variables de Condición

   • El monitor garantiza exclusión mutua implícita sobre las variables privadas.
   • Muchos problemas requieren sincronización adicional y explícita.
   • Ejemplo clásico: Productor-Consumidor.
       • El productor se bloquea si el buffer está lleno.
       • El consumidor se bloquea si el buffer está vacío.
```

## Página 13

[Ver página original](teorica-03-monitores.pdf#page=13)

```text
Variables de Condición

   • El monitor garantiza exclusión mutua implícita sobre las variables privadas.
   • Muchos problemas requieren sincronización adicional y explícita.
   • Ejemplo clásico: Productor-Consumidor.
        • El productor se bloquea si el buffer está lleno.
        • El consumidor se bloquea si el buffer está vacío.

  Variables de condición (o condición)
  Son colas de espera explícitas (FIFO) manejadas por la abstracción que permiten
  definir sincronizaciones adicionales entre operaciones.
    • No almacenan valores; representan procesos temporalmente suspendidos.
    • Permiten que un proceso libere el monitor voluntariamente cuando un recurso
      no está disponible.
```

## Página 14

[Ver página original](teorica-03-monitores.pdf#page=14)

```text
Elementos principales de un monitor



   • Un conjunto de operaciones (procedimientos) encapsuladas en módulos
     (o clases).
   • Existe un único lock (mutex) global que asegura la exclusión mutua entre
     todas las operaciones en el monitor.
       • Exclusión mutua automática.
   • Variables de condiciones (condition variables) para definir sincronización
     condicional.
```

## Página 15

[Ver página original](teorica-03-monitores.pdf#page=15)

```text
Variables de condición




   • Son globales al monitor.
   • Cada una representa una cola de procesos bloqueados.
   • Poseen dos operaciones principales:
       • wait(c): Bloquea el proceso en ejecución y lo asocia a la variable c. El
         proceso debe liberar el lock de exclusión mutua.
       • signal(c): Desbloquea al primer proceso bloqueado en la variable c.
```

## Página 16

[Ver página original](teorica-03-monitores.pdf#page=16)

```text
Estructura de Sincronización en Monitores

                                      Monitor
              Operación



                                                Condición
                                     c1


                                     c2


              vars...
```

**Información gráfica:** [consultar el diagrama o tabla de la página 16](teorica-03-monitores.pdf#page=16). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 17

[Ver página original](teorica-03-monitores.pdf#page=17)

```text
Comportamiento Típico en Monitores


                      p
                                     1. Intento de Acceso
                                       • El proceso p llega al monitor
                                         queriendo ejecutar una
                                         operación.
                          c1           • Al encontrar la puerta
                                         abierta, se dispone a ingresar.

                          c2


   vars...
```

**Información gráfica:** [consultar el diagrama o tabla de la página 17](teorica-03-monitores.pdf#page=17). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 18

[Ver página original](teorica-03-monitores.pdf#page=18)

```text
Comportamiento Típico en Monitores



                                     2. Exclusión Mutua
                                       • p ingresa con éxito a la
      p                                  primera operación.
                                       • El lock se activa y la puerta
                        c1               se cierra.
                                       • Ningún otro proceso puede
                                         irrumpir temporalmente.
                        c2


   vars...
```

**Información gráfica:** [consultar el diagrama o tabla de la página 18](teorica-03-monitores.pdf#page=18). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 19

[Ver página original](teorica-03-monitores.pdf#page=19)

```text
Comportamiento Típico en Monitores


                      q
                                     3. Competencia
                                      • Mientras p continúa activo
      p                                 adentro, arriba un proceso q.
                                      • Como el monitor está
                          c1            ocupado, q debe esperar en
                                        la entrada.

                          c2


   vars...
```

**Información gráfica:** [consultar el diagrama o tabla de la página 19](teorica-03-monitores.pdf#page=19). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 20

[Ver página original](teorica-03-monitores.pdf#page=20)

```text
Comportamiento Típico en Monitores


                      q        r   s
                                       4. Llegan más procesos
                                         • Llegan más hilos
      p                                    concurrentes (r , s).
                                         • Debido a la exclusión mutua,
                          c1               se acumulan esperando su
                                           turno en la entrada.

                          c2


   vars...
```

**Información gráfica:** [consultar el diagrama o tabla de la página 20](teorica-03-monitores.pdf#page=20). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 21

[Ver página original](teorica-03-monitores.pdf#page=21)

```text
Comportamiento Típico en Monitores


                      q        r       s
                                           5. Bloqueo en Condición
                                             • p se bloquea al invocar un
                                   p           wait(c1).
                                             • Abandona la exclusión: se
                          c1                   agrega a la cola de c1 y la
                                               puerta se abre
                                               automáticamente.
                          c2


   vars...
```

**Información gráfica:** [consultar el diagrama o tabla de la página 21](teorica-03-monitores.pdf#page=21). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 22

[Ver página original](teorica-03-monitores.pdf#page=22)

```text
Operación wait(c)



  • Similar a la operación del wait del semáforo.
      • wait(c) bloquea el proceso en ejecución y lo asocia a la variable c.

  • El proceso bloqueado debe liberar el lock de exclusión mutua.
      • Permite que ingrese algún otro proceso al monitor.

  • ¿En qué difiere esta operación de la operación wait de semáforo?
```

## Página 23

[Ver página original](teorica-03-monitores.pdf#page=23)

```text
Comportamiento Típico: Operación signal(c1) al final



                                       6. Estado de bloqueo
                                         • En una variable de condición
                                           c1 se encuentra una cola de
                        c1                 hilos suspendidos esperando
                                           por un recurso o estado.
                                         • Un proceso en ejecución
                        c2
                                           modifica el estado (o genera
                                           el recurso) que están
   vars...                                 esperando los procesos
                                           bloqueados.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 23](teorica-03-monitores.pdf#page=23). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 24

[Ver página original](teorica-03-monitores.pdf#page=24)

```text
Comportamiento Típico: Operación signal(c1) al final



                                       7. Operación Signal
                                         • El proceso que está adentro
                                           ejecuta la instrucción
                        c1                 signal(c1) como última
                                           instrucción antes de salir.
                                         • La llamada a signal
                        c2
                                           desbloquea a un proceso
                                           esperando en la cola.
   vars...                               • El proceso se despierta y
                                           continúa su ejecución.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 24](teorica-03-monitores.pdf#page=24). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 25

[Ver página original](teorica-03-monitores.pdf#page=25)

```text
Operación signal como última instrucción



  • Desbloquea al primer proceso bloqueado en la variable de condición.
  • Si no hay procesos esperando en la cola, la operación no tiene efecto (no se
    almacena el signal).
```

## Página 26

[Ver página original](teorica-03-monitores.pdf#page=26)

```text
Operación signal como última instrucción



  • Desbloquea al primer proceso bloqueado en la variable de condición.
  • Si no hay procesos esperando en la cola, la operación no tiene efecto (no se
    almacena el signal).
  • Al desbloquear un proceso, el desbloqueado continúa su ejecución desde
    la instrucción que sigue a la llamada del wait que lo bloqueó.
```

## Página 27

[Ver página original](teorica-03-monitores.pdf#page=27)

```text
Operación signal como última instrucción



  • Desbloquea al primer proceso bloqueado en la variable de condición.
  • Si no hay procesos esperando en la cola, la operación no tiene efecto (no se
    almacena el signal).
  • Al desbloquear un proceso, el desbloqueado continúa su ejecución desde
    la instrucción que sigue a la llamada del wait que lo bloqueó.
  • ¿Qué sucede con el lock?
    El proceso que ejecuta la llamada a signal(c) termina y pasa el lock de
    exclusión mutua del monitor al proceso que se desbloquea.
```

## Página 28

[Ver página original](teorica-03-monitores.pdf#page=28)

```text
Implementación de las Variables de Condición




  Estructura y atomicidad
    • Una cola FIFO de procesos bloqueados.
    • Las operaciones se ejecutan de manera atómica.
```

## Página 29

[Ver página original](teorica-03-monitores.pdf#page=29)

```text
Implementación de las Variables de Condición


  Estructura y atomicidad
    • Una cola FIFO de procesos bloqueados.
    • Las operaciones se ejecutan de manera atómica.


 waitC(cond)                          • Suspensión: El proceso que ejecuta
   append p to cond                     wait se suspende inmediatamente en la
   p.state = blocked                    cola cond.
   monitor.lock.unlock()
                                      • Libera el Lock para permitir que otro
                                        proceso ingrese al monitor.
```

## Página 30

[Ver página original](teorica-03-monitores.pdf#page=30)

```text
Implementación de las Variables de Condición


  Estructura y atomicidad
    • Una cola FIFO de procesos bloqueados.
    • Las operaciones se ejecutan de manera atómica.


 signalC(cond)                        • Cambio de Estado: El proceso que se
   if cond != empty                     despierta pasa al estado listo (ready ).
     q = pop cond                     • Riesgo de violación mutex: si signal
     q.state = ready
                                        no es la última operación del proceso
                                        dentro del monitor, se puede violar la
                                        exclusión mutua.
```

## Página 31

[Ver página original](teorica-03-monitores.pdf#page=31)

```text
Implementación de las Variables de Condición




  Estructura y atomicidad
    • Una cola FIFO de procesos bloqueados.
    • Las operaciones se ejecutan de manera atómica.


                                      • Devuelve un valor booleano indicando si
 empty(cond)                            la cola de procesos bloqueados está
   return cond == empty                 vacía o no.
```

## Página 32

[Ver página original](teorica-03-monitores.pdf#page=32)

```text
Simulación de un Semáforo mediante Monitores
```

## Página 33

[Ver página original](teorica-03-monitores.pdf#page=33)

```text
Simulación de un Semáforo mediante Monitores



Estructura del Monitor:
  • sv: Variable entera privada
    que representa el valor actual
    del semáforo.
  • noCero: Variable de
    condición donde se suspenden
    los procesos cuando el
    semáforo llega a cero.
```

## Página 34

[Ver página original](teorica-03-monitores.pdf#page=34)

```text
Simulación de un Semáforo mediante Monitores



Estructura del Monitor:
                                     monitor Semaforo {
  • sv: Variable entera privada        private int sv ;
    que representa el valor actual     private Condition noCero ;
    del semáforo.
                                         public Semaforo ( int v ) {
  • noCero: Variable de                    // pre : v >= 0
    condición donde se suspenden           this . sv = v ;
    los procesos cuando el               }
    semáforo llega a cero.           }
```

## Página 35

[Ver página original](teorica-03-monitores.pdf#page=35)

```text
Operaciones del Monitor Semáforo


     public void wait () {
       if ( sv == 0)
         wait ( noCero ) ;

        sv - -;
    }

    public void signal () {
      sv ++;
      signal ( noCero ) ;
    }
  } // fin monitor
```

## Página 36

[Ver página original](teorica-03-monitores.pdf#page=36)

```text
Corrección de la simulación del Semáforo



  p1: Sem.wait,     p2: Sem.signal,   p2: Sem.signal,
  q1: Sem.wait,      q1: Sem.wait,       blocked,
      1, <>              0, <>           0, < q >




   p1: Sem.wait,       blocked,
  q2: Sem.signal,   q2: Sem.signal,
       0, <>           0, < p >


   • Paso Único Atómico: Las operaciones se ejecutan en exclusión mutua.
     Sus instrucciones se agrupan en una única transición.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 36](teorica-03-monitores.pdf#page=36). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 37

[Ver página original](teorica-03-monitores.pdf#page=37)

```text
Corrección de la simulación del Semáforo



  p1: Sem.wait,     p2: Sem.signal,   p2: Sem.signal,
  q1: Sem.wait,      q1: Sem.wait,       blocked,
      1, <>              0, <>           0, < q >




   p1: Sem.wait,       blocked,
  q2: Sem.signal,   q2: Sem.signal,
       0, <>           0, < p >


   • Simplicidad: El diagrama es compacto porque las transiciones internas de
     las sentencias del monitor se abstraen en un solo paso.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 37](teorica-03-monitores.pdf#page=37). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 38

[Ver página original](teorica-03-monitores.pdf#page=38)

```text
Corrección de la simulación del Semáforo



  p1: Sem.wait,     p2: Sem.signal,   p2: Sem.signal,
  q1: Sem.wait,      q1: Sem.wait,       blocked,
      1, <>              0, <>           0, < q >




   p1: Sem.wait,       blocked,
  q2: Sem.signal,   q2: Sem.signal,
       0, <>           0, < p >


   • Bloqueo en cola: Cuando un proceso ejecuta Sem.wait cuando s = 0, se
     bloquea en la cola noCero.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 38](teorica-03-monitores.pdf#page=38). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 39

[Ver página original](teorica-03-monitores.pdf#page=39)

```text
Corrección de la simulación del Semáforo



  p1: Sem.wait,     p2: Sem.signal,   p2: Sem.signal,
  q1: Sem.wait,      q1: Sem.wait,       blocked,
      1, <>              0, <>           0, < q >




   p1: Sem.wait,       blocked,
  q2: Sem.signal,   q2: Sem.signal,
       0, <>           0, < p >


   • Garantía: No existe un estado de la forma (p2: Sem.signal, q2:
     Sem.signal), hay exclusión mutua.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 39](teorica-03-monitores.pdf#page=39). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 40

[Ver página original](teorica-03-monitores.pdf#page=40)

```text
Implementación de un Buffer (dimensión 1) con Monitores
```

## Página 41

[Ver página original](teorica-03-monitores.pdf#page=41)

```text
Implementación de un Buffer (dimensión 1) con Monitores
 Variables de Condición:
   • hayEspacio: Los productores esperan en esta cola hasta que el buffer se
     libere.
   • conDato: Los consumidores esperan en esta cola hasta que haya un dato
     disponible.

 Estructura del Monitor:
   monitor Buffer {
     private condition hayEspacio ;
     private condition conDato ;
     private boolean estaVacio = true ;
     private int buf ;

     // ... sigue en la siguiente diapositiva
```

## Página 42

[Ver página original](teorica-03-monitores.pdf#page=42)

```text
Operaciones del Monitor Buffer


      Consumidor (leer)
 public int leer () {
   if ( estaVacio )
     wait ( conDato ) ;

     estaVacio = true ;
     int res = buf ;
     signal ( hayEspacio ) ;
     return res ;
 }
```

## Página 43

[Ver página original](teorica-03-monitores.pdf#page=43)

```text
Operaciones del Monitor Buffer


      Consumidor (leer)                   Productor (escribir)
 public int leer () {          public void escribir ( int dato ) {
   if ( estaVacio )              if (! estaVacio )
     wait ( conDato ) ;            wait ( hayEspacio ) ;

     estaVacio = true ;            buf = dato ;
     int res = buf ;               estaVacio = false ;
     signal ( hayEspacio ) ;       signal ( conDato ) ;
     return res ;              }
 }
```

## Página 44

[Ver página original](teorica-03-monitores.pdf#page=44)

```text
Operaciones del Monitor Buffer


      Consumidor (leer)                          Productor (escribir)
 public int leer () {                public void escribir ( int dato ) {
   if ( estaVacio )                    if (! estaVacio )
     wait ( conDato ) ;                  wait ( hayEspacio ) ;

     estaVacio = true ;                  buf = dato ;
     int res = buf ;                     estaVacio = false ;
     signal ( hayEspacio ) ;             signal ( conDato ) ;
     return res ;                    }
 }
 Notar: que en leer, signal no es la última instrucción. Se puede solapar el return
 con un escribir. Aquí no habría problemas porque no se tocan la variables
 compartidas
```

## Página 45

[Ver página original](teorica-03-monitores.pdf#page=45)

```text
Disciplinas para signal



   • Hasta ahora consideramos la versión en la cual el signal implica la salida
     del monitor:
       • El signal se encuentra estrictamente al final del método.
       • El lock mutex se transfiere de inmediato al proceso que se despierta.

   • Otras posibilidades y variantes:
       • ¿Qué sucede si el signal no está al final del procedimiento?
```

## Página 46

[Ver página original](teorica-03-monitores.pdf#page=46)

```text
Coexistencia tras un Signal

   • Violación de mutex:
       • El proceso p ejecuta signalC(cond) y puede continuar con su siguiente
         instrucción.
       • El proceso q (desbloqueado) puede continuar desde la instrucción posterior a
         su waitC(cond).
   • Este escenario debería ser posible. La especificación de un monitor exige que
     como máximo un proceso a la vez pueda estar ejecutando instrucciones
     adentro.
```

## Página 47

[Ver página original](teorica-03-monitores.pdf#page=47)

```text
Coexistencia tras un Signal

   • Violación de mutex:
       • El proceso p ejecuta signalC(cond) y puede continuar con su siguiente
         instrucción.
       • El proceso q (desbloqueado) puede continuar desde la instrucción posterior a
         su waitC(cond).
   • Este escenario debería ser posible. La especificación de un monitor exige que
     como máximo un proceso a la vez pueda estar ejecutando instrucciones
     adentro.
   • Necesidad de una Disciplina: Se debe evitar que haya dos procesos al
     mismo tiempo.
```

## Página 48

[Ver página original](teorica-03-monitores.pdf#page=48)

```text
Coexistencia tras un Signal

   • Violación de mutex:
       • El proceso p ejecuta signalC(cond) y puede continuar con su siguiente
         instrucción.
       • El proceso q (desbloqueado) puede continuar desde la instrucción posterior a
         su waitC(cond).
   • Este escenario debería ser posible. La especificación de un monitor exige que
     como máximo un proceso a la vez pueda estar ejecutando instrucciones
     adentro.
   • Necesidad de una Disciplina: Se debe evitar que haya dos procesos al
     mismo tiempo.
       • E (Entry): Procesos bloqueados intentando entrar por primera vez al
         monitor.
       • W (Waiting): Procesos recién liberados de una cola de condición.
       • S (Signaling): Procesos que acaban de ejecutar una llamada a signal.
```

## Página 49

[Ver página original](teorica-03-monitores.pdf#page=49)

```text
El Requerimiento de Reanudación Inmediata (IRR)

   • Restricciones de Sentido Común: Existen 13 formas de ordenar las
     prioridades (E , W , S). Muchas carecen de sentido práctico.
       • Hacer que E > W o E > S provocaría inanición.
```

## Página 50

[Ver página original](teorica-03-monitores.pdf#page=50)

```text
El Requerimiento de Reanudación Inmediata (IRR)

   • Restricciones de Sentido Común: Existen 13 formas de ordenar las
     prioridades (E , W , S). Muchas carecen de sentido práctico.
       • Hacer que E > W o E > S provocaría inanición.
   • La Política Clásica de Hoare (E < S < W ):
       • Conocida formalmente como Immediate Resumption Requirement (IRR)
         o disciplina de Signal y Espera.
       • Cuando un proceso bloqueado se despierta, toma la CPU e inicia su
         ejecución de inmediato.
```

## Página 51

[Ver página original](teorica-03-monitores.pdf#page=51)

```text
El Requerimiento de Reanudación Inmediata (IRR)

   • Restricciones de Sentido Común: Existen 13 formas de ordenar las
     prioridades (E , W , S). Muchas carecen de sentido práctico.
       • Hacer que E > W o E > S provocaría inanición.
   • La Política Clásica de Hoare (E < S < W ):
       • Conocida formalmente como Immediate Resumption Requirement (IRR)
         o disciplina de Signal y Espera.
       • Cuando un proceso bloqueado se despierta, toma la CPU e inicia su
         ejecución de inmediato.

   • Intuición: El proceso que hace signal acaba de modificar el estado para
     que la condición se cumpla. Si el hilo despertado entra de forma inmediata y
     atómica, tiene la certeza de que la condición sigue siendo verdadera y
     puede avanzar sin reevaluar.
```

## Página 52

[Ver página original](teorica-03-monitores.pdf#page=52)

```text
Jerarquías de Prioridades: Hoare vs. Java (Mesa)

   • Las distintas semánticas de monitores se diferencian por cómo ordenan las
     prioridades:

     Semántica              Orden        Mutex
     Monitor      Clásico   E <S<W       El proceso despertado (W ) aquiere el
     (Hoare / IRR)                       mutex. El señalizador (S) va primero
                                         en la cola del mutex. El mutex es
                                         fair
     Monitor Moderno        E =W <S      El señalizador (S) termina primero. El
     (Mesa/Java)                         despertado (W ) compite en igualdad
                                         con las nuevas entradas (E ). El mu-
                                         tex es generalmente unfair
```

**Información gráfica:** [consultar el diagrama o tabla de la página 52](teorica-03-monitores.pdf#page=52). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 53

[Ver página original](teorica-03-monitores.pdf#page=53)

```text
Signal y Espera Urgente (Hoare)

                       p
                                  Quien ejecuta signal se agrega
                                   al inicio de la cola de espera
                                            por el mutex

                           c1


                           c2      Se pasa el mutex al proceso
                                        que se despierta

    vars...
```

**Descripción editorial del esquema:** Signal y espera urgente: quien señaliza cede el mutex al despertado y queda en la cola urgente, por delante de entradas nuevas.

**Información gráfica:** [consultar el diagrama o tabla de la página 53](teorica-03-monitores.pdf#page=53). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 54

[Ver página original](teorica-03-monitores.pdf#page=54)

```text
Signal y Continúa (Mesa)




       p                         Quien ejecuta el signal
                                continúa con su ejecución
                           c1


                           c2
                                El proceso que se desbloquea
    vars...                     pasa a la cola de espera por el
                                            mutex
```

**Descripción editorial del esquema:** Signal y continúa: quien señaliza conserva el mutex; el despertado pasa a competir por el mutex y debe esperar a que sea liberado.

**Información gráfica:** [consultar el diagrama o tabla de la página 54](teorica-03-monitores.pdf#page=54). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 55

[Ver página original](teorica-03-monitores.pdf#page=55)

```text
Signal y continúa


  Política
    • El proceso p que ejecuta signal continúa su ejecución de forma normal
      dentro del monitor, mientras que:
    • El proceso q que se desbloquea abandona la variable de condición y se
      une a la cola de espera por el mutex de entrada.
```

## Página 56

[Ver página original](teorica-03-monitores.pdf#page=56)

```text
Signal y continúa


  Política
    • El proceso p que ejecuta signal continúa su ejecución de forma normal
      dentro del monitor, mientras que:
    • El proceso q que se desbloquea abandona la variable de condición y se
      une a la cola de espera por el mutex de entrada.

  Problema crítico
    • El proceso p puede seguir modificando el estado y las variables globales del
      monitor luego de haber ejecutado el signal.
```

## Página 57

[Ver página original](teorica-03-monitores.pdf#page=57)

```text
Signal y continúa


  Política
    • El proceso p que ejecuta signal continúa su ejecución de forma normal
      dentro del monitor, mientras que:
    • El proceso q que se desbloquea abandona la variable de condición y se
      une a la cola de espera por el mutex de entrada.

  Problema crítico
    • El proceso p puede seguir modificando el estado y las variables globales del
      monitor luego de haber ejecutado el signal.
    • Cuando q finalmente logre volver a entrar al monitor, la condición por la que se
      despertó podría haber dejado de ser verdadera.
```

## Página 58

[Ver página original](teorica-03-monitores.pdf#page=58)

```text
Signal y continúa: Patrón de Programación


   • Debido a que la condición puede cambiar antes de recuperar el mutex,
     necesitamos programar teniendo en cuenta esto.
```

## Página 59

[Ver página original](teorica-03-monitores.pdf#page=59)

```text
Signal y continúa: Patrón de Programación


      • Debido a que la condición puede cambiar antes de recuperar el mutex,
        necesitamos programar teniendo en cuenta esto.

  public void wait () {                   • Se debe utilizar un patrón conocido
    // Al despertar ,                       como “pasar la condición”:
     vuelve a verificar                       • Se sustituye if por un bucle while
    while ( sv == 0)                            para reevaluar.
      wait ( noCero ) ;

       sv - -;
  }
```

## Página 60

[Ver página original](teorica-03-monitores.pdf#page=60)

```text
Signal y continúa: Patrón de Programación


      • Debido a que la condición puede cambiar antes de recuperar el mutex,
        necesitamos programar teniendo en cuenta esto.

  public void wait () {                   • Se debe utilizar un patrón conocido
    // Al despertar ,                       como “pasar la condición”:
     vuelve a verificar                       • Se sustituye if por un bucle while
    while ( sv == 0)                            para reevaluar.
      wait ( noCero ) ;
                                          • Consecuencia: Riesgo de inanición si
       sv - -;                              procesos nuevos le quitan el mutex al
  }                                         recién desbloqueado.
```

## Página 61

[Ver página original](teorica-03-monitores.pdf#page=61)

```text
Signal y continúa: la preferida hoy


   • Percepción inicial: Parece menos intuitivo debido a la necesidad de
     reevaluar las condiciones en un bucle while.

   • Es la disciplina preferida hoy en la mayoría de los lenguajes de
     programación s por los siguientes motivos:
       • Semántica más simple: No requiere de un traspaso atómico del lock entre
         procesos.
       • Compatibilidad con políticas de scheduling: por ejemplo políticas de
         prioridad.
```

## Página 62

[Ver página original](teorica-03-monitores.pdf#page=62)

```text
Signal y continúa: Equidad (Fairness)


   public void wait () {
       while ( sv == 0)
           wait ( noCero ) ;

       sv - -;                  ¿Es fair? ¿Qué sucede con un proceso
   }                             que estaba esperando para adquirir
                                               el lock?
   public void signal () {
       sv ++;
       signal ( noCero ) ;
   }
```

## Página 63

[Ver página original](teorica-03-monitores.pdf#page=63)

```text
Signal y continúa: Falta de Equidad


   • Problema de Fairness: No es fair, dado que un proceso fuera del monitor
     podría adelantarse y robar el token (el recurso) antes de que el proceso
     despertado logre reingresar.

   • Alternativa de solución:
       • El proceso que ejecuta signal le pasa directamente la información de que
         sv tiene un valor positivo al proceso que se despierta.
       • Para implementar esto de forma segura, necesitamos utilizar la operación
         empty(cv).
       • Esta función nos permite conocer si existen procesos bloqueados en la
         variable de condición.
```

## Página 64

[Ver página original](teorica-03-monitores.pdf#page=64)

```text
Semáforo Equitativo con empty()


Solución al robo del Token:
                                          public void wait () {
  • Se cambia el while por un if bajo       if ( sv == 0)
    una nueva condición.                      wait ( noCero ) ;
                                            else
  • El proceso que hace signal no             sv - -;
    incrementa sv si detecta que hay      }
    alguien esperando en la cola.
                                          public void signal () {
  • El token se transfiere directamente     if ( empty ( noCero ) )
    de forma lógica al proceso                sv ++;
    despertado, impidiendo que hilos        else
    externos se lo quiten.                    signal ( noCero ) ;
                                          }
```

## Página 65

[Ver página original](teorica-03-monitores.pdf#page=65)

```text
Buffer de Capacidad N (Signal y Salida Urgente)

 Estructura del Monitor:
   • Utiliza un arreglo circular de tamaño N y un contador explícito (cant).
   • Al ser la semántica de Hoare, las validaciones de las colas de condición se
     realizan de forma segura mediante un condicional if.

   monitor Buffer {
     private condition hayEspacio ;
     private condition conDato ;
     private int buffer [ N ];
     private int inicio = 0 , fin = 0 , cant = 0;

      // ... las operaciones siguen en la proxima
       diapositiva
```

## Página 66

[Ver página original](teorica-03-monitores.pdf#page=66)

```text
Buffer de Capacidad N (Signal y Salida Urgente)


          Productor (escribir)                 Consumidor (leer)
public void escribir ( int dato ) {   public int leer () {
  if ( cant == N )                      if ( cant == 0)
    wait ( hayEspacio ) ;                 wait ( conDato ) ;

    buffer [ fin ] = dato ;               int aux = buffer [ inicio ];
    fin = ( fin + 1) % N ;                inicio = ( inicio + 1) % N ;
    cant ++;                              cant - -;

    signal ( conDato ) ;                  signal ( hayEspacio ) ;
    return ;                              return aux ;
}                                     }
```

## Página 67

[Ver página original](teorica-03-monitores.pdf#page=67)

```text
Buffer de Capacidad N (Signal y Continúa)

 Estructura del Monitor:
   • Mantiene la misma lógica de arreglo circular y contador explícito (cant).
   • Diferencia clave: Al utilizar la política de Mesa, es obligatorio proteger las
     esperas con un bucle while para reevaluar la condición al despertar.

   monitor Buffer {
     private condition hayEspacio ;
     private condition conDato ;
     private int buffer [ N ];
     private int inicio = 0 , fin = 0 , cant = 0;

      % ... las operaciones siguen en la proxima
       diapositiva
```

## Página 68

[Ver página original](teorica-03-monitores.pdf#page=68)

```text
Operaciones del Buffer Circular (Mesa)


          Productor (escribir)                    Consumidor (leer)
public void escribir ( int dato ) {      public int leer () {
  while ( cant == N )                      while ( cant == 0)
    wait ( hayEspacio ) ;                    wait ( conDato ) ;

    buffer [ fin ] = dato ;                  int aux = buffer [ inicio ];
    fin = ( fin + 1) % N ;                   inicio = ( inicio + 1) % N ;
    cant ++;                                 cant - -;

    signal ( conDato ) ;                     signal ( hayEspacio ) ;
    return ;                                 return aux ;
}                                        }
```

## Página 69

[Ver página original](teorica-03-monitores.pdf#page=69)

```text
Buffer N: Optimización de Señalización

         Productor Optimizado                  Consumidor Optimizado
public void escribir ( int dato ) {      public int leer () {
  while ( cant == N )                      while ( cant == 0)
    wait ( hayEspacio ) ;                    wait ( conDato ) ;

    buffer [ fin ] = dato ;                  int aux = buffer [ inicio ];
    fin = ( fin + 1) % N ;                   inicio = ( inicio + 1) % N ;
    cant ++;                                 cant - -;

    if ( cant == 1)                          if ( cant == N - 1)
      signal ( conDato ) ;                     signal ( hayEspacio ) ;

    return ;                                 return aux ;
}                                        }
```

## Página 70

[Ver página original](teorica-03-monitores.pdf#page=70)

```text
¡Error: Lost Wake-up!

   • La intención de la optimización: Evitar ejecutar signal() de forma
     innecesaria en cada iteración, enviándolo únicamente cuando el buffer pasa
     de estar vacío a tener un elemento (cant == 1).
   • Por qué se rompe en la Semántica de Mesa:
       1. Supongamos que el buffer está lleno (cant = N) y hay dos consumidores
          esperando en la cola conDato.
       2. Un productor inserta un elemento, el contador no es 1, por lo que no hace
          signal.
       3. Debido al entrelazado y a que la notificación condicional se "perdió", un hilo
          que tenía derecho a despertarse se queda suspendido indefinidamente.

   • Conclusión: En la política de Mesa (Signal & Continua), condicionar el
     signal a un valor puntual suele provocar que los hilos se queden dormidos
     para siempre. Se debe señalizar siempre.
```

## Página 71

[Ver página original](teorica-03-monitores.pdf#page=71)

```text
Filósofos Comensales con Monitores



   • El monitor encapsula hace atómica la operación de tomar dos tenedores:
   monitor DiningPhilosophers {
     // forks [ i ] cuenta tenedores
     // libres para el filosofo i
     private int forks [5] = {2 ,2 ,2 ,2 ,2};
     private condition okToEat [5];

       // ... sigue en la proxima slide
   }
```

## Página 72

[Ver página original](teorica-03-monitores.pdf#page=72)

```text
Operaciones del Monitor de los Filósofos

        Entrada (takeForks)                  Salida (releaseForks)
 public void takeForks ( int i )    public void releaseForks ( int i
     {                                 ) {
   while ( forks [ i ] < 2)           forks [ left ( i ) ]++;
     wait ( okToEat [ i ]) ;          forks [ right ( i ) ]++;

    // Decrementa los                   // Si un vecino ahora tiene
     tenedores                           2
    // disponibles de sus               // tenedores , lo despierta
     vecinos                            if ( forks [ left ( i ) ] == 2)
    forks [ left ( i ) ] - -;              signal ( okToEat [ left ( i ) ]) ;
    forks [ right ( i ) ] - -;          if ( forks [ right ( i ) ] == 2)
}                                          signal ( okToEat [ right ( i ) ]) ;
                                    }
```

## Página 73

[Ver página original](teorica-03-monitores.pdf#page=73)

```text
Problema de los Lectores / Escritores


 Estructura del Monitor (Semántica de Mesa):
   • writer: Booleano que indica si hay un escritor activo.
   • readers#: Contador de lectores que se encuentran leyendo.
   • cond: Condición única donde esperan tanto lectores como escritores.
   monitor RW {
     private condition cond ;
     private boolean writer = false ;
     private int readers = 0;

     //   las operaciones siguen en la proxima diapositiva
```

## Página 74

[Ver página original](teorica-03-monitores.pdf#page=74)

```text
Operaciones del Monitor RW

       Gestión de Lectores         Gestión de Escritores
 public void startRead () {   public void startWrite () {
   while ( writer )             while ( writer || readers >
     wait ( cond ) ;              0)
   readers ++;                    wait ( cond ) ;
 }                              writer = true ;
                              }
 public void endRead () {
   readers - -;               public void endWrite () {
   if ( readers == 0)           writer = false ;
     signalAll ( cond ) ;       signalAll ( cond ) ;
 }                            }
```

## Página 75

[Ver página original](teorica-03-monitores.pdf#page=75)

```text
Operaciones del Monitor RW

        Gestión de Lectores                        Gestión de Escritores
 public void startRead () {                 public void startWrite () {
   while ( writer )                           while ( writer || readers >
     wait ( cond ) ;                            0)
   readers ++;                                  wait ( cond ) ;
 }                                            writer = true ;
                                            }
 public void endRead () {
   readers - -;                             public void endWrite () {
   if ( readers == 0)                         writer = false ;
     signalAll ( cond ) ;                     signalAll ( cond ) ;
 }                                          }

   • ¡Peligro de Lost Wake-up! Si se reemplaza signalAll por un signal , una
     notificación para un escritor podría despertar a un lector (o viceversa).
```

## Página 76

[Ver página original](teorica-03-monitores.pdf#page=76)

```text
Lectores/Escritores: Condición Dividida


 Estructura del Monitor (Semántica de Mesa):
   • Separar las colas de espera permite despertar selectivamente a lectores o
     escritores, evitando el uso ineficiente de signalAll.

   monitor RW {
     private condition readOk ;
     private condition writeOk ;
     private boolean writer = false ;
     private int readers = 0;

     // ... las operaciones siguen en las proximas diapositivas
```

## Página 77

[Ver página original](teorica-03-monitores.pdf#page=77)

```text
Operaciones con Colas Separadas

      Operaciones de Lectura                     Operaciones de Escritura
 public void startRead () {                 public void startWrite () {
   while ( writer )                           while ( writer || readers >
     wait ( readOk ) ;                          0)
   readers ++;                                  wait ( writeOk ) ;
   signal ( readOk ) ; // Cascada             writer = true ;
 }                                          }

 public void endRead () {                   public void endWrite () {
   readers - -;                               writer = false ;
   if ( readers == 0)                         signal ( writeOk ) ;
     signal ( writeOk ) ;                     signal ( readOk ) ;
 }                                          }

 Nota: El signal(readOk) en startRead genera un efecto dominó que despierta a
 todos los lectores acumulados en cadena.
```

## Página 78

[Ver página original](teorica-03-monitores.pdf#page=78)

```text
Escritores: Control de Prioridad a Lectores

Finalización del Escritor:
                                      public void startWrite () {
  • El escritor que abandona el         while ( writer || readers > 0)
    monitor evalúa el estado de las       wait ( writeOk ) ;
    colas (empty).                      writer = true ;
                                      }
  • Si hay lectores esperando en
    readOk, se los despierta para     public void endWrite () {
    garantizar la Prioridad de          writer = false ;
    Lectores.                           if ( empty ( readOk ) ) {
                                           signal ( writeOk ) ;
  • Si no hay lector esperando, se      } else {
    emite signal en la cola de             signal ( readOk ) ; // Prioridad
                                          Lectores
    escritores writeOk.
                                        }
                                      }
```

## Página 79

[Ver página original](teorica-03-monitores.pdf#page=79)

```text
Comparación: Semáforos vs. Variables de condición

                 Semáforo                           Variable de Condición
    wait puede o no bloquear al proceso      wait siempre bloquea al proceso en
    (depende del valor).                     ejecución.
    signal siempre tiene un efecto (incre-   signal no tiene efecto si la cola de
    menta el valor o despierta a alguien).   la condición está vacía.
    signal desbloquea a un proceso arbi-     signal desbloquea estrictamente al
    trario de la cola.                       proceso que está a la cabeza de la co-
                                             la (FIFO).
    Un proceso desbloqueado por signal       Un proceso desbloqueado signal debe
    puede reanudar su ejecución de forma     esperar a que el proceso señalizador li-
    inmediata.                               bere o deje el monitor.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 79](teorica-03-monitores.pdf#page=79). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 80

[Ver página original](teorica-03-monitores.pdf#page=80)

```text
Monitores en Java: Opción 1


   • Estructura básica: Una clase común y corriente cuyos métodos se declaran
     explícitamente como synchronized.

   • Gestión de condiciones:
       • Posee una única condición anónima (no declarada de forma explícita).
       • Está asociada intrínsecamente al propio objeto que actúa como lock.

   • Primitivas de sincronización integradas:
       • wait(): Suspende al hilo actual liberando temporalmente el lock.
       • notify(): Despierta a un único hilo al azar de la cola de condición.
       • notifyAll(): Despierta a todos los hilos bloqueados en la condición.
```

## Página 81

[Ver página original](teorica-03-monitores.pdf#page=81)

```text
Ejemplo de Monitor Simple en Java (Opción 1)

Características del diseño
                                    public class SimpleMonitor {
nativo:
  • synchronized garantiza la           public synchronized void A ()
    exclusión mutua al ingresar a           throws InterruptedException {
    los métodos de la instancia.          // ... operaciones ...
                                          wait () ; // Bloquea el hilo
  • wait(), notify() y                    // ... continua ...
    notifyAll() se invocan              }
    directamente (llamada this).
  • Cuenta con una única cola de        public synchronized void B () {
                                          // ... cambia estado ...
    condición implícita por cada
                                          notifyAll () ; // Despierta hilos
    objeto.                             }
                                    }
```

## Página 82

[Ver página original](teorica-03-monitores.pdf#page=82)

```text
Monitores en Java: Opción 2


   • Mecanismo de exclusión: Uso de unlock explícito mediante una instancia
     de la clase ReentrantLock.
       • Implementa la interfaz estándar Lock.
       • Se controla el acceso manualmente mediante lock() y unlock().

   • Gestión de condiciones explícitas:
       • Permite declarar múltiples variables de condición independientes invocando
         el método newCondition().
       • Ofrece un control fino de sincronización con sus propias primitivas: await(),
         signal() y signalAll().
```

## Página 83

[Ver página original](teorica-03-monitores.pdf#page=83)

```text
Ejemplo de Monitor con Locks Explícitos (Opción 2)

Ventajas del control explícito:
                                      public class SimpleMonitor {
  • ReentrantLock sustituye             private final Lock lock = new
    synchronized.                        ReentrantLock () ;
  • Se deben invocar lock()             private Condition c = lock .
                                         newCondition () ;
    unlock() (unlock dentro de un
    bloque finally para asegurar la    public void A () throws
    liberación del lock).                InterruptedException {
  • Permite declarar múltiples            lock . lock () ;
    variables de condición                try {
                                            // ... operaciones ...
    independientes
                                            c . await () ; // Espera explicita
    (lock.newCondition()).                } finally {
                                            lock . unlock () ;
                                          }
                                       }
```

## Página 84

[Ver página original](teorica-03-monitores.pdf#page=84)

```text
Lectores/Escritores: Solución Alternativa en Java

 Estructura del Monitor:
   • readers: Lectores leyendo.
   • Condición de vaciado: No quedan lectores activos cuando readIn ==
     readOut.


   public class RW {
       private final Lock lock = new ReentrantLock () ;
       private final Condition condition = lock . newCondition () ;
       private int readers = 0;
       private boolean writer = false ;

       // ... las operaciones siguen en las proximas
      diapositivas
```

## Página 85

[Ver página original](teorica-03-monitores.pdf#page=85)

```text
RW Alternativo: Operaciones de Lectura


          Entrada (startRead)                          Salida (endRead)
 public void startRead ()                      public void endRead () {
      throws                                     lock . lock () ;
     InterruptedException {     try {
   lock . lock () ;                                readers - -;
   try {                                           if ( readers == 0)
      while ( writer )                                condition . signalAll () ;
        condition . await () ;                   } finally {
      readers ++;                                  lock . unlock () ;
   } finally {                                   }
      lock . unlock () ;                       }
   }
 }
```

## Página 86

[Ver página original](teorica-03-monitores.pdf#page=86)

```text
RW Alternativo: Operaciones de Escritura

          Entrada (startWrite)                              Salida (endWrite)
public void startWrite ()                            public void endWrite () {
    throws InterruptedException     lock . lock () ;
    {                                                  try {
  lock . lock () ;                                       writer = false ;
  try {                                                  condition . signalAll () ;
    while ( writer )                                   } finally {
       condition . await () ;                            lock . unlock () ;
    writer = true ;                                    }
                                                     }
      while ( readers != 0)
        condition . await () ;
    } finally {
      lock . unlock () ;
    }
}
```
