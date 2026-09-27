# teorica-04-conjuntos-concurrentes-con-y-sin-locks — transcripción

- Fuente: [teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf)
- Páginas del PDF: 131.
- SHA-256 del PDF: `0ea86a4f9d80babb4053298c4cd833795828c4b3d38bdb15e56823836ec4f1a3`.
- Tipo: transcripción textual por página, con disposición espacial conservada y referencias al original para esquemas.

> Se conserva el contenido del original, incluidos ejemplos incorrectos, erratas y diapositivas progresivas. Los bloques `text` preservan columnas y sangría; no son código listo para compilar. En código repartido en columnas, seguir las indicaciones «continúa a la derecha». Las notas editoriales se distinguen del original. Para símbolos cuya extracción sea ambigua, consultar la página enlazada.

## Página 1

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=1)

```text
Conjuntos concurrentes:
con y sin locks
```

## Página 2

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=2)

```text
Objetos Concurrentes

  Comportamiento de objetos concurrentes
  El comportamiento de los objetos concurrentes se describe mediante:
```

## Página 3

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=3)

```text
Objetos Concurrentes

  Comportamiento de objetos concurrentes
  El comportamiento de los objetos concurrentes se describe mediante:


   • Correctitud (Safety): Algún tipo de equivalencia con el
     comportamiento secuencial.
        • Consistencia Secuencial Condición fuerte.
        • Linealizabilidad (Linearizability) Aún más fuerte. Soporta composición.
        • Consistencia en Reposo (Quiescent) Consistencia débil.
```

## Página 4

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=4)

```text
Objetos Concurrentes

  Comportamiento de objetos concurrentes
  El comportamiento de los objetos concurrentes se describe mediante:


   • Progreso (Liveness)
        • Bloqueante (blocking) La demora de un proceso puede impedir que los
          demás progresen.
        • No bloqueante (nonblocking) La demora de un proceso no puede demorar
          a los demás indefinidamente.
```

## Página 5

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=5)

```text
Objetos Secuenciales

  Objeto como TDA
  Un contenedor de datos + un conjunto de operaciones (métodos), que proveen
  la única vía para manipular los datos.

   • Clase o definición de tipo: Cada objeto tiene una clase que define:
       • estado
       • comportamiento (operaciones)
```

## Página 6

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=6)

```text
Especificaciones Secuenciales

  Especificación de operaciones
    • Precondición: Describe los estados en que puede invocarse una
      operación.
    • Postcondición: Describe el valor de retorno de la operación y el estado
      del objeto después de ejecutar la operación.
```

## Página 7

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=7)

```text
Ejemplo: Especificación secuencial de cola FIFO

  Estados de una cola
  Una secuencia de elementos, posiblemente vacía.

   • Método enq(z):
        • Precondición: True.
        • Postcondición: El estado de la cola pasa a q · z (con q el estado al inicio).
   • Método deq():
        • Precondición: True.
        • Postcondición:
             • Si q vacía, lanza EmptyException y no cambia estado.
             • Si q = a · q ′ , retorna a y cambia estado a q ′ .
```

## Página 8

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=8)

```text
Especificación Secuencial

  En el modelo secuencial
  Un único proceso manipula a los objetos.
    • La especificación describe el estado antes y después de cada operación.
    • se ignoran los estados intermedios durante la ejecución del método.
    • Los estados relevantes son entre llamadas a métodos.
```

## Página 9

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=9)

```text
Especificación Secuencial

  En el modelo secuencial
  Un único proceso manipula a los objetos.
    • La especificación describe el estado antes y después de cada operación.
    • se ignoran los estados intermedios durante la ejecución del método.
    • Los estados relevantes son entre llamadas a métodos.

  Falla en Concurrencia
    • Las ejecuciones de operaciones se pueden superponer en un mismo
      instante de tiempo.
    • Una llamada debe estar preparada para encontrar un estado del objeto que
      refleje efectos incompletos de otras ejecuciones.
```

## Página 10

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=10)

```text
Consistencia Secuencial

   • Las llamadas toman tiempo: Una llamada a un método es el intervalo
     de tiempo que comienza con el evento de invocación y concluye con el
     evento de retorno.
   • Las llamadas hechas por un proceso son siempre secuenciales (no
     superpuestas).
   • Las llamadas hechas por procesos concurrentes pueden superponerse.
   • Una llamada está pendiente si su evento de invocación ocurrió, pero el
     correspondiente evento de retorno no sucedió.
```

## Página 11

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=11)

```text
Consistencia Secuencial: Intuición

  Orden secuencial
  La llamadas deben aparentar una ejecución secuencial, una a la vez.

                          r.write(7)
  Thread A
                              r.write(-3)         r.read(?)
  Thread B
   • Se escribe concurrentemente un registro con los valores −3 y 7.
        • El orden de las operaciones no está definido.
   • Luego se lee el registro r . ¿Cuál es el resultado?
```

**Información gráfica:** [consultar el diagrama o tabla de la página 11](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=11). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 12

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=12)

```text
Consistencia Secuencial: Intuición

  Orden secuencial
  La llamadas deben aparentar una ejecución secuencial, una a la vez.

                          r.write(7)
  Thread A
                              r.write(-3)         r.read(?)
  Thread B
   • Se escribe concurrentemente un registro con los valores −3 y 7.
        • El orden de las operaciones no está definido.
   • Luego se lee el registro r . ¿Cuál es el resultado?
        • −3 y 7 son aceptables.
        • −7 no es aceptable (mezcla los efectos de las dos operaciones)
```

**Información gráfica:** [consultar el diagrama o tabla de la página 12](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=12). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 13

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=13)

```text
Consistencia Secuencial: Intuición

  Orden del Programa (Program Order) PO
  Es el orden en el que un proceso emite sus llamadas a métodos.

      r.write(7)    r.write(-3)      r.read(?)

   • Un único proceso escribe primero 7 y luego −3 y más tarde lee r .
        • Si no hay otros procesos, uno espera leer −3.
        • La respuesta 7 es inaceptable (en muchos casos)
```

**Información gráfica:** [consultar el diagrama o tabla de la página 13](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=13). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 14

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=14)

```text
Consistencia Secuencial

  Consistencia Secuencial
  Todas las llamadas a métodos actúan como si hubieran ocurrido en un orden
  secuencial consistente con el orden del programa.

   • Debe existir una forma de ordenar todas las llamadas de cualquier ejecución
     concurrente de modo que cumplan dos condiciones simultáneas:
     (1) Sean consistentes con el program order de cada hilo individual.
     (2) Cumplan estrictamente con la especificación secuencial del objeto
         compartido.
```

## Página 15

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=15)

```text
Ejemplo: Cola FIFO Concurrente

                               enq(x)                deq(y)

           Thread A
                                   enq(y)        deq(x)

           Thread B
   • Dos órdenes secuenciales explican este resultado:
       • Opción 1: A.enq(x) → B.enq(y) → B.deq(x) → A.deq(y)
       • Opción 2: B.enq(y) → A.enq(x) → A.deq(y) → B.deq(x)
```

**Información gráfica:** [consultar el diagrama o tabla de la página 15](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=15). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 16

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=16)

```text
Ejemplo: Cola FIFO Concurrente

                               enq(x)                deq(y)

           Thread A
                                   enq(y)        deq(x)

           Thread B
   • Dos órdenes secuenciales explican este resultado:
```

**Información gráfica:** [consultar el diagrama o tabla de la página 16](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=16). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 17

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=17)

```text
Ejemplo: Cola FIFO Concurrente

                               enq(x)                deq(y)

           Thread A
                                   enq(y)        deq(x)

           Thread B
   • Dos órdenes secuenciales explican este resultado:
       • Opción 1: A.enq(x) → B.enq(y) → B.deq(x) → A.deq(y)
       • Opción 2: B.enq(y) → A.enq(x) → A.deq(y) → B.deq(x)
```

**Información gráfica:** [consultar el diagrama o tabla de la página 17](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=17). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 18

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=18)

```text
Consistencia Secuencial vs. Tiempo Real

                                 enq(x)            deq(y)

             Thread A
                                          enq(y)

             Thread B

   • Satisface consistencia secuencial?
```

**Información gráfica:** [consultar el diagrama o tabla de la página 18](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=18). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 19

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=19)

```text
Consistencia Secuencial vs. Tiempo Real

                               enq(x)                        deq(y)

            Thread A
                                              enq(y)

            Thread B

   • Satisface consistencia secuencial? Si.
       • Basta ordernar la operación de B antes de las de A.
       • Notar que encolar x ocurrió antes de que comenzara encolar y , pero y se
          desencola primero.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 19](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=19). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 20

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=20)

```text
Consistencia Secuencial vs. Tiempo Real

                               enq(x)                        deq(y)

            Thread A
                                              enq(y)

            Thread B

   • Satisface consistencia secuencial? Si.
       • Basta ordernar la operación de B antes de las de A.
       • Notar que encolar x ocurrió antes de que comenzara encolar y , pero y se
          desencola primero.

  ¿Por qué esto es legal?
  enq(x) y enq(y) ocurren en procesos distintos, no tienen relación de pro-
  gram order.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 20](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=20). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 21

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=21)

```text
Propiedades de Correctitud: Composicionalidad


  ¿Qué es la Composicionalidad?
  Una propiedad de correctitud P es composicional si, cuando cada objeto del
  sistema satisface P de manera individual, el sistema también satisface P.


   • ¿Por qué es importante?
     Permite ensamblar fácilmente un sistema complejo a partir de componentes
     desarrollados de manera independiente.
   • ¿Es la consistencia secuencial composicional?
```

## Página 22

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=22)

```text
Propiedades de Correctitud: Composicionalidad


  ¿Qué es la Composicionalidad?
  Una propiedad de correctitud P es composicional si, cuando cada objeto del
  sistema satisface P de manera individual, el sistema también satisface P.


   • ¿Por qué es importante?
     Permite ensamblar fácilmente un sistema complejo a partir de componentes
     desarrollados de manera independiente.
   • ¿Es la consistencia secuencial composicional? Lamentablemente, NO.
```

## Página 23

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=23)

```text
Consistencia Secuencial NO es Composicional

                       p.enq(x)              q.enq(x)              p.deq(y)

              A
                                  q.enq(y)              p.enq(y)              q.deq(x)

              B


 Cada cola (p y q) es secuencialmente consistente, pero el sistema no
  1. Como A desencola y de p: ⟨p.enq(y ) B⟩ → ⟨p.enq(x ) A⟩
  2. Simétricamente, como B desencola x de q : ⟨q.enq(x ) A⟩ → ⟨q.enq(y ) B⟩
  3. Por el Program Order (PO) de cada hilo establece que:
     ⟨p.enq(x ) A⟩ → ⟨q.enq(x ) A⟩ y ⟨q.enq(y ) B⟩ → ⟨p.enq(y ) B⟩
  4. Contradicción: Se forma un ciclo:
     ⟨p.enq(x ) A⟩ → ⟨q.enq(x ) A⟩ → ⟨q.enq(y ) B⟩ → ⟨p.enq(y ) B⟩ →
     ⟨p.enq(x ) A⟩.
```

**Descripción editorial del esquema:** Dos líneas de tiempo representan los threads A y B; p y q son colas distintas. Las restricciones FIFO y el orden de cada thread forman el ciclo escrito debajo del gráfico; por eso la consistencia secuencial por objeto no implica consistencia secuencial del sistema.

**Información gráfica:** [consultar el diagrama o tabla de la página 23](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=23). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 24

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=24)

```text
Linearizabilidad (Linearizability)

  Principio
  Cada llamada a un método debe aparentar tener efecto de manera instantánea
  en algún momento entre su invocación y su respuesta.

   • Preservación del Tiempo Real: Este principio establece que el orden en
     tiempo real (real-time order) debe ser preservado.
   • Relación de Inclusión: Toda ejecución linealizable es secuencialmente
     consistente, pero no viceversa.
```

## Página 25

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=25)

```text
Puntos de Linealización

  Puntos de Linealización
  Un instante específico donde los efectos de la operación ocurren.

   • Implementaciones basadas en Locks: Cualquier punto dentro de la
     sección crítica puede servir como su punto de linealización.
   • Implementaciones sin Locks (Lock-free):
     El punto de linealización es típicamente una instrucción (e.g., un CAS)
     donde los efectos se vuelven visibles para todo el sistema.
```

## Página 26

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=26)

```text
Progreso

 Progreso
 Es una propiedad de liveness que exige a las operaciones producir respuestas
 a las invocaciones pendientes.

  • Idealmente queremos que toda invocación pendiente obtenga una respuesta.
  • Esto no es posible si los procesos que contienen las invocaciones pendientes
    dejan de ejecutar (por ejemplo, se mueren).
  • Suposición: Se exige progreso para los procesos que se mantienen
    ejecutando de manera activa.
```

## Página 27

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=27)

```text
Condiciones de Progreso Bloqueantes
  Dependencia de fairness
  Asumimos que el scheduler es fair.

  Libre de Inanición (Starvation-free)
  Un método esta libre de inanición si toda llamada finaliza en una cantidad
  finita de pasos (si los procesos con llamadas pendientes continúen).
     • Garantiza un progreso individual, condicionada a la ejecución de los demás.

  Libre de Deadlock
  Un método es libre de deadlock si al menos alguna llamada finaliza en una
  cantidad finita de pasos (si los procesos con llamadas pendientes ejecutan).
    • Garantiza únicamente un progreso mínimo global para todo el sistema.
```

## Página 28

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=28)

```text
Condiciones de Progreso No Bloqueantes: Wait-freedom

  Condición No Bloqueante
  Un retraso arbitrario por parte de un proceso no puede impedir que los demás
  procesos sigan progresando de manera independiente.

  Método Wait-free
  Un método es wait-free si cualquier llamada pendiente puede completar en una
  cantidad finita de pasos, independientemente de las ejecuciones de los
  demás.

  Implementación de un objeto es Wait-free
  La implementación de Un objeto es wait-free si todos sus métodos son wait-free.
```

## Página 29

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=29)

```text
Condiciones de Progreso: Lock-freedom


  Lock-freedom
  Un método es lock-free si su ejecución garantiza que al menos una llamada
  pendiente finalice en una cantidad finita de pasos.

   • Riesgo de Inanición (Starvation):
     Admite la posibilidad de que algunos procesos sufran inanición.
   • Toda implementación wait-free es lock-free (no viceversa)
```

## Página 30

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=30)

```text
Conjuntos




  Interfaz Set

   public interface Set {
       public boolean add ( Object o ) ;
       public boolean remove ( Object o ) ;
       public boolean contains ( Object o ) ;
   }
```

## Página 31

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=31)

```text
Implementación como lista simplemente enlazada

Representación

  public class Node {
    public Object item ;
    public int key ;
    public Node next ;
    public Node ( int key ) {...}
  }

  public class List {
    Node head ;
  }
```

## Página 32

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=32)

```text
Implementación como lista simplemente enlazada

Representación                      Invariante
                                      • key es el hash-code del elemento item
  public class Node {
    public Object item ;              • No existen claves duplicadas
    public int key ;
    public Node next ;                • Nodos ordenados ascendentemente
    public Node ( int key ) {...}
  }
                                        por key
                                      • Dos nodos centinelas.
  public class List {
    Node head ;                           • Marcan el inicio y el fin de la lista.
  }                                       • No contienen elementos.
```

## Página 33

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=33)

```text
Implementación como lista simplemente enlazada

Representación                          Invariante
                                           • key es el hash-code del elemento item
  public class Node {
    public Object item ;                   • No existen claves duplicadas
    public int key ;
    public Node next ;                     • Nodos ordenados ascendentemente
    public Node ( int key ) {...}
  }
                                             por key
                                           • Dos nodos centinelas.
  public class List {
    Node head ;                                 • Marcan el inicio y el fin de la lista.
  }                                             • No contienen elementos.


  Función de abstracción
  Un elemento pertenece al conjunto si y solo si está en un nodo (distinto de head
  y tail) alcanzable desde head
```

## Página 34

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=34)

```text
Implementación como lista simplemente enlazada




            Constructor

              public class List {
                Node head ;
                public List () {
                  head = new Node ( Integer . MIN_VALUE ) ;
                  head . next = new Node ( Integer . MAX_VALUE ) ;
                }
              }
```

## Página 35

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=35)

```text
Sincronización de granularidad gruesa

                                   Método add
• Un lock para toda la
  estructura (e.g., un monitor).    public synchronized add ( Object o ) {
                                      Node pred , curr ;
• Simple y correcto.                  int key = o . hashCode () ;
                                      pred = head ;
• No funciona bien con                curr = pred . next ;
  contención.                         while ( curr . key < key ) {
                                        pred = curr ;
                                        curr = curr . next ;
                                      }
                                      if ( key == curr . key ) return false ;
                                      else {
                                        Node node = new Node ( o ) ;
                                        node . next = curr ;
                                        pred . next = node ;
                                        return true ;
                                      }
                                    }
```

## Página 36

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=36)

```text
Sincronización de granularidad gruesa

                                   Método remove
• Un lock para toda la
  estructura (e.g., un monitor).    public synchronized remove ( Object o ) {
                                      Node pred , curr ;
• Simple y correcto.                  int key = o . hashCode () ;
                                      pred = head ;
• No funciona bien con                curr = pred . next ;
  contención.                         while ( curr . key <= key ) {
                                        if ( o == curr . item ) {
                                          pred . next = curr . next ;
                                          return true ;
                                        }
                                        pred = curr ;
                                        curr = curr . next ;
                                      }
                                      return false ;
                                    }
```

## Página 37

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=37)

```text
Granularidad gruesa: Análisis
  Correctitud : Linearizabilidad
  Garantizar que la estructura es linealizable.

   • Punto de Linealizabilidad: Un paso atómico (lectura, escritura o CAS) donde la
     operación “tiene efecto”.
   • Función de Abstracción: Al aplicar la función de abstracción en un punto de
     linealización, el resultado debe definir una ejecución secuencial válida de un
     conjunto (add, remove, contains).
```

## Página 38

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=38)

```text
Granularidad gruesa: Análisis
  Correctitud : Linearizabilidad
  Garantizar que la estructura es linealizable.

   • Punto de Linealizabilidad: Un paso atómico (lectura, escritura o CAS) donde la
     operación “tiene efecto”.
   • Función de Abstracción: Al aplicar la función de abstracción en un punto de
     linealización, el resultado debe definir una ejecución secuencial válida de un
     conjunto (add, remove, contains).
  Puntos de Linearización
    • add(a) o remove(a) Exitosos: Se linearizan cuando actualizan el atributo
      next del nodo predecesor.
    • add(a) / remove(a) Inexitosos y contains(a): Se pueden linealizar en el
      momento en que adquieren el lock (o en cualquier instante mientras se retenga).
```

## Página 39

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=39)

```text
Granularidad gruesa: Progreso
  Garantías de Progreso
  Diferentes algoritmos de listas imponen diferentes compromisos:
    • Algoritmos Bloqueantes (Con Locks):
      Requieren analizar la ausencia de deadlocks (deadlock-free) e inanición
      (starvation-free).
    • Algoritmos No Bloqueantes (Nonblocking):
         • Wait-free: Cada llamada pendiente se completa de forma individual en
           una cantidad finita de pasos.
         • Lock-free: Alguna llamada pendiente siempre finaliza, garantizando el
           progreso global del sistema.
```

## Página 40

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=40)

```text
Granularidad gruesa: Progreso
  Garantías de Progreso
  Diferentes algoritmos de listas imponen diferentes compromisos:
    • Algoritmos Bloqueantes (Con Locks):
      Requieren analizar la ausencia de deadlocks (deadlock-free) e inanición
      (starvation-free).
    • Algoritmos No Bloqueantes (Nonblocking):
         • Wait-free: Cada llamada pendiente se completa de forma individual en
           una cantidad finita de pasos.
         • Lock-free: Alguna llamada pendiente siempre finaliza, garantizando el
           progreso global del sistema.


  Garantía de Progreso
  Es libre de inanición si su lock lo es.
```

## Página 41

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=41)

```text
Sincronización de granularidad fina




   • Dividir una estructura en partes, donde cada parte tiene su propio lock.
   • Operaciones que actúan sobre distintas partes no necesitan ejecutar en
     exclusión mutua.
   • Los algoritmos se tienen que pensar cuidadosamente.
```

## Página 42

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=42)

```text
Sincronización de granularidad fina




Clase Node
                                • Se agrega unlock en cada nodo con los
  public class Node {             métodos correspondientes.
    public Object item ;
    public int key ;
                                • En Java se heredan de Object
    public Node next ;                • Cada objeto tiene unlock reentrante
      public lock();                    incorporado (lock de monitor)
      public unlock ();
  }
```

## Página 43

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=43)

```text
Granularidad fina: Eliminación de un nodo




                            a       b   c   d




   • Queremos eliminar al nodo b.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 43](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=43). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 44

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=44)

```text
Granularidad fina: Eliminación de un nodo




                               pred      curr



                                a            b   c   d




   • Se recorre la lista hasta llegar a b.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 44](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=44). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 45

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=45)

```text
Granularidad fina: Eliminación de un nodo




                              pred     curr



                               a        b        c        d




   • Se debe modificar el puntero next del predecesor a para hacerlo apuntar a c.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 45](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=45). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 46

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=46)

```text
Granularidad fina: Eliminación de un nodo




                             pred     curr



                              a        b       c        d




   • Regla: Se adquiere el lock del nodo apuntado por pred antes de modificar.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 46](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=46). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 47

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=47)

```text
Granularidad fina: Eliminación de un nodo




                             pred   curr



                              a      b     c   d




   • Se modifica la lista.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 47](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=47). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 48

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=48)

```text
Granularidad fina: Eliminación de un nodo




                          pred   curr



                           a      b     c   d




   • Se libera el lock.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 48](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=48). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 49

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=49)

```text
Granularidad fina: Eliminación de un nodo




                             a                  c   d




   • Se libera la memoria del nodo eliminado.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 49](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=49). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 50

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=50)

```text
Dos eliminaciones concurrentes




                             a        b        c           d




   • El Thread 1 quiere borrar b (bloquea a con pred1 ).
   • El Thread 2 quiere borrar c (bloquea b con pred2 ).
```

**Información gráfica:** [consultar el diagrama o tabla de la página 50](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=50). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 51

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=51)

```text
Dos eliminaciones concurrentes



                                             pred2
                              pred1     curr1        curr2




                               a         b            c      d




   • Los dos procesos recorren la lista hasta llegar all nodo a eliminar.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 51](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=51). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 52

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=52)

```text
Dos eliminaciones concurrentes



                                           pred2
                             pred1    curr1        curr2




                              a        b            c      d




   • Los dos procesos toma el lock sobre los nodos a modificar.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 52](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=52). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 53

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=53)

```text
Dos eliminaciones concurrentes



                                           pred2
                             pred1    curr1        curr2




                              a        b            c      d




   • Se modifican los punteros para eliminar los nodos.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 53](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=53). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 54

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=54)

```text
Dos eliminaciones concurrentes



                                           pred2
                             pred1    curr1        curr2




                              a        b            c      d




   • Problema: Al borrar b, el puntero de a pasa a apuntar a c.
   • ¡Pero el nodo c fue removido por el Thread 2! Esta eliminación se pierde.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 54](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=54). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 55

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=55)

```text
Dos eliminaciones concurrentes




                              a        b        c        d




   • Problema: Al borrar b, el puntero de a pasa a apuntar a c.
   • ¡Pero el nodo c fue removido por el Thread 2! Esta eliminación se pierde.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 55](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=55). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 56

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=56)

```text
Dos eliminaciones concurrentes




                              a                 c        d




   • Problema: Al borrar b, el puntero de a pasa a apuntar a c.
   • ¡Pero el nodo c fue removido por el Thread 2! Esta eliminación se pierde.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 56](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=56). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 57

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=57)

```text
Solución: Intuición




   • Cuando se tiene el lock sobre un nodo para modificar, no puede eliminarse el
     nodo sucesor.
```

## Página 58

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=58)

```text
Solución: Intuición




   • Cuando se tiene el lock sobre un nodo para modificar, no puede eliminarse el
     nodo sucesor.
   • Adquirir también el lock sobre el nodo que va a ser eliminado.
```

## Página 59

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=59)

```text
Eliminar un nodo (Lock Coupling)




                             a        b      c   d

   • Al eliminar un nodo se deben adquirir
```

**Información gráfica:** [consultar el diagrama o tabla de la página 59](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=59). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 60

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=60)

```text
Eliminar un nodo (Lock Coupling)



                              pred      curr




                               a         b         c         d

   • Al eliminar un nodo se deben adquirir
       • el lock sobre el nodo predecesor
       • el lock sobre el nodo a eliminar (garantiza que el sucesor no puede ser
         eliminado concurrentemente)
```

**Información gráfica:** [consultar el diagrama o tabla de la página 60](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=60). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 61

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=61)

```text
Eliminar un nodo (Lock Coupling)



                              pred      curr




                               a         b         c         d


   • Al eliminar un nodo se deben adquirir
       • el lock sobre el nodo predecesor
       • el lock sobre el nodo a eliminar (esto garantiza que el sucesor no puede ser
         eliminado concurrentemente)
   • Un thread debe adquirir el lock sobre un nodo cuando tiene el lock sobre el
     nodo predecesor (hand-over-hand locking / lock coupling)
```

**Información gráfica:** [consultar el diagrama o tabla de la página 61](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=61). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 62

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=62)

```text
Remove - Versión secuencial


   public boolean remove ( Object o ) {
       Node pred , curr ;
       int key = o . hashCode () ;
       pred = head ;
       curr = pred . next ;
       while ( curr . key <= key ) {
           if ( o == curr . item ) {
                pred . next = curr . next ;
                return true ;
           }
           pred = curr ;
           curr = curr . next ;
       }
       return false ;
   }
```

## Página 63

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=63)

```text
Lock hand-over-hand (Remove)


  public boolean remove ( T item ) {       // ... Viene de la izquierda
    int key = item . hashCode () ;              if ( curr . key == key ) {
    head.lock();                                    pred.next = curr.next;
    Node pred = head ;                              return true ;
    try {                                         }
      Node curr = pred . next ;                   return false ;
      curr.lock();                              } finally {

      try {                                       curr.unlock();
        while ( curr . key < key ) {          }
           pred.unlock();                   } finally {

          pred = curr ;                         pred.unlock();
          curr = curr . next ;              }
           curr.lock();                }

        }
     // Contin ú a a la derecha ...
```

## Página 64

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=64)

```text
Agregar un nodo




                                a             c   d

                                       b



   • Al agregar un nodo se debe adquirir el
```

**Información gráfica:** [consultar el diagrama o tabla de la página 64](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=64). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 65

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=65)

```text
Agregar un nodo




                                 a            c   d

                                         b



   • Al agregar un nodo se debe adquirir el
       • lock sobre el nodo predecesor
```

**Información gráfica:** [consultar el diagrama o tabla de la página 65](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=65). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 66

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=66)

```text
Agregar un nodo




                                 a            c   d

                                         b



   • Al agregar un nodo se debe adquirir el
       • lock sobre el nodo predecesor
       • lock sobre el nodo sucesor
```

**Información gráfica:** [consultar el diagrama o tabla de la página 66](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=66). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 67

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=67)

```text
add - Versión secuencial

   public boolean add ( Object o ) {
       Node pred , curr ;
       int key = o . hashCode () ;
       pred = head ;
       curr = pred . next ;
       while ( curr . key < key ) {
            pred = curr ;
            curr = curr . next ;
       }
       if ( key == curr . key ) return false ;
       else {
            Node node = new Node ( o ) ;
            node . next = curr ;
            pred . next = node ;
            return true ;
       }
   }
```

## Página 68

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=68)

```text
Lock hand-over-hand (add)


  public boolean add ( T item ) {               // ... Viene de la izquierda
    int key = item . hashCode () ;              if ( curr . key == key ) {
    head . lock () ;                              return false ;
    Node pred = head ;                          }
    try {                                       Node node = new Node ( item ) ;
      Node curr = pred . next ;                  node.next = curr;
      curr . lock () ;
      try {                                      pred.next = node;
         while ( curr . key < key ) {
                                                return true ;
            pred.unlock();                    } finally {
            pred = curr ;                       curr . unlock () ;
            curr = curr . next ;              }
                                            } finally {
            curr.lock();                      pred . unlock () ;
          }                                 }
          // Contin ú a a la derecha    }
    ...
```

## Página 69

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=69)

```text
Granularidad Fina: Progreso (ausencia de deadlocks)

 Ausencia de deadlocks
 Para evitar deadlocks, todos los métodos adquieren los locks exactamente
 en el mismo orden.
   • La adquisición comienza en el nodo head.
   • Luego se adquieren siguiendo las referencias next (un orden total).
```

## Página 70

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=70)

```text
Granularidad Fina: Progreso (libre de inanición)

 Inanición
 Es libre de inanición si los locks son fair.

 Demostración
  • Éxito garantizado en la cabeza: Si un thread intenta adquirir head,
    eventualmente lo conseguirá
  • Como no existen deadlocks, todos los locks tomados hacia adelante por
    otros procesos serán eventualmente liberados.
  • Por lo tanto, el thread avanzará y logrará bloquear los nodos pred y curr.
```

## Página 71

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=71)

```text
Granularidad Fina: Correctitud

 Puntos de linearización
   • add(a) y remove(a) Exitosos: cuando actualiza el campo next del nodo
     predecesor para enlazar al nuevo nodo.
   • Cualquier llamada a contains(a), o un add(a) / remove(a) inexitoso, cuando
     adquiere el lock de un nodo cuya clave es mayor o igual a a.
```

## Página 72

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=72)

```text
Características




   • Mejora la solución de granularidad gruesa
        • Muchos threads pueden manipular la estructura concurrentemente
   • Ineficiente: larga cadena de adquisición y liberación de locks
```

## Página 73

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=73)

```text
Sincronización Optimista

 Estrategia
  1. Buscar: Recorrer la lista sin adquirir locks.
  2. Bloquear: Una vez localizados los nodos (pred y curr), adquirir sus locks.
```

## Página 74

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=74)

```text
Remove - Tentativa

  public boolean remove ( Object o ) {
      Node pred , curr ;
      int key = o . hashCode () ;
      pred = head ;
      curr = pred . next ;
      while ( curr . key < key ) {
          pred = curr ;
          curr = curr . next ;
      }
      try {
          pred . lock () ; curr . lock () ;
          if ( curr . item == o ) {
               pred . next = curr . next ;
               return true ;
          } else
               return false ;
      } finally { pred . unlock () ; curr . unlock () ; }
  }
```

## Página 75

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=75)

```text
Add - Tentativa

   public boolean add ( Object o ) {
       Node pred , curr ;
       int key = o . hashCode () ;
       pred = head ;
       curr = pred . next ;
       while ( curr . key < key ) {
           pred = curr ;
           curr = curr . next ;
       }
       try {
           pred . lock () ; curr . lock () ;
           if ( key == curr . key ) return false ;
           else {
                Node node = new Node ( o ) ;
                node . next = curr ;
                pred . next = node ;
                return true ;
           }
       } finally { pred . unlock () ; curr . unlock () ; }
   }
```

## Página 76

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=76)

```text
Eliminar c y agregar b



                        predA               currA

                             predB               currB




                         a                   c           d      e
                                     b



   • Ambos hilos recorren la lista de forma optimista sin adquirir ningún lock.
   • Identifican las posiciones locales para sus respectivas operaciones.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 76](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=76). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 77

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=77)

```text
Eliminar c y agregar b



                       predA              currA

                            predB              currB




                        a                  c           d     e
                                    b



   • El Thread 1 (A) adquiere los locks sobre su predecesor y objetivo.
   • Borra exitosamente el nodo c modificando el puntero para apuntar a d.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 77](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=77). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 78

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=78)

```text
Eliminar c y agregar b



                       predA              currA

                            predB              currB




                        a                  c           d     e
                                    b



   • El Thread 2 (B) adquiere locks sobre a y c (sin saber que ya fueron
     modificados).
   • Enlaza el nuevo nodo b. Resultado: se agregó b pero no se eliminó c.
```

**Descripción editorial del esquema:** El diagrama muestra a -> b -> c -> d -> e. B enlaza b desde a usando una ventana obsoleta a,c; el resultado conserva c, aunque otra operación lo había eliminado. Los candados y pred/curr identifican la ventana tomada por cada thread.

**Información gráfica:** [consultar el diagrama o tabla de la página 78](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=78). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 79

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=79)

```text
Sincronización Optimista

 Estrategia
 Tres pasos ejecutados suponiendo que no habrá colisiones:
  1. Buscar: Recorrer la lista sin adquirir locks.
  2. Bloquear: Una vez localizados los nodos (pred y curr), adquirir sus locks.
  3. Validar: Verificar que los nodos bloqueados son válidos.

 Manejo de Conflictos
 Si hubo cambios (los nodos bloqueados no son válidos), volver a empezar
 liberando los locks y reiniciar.
```

## Página 80

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=80)

```text
Validación

   private boolean validate ( Node pred , Node curr ) {
     Node aux = head ;
     while ( aux . key <= pred . key ) {
       if ( aux == pred ) // accessible
           return   pred.next == curr;   // adyacentes
         aux = aux . next ;
       }
       return false ;
   }
```

## Página 81

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=81)

```text
Lock optimista (remove)

 public boolean remove ( T item ) {       // ... Viene de la izquierda
   int key = item . hashCode () ;                 if ( validate(pred, curr) ) {
   while (true)   {                                  if ( curr . key == key ) {
     Node pred = head ;                                pred . next = curr . next ;
     Node curr = pred . next ;                         return true ;
     // Recorrido sin adquirir                       } else {
   locks                                               return false ;
     while ( curr . key < key ) {                    }
       pred = curr ;                              }
       curr = curr . next ;                     } finally {
     }                                            curr . unlock () ;
     pred . lock () ;                           }
     try {                                    } finally {
       curr . lock () ;                         pred . unlock () ;
       try {                                  }
   // Contin ú a a la derecha ...         }
                                      }
```

## Página 82

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=82)

```text
Correctitud (remove)


   • Si
          • se tienen los locks de los dos nodos a y c
          • si a todavía es accesible desde head
          • si c es el sucesor de a

   • entonces
          • a y c no pueden ser eliminados
          • Se puede borrar y retornar true
```

## Página 83

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=83)

```text
Lock optimista (add)

public boolean add ( T item ) {                   // ... Viene de la izquierda
  int key = item . hashCode () ;                  if ( validate(pred, curr) ) {
  while (true)   {                                   if ( curr . key == key ) {
    Node pred = head ;                                 return false ;
    Node curr = pred . next ;                        } else {
    // Recorrido sin adquirir                          Node node = new Node ( item ) ;
    locks                                                node.next = curr;
    while ( curr . key < key ) {
      pred = curr ;                                      pred.next = node;
      curr = curr . next ;
    }                                                    return true ;
    pred . lock () ;                                 }
    try {                                         }
      curr . lock () ;                          } finally {
      try {                                       curr . unlock () ;
         // Contin ú a a la derecha             }
    ...                                       } finally {
                                                pred . unlock () ;
                                              }
                                          }
                                      }
```

## Página 84

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=84)

```text
Lock optimista (contains)


public boolean contains ( T item ) {               // ... Viene de la izquierda
  int key = item . hashCode () ;                   if ( validate(pred, curr) ) {
   while (true)   {                                   return ( curr . key == key ) ;
    Node pred = head ;                             }
    Node curr = pred . next ;                    } finally {
    // B ú squeda sin locks                        curr . unlock () ;
    while ( curr . key < key ) {                 }
      pred = curr ;                            } finally {
      curr = curr . next ;                       pred . unlock () ;
    }                                          }
    pred . lock () ;                       }
    try {                              }
      curr . lock () ;
      try {
         // Contin ú a a la derecha
    ...
```

## Página 85

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=85)

```text
Características


   • Menos adquisiciones y liberaciones de locks
        • Mayor performance
        • Mayor concurrencia

   • Requiere recorrer la lista dos veces (una vez adicional para la verificación)

   • El método contains necesita de la adquisición de locks
```

## Página 86

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=86)

```text
Sincronización Optimista: Corrrectitud

 Recorrido sin Locks e Interferencia
 Al recorrer sin tomar locks, un proceso puede atravesar nodos eliminados.
   • Ausencia de interferencia: cuando un nodo es desvinculado, next no
      cambia.
   • Garbage collector garantiza que un nodo no es reciclado mientras está
      siendo usado
   • Una secuencia de punteros huérfanos eventualmente redirige a la lista.

 Puntos de Linealización (condicionales)
 Idénticos a los de la granularidad fina, pero considerando validaciones fallidas:
   • Una llamada a un método se linealiza cuando adquiere locks y la
      validación posterior es exitosa.
```

## Página 87

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=87)

```text
Sincronización Optimista: Progreso

 Libre de deadlocks
 Porque los deadlocks se toman en orden creciente.

 Posibilidad de Inanición
 NO es libre de inanición incluso si todos los locks de los nodos son fair.
   • Un proceso puede ser relegado indefinidamente si otros procesos agregan y
     eliminan nodos repetidamente de forma concurrente.

 Evaluación en la práctica
   • El fenómeno de la inanición es infrecuente.
   • Funciona mejor si el costo de recorrer dos veces sin usar locks es
     significativamente menor que el costo de recorrer una sola vez tomando locks.
```

## Página 88

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=88)

```text
Sincronización Lazy

   • Antes de borrar marcar cada nodo como borrado
   • No se necesita recorrer la lista dos veces (para validar)
   • El remove es lazy:
        • Primero marca el nodo a borrar (lo remueve lógicamente)
        • Luego cambia el puntero next

   • Extendemos la representación de los nodos con un campo marked
     (inicialmente false)
```

## Página 89

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=89)

```text
Sincronización Lazy: Implementación

Representación                            Invariante
 public class Node {
   public Object item ;                     • key es el hash-code del elemento item
   public int key ;
   public Node next ;
                                            • No existen claves duplicadas
   public Node ( int key ) {...}            • Nodos ordenados ascendentemente
   public lock () ;
   public unlock () ;                         por key
   public boolean marked = false ;
 }                                          • Dos nodos centinelas.
 public class List {                             • Marcan el inicio y el fin de la lista.
   Node head ;
 }                                               • No contienen elementos.

  Función de abstracción
  Un elemento pertenece al conjunto si y solo si está en un nodo (distinto de head
  y tail) alcanzable desde head y no está marcado.
```

## Página 90

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=90)

```text
Validación


   private boolean validate ( Node pred , Node curr ) {
     return ! pred . marked && ! curr . marked && pred . next == curr ;
   }
```

## Página 91

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=91)

```text
Sincronización Lazy (remove)

public boolean remove ( T item ) {            // ... Viene de la izquierda
  int key = item . hashCode () ;              if ( validate(pred, curr) ) {
  while ( true ) {
    Node pred = head ;                           if ( curr . key == key ) {
    Node curr = head . next ;                      // Remoci ó n l ó gica
    while ( curr . key < key ) {                    curr.marked = true;
      pred = curr ;
      curr = curr . next ;                         // Remoci ó n f í sica
    }                                               pred.next = curr.next;
    pred . lock () ;
                                                   return true ;
    try {
                                                 } else {
      curr . lock () ;
                                                   return false ;
      try {
                                                 }
         // Contin ú a a la derecha
                                              }
    ...
                                            } finally {
                                              curr . unlock () ;
                                            }
                                          } finally {
                                            pred . unlock () ;
                                          }
                                      }
```

## Página 92

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=92)

```text
Sincronización Lazy (add)

public boolean remove ( T item ) {                // ... Viene de la izquierda
  int key = item . hashCode () ;                  if ( validate(pred, curr) ) {
  while ( true ) {
    Node pred = head ;                               if ( curr . key == key ) {
    Node curr = head . next ;                          return false ;
    while ( curr . key < key ) {                     } else {
      pred = curr ;                                    Node node = new Node ( o ) ;
      curr = curr . next ;                             node . next = curr ;
    }                                                  pred . next = node ;
    pred . lock () ;                                   return true ;
    try {                                            }
      curr . lock () ;                            }
      try {                                     } finally {
         // Contin ú a a la derecha               curr . unlock () ;
    ...                                         }
                                              } finally {
                                                pred . unlock () ;
                                              }
                                          }
                                      }
```

## Página 93

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=93)

```text
Sincronización Lazy (contains)


   public boolean contains ( T item ) {
     int key = item . hashCode () ;
     Node curr = head ;
     while ( curr . key < key )
       curr = curr . next ;
     return curr . key == key && ! curr . marked ;
   }
```

## Página 94

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=94)

```text
Sincronización Lazy: Progreso


  Garantías de Progreso en add() y remove()
  Al igual que en el algoritmo optimista, los métodos add() y remove() NO son
  libres de inanición.
     • Los recorridos de un proceso pueden demorarse indefinidamente por
       modificaciones concurrentes continuas.

  contains() es Wait-free
  El método recorre la lista una sola vez, ignorando por completo los locks. Retorna
  true si el nodo buscado está presente y no marcado; de lo contrario, retorna
  false.
```

## Página 95

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=95)

```text
Sincronización Lazy

  Puntos de Linearización de add(), remove() y contains() Exitoso
    • add() y remove() Inexitoso: Mismo criterio que en el algoritmo optimista (en
      el instante en que se adquieren los locks y la validación resulta exitosa).
    • remove() Exitoso: Se lineariza exactamente cuando el nodo es marcado
      lógicamente (en el instante en que se setea el bit marked).
    • contains() Exitoso: Se lineariza en el momento en que se localiza el nodo
      coincidente no marcado.
```

## Página 96

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=96)

```text
Linealización del contains() no existoso


                      predA




         head 0               0                        b 0       tail 0

                                          1   a 1

                                  currA


   • El proceso A busca a, recorre una sección eliminada (lógicamente) de la
     lista.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 96](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=96). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 97

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=97)

```text
Linealización del contains() no existoso


                       predA




         head 0                0                        b 0   tail 0

                                           1   a 1

                                   currA


   • Otro proceso elimina físicamente la parte eliminada.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 97](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=97). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 98

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=98)

```text
Linealización del contains() no existoso


                      predA




         head 0               0                      b 0        tail 0

                                          1   a 1

                                  currA


   • Cuando A retoma, detecta que a no pertenece al conjunto.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 98](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=98). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 99

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=99)

```text
Linealización del contains() no existoso


                      predA




         head 0               0                        b 0      tail 0

                                          1   a 1

                                  currA


   • Podemos linealizar la llamada en el punto en el que A detecta que a está
     marcado y no pertenece al conjunto.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 99](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=99). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 100

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=100)

```text
Linealización del contains() no existoso

                                          a 0
                      predA




         head 0               0                       b 0      tail 0

                                          1     a 1

                                  currA


   • Otro proceso podría llamar a add(a), insertando un nuevo nodo válido con
     clave a en la parte alcanzable.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 100](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=100). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 101

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=101)

```text
Linealización del contains() no existoso

                                          a 0
                      predA




         head 0               0                       b 0     tail 0

                                          1     a 1

                                  currA


   • No podemos linealizar a A cuando encuentra la marca, porque ocurre
     después de la nueva inserción.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 101](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=101). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 102

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=102)

```text
Linealización del contains() no existoso

                                         a 0
                     predA




         head 0              0                       b 0     tail 0

                                         1     a 1

                                 currA


   • El punto es inmediatamente anterior a que el nuevo nodo con clave a sea
     añadido a la lista por el otro hilo concurrentemente.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 102](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=102). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 103

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=103)

```text
Linealización del contains() no existoso

                                          a 0
                      predA




         head 0               0                       b 0      tail 0

                                          1     a 1

                                  currA


   • El punto es inmediatamente anterior a que el nuevo nodo con clave a sea
     añadido a la lista por el otro hilo concurrentemente.
   • El punto de linealización está determinado por el orden global de eventos;
     ocurre de forma dinámica y puede no coincidir con un paso propio del
     proceso.
```

**Descripción editorial del esquema:** El recorrido A conserva una referencia a un nodo a marcado y desconectado, mientras la lista alcanzable incluye un nuevo a sin marcar antes de b. El punto de linealización de la búsqueda fallida se ubica inmediatamente antes de la inserción del nuevo a, como explica el texto.

**Información gráfica:** [consultar el diagrama o tabla de la página 103](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=103). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 104

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=104)

```text
Sincronización Optimista: Características


   • Contains no necesita locks
   • Se evita el doble recorrido de la lista para las operaciones no conflictivas
   • Las operaciones con contención necesitan recorrer dos veces la lista
   • Uso de locks es crítico si un thread no lo devuelve
```

## Página 105

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=105)

```text
Sincronización No Bloqueante


   • Las operaciones con contención necesitan recorrer dos veces la lista
   • Eliminar completamente locks
   • Usar sólo CompareAndSet()
```

## Página 106

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=106)

```text
Sincronización No Bloqueante: Agregar un nodo (naïve)




                     pred            curr



                      a               c      d      e
```

**Información gráfica:** [consultar el diagrama o tabla de la página 106](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=106). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 107

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=107)

```text
Sincronización No Bloqueante: Agregar un nodo (naïve)




                     pred            curr



                      a               c      d      e

                              b
```

**Información gráfica:** [consultar el diagrama o tabla de la página 107](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=107). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 108

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=108)

```text
Sincronización No Bloqueante: Agregar un nodo (naïve)




                     pred

                            CAS

                      a               c      d      e

                                  b
```

**Información gráfica:** [consultar el diagrama o tabla de la página 108](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=108). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 109

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=109)

```text
Sincronización No Bloqueante: Eliminar un nodo (naïve)




                          pred   curr



                           a      c      d      e
```

**Información gráfica:** [consultar el diagrama o tabla de la página 109](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=109). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 110

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=110)

```text
Sincronización No Bloqueante: Eliminar un nodo (naïve)




                          pred         curr



                           a            c     d   e
                                 CAS
```

**Información gráfica:** [consultar el diagrama o tabla de la página 110](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=110). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 111

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=111)

```text
Sincronización No Bloqueante: Eliminación Concurrente




                        predA   currA




                         a       c       d      e

                                predB   currB
```

**Información gráfica:** [consultar el diagrama o tabla de la página 111](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=111). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 112

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=112)

```text
Sincronización No Bloqueante: Eliminación Concurrente




                        predA         currA




                         a             c       d      e
                                CAS
                                      predB   currB
```

**Información gráfica:** [consultar el diagrama o tabla de la página 112](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=112). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 113

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=113)

```text
Sincronización No Bloqueante: Eliminación Concurrente




                         a      c             d      e
                                       CAS
                               predB         currB
```

**Información gráfica:** [consultar el diagrama o tabla de la página 113](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=113). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 114

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=114)

```text
Sincronización No Bloqueante: Eliminación Concurrente




                         a      c       d      e

                               predB   currB




                               Déjà-vu!!!
```

**Información gráfica:** [consultar el diagrama o tabla de la página 114](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=114). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 115

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=115)

```text
Sincronización No Bloqueante : solución


   • Se actualiza un nodo (el campo next) luego de que fue eliminado (nodo c)
   • Usar eliminación lazy
       • Primero marcar que el nodo fue removido
       • Después cambiar el puntero

   • CAS (modificado)
       • los campos next y marked del nodo se tratan como una única unidad
         atómica: cualquier intento de actualizar el campo next cuando el campo
         marked es verdadero (true) falla.
```

## Página 116

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=116)

```text
Interfaz Referencias marcadas
   interface ReferenciaMarcada <T >{

       public boolean compareAndSet ( T expectedReference ,
                                      T newReference ,
                                      boolean expectedMark ,
                                      boolean newMark ) ;

       public T get ( boolean [] marked ) ; // marked is output
       public T getReference () ;
       public boolean isMarked () ;
   }



  Comportamiento de compareAndSet
  Verifica de forma atómica que la referencia y la marca actuales coincidan con
  los valores esperados (expectedReference y expectedMark). Si ambas condi-
  ciones se cumplen, los reemplaza por los nuevos valores actualizados.
```

## Página 117

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=117)

```text
Sincronización No Bloqueante: Implementación

  Representación
   public class Node {
     public Object item ;
     public int key ;
     public ReferenciaMarcada < Node > next ;
     public Node ( int key ) {...}
   }
   public class List {
     ReferenciaMarcada < Node > head ;
   }
```

## Página 118

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=118)

```text
Eliminación Concurrente



                          predA   currA




                           a       c       d      e

                                  predB   currB
```

**Información gráfica:** [consultar el diagrama o tabla de la página 118](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=118). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 119

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=119)

```text
Eliminación Concurrente



                          predA   currA    Se marca


                                       X
                           a       c            d     e

                                  predB       currB
```

**Información gráfica:** [consultar el diagrama o tabla de la página 119](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=119). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 120

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=120)

```text
Eliminación Concurrente




                                   X
                          a    c          d        e

                              predB      currB

                                      CAS
                               FALLA POR EL NODO
                                   MARCADO
```

**Información gráfica:** [consultar el diagrama o tabla de la página 120](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=120). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 121

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=121)

```text
Eliminación Concurrente




                                              X        X
                                a         c        d       e

                                         predB    currB




   • Nodo c se elimina físicamente.
   • Nodo d se elimina lógicamente (marcado)
       • Será removido por las subsiguientes operaciones
```

**Información gráfica:** [consultar el diagrama o tabla de la página 121](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=121). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 122

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=122)

```text
Algoritmo Lock-Free: Limpieza cooperativac

  Limitación
  No es seguro cambiar next de un nodo ya marcado (ejemplo, eliminación con-
  currente). Si un hilo detecta esto, debe reiniciar el recorrido desde head.
```

## Página 123

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=123)

```text
Algoritmo Lock-Free: Limpieza cooperativac

  Limitación
  No es seguro cambiar next de un nodo ya marcado (ejemplo, eliminación con-
  currente). Si un hilo detecta esto, debe reiniciar el recorrido desde head.

  Estrategia de Recorrido Cooperativo
  Para evitar reintentos:
    • add() y remove(): Limpian físicamente cualquier nodo marcado que
      encuentren en su camino hacia el objetivo.
    • contains(): Al ser de solo lectura, no participa en la limpieza y puede
      atravesar nodos marcados y no marcados sin detenerse.
```

## Página 124

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=124)

```text
Algoritmo Lock-Free: Clase Window

  Abstracción
   • Se factoriza la lógica de busqueda y de pred y curr y limpieza.
   • El método find() avanza por la lista y, cada vez que encuentra un nodo
     marcado, intenta un compareAndSet() para removerlo físicamente.
   • Si falla debido a cambios concurrentes, reinicia el recorrido desde head.
```

## Página 125

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=125)

```text
Estructura Window y Método find

class Window {                                     // ... Viene de la izquierda
  public Node pred , curr ;                            succ = curr . next . get ( marked ) ;
  Window ( Node myPred , Node myCurr ) {               while ( marked [0]) {
    pred = myPred ; curr = myCurr ;                      snip = pred . next . compareAndSet (
  }                                                                  curr , succ , false , false ) ;
}                                                        if (! snip ) continue retry ;
                                                         curr = succ ;
public Window find ( Node head , int key ) {             succ = curr . next . get ( marked ) ;
  Node pred = null , curr = null ,                     }
     succ = null ;                                     if ( curr . key >= key )
  boolean [] marked = { false };                         return new Window ( pred , curr ) ;
  boolean snip ;                                       pred = curr ;
  retry : while ( true ) {                             curr = succ ;
    pred = head ;                                    }
    curr = pred . next . getReference () ;         }
    while ( true ) {                           }
      // Contin ú a a la derecha ...         }
```

## Página 126

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=126)

```text
Algoritmo Lock-Free: Método add

  public boolean add ( T item ) {
    int key = item . hashCode () ;
    while ( true ) {
      Window window = find ( head , key ) ;
      Node pred = window . pred , curr = window . curr ;
      if ( curr . key == key ) {
        return false ;
      } else {
        Node node = new Node ( item ) ;
        node . next = new AtomicMarkableReference ( curr , false ) ;
        if ( pred . next . compareAndSet ( curr , node , false , false ) ) {
           return true ;
        }
      }
    }
  }
```

## Página 127

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=127)

```text
Algoritmo Lock-Free: Método remove

  public boolean remove ( T item ) {
    int key = item . hashCode () ;
    boolean snip ;
    while ( true ) {
      Window window = find ( head , key ) ;
      Node pred = window . pred , curr = window . curr ;
      if ( curr . key != key ) {
        return false ;
      } else {
        Node succ = curr . next . getReference () ;
        snip = curr . next . compareAndSet ( succ , succ , false , true ) ;
        if (! snip )
           continue ;
        pred . next . compareAndSet ( curr , succ , false , false ) ;
        return true ;
      }
    }
  }
```

## Página 128

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=128)

```text
Algoritmo Lock-Free: Método contains

   public boolean contains ( T item ) {
     int key = item . hashCode () ;
     Node curr = head ;
     while ( curr . key < key ) {
       curr = curr . next . getReference () ;
     }
     return ( curr . key == key && ! curr . next . isMarked () ) ;
   }
```

## Página 129

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=129)

```text
Algoritmo Lock-Free: Puntos de Linealización
  Operaciones Exitosas (Modificaciones)
  El punto de linealización ocurre de forma atómica en el orden global de eventos:
     • add Exitoso: lineariza en el compareAndSet() de donde el nuevo nodo
       se enlaza a la lista.
     • remove Exitoso: Se lineariza exactamente en el compareAndSet() en el
       nodo pasa de estar desmarcado a marcado (remoción lógica).
  Operaciones Inexitosas
    • add o remove Inexitosos: Se linearizan cuando se evalúa que la
      condición en la clave del nodo (ya existe o no está).
    • contains(item): depende del resultado:
         • Exitoso: cuando se lee la referencia del nodo coincidente y verifica que su
           bit de marca es false.
         • Inexitoso: cuando se lee un nodo con clave mayor, o cuando detecta que
           el nodo con la clave buscada está lógicamente marcado.
```

## Página 130

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=130)

```text
Algoritmo Lock-Free: Análisis de Progreso
  Lock-Freedom
  Garantiza progreso global. Si un proceso se ve obligado a reiniciar (retry), es
  porque al menos otro proceso logró completar una operación exitosa en
  el sistema.

  ¿Por qué los reintentos no causan Deadlock ni inanición global?
  Un hilo solo reinicia el recorrido desde head en dos situaciones:
   1. Fallo en el CAS de limpieza (snip) dentro de find(): Significa que
      otro hilo concurrente ya modificó el enlace pred.next. Ese otro hilo
      modificó con éxito la lista (ya sea insertando un nodo o removiendo
      físicamente otro).
   2. Fallo en el CAS de modificación (add() o remove()): Significa que
      otro hilo modificó la ventana. Hubo progreso.
```

## Página 131

[Ver página original](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf#page=131)

```text
Algoritmo Lock-Free: Análisis de Progreso
  Progreso Inmune: contains()
    • El método contains() no ejecuta instrucciones CAS ni altera punteros,
      por lo que nunca se bloquea ni reinicia el recorrido.
    • Es Wait-Free: termina en un número finito de pasos determinado
      únicamente por la longitud física de la lista.
```
