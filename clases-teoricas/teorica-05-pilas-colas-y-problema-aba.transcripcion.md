# teorica-05-pilas-colas-y-problema-aba — transcripción

- Fuente: [teorica-05-pilas-colas-y-problema-aba.pdf](teorica-05-pilas-colas-y-problema-aba.pdf)
- Páginas del PDF: 118.
- SHA-256 del PDF: `09d18aa0a73f752cf322243f1218b97da9694974bc61d982a7aec3de084ad23d`.
- Tipo: transcripción textual por página, con disposición espacial conservada y referencias al original para esquemas.

> Se conserva el contenido del original, incluidos ejemplos incorrectos, erratas y diapositivas progresivas. Los bloques `text` preservan columnas y sangría; no son código listo para compilar. En código repartido en columnas, seguir las indicaciones «continúa a la derecha». Las notas editoriales se distinguen del original. Para símbolos cuya extracción sea ambigua, consultar la página enlazada.

## Página 1

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=1)

```text
Pools (Pilas y Colas):
Problema ABA
```

## Página 2

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=2)

```text
Pools (Estructuras de Datos)


  ¿Pool?
  Un multiset
    • Permite duplicados (un elemento puede aparecer más de una vez).
    • No necesariamente incluye un método contains().

  La Interfaz Pool<T>

           public interface Pool <T > {
             void put ( T item ) ;
             T get () ;
           }
```

## Página 3

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=3)

```text
Pools


  Utilización típica
  Como un buffer intermedio entre productores y consumidores.

  Acotados (Bounded) vs. No Acotados (Unbounded):
    • Acotados: Tienen capacidad limitada. Mantienen a los productores y
      consumidores sincronizados impidiendo que los productores se alejen
      mucho de los consumidores.
    • No acotados: Capacidad ilimitada. Útiles si es difícil fijar un límite al ritmo
      del productor.
```

## Página 4

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=4)

```text
Métodos Parciales y Totales


 Método Parcial
 Las llamadas pueden esperar a que se cumplan ciertas condiciones.
   • get() y put() se bloquean si no se pueden completar.
   • Uso: Tiene sentido si el productor o consumidor tienen que esperar
      necesariamente.

 Método Total
 Las llamadas no esperan condiciones para ejecutar.
   • Un get() put() retornan error o lanzan excepción si no se pueden
      completar.
   • Uso: Cuando el proceso puede hacer otras cosas (probar con otro buffer).
```

## Página 5

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=5)

```text
Métodos Síncronos



 Método Parcial Síncrono
 Espera a que otro método se solape con su propio intervalo de ejecución.
   • Pool síncrono:
        • El método que agrega un elemento se bloquea hasta que otra llamada lo
          remueve.
        • Simétricamente, el método que remueve un elemento se bloquea hasta que
          otra llamada lo hace disponible.
   • Implementan rendezvous para intercambiar información.
```

## Página 6

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=6)

```text
Garantías de Equidad (Fairness) en Pools



  Políticas de Acceso
  Los pools ofrecen diferentes garantías de equidad para procesar elementos:
    • FIFO (First-In-First-Out): Funciona como una cola. Los elementos se
      procesan en el orden estricto en que llegaron.
    • LIFO (Last-In-First-Out): Funciona como una pila. El último elemento
      en entrar es el primero en salir.
    • Otras propiedades: Políticas con garantías típicamente más débiles o
      aleatorias.
```

## Página 7

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=7)

```text
Colas Concurrentes (FIFO)



 Definición de Queue⟨T⟩
 Una secuencia ordenada de elementos de tipo T que provee:
   • enq(x): Agrega el elemento x al final de la cola (tail). Equivale a put().
   • deq() Remueve y devuelve el elemento al principio de la cola (head).
     Equivale a get().

 Correctitud
   • Linealizabilidad: Una cola concurrente debe ser linealizable con respecto a
     una cola secuencial.
```

## Página 8

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=8)

```text
Nivel de Concurrencia en Colas Acotadas

  Interferencia entre Operaciones
  ¿Cuánta concurrencia podemos esperar con múltiples hilos encolando y desen-
  colando?
    • enq() vs. deq(): Operan en extremos opuestos (tail y head). Si la cola
       no está vacía ni llena, deberían proceder sin interferirse.
    • Llamadas concurrentes del mismo tipo: Múltiples enq()
       probablemente interfieran entre sí. Lo mismo ocurre entre múltiples deq().
```

## Página 9

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=9)

```text
Nivel de Concurrencia en Colas Acotadas

  Interferencia entre Operaciones
  ¿Cuánta concurrencia podemos esperar con múltiples hilos encolando y desen-
  colando?
    • enq() vs. deq(): Operan en extremos opuestos (tail y head). Si la cola
       no está vacía ni llena, deberían proceder sin interferirse.
    • Llamadas concurrentes del mismo tipo: Múltiples enq()
       probablemente interfieran entre sí. Lo mismo ocurre entre múltiples deq().

  El Desafío de la Implementación
  Aunque este razonamiento informal suena convincente y es mayormente correcto,
  lograr este nivel de concurrencia en la práctica no es trivial.
```

## Página 10

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=10)

```text
Representación Secuencial de la Cola Acotada

public class SequentialBoundedQueue <T > {                           protected class Node {
  volatile Node head , tail ;                                          public T value ;
  int size ;                                                           public volatile Node next ;
  final int capacity ;                                                 public Node ( T x ) {
                                                                         value = x ;
    public SequentialBoundedQueue ( int c ) {       next = null ;
      capacity = c ;                                                   }
      head = new Node ( null ) ;                                     }
      tail = head ;
      size = 0;
    }
}

    Invariante de representación
      • head puede contener null. Los restantes nodos no.
      • tail es alcanzable y desde head.
      • capacity es la cantidad de nodos alcanzables desde head.
```

## Página 11

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=11)

```text
Estructura de la Cola Concurrente Acotada


 public class BoundedQueue <T > {                                        protected class Node {
   ReentrantLock enqLock , deqLock ;                                       public T value ;
   Condition notEmptyCondition ;                           public volatile Node next ;
   Condition notFullCondition ;
   AtomicInteger size ;                                                      public Node ( T x ) {
   volatile Node head , tail ;                                                 value = x ;
   final int capacity ;                                                        next = null ;
                                                                             }
     public BoundedQueue ( int c ) {                                     }
       capacity = c ;
       head = new Node ( null ) ;
       tail = head ;
       size = new AtomicInteger (0) ;
       enqLock = new ReentrantLock () ;
       notFullCondition = enqLock . newCondition () ;
       deqLock = new ReentrantLock () ;
       notEmptyCondition = deqLock . newCondition () ;
     }
 }
```

## Página 12

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=12)

```text
Representación

   • Dos Locks enqLock y deqLock
       • Permite que un productor y un consumidor operen simultáneamente.
       • Evita que los procesos que encolan bloqueen innecesariamente a los que
         desencolan y viceversa.
   • Variables de Condición
       • notFullCondition: Asociada a enqLock, para notificar que hay espacio.
       • notEmptyCondition: Asociada a deqLock, para notificar que hay
         elementos.
   • Control de Capacidad
       • size es un AtomicInteger que mantiene la cantidad de elementos en la
         cola.
       • Es atómico porque va a ser accedido sin protección.
```

## Página 13

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=13)

```text
Implementación del Método enq(x)
  public void enq ( T x ) {                                   • Lock Dividido: enqLock
    boolean mustWakeDequeuers = false ;
    Node e = new Node ( x ) ;                                   protege el final de la cola.
    enqLock . lock () ;
    try {
                                                              • Espera: Si la cola está llena, el
      while ( size . get () == capacity )                       hilo se bloquea en
         notFullCondition . await () ;
      tail . next = e ;                                         notFullCondition.
      tail = e ;                                              • Wake-ups: Sólo despierta a los
      if ( size . getAndIncrement () == 0)
         mustWakeDequeuers = true ;             consumidores (deqLock) si la
    } finally {
      enqLock . unlock () ;
                                                                cola pasa de estar vacía (size
    }                                                           == 0) a tener un elemento.
    if ( mustWakeDequeuers ) {
      deqLock . lock () ;
      try {
         notEmptyCondition . signalAll () ;
      } finally {
         deqLock . unlock () ;
      }
    }
  }
```

## Página 14

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=14)

```text
Implementación del Método deq()

public T deq () {                                          •   Uso del Centinela: El valor se
  T result ;
  boolean mustWakeEnqueuers = false ;          extrae de head.next.value.
  deqLock . lock () ;                                          Luego, ese nodo se convierte en la
  try {
    while ( head . next == null )                              nueva cabeza (head), descartando
       notEmptyCondition . await () ;          el valor viejo.
    result = head . next . value ;
    head = head . next ;                                   •   Espera: Si head.next es nulo, la
    if ( size . getAndDecrement () == capacity ) {
       mustWakeEnqueuers = true ;              cola está vacía y el hilo espera en
    }                                                          notEmptyCondition.
  } finally { deqLock . unlock () ; }
  if ( mustWakeEnqueuers ) {               •   Optimización de Señal: Solo
    enqLock . lock () ;
    try {                                                      despierta a los productores
       notFullCondition . signalAll () ;         (enqLock) si la cola pasa de estar
    } finally { enqLock . unlock () ; }
  }                                                            completamente llena (size ==
  return result ;                                              capacity) a liberar un lugar.
}
```

## Página 15

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=15)

```text
Por qué no hay Lost-Wakeup

  Progreso del Productor
    • Despues de determinar que hay espacio, encolar termina con éxito.
    • Ningún otro proceso puede llenar la cola porque los demás productores
      están bloqueados y los consumidores solo incrementan el espacio libre.
```

## Página 16

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=16)

```text
Por qué no hay Lost-Wakeup

  Progreso del Productor
    • Despues de determinar que hay espacio, encolar termina con éxito.
    • Ningún otro proceso puede llenar la cola porque los demás productores
      están bloqueados y los consumidores solo incrementan el espacio libre.

  Sincronización
    • El productor detecta la cola llena en dos pasos: evalúa size y luego
      espera en la condición).
    • Aunque size no usa locks, el proceso que desencola debe adquirir
      enqLock antes de hacer signal.
    • Garantía: Esto impide que el signal pueda ocurrir en el medio de los dos
      pasos del productor.
```

## Página 17

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=17)

```text
Método deq()

 Detección de Cola Vacía
   • Verificación: Comprueba si head.next == null.
        • El consumidor no lee el campo size.
   • Si la cola está vacía, espera en notEmptyCondition.
   • Al despertar, vuelve a verificar la condición antes de avanzar.

 Extracción y Notificación
   • Exclusión Mutua: La cola permanecerá no vacía si se confirma que no
     está vacía porque los demás consumidores están bloqueados.
   • Se lee el valor del primer nodo head.next y se avanza el centinela.
   • Decrementa size. Si la cola estaba llena, adquiere enqLock y despierta a
     los productores.
```

## Página 18

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=18)

```text
Punto de Linealización e Invariantes Temporales
  Linealización de enq()
  El elemento se agrega cuando se actualiza el puntero tail.next, no tail.
    • Linearización: en la redirección de tail.next = e.
    • Desfase: El elemento es parte de la cola aunque el proceso no haya
       actualizado tail.

  Efectos
    • Un consumidor puede avanzar antes de que el productor termine.
        • Desencolar puede extraer el nuevo nodo y avanzar head de inmediato.
    • size puede ser negativo temporalmente si el consumidor decrementa
      antes del incremento del productor.
    • El productor no necesita despertar porque su elemento ya fue removido.
```

## Página 19

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=19)

```text
Inserción en la Cola (enq)


                                    size = 2


                   head                   tail             e




                                a          b              c

                                               null            null




   • Se instancia en memoria el nuevo nodo e con el valor b.
   • El puntero tail referencia al último elemento de la lista (a). Su enlace
     next es null.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 19](teorica-05-pilas-colas-y-problema-aba.pdf#page=19). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 20

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=20)

```text
Inserción en la Cola (enq)


                                   size = 2


                   head                   tail            e




                               a           b             c

                                               null           null




   • El hilo adquiere de manera exclusiva el enqLock para garantizar exclusión
     mutua.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 20](teorica-05-pilas-colas-y-problema-aba.pdf#page=20). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 21

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=21)

```text
Inserción en la Cola (enq)


                                 size = 2


                  head                 tail          e




                             a          b            c

                                                         null




   • Punto de Linealización: Se ejecuta tail.next = e. El elemento pasa a
     pertenecer abstractamente a la cola.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 21](teorica-05-pilas-colas-y-problema-aba.pdf#page=21). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 22

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=22)

```text
Inserción en la Cola (enq)


                                   size = 3


                   head                                  tail
                                                          e




                               a          b               c

                                                              null




   • Se actualiza la referencia global tail = e y se incrementa el contador de
     estado atómico size a 2.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 22](teorica-05-pilas-colas-y-problema-aba.pdf#page=22). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 23

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=23)

```text
Remoción en la Cola (deq)


                          size = 3   |   result = —

                   head                                 tail




                               a          b              c

                                                             null




   • Un hilo invoca a deq(). Se verifica que la cola no esté vacía comprobando
     que head.next != null (el nodo a existe).
   • El nodo head actual opera puramente como centinela.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 23](teorica-05-pilas-colas-y-problema-aba.pdf#page=23). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 24

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=24)

```text
Remoción en la Cola (deq)


                           size = 3   |   result = —

                    head                                 tail




                                a          b              c

                                                              null




   • El hilo consumidor adquiere de manera exclusiva el deqLock para proteger la
     estructura de la cabeza de la cola.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 24](teorica-05-pilas-colas-y-problema-aba.pdf#page=24). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 25

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=25)

```text
Remoción en la Cola (deq)


                          size = 3   |   result = a

                   head                                tail




                               a          b             c

                                                            null




   • Se lee el valor del primer nodo real de la cola: result = head.next.value
     (retorna el valor a).
```

**Información gráfica:** [consultar el diagrama o tabla de la página 25](teorica-05-pilas-colas-y-problema-aba.pdf#page=25). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 26

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=26)

```text
Remoción en la Cola (deq)


                        size = 2   |   result = a

                            head                    tail




                             a          b            c

                                                         null




   • Punto de Linealización: Se ejecuta head = head.next. El nodo que
     contenía a pasa a ser el nuevo centinela vacío.
   • Se decrementa el contador size a 2 y se libera el bloqueo.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 26](teorica-05-pilas-colas-y-problema-aba.pdf#page=26). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 27

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=27)

```text
Anomalía Concurrente: size temporalmente en −1


                 size = 0    |      Operación: Cola Vacía (Inicial)

                             head                e




                                                 a

                             tail
                                 null                null




   • Estado inicial: La cola está completamente vacía. El puntero tail apunta
     al centinela vacío y size es 0.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 27](teorica-05-pilas-colas-y-problema-aba.pdf#page=27). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 28

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=28)

```text
Anomalía Concurrente: size temporalmente en −1


           size = 0   |   Operación: enq(a) enlazado estructuralmente

                              head            e




                                              a

                              tail
                                                  null




   • Productor interviene: Se ejecuta tail.next = e. El elemento a queda
     enlazado físicamente (punto de linealización de enq), pero el hilo se
     suspende antes de avanzar tail o sumar a size.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 28](teorica-05-pilas-colas-y-problema-aba.pdf#page=28). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 29

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=29)

```text
Anomalía Concurrente: size temporalmente en −1


            size = -1   |   Operación: deq() intercala y decrementa

                                             head
                                              e




                                              a

                             tail
                                                  null




   • Consumidor se intercala: Llama a deq(). En vez de leer el contador,
     comprueba si head.next != null. Como es verdadero (apunta a a), extrae
     el elemento, avanza head y decrementa: size cae temporalmente a -1.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 29](teorica-05-pilas-colas-y-problema-aba.pdf#page=29). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 30

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=30)

```text
Anomalía Concurrente: size temporalmente en −1


               size = 0    |   Operación: enq(a) despierta y finaliza

                                                head
                                                 e




                                                 a

                                                tail
                                                    null




   • Productor finaliza: El hilo original se despierta, mueve tail = e y ejecuta
     size.getAndIncrement(). El valor de size sube a 0 y la estructura
     vuelve a quedar consistente y vacía.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 30](teorica-05-pilas-colas-y-problema-aba.pdf#page=30). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 31

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=31)

```text
Cola Total No Acotada (UnboundedQueue)




 Operaciones totales
  • enq(x): Siempre tiene éxito porque no tiene una capacidad acotada.
  • deq() Es total. si la cola está vacía, no espera y lanza una excepción
    EmptyException.
```

## Página 32

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=32)

```text
Estructura de la Cola No Acotada



  public class UnboundedQueue <T > {   public UnboundedQueue () {
    ReentrantLock enqLock ;              head = new Node ( null ) ;
    ReentrantLock deqLock ;              tail = head ;
    volatile Node head , tail ;          enqLock = new ReentrantLock () ;
                                         deqLock = new ReentrantLock () ;
      protected class Node {           }
        public T value ;
        public volatile Node next ;
        public Node ( T x ) {
          value = x ;
          next = null ;
        }
      }
  }
```

## Página 33

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=33)

```text
Métodos Totales: enq() y deq()



 public void enq ( T x ) {       public T deq () throws
   Node e = new Node ( x ) ;         EmptyException {
   enqLock . lock () ;             T result ;
   try {                           deqLock . lock () ;
     tail . next = e ;             try {
     tail = e ;                      if ( head . next == null ) {
   } finally {                         throw new EmptyException () ;
     enqLock . unlock () ;           }
   }                                 result = head . next . value ;
 }                                   head = head . next ;
                                   } finally {
                                     deqLock . unlock () ;
                                   }
                                   return result ;
                                 }
```

## Página 34

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=34)

```text
Cola Total No Acotada: Análisis



 Correctitud. Linealización
   • enq: el elemento se añade cuando se redirige el puntero next de tail.
   • deq: existoso: cuando se cambia head.
   • deq: no existoso: cuando se chequea head.next == null.

 Progreso
   • Cada método adquiere únicamente un lock (enqLock o deqLock). Luego no
     puede haber deadlocks
   • Si los locks son fair, libre de inanición.
```

## Página 35

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=35)

```text
Cola Libre de Bloqueos (LockFreeQueue): Representación




public class LockFreeQueue <T > {                       public class Node {
  AtomicReference < Node > head , tail ;                  public T value ;
                                                          public AtomicReference < Node > next ;
    public   LockFreeQueue () {
      Node   node = new Node ( null ) ;                     public Node ( T value ) {
      head   = new AtomicReference ( node ) ;         this . value = value ;
      tail   = new AtomicReference ( node ) ;         next = new AtomicReference < Node >( null ) ;
    }                                                       }
}                                                       }
```

## Página 36

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=36)

```text
Método enq()


public void enq ( T value ) {                        • Verificar que el último nodo no tenga
  Node node = new Node ( value ) ;
  while ( true ) {                                     sucesor (next == null).
    Node last = tail . get () ;
    Node next = last . next . get () ;
                                                     • Intenta enlazar al nuevo nodo
    if ( last == tail . get () ) {                     mediante un CAS. Si tiene éxito, la
                if (next == null) {                    inserción es efectiva.
                  if (last.next.CAS(next, node)) {
                     tail.CAS(last, node);
                                                     • Intenta adelantar tail. Si falla,
                   return ;                            significa que otra operación lo ayudó.
                }
                } else {                             • Ayuda: Si se detecta que el nodo final
                  tail.CAS(last, next);
            }
                                                       tiene un sucesor, intenta adelantar el
        }                                              puntero tail antes de reintentar la
    }
}                                                      inserción.
```

## Página 37

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=37)

```text
Método enq()


public void enq ( T value ) {                        • Verificar que el último nodo no tenga
  Node node = new Node ( value ) ;
  while ( true ) {                                     sucesor (next == null).
    Node last = tail . get () ;
    Node next = last . next . get () ;
                                                     • Intenta enlazar al nuevo nodo
    if ( last == tail . get () ) {                     mediante un CAS. Si tiene éxito, la
        if (next == null) {
                                                       inserción es efectiva.
                  if (last.next.CAS(next, node)) {
                    tail.CAS(last, node);
                                                     • Intenta adelantar tail. Si falla,
                  return ;                             significa que otra operación lo ayudó.
                }
                } else {                             • Ayuda: Si se detecta que el nodo final
                  tail.CAS(last, next);
            }
                                                       tiene un sucesor, intenta adelantar el
        }                                              puntero tail antes de reintentar la
    }
}                                                      inserción.
```

## Página 38

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=38)

```text
Método enq()


public void enq ( T value ) {                 • Verificar que el último nodo no tenga
  Node node = new Node ( value ) ;
  while ( true ) {                              sucesor (next == null).
    Node last = tail . get () ;
    Node next = last . next . get () ;
                                              • Intenta enlazar al nuevo nodo
    if ( last == tail . get () ) {              mediante un CAS. Si tiene éxito, la
        if (next == null) {
           if (last.next.CAS(next, node)) {     inserción es efectiva.
                    tail.CAS(last, node);     • Intenta adelantar tail. Si falla,
                  return ;                      significa que otra operación lo ayudó.
                }
                } else {                      • Ayuda: Si se detecta que el nodo final
                  tail.CAS(last, next);
            }
                                                tiene un sucesor, intenta adelantar el
        }                                       puntero tail antes de reintentar la
    }
}                                               inserción.
```

## Página 39

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=39)

```text
Método enq()

public void enq ( T value ) {                 • Verificar que el último nodo no tenga
  Node node = new Node ( value ) ;
  while ( true ) {                              sucesor (next == null).
    Node last = tail . get () ;
    Node next = last . next . get () ;
                                              • Intenta enlazar al nuevo nodo
    if ( last == tail . get () ) {              mediante un CAS. Si tiene éxito, la
        if (next == null) {
           if (last.next.CAS(next, node)) {     inserción es efectiva.
              tail.CAS(last, node);           • Intenta adelantar tail. Si falla,
            return ;
         }                                      significa que otra operación lo ayudó.
                } else {                      • Ayuda: Si se detecta que el nodo final
                  tail.CAS(last, next);         tiene un sucesor, intenta adelantar el
            }                                   puntero tail antes de reintentar la
        }
    }                                           inserción.
}
```

## Página 40

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=40)

```text
Funcionamiento Detallado de deq()


public T deq () throws EmptyException {      • Chequea que no haya interferencias de
  while ( true ) {
    Node first = head . get () ;                deq.
    Node last = tail . get () ;
    Node next = first . next . get () ;
                                              • Si la cabeza y la cola coinciden pero
    if ( first == head . get () ) {             next no es nulo, se ayuda adelantando
      if ( first == last ) {
         if ( next == null ) {                  tail.
           throw new EmptyException () ;    • Comprueba si es vacía
         }
         tail . CAS ( last , next ) ;         • Si es vacía, lanza una excepción
      } else {
         T value = next . value ;               EmptyException.
         if ( head . CAS ( first , next ) )
           return value ;
                                              • Si la cola tiene elementos, lee el valor
      }                                         del sucesor e intenta avanzar head
    }
  }                                             mediante un CAS.
}
```

## Página 41

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=41)

```text
Funcionamiento Detallado de deq()

public T deq () throws EmptyException {         • Chequea que no haya interferencias de
  while ( true ) {
    Node first = head . get () ;                   deq concurrentes volviendo a validar la
    Node last = tail . get () ;                    cabeza.
    Node next = first . next . get () ;
            if (first == head.get()) {
                                                 • Si la cabeza y la cola coinciden pero
              if (first == last) {                 next no es nulo, se ayuda adelantando
                if (next == null) {                tail.
                   throw new EmptyException();
              }                                  • Comprueba de forma segura si la
                tail.CAS(last, next);
            } else {                               estructura está vacía.
                T value = next.value;
                if (head.CAS(first, next))
                                                 • Si está efectivamente vacía, lanza una
                    return value ;                 excepción EmptyException.
            }
        }                                        • Si la cola tiene elementos, lee el valor
    }                                              del sucesor e intenta avanzar head
}
                                                   mediante un CAS.
```

## Página 42

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=42)

```text
Funcionamiento Detallado de deq()

public T deq () throws EmptyException {          • Chequea que no haya interferencias de
  while ( true ) {
    Node first = head . get () ;                    deq concurrentes volviendo a validar la
    Node last = tail . get () ;                     cabeza.
    Node next = first . next . get () ;
      if (first == head.get()) {                  • Si la cabeza y la cola coinciden pero
             if (first == last) {                   next no es nulo, se ayuda adelantando
                  if (next == null) {               tail.
                    throw new EmptyException();
              }                                   • Comprueba de forma segura si la
                  tail.CAS(last, next);             estructura está vacía.
            } else {
                T value = next.value;
                                                  • Si está efectivamente vacía, lanza una
                if (head.CAS(first, next))          excepción EmptyException.
                   return value ;
            }                                     • Si la cola tiene elementos, lee el valor
        }
    }                                               del sucesor e intenta avanzar head
}                                                   mediante un CAS.
```

## Página 43

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=43)

```text
Funcionamiento Detallado de deq()

public T deq () throws EmptyException {          • Chequea que no haya interferencias de
  while ( true ) {
    Node first = head . get () ;                    deq concurrentes volviendo a validar la
    Node last = tail . get () ;                     cabeza.
    Node next = first . next . get () ;
      if (first == head.get()) {                  • Si la cabeza y la cola coinciden pero
        if (first == last) {
                                                    next no es nulo, se ayuda adelantando
                  if (next == null) {
                                                    tail.
                    throw new EmptyException();
              }                                   • Comprueba de forma segura si la
                tail.CAS(last, next);
            } else {                                estructura está vacía.
                T value = next.value;
                if (head.CAS(first, next))
                                                  • Si está efectivamente vacía, lanza una
                   return value ;                   excepción EmptyException.
            }
        }                                         • Si la cola tiene elementos, lee el valor
    }                                               del sucesor e intenta avanzar head
}
                                                    mediante un CAS.
```

## Página 44

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=44)

```text
Funcionamiento Detallado de deq()

public T deq () throws EmptyException {        • Chequea que no haya interferencias de
  while ( true ) {
    Node first = head . get () ;                  deq concurrentes volviendo a validar la
    Node last = tail . get () ;                   cabeza.
    Node next = first . next . get () ;
      if (first == head.get()) {                • Si la cabeza y la cola coinciden pero
        if (first == last) {
           if (next == null) {                    next no es nulo, se ayuda adelantando
                  throw new EmptyException();     tail.
              }                                 • Comprueba de forma segura si la
                tail.CAS(last, next);
            } else {                              estructura está vacía.
                T value = next.value;
                if (head.CAS(first, next))
                                                • Si está efectivamente vacía, lanza una
                   return value ;                 excepción EmptyException.
            }
        }                                       • Si la cola tiene elementos, lee el valor
    }                                             del sucesor e intenta avanzar head
}
                                                  mediante un CAS.
```

## Página 45

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=45)

```text
Funcionamiento Detallado de deq()

public T deq () throws EmptyException {     • Chequea que no haya interferencias de
  while ( true ) {
    Node first = head . get () ;               deq concurrentes volviendo a validar la
    Node last = tail . get () ;                cabeza.
    Node next = first . next . get () ;
      if (first == head.get()) {             • Si la cabeza y la cola coinciden pero
        if (first == last) {
           if (next == null) {                 next no es nulo, se ayuda adelantando
             throw new EmptyException();       tail.
         }
           tail.CAS(last, next);             • Comprueba de forma segura si la
      } else {
                                               estructura está vacía.
                T value = next.value;
                                             • Si está efectivamente vacía, lanza una
                if (head.CAS(first, next))
                                               excepción EmptyException.
                   return value ;
            }                                • Si la cola tiene elementos, lee el valor
        }
    }                                          del sucesor e intenta avanzar head
}                                              mediante un CAS.
```

## Página 46

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=46)

```text
Funcionamiento Detallado de deq()

public T deq () throws EmptyException {   • Chequea que no haya interferencias de
  while ( true ) {
    Node first = head . get () ;             deq concurrentes volviendo a validar la
    Node last = tail . get () ;              cabeza.
    Node next = first . next . get () ;
      if (first == head.get()) {           • Si la cabeza y la cola coinciden pero
        if (first == last) {
           if (next == null) {               next no es nulo, se ayuda adelantando
             throw new EmptyException();     tail.
         }
           tail.CAS(last, next);           • Comprueba de forma segura si la
      } else {
           T value = next.value;
                                             estructura está vacía.
           if (head.CAS(first, next))      • Si está efectivamente vacía, lanza una
              return value ;
      }                                      excepción EmptyException.
    }
  }                                        • Si la cola tiene elementos, lee el valor
}                                            del sucesor e intenta avanzar head
                                             mediante un CAS.
```

## Página 47

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=47)

```text
Técnica de Ayuda: tail retrasado en deq()


   Condición: first == last y next != null       |      Estado: Inconsistencia inicial

                             head




                                             a

                             tail
                                                 null




   • El problema: Un productor enlazó el nodo a pero se suspendió antes de
     actualizar tail. El puntero tail quedó rezagado (*lagging behind*).
```

**Información gráfica:** [consultar el diagrama o tabla de la página 47](teorica-05-pilas-colas-y-problema-aba.pdf#page=47). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 48

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=48)

```text
Técnica de Ayuda: tail retrasado en deq()


  Condición: first == last y next != null   |          Estado: deq() detecta retraso

                            head




                                            a

                            tail
                                                null




   • Detección: Un consumidor llama a deq() y nota que first == last
     (head == tail) pero su sucesor next no es nulo. Sabe que no puede
     remover el centinela todavía.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 48](teorica-05-pilas-colas-y-problema-aba.pdf#page=48). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 49

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=49)

```text
Técnica de Ayuda: tail retrasado en deq()


   Condición: first == last y next != null    |     Estado: deq() ayuda y avanza
                                    tail

                              head




                                              a

                                             tail
                                                 null




   • Técnica de Ayuda: El consumidor ejecuta tail.CAS(last, next) para
     acomodar la estructura del productor flojo, moviendo tail de forma segura
     hacia el nodo a.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 49](teorica-05-pilas-colas-y-problema-aba.pdf#page=49). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 50

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=50)

```text
Técnica de Ayuda: tail retrasado en deq()


  Condición: first == last y next != null   |      Estado: deq() linealiza y avanza
                                    head

                                            head




                                             a

                                            tail
                                                null




   • Linealización de deq: Con la cola ya consistente, el consumidor ejecuta
     con éxito su propio head.CAS(first, next), transformando al nodo a en
     el nuevo centinela y retornando su valor.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 50](teorica-05-pilas-colas-y-problema-aba.pdf#page=50). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 51

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=51)

```text
Correctitud : Linearización



 Inserción: enq()
   • enq() se linealiza cuando se modifica con éxito el puntero del último nodo
     (CAS exitoso sobre last.next)

 Eliminación: deq()
   • exitosa (Retorna valor): Se linealiza cuando se modifica con éxito el
     puntero de la cabeza (head.CAS(first, next)).
   • no exitoso (EmptyException): Se linealiza cuando se lee next del
     centinela.
```

## Página 52

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=52)

```text
Progreso



 Cola es Lock-Free
  • Cada método verifica primero si existe una operación enq() incompleta e
     intenta terminarla.
  • En el peor de los casos, todos los procesos intentan avanzar el campo tail
     de la cola, y al menos uno de ellos debe tener éxito.
  • Un hilo falla al intentar encolar o desencolar un nodo solo si la operación de
     otro hilo tiene éxito al modificar la referencia. Por lo tanto, siempre hay
     algún método que logra completar su trabajo.
```

## Página 53

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=53)

```text
Manejo de memoria




 Motivaciones
  • Limitaciones del lenguaje: Lenguajes como C o C++ no tienen garbage
    collector.
  • Eficiencia: Puede ser más eficiente que una clase gestione sus propios
    nodos cuando se crean y liberan constantemente múltiples objetos pequeños.
  • Garantías de progreso: Si el garbage collector no es lock-free.
```

## Página 54

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=54)

```text
Dinámica de la freeList Local

 Reciclaje
 Cada hilo administra su propia lista privada y local de nodos no usados.
   ThreadLocal < Node > freeList = new ThreadLocal < Node >() {
      protected Node initialValue () { return null ; };
   };

 Ciclo de Vida de los Nodos
   • Asignación en enq(): Cuando un productor necesita un nodo, intenta
     extraerlo de su lista local. Si la lista está vacía, crea uno nuevo (new).
   • Devolución en deq(): Cuando un consumidor desencola y descarta un
     nodo, lo agrega a la lista local para su reutilización futura.
 Limitaciones
   • Funciona bien bajo una cantidad balanceada de inserciones y extracciones.
   • Otras situaciones requieren técnicas como de robo de nodos (node stealing).
```

## Página 55

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=55)

```text
El Problema ABA con Reciclaje Manual


                                         Pool




    head                     tail



     a           b            c

                                  null



   • Estado Inicial: La estructura cuenta con el centinela a y los elementos b y
     c en la cola.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 55](teorica-05-pilas-colas-y-problema-aba.pdf#page=55). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 56

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=56)

```text
El Problema ABA con Reciclaje Manual


                                        Pool




    head                    tail



     a           b           c

   first        next
                                 null



   • Paso 1: El Thread 1 inicia un deq(), lee los punteros locales first = a y
     next = b, y es instantáneamente suspendido antes de ejecutar su CAS.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 56](teorica-05-pilas-colas-y-problema-aba.pdf#page=56). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 57

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=57)

```text
El Problema ABA con Reciclaje Manual


                                        Pool




    head                    tail



     a           b           c

   first        next
                                 null



   • Interferencia del Thread 2 (Paso A): Un segundo hilo concurrente
     interviene y ejecuta de forma veloz dos operaciones deq() consecutivas.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 57](teorica-05-pilas-colas-y-problema-aba.pdf#page=57). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 58

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=58)

```text
El Problema ABA con Reciclaje Manual


                                        Pool
                                                a         b

                                               first     next
                                                             null

                          head tail



                             c

                                 null



   • Efecto del Desencolado: Los nodos a y b son removidos físicamente de la
     cola abstracta y se alojan en el Pool de reciclaje. head y tail ahora
     apuntan a c.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 58](teorica-05-pilas-colas-y-problema-aba.pdf#page=58). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 59

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=59)

```text
El Problema ABA con Reciclaje Manual


                                         Pool
                                                             b

                                                            next
                                                                null

                            head        tail



                             c           a

                                       first
                                           null



   • Reinserción: El Thread 2 realiza un enq(). Extrae el nodo a del pool y lo
     conecta detrás de c. La cola vuelve a tener la dirección de memoria A al
     final.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 59](teorica-05-pilas-colas-y-problema-aba.pdf#page=59). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 60

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=60)

```text
El Problema ABA con Reciclaje Manual


                                         Pool
                                                   c          b

                                                             next
                                                                 null

                                      head tail



                                         a

                                        first
                                            null



   • Otro hilo ejecuta un deq() adicional. El nodo c es removido y va al Pool.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 60](teorica-05-pilas-colas-y-problema-aba.pdf#page=60). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 61

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=61)

```text
El Problema ABA con Reciclaje Manual

                                                          head


                                       Pool
                                                 c         b

                                                          next
                                                              null

                                      tail



                                       a

                                      first
                                          null



   • El Thread 1 despierta y ejecuta su CAS postergado. Como head es first,
     el CAS tiene éxito y desplaza la cabeza hacia b (next), apuntando a un
     elemento en el Pool.
```

**Descripción editorial del esquema:** El CAS postergado vuelve a encontrar la referencia esperada por reciclaje de nodos y mueve head hacia b, que ya está en el pool. La igualdad de dirección no detecta el cambio de historia: es el problema ABA.

**Información gráfica:** [consultar el diagrama o tabla de la página 61](teorica-05-pilas-colas-y-problema-aba.pdf#page=61). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 62

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=62)

```text
Solución al Problema ABA: AtomicStampedReference


 Sello (Versiones)
 A cada referencia atómica se le pone un sello (stamp) que actúa como número
 de versión.
   • Clase AtomicStampedReference<T> que encapsula una referencia al objeto
     de tipo T y un entero que representa el sello
   • Control de Versiones: Cada vez que se modifica la referencia, el valor del
     sello se incrementa. Aunque una dirección
   • Aunque se vuelva a la misma referencia, los sellos son distintos y el CAS
     fallará.
```

## Página 63

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=63)

```text
API de AtomicStampedReference



   • Provee los siguientes métodos atómicos.

  public boolean compareAndSet (               public T getReference () ;
     T expectedReference ,
     T newReference ,                          public int getStamp () ;
     int expectedStamp ,
     int newStamp                              public void set (
  );                                              T newReference ,
                                                  int newStamp
  public T get ( int [] stampHolder ) ;        );
```

## Página 64

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=64)

```text
Método deq() empleando Sellos de Versión

public T deq () throws EmptyException {                  } else {
  int [] lastStamp = new int [1];                           T value = next . value ;
  int [] firstStamp = new int [1];                          if (head.compareAndSet(first, next,
  int [] nextStamp = new int [1];                               firstStamp[0], firstStamp[0]+1)) {
  while ( true ) {                                             free ( first ) ;
    Node first = head.get(firstStamp);                         return value ;
                                                            }
    Node last = tail.get(lastStamp);                      }
                                                      }
    Node next = first.next.get(nextStamp);        }
    if (head.getStamp() == firstStamp[0]) {   }
      if ( first == last ) {
         if ( next == null ) {
            throw new EmptyException () ;
         }
          tail.compareAndSet(last, next,
            lastStamp[0], lastStamp[0]+1);



     • Se leen las referencias y los sellos de versión.
```

## Página 65

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=65)

```text
Método deq() empleando Sellos de Versión

public T deq () throws EmptyException {                  } else {
  int [] lastStamp = new int [1];                           T value = next . value ;
  int [] firstStamp = new int [1];                          if (head.compareAndSet(first, next,
  int [] nextStamp = new int [1];                               firstStamp[0], firstStamp[0]+1)) {
  while ( true ) {                                             free ( first ) ;
    Node first = head.get(firstStamp);                         return value ;
    Node last = tail.get(lastStamp);                        }
    Node next = first.next.get(nextStamp);                }
    if (head.getStamp() == firstStamp[0]) {           }
                                                  }
      if ( first == last ) {                  }
        if ( next == null ) {
           throw new EmptyException () ;
        }
         tail.compareAndSet(last, next,
           lastStamp[0], lastStamp[0]+1);



     • Se verifica que el sello de la cabeza no haya cambiado.
```

## Página 66

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=66)

```text
Método deq() empleando Sellos de Versión

public T deq () throws EmptyException {                  } else {
  int [] lastStamp = new int [1];                           T value = next . value ;
  int [] firstStamp = new int [1];                            if (head.compareAndSet(first, next,
  int [] nextStamp = new int [1];
  while ( true ) {                                                  firstStamp[0], firstStamp[0]+1)) {
    Node first = head.get(firstStamp);
    Node last = tail.get(lastStamp);                              free ( first ) ;
    Node next = first.next.get(nextStamp);                        return value ;
    if (head.getStamp() == firstStamp[0]) {                   }
       if ( first == last ) {                             }
         if ( next == null ) {                        }
            throw new EmptyException () ;       }
         }                                    }

          tail.compareAndSet(last, next,

             lastStamp[0], lastStamp[0]+1);



     • Al avanzar los punteros (tail o head), se exige que el sello no haya
       cambiado y se incrementa en +1.
```

## Página 67

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=67)

```text
Método enq() empleando Sellos de Versión

public void enq ( T   value ) {                         if (last.next.CAS(next, node,
  Node node = new     Node ( value ) ;                        nextStamp, nextStamp+1)) {
  int [] lastStamp    = new int ;                             tail.CAS(last, node,
  int [] nextStamp    = new int ;                                  lastStamp, lastStamp+1);
  while ( true ) {                                           return ;
    Node last = tail.get(lastStamp);                       }
                                                        } else {
    Node next = last.next.get(nextStamp);                  tail . CAS ( last , next ,
                                                             lastStamp , lastStamp +1) ;
    if ( last == tail . get () ) {                      }
      if ( next == null ) {                         }
                                                }
                                            }



      • Lectura de Sellos: Al igual que en la remoción, el productor captura
        simultáneamente los nodos last y next junto con sus sellos de versión
        vigentes.
```

## Página 68

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=68)

```text
Método enq() empleando Sellos de Versión

public void enq ( T value ) {                           if (last.next.CAS(next, node,
  Node node = new Node ( value ) ;
  int [] lastStamp = new int ;                               nextStamp, nextStamp+1)) {
  int [] nextStamp = new int ;                                tail.CAS(last, node,
  while ( true ) {                                                lastStamp, lastStamp+1);
    Node last = tail.get(lastStamp);                         return ;
    Node next = last.next.get(nextStamp);                 }
    if ( last == tail . get () ) {                      } else {
       if ( next == null ) {                              tail . CAS ( last , next ,
                                                            lastStamp , lastStamp +1) ;
                                                        }
                                                    }
                                                }
                                            }



      • Enlace Físico con Incremento: El primer CAS exige que el sello de next
        coincida con el leído (que debería ser null). Al enlazar el nodo, incrementa
        su versión para alertar a otros hilos.
```

## Página 69

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=69)

```text
Método enq() empleando Sellos de Versión

public void enq ( T value ) {                           if (last.next.CAS(next, node,
  Node node = new Node ( value ) ;                            nextStamp, nextStamp+1)) {
  int [] lastStamp = new int ;                               tail.CAS(last, node,
  int [] nextStamp = new int ;
  while ( true ) {                                               lastStamp, lastStamp+1);
    Node last = tail.get(lastStamp);
    Node next = last.next.get(nextStamp);                    return ;
    if ( last == tail . get () ) {                        }
       if ( next == null ) {                            } else {
                                                          tail . CAS ( last , next ,
                                                            lastStamp , lastStamp +1) ;
                                                        }
                                                    }
                                                }
                                            }



      • El productor (o un ayudante) avanza el puntero tail validando su sello
        original e incrementándolo en +1.
```

## Página 70

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=70)

```text
Cola síncrona (capacidad 0)




 Rendezvous
   • Uno o más productores generan elementos para ser removidos, en orden
     FIFO, por uno o más hilos consumidores.
   • Los productores y los consumidores realizan un rendezvous: un productor
     que coloca un elemento en la cola se bloquea hasta que dicho elemento sea
     removido por un consumidor, y viceversa.
```

## Página 71

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=71)

```text
Cola Síncrona (Monitor): Representación

   public class SynchronousQueue <T > {
     T item = null ;
     boolean enqueuing ;
     Lock lock ;
     Condition condition ;


  Wake-ups espúreos
    • Al usar una única condición, cada notificación despierta a productores y a
      consumidores.
    • Se puede mitigar usando varias condiciones, pero se debe bloquear toda la
      estructura.
```

## Página 72

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=72)

```text
Cola Síncrona (Monitor): Método enq()


public void enq ( T value ) {      • Si hay otro productor esperando, se
  lock . lock () ;
  try {                              bloquea en la condición.
      while (enqueuing)            • Tras depositar el elemento y
        condition.await();           despertar a los demás hilos, se
      enqueuing = true ;             bloquea hasta que un consumidor
      item = value ;
      condition . signalAll () ;     retire el objeto.
      while (item != null)         • Cuando el encuentro se concreta, el
        condition.await();
      enqueuing = false;             productor libera la bandera
      condition.signalAll();
    } finally {
                                     enqueuing y despierta a los
      lock . unlock () ;             productores en espera.
    }
}
```

## Página 73

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=73)

```text
Cola Síncrona (Monitor): Método enq()


public void enq ( T value ) {      • Si hay otro productor esperando, se
  lock . lock () ;
  try {                              bloquea en la condición.
    while (enqueuing)
      condition.await();
                                   • Tras depositar el elemento y
    enqueuing = true ;               despertar a los demás hilos, se
    item = value ;
    condition . signalAll () ;       bloquea hasta que un consumidor
      while (item != null)           retire el objeto.
        condition.await();         • Cuando el encuentro se concreta, el
      enqueuing = false;             productor libera la bandera
      condition.signalAll();
    } finally {
                                     enqueuing y despierta a los
      lock . unlock () ;             productores en espera.
    }
}
```

## Página 74

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=74)

```text
Cola Síncrona (Monitor): Método enq()


public void enq ( T value ) {      • Si hay otro productor esperando, se
  lock . lock () ;
  try {                              bloquea en la condición.
    while (enqueuing)
      condition.await();
                                   • Tras depositar el elemento y
    enqueuing = true ;               despertar a los demás hilos, se
    item = value ;
    condition . signalAll () ;       bloquea hasta que un consumidor
    while (item != null)             retire el objeto.
      condition.await();
      enqueuing = false;
                                   • Cuando el encuentro se concreta, el
                                     productor libera la bandera
      condition.signalAll();
    } finally {
                                     enqueuing y despierta a los
      lock . unlock () ;             productores en espera.
    }
}
```

## Página 75

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=75)

```text
Cola Síncrona (Monitor): Método deq()


public T deq () {                  • Adquiere la cerradura y, si no hay
  lock . lock () ;
  try {                              ningún elemento disponible, se
       while (item == null)          bloquea esperando a un productor.
         condition.await();        • Remueve la referencia del objeto,
       T t = item ;                  vacía el campo fijándolo en null y
       item = null;
       condition.signalAll();        despierta al productor para
       return t;                     confirmarle que su elemento ya fue
    } finally {
      lock . unlock () ;             tomado.
    }                              • Retorna el valor obtenido.
}
```

## Página 76

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=76)

```text
Cola Síncrona (Monitor): Método deq()


public T deq () {                  • Adquiere la cerradura y, si no hay
  lock . lock () ;
  try {                              ningún elemento disponible, se
     while (item == null)            bloquea esperando a un productor.
        condition.await();
     T t = item ;                  • Remueve la referencia del objeto,
       item = null;                  vacía el campo fijándolo en null y
       condition.signalAll();        despierta al productor para
       return t;                     confirmarle que su elemento ya fue
    } finally {
      lock . unlock () ;             tomado.
    }                              • Retorna el valor obtenido.
}
```

## Página 77

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=77)

```text
Cola Síncrona (Monitor): Método deq()



public T deq () {                  • Adquiere la cerradura y, si no hay
  lock . lock () ;
  try {                              ningún elemento disponible, se
     while (item == null)            bloquea esperando a un productor.
        condition.await();
     T t = item ;                  • Remueve la referencia del objeto,
     item = null;
     condition.signalAll();          vacía el campo fijándolo en null y
       return t;                     despierta al productor para
    } finally {                      confirmarle que su elemento ya fue
      lock . unlock () ;             tomado.
    }
}                                  • Retorna el valor obtenido.
```

## Página 78

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=78)

```text
Estructuras de Datos Duales (DualStructures)


 Concepto General
 Trata enq() y deq() simétricamente. Si un consumidor encuentra la cola vacía,
 divide la operación en dos pasos:
    • Reservación: Inserta un objeto de reservación en la cola indicando que
      espera un productor.
    • Espera Pasiva: El consumidor hace spinning local sobre un espacio vacío
      hasta que un productor deposita el elemento.

 Exclusión Mutua de Tipos
 La cola solo puede contener elementos en espera o reservaciones en espera,
 nunca ambos tipos en simultáneo.
```

## Página 79

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=79)

```text
Estructura Dual: Método enq()

public void enq ( T e ) {                                      } else {
  Node offer = new Node (e , NodeType . ITEM ) ;                 Node n = h . next . get () ;
  while ( true ) {                                               if ( t != tail . get () ||
    Node t = tail . get () , h = head . get () ;                    h != head . get () || n == null ) {
    if ( h == t || t . type == NodeType . ITEM ) {                    continue ;
      Node n = t . next . get () ;                               }
      if ( t == tail . get () ) {                                boolean success =
         if ( n != null ) {                                          n.item.CAS(null, e);
           tail . CAS (t , n ) ;                                 head.CAS(h, n);
         } else if ( t . next . CAS (n , offer ) ) {             if ( success ) return ;
               tail.CAS(t, offer);                             }
                                                           }
               while (offer.item.get() == e);          }
                   h = head . get () ;
                   if ( offer == h . next . get () )   • Si la cola está vacía o contiene otros
                     head . CAS (h , offer ) ;           elementos, el productor enlaza su nodo
                   return ;
               }                                         mediante un CAS, avanza la cola y
           }                                             hace spinning local hasta que un
       }
                                                         consumidor retire el dato.
```

## Página 80

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=80)

```text
Estructura Dual: Método enq()

public void enq ( T e ) {                                      } else {
  Node offer = new Node (e , NodeType . ITEM ) ;                 Node n = h . next . get () ;
  while ( true ) {                                               if ( t != tail . get () ||
    Node t = tail . get () , h = head . get () ;                   h != head . get () || n == null ) {
    if ( h == t || t . type == NodeType . ITEM ) {                    continue ;
      Node n = t . next . get () ;                               }
      if ( t == tail . get () ) {                                  boolean success =
         if ( n != null ) {
           tail . CAS (t , n ) ;                                       n.item.CAS(null, e);
         } else if ( t . next . CAS (n , offer ) ) {
                                                                   head.CAS(h, n);
            tail.CAS(t, offer);
            while (offer.item.get() == e);                         if ( success ) return ;
                                                               }
              h = head . get () ;
              if ( offer == h . next . get () )            }
                                                       }
                head . CAS (h , offer ) ;
              return ;
           }                                           • Consumación de Reservación: Si la
         }
      }
                                                         cola contiene reservaciones, el
                                                         productor toma la primera de la lista e
                                                         intenta depositar el elemento e
```

## Página 81

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=81)

```text
Estructura Dual: Método enq()

public void enq ( T e ) {                                      } else {
  Node offer = new Node (e , NodeType . ITEM ) ;                 Node n = h . next . get () ;
  while ( true ) {                                               if ( t != tail . get () ||
    Node t = tail . get () , h = head . get () ;                    h != head . get () || n == null ) {
    if ( h == t || t . type == NodeType . ITEM ) {                    continue ;
      Node n = t . next . get () ;                               }
      if ( t == tail . get () ) {                                boolean success =
         if ( n != null ) {                                          n.item.CAS(null, e);
           tail . CAS (t , n ) ;                                   head.CAS(h, n);
         } else if ( t . next . CAS (n , offer ) ) {
            tail.CAS(t, offer);                                    if ( success ) return ;
            while (offer.item.get() == e);                     }
              h = head . get () ;                          }
              if ( offer == h . next . get () )        }
                head . CAS (h , offer ) ;
              return ;                                 • Avanzar Cabeza: Sin importar si el
           }
         }                                               CAS del dato tuvo éxito o fue ganado
      }                                                  por otro productor concurrente, el hilo
                                                         actual colabora avanzando el puntero
```

## Página 82

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=82)

```text
Estructuras LIFO: Introducción a las Pilas Concurrentes

  Definición de la Clase Stack<T>
  Una pila es una colección de elementos (de tipo T) que expone los métodos
  push() y pop() bajo la propiedad estricta LIFO (Last-In-First-Out): el último
  elemento en ingresar es el primero en ser removido.

  El Desafío de la Concurrencia
  A primera vista, las pilas parecen ofrecer poca posibilidad de optimización con-
  currente debido a su estructura:
    • Las inserciones y las extracciones necesitan sincronizar en un único punto
       crítico: el tope de la pila
    • Esta centralización genera un alto nivel de contención en escenarios de
       alta concurrencia.
```

## Página 83

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=83)

```text
Pila (libre de lock): Operación push(a)
                            top




                                  b       c

                                              null




   • Estado inicial
```

**Información gráfica:** [consultar el diagrama o tabla de la página 83](teorica-05-pilas-colas-y-problema-aba.pdf#page=83). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 84

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=84)

```text
Pila (libre de lock): Operación push(a)
                                 top




                          a            b            c

                                                        null




   • Se asigna memoria para el nuevo nodo con el valor a y se fija el link a top.
     No se cambi top.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 84](teorica-05-pilas-colas-y-problema-aba.pdf#page=84). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 85

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=85)

```text
Pila (libre de lock): Operación push(a)
                                 top




                          a            b            c

                                                        null




   • Se ejecuta un CAS para modificar a top. Al tener éxito, la referencia global
     se desplaza hacia a, consumando la inserción.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 85](teorica-05-pilas-colas-y-problema-aba.pdf#page=85). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 86

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=86)

```text
Pila (libre de lock): Operación pop()
                           top




                          a            b            c

                                                        null




   • Al invocar pop() se lee el tope actual de la pila.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 86](teorica-05-pilas-colas-y-problema-aba.pdf#page=86). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 87

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=87)

```text
Pila (libre de lock): Operación pop()
                         top




                         a           b         c

                                                   null




   • Se almacena el valor a del nodo y next.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 87](teorica-05-pilas-colas-y-problema-aba.pdf#page=87). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 88

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=88)

```text
Pila (libre de lock): Operación pop()
                           top




                          a            b            c

                                                        null




   • Intenta adelantar la referencia global ejecutando CAS. Si tiene éxito, el nodo
     se elimina de la pila y se retorna su valor.
```

**Información gráfica:** [consultar el diagrama o tabla de la página 88](teorica-05-pilas-colas-y-problema-aba.pdf#page=88). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 89

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=89)

```text
Estructura de la Pila Libre de Bloqueos


  Representación de Componentes
  La clase delega el estado del tope a una referencia atómica e incorpora una
  estrategia de backoff para mitigar la contención en el hardware.


  public class LockFreeStack <T > {               public class Node {
    AtomicReference < Node > top =                  public T value ;
      new AtomicReference < Node >( null ) ;        public Node next ;

      static final int MIN_DELAY = ...;               public Node ( T value ) {
      static final int MAX_DELAY = ...;                 this . value = value ;
                                                        this . next = null ;
      Backoff backoff =                               }
        new Backoff ( MIN_DELAY , MAX_DELAY ) ;   }
  }
```

## Página 90

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=90)

```text
Método push() con Estrategia de Backoff


 protected boolean tryPush ( Node node ) {    • Punto de Linealización: Ocurre
   Node oldTop = top . get () ;
   node . next = oldTop ;                       en el instante en que el método
     return top.compareAndSet(oldTop,node);     compareAndSet dentro de
 }                                              tryPush() redirige exitosamente
 public void push ( T value ) {
                                                la referencia top hacia el nuevo
   Node node = new Node ( value ) ;             nodo.
   while ( true ) {
     if ( tryPush ( node ) ) {                • Manejo de Contención: Si el
       return ;                                 intento de inserción física en
     } else {
        backoff.backoff();                      tryPush() fracasa por colisión
     }                                          concurrente, el hilo se suspende
   }
 }                                              temporalmente mediante el
                                                backoff.
```

## Página 91

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=91)

```text
Método push() con Estrategia de Backoff


 protected boolean tryPush ( Node node ) {   • Punto de Linealización: Ocurre
    Node oldTop = top . get () ;
    node . next = oldTop ;                     en el instante en que el método
   return top.compareAndSet(oldTop,node);      compareAndSet dentro de
 }
                                               tryPush() redirige exitosamente
 public void push ( T value ) {                la referencia top hacia el nuevo
   Node node = new Node ( value ) ;
   while ( true ) {                            nodo.
     if ( tryPush ( node ) ) {
       return ;
                                             • Manejo de Contención: Si el
     } else {                                  intento de inserción física en
             backoff.backoff();                tryPush() fracasa por colisión
         }                                     concurrente, el hilo se suspende
     }
 }                                             temporalmente mediante el
                                               backoff.
```

## Página 92

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=92)

```text
Método pop() con Estrategia de Backoff
protected Node tryPop () throws EmptyException {   • Punto de Linealización: Ocurre
  Node oldTop = top . get () ;
  if ( oldTop == null ) {                              en el instante en que el método
    throw new EmptyException () ;                    compareAndSet dentro de
  }
  Node newTop = oldTop . next ;                        tryPop() redirige exitosamente
 if (top.compareAndSet(oldTop, newTop)) {              la referencia top hacia el nodo
    return oldTop ;                                    sucesor.
  } else {
    return null ;                                    • Manejo de Contención: Si el
  }                                                    intento de extracción fracasa por
}
public T pop () throws EmptyException {               conflicto de concurrencia y
  while ( true ) {                                     retorna null, el método pop()
    Node returnNode = tryPop () ;
    if ( returnNode != null ) {                        suspende temporalmente al hilo
      return returnNode . value ;
    } else {
                                                       en el backoff.
       backoff.backoff();
    }
  }
}
```

## Página 93

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=93)

```text
Método pop() con Estrategia de Backoff
protected Node tryPop () throws EmptyException {   • Punto de Linealización: Ocurre
   Node oldTop = top . get () ;
   if ( oldTop == null ) {                             en el instante en que el método
     throw new EmptyException () ;                   compareAndSet dentro de
  }
   Node newTop = oldTop . next ;                       tryPop() redirige exitosamente
  if (top.compareAndSet(oldTop, newTop)) {             la referencia top hacia el nodo
     return oldTop ;
  } else {                                             sucesor.
     return null ;
  }
                                                     • Manejo de Contención: Si el
}                                                      intento de extracción fracasa por
public T pop () throws EmptyException {
   while ( true ) {                                    conflicto de concurrencia y
     Node returnNode = tryPop () ;                     retorna null, el método pop()
     if ( returnNode != null ) {
        return returnNode . value ;                    suspende temporalmente al hilo
     } else {                                          en el backoff.
            backoff.backoff();
        }
    }
}
```

## Página 94

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=94)

```text
Estrategia de Espera Exponencial (Backoff)

  Principio del backoff
  La estrategia de backoff introduce un retardo antes de permitir que el hilo fallido
  vuelva a reintentar la operación.

  Dinámica Exponencial
    • Retraso Inicial: El hilo se suspende por un período breve configurado por
      MIN_DELAY.
    • Crecimiento: Con cada colisión consecutiva detectada por el CAS, el
      rango de espera se duplica exponencialmente.
    • Límite: El tiempo máximo de suspensión queda acotado estrictamente
      por el valor MAX_DELAY.
```

## Página 95

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=95)

```text
Progreso y correctitud



  Propiedad Lock-Free
  La pila es lock-free: Un hilo falla al completar un método push() o pop()
  únicamente si existen infinitas modificaciones exitosas concurrentes de top.

  Puntos de Linearización
    • push y pop existoso: cuando se ejecuta exitosamente el compareAndSet.
    • top no exitoso: cuando se observa null al leer pop() en una pila vacía.
```

## Página 96

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=96)

```text
El Principio de Eliminación

  El Cuello de Botella Secuencial
  El backoff no resuelve la secuencialidad.
```

## Página 97

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=97)

```text
El Principio de Eliminación

  El Cuello de Botella Secuencial
  El backoff no resuelve la secuencialidad.

  Principio de eliminación
  Un push() seguido inmediatamente por un pop() se cancelan mutuamente: el
  estado de la pila no cambia.
    • un productor puede entregarle su valor directamente a un consumidor
       concurrente sin tocar la pila.
    • Las operaciones se eliminan entre sí.
    • Linearización: El sistema combinado es linearizable.
         • Las llamadas eliminadas se ordenan en el instante exacto en que
           intercambian sus valores.
```

## Página 98

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=98)

```text
Eliminación usando un arreglo



  El EliminationArray
    • Estructura auxiliar que se usa para que productor y consumidor
      intercambien valores.
    • Los hilos eligen posiciones aleatorias en un arreglo para intercambiar.
    • Si la operación no logra eliminarse (no halla socio o encuentra el rol
      equivocado, como un push() con otro push()), intenta en otra posición
      o accede a la pila central.
```

## Página 99

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=99)

```text
Elimination back-off stack




 Idea
   • Usa un EliminationArray como un esquema de back-off
        • Cada hilo accede primero a la LockFreeStack y, si no logra completar su
          llamada , intenta eliminar utilizando el arreglo.
        • Si no consigue eliminarse, vuelve a invocar a la LockFreeStack, y así
          sucesivamente.
```

## Página 100

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=100)

```text
Intercambiador Libre de Bloqueos (LockFreeExchanger)

  Principio de Operación
  Permite a dos hilos intercambiar valores de tipo T. El primer hilo en llegar deposita
  su valor y hace spinning con un tiempo límite (timeout). El segundo hilo toma
  el elemento, deposita el suyo y confirma el encuentro.
```

## Página 101

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=101)

```text
Intercambiador Libre de Bloqueos (LockFreeExchanger)

  Principio de Operación
  Permite a dos hilos intercambiar valores de tipo T. El primer hilo en llegar deposita
  su valor y hace spinning con un tiempo límite (timeout). El segundo hilo toma
  el elemento, deposita el suyo y confirma el encuentro.

  Máquina de Estados Basada en Sellos
    • EMPTY (Vacío): No hay ningún hilo esperando. El canal está libre para
      iniciar un encuentro.
    • WAITING (Esperando): Un hilo llegó primero, depositó su objeto y se
      encuentra esperando un socio.
    • BUSY (Ocupado): Dos hilos están activamente consolidando un
      intercambio en este espacio.
```

## Página 102

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=102)

```text
Estructura de LockFreeExchanger

 Representación
 Una referencia de atómica con sello.
    public class LockFreeExchanger <T > {
      static final int EMPTY = ... , WAITING = ... , BUSY = ...;

        AtomicStampedReference <T > slot =
          new AtomicStampedReference <T >( null , 0) ;
    }

 Variables Globales
   • EMPTY, WAITING, BUSY: Constantes que representan los estados.
   • slot: Referencia de tipo AtomicStampedReference que resguarda el
     elemento actual y cuya estampa registra el estado interno.
```

## Página 103

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=103)

```text
Funcionamiento de LockFreeExchanger (1/2)
                                                                        Caso EMPTY:
  public T exchange ( T myItem ) {                                        • El primer hilo
    int [] stampHolder = { EMPTY };
    while ( true ) {
                                                                            cambia el estado a
      T yrItem = slot . get ( stampHolder ) ;                               WAITING y deposita
      int stamp = stampHolder ;                                             su elemento.
      switch ( stamp ) {
          case EMPTY:                                                     • Queda en un bucle
            if ( slot . CAS ( yrItem , myItem , EMPTY , WAITING ) ) {       de spinning
              while ( true ) {                                              esperando a un socio
                 yrItem = slot . get ( stampHolder ) ;
                 if ( stampHolder == BUSY ) {                               concurrente.
                    slot . set ( null , EMPTY ) ;                         • Cuando el socio
                    return yrItem ;
                 }                                                          cambia el estado a
              }                                                             BUSY, consume el
            }
            break ;                                                         nuevo valor, resetea
          case WAITING : ... // Oculto para analisis                        a EMPTY y retorna.
          case BUSY : break ;
          }
      }
  }
```

## Página 104

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=104)

```text
Funcionamiento de LockFreeExchanger (2/2)

  public T exchange ( T myItem ) {                                   • Ya existe un hilo
    int [] stampHolder = { EMPTY };
    while ( true ) {
                                                                       esperando en la
      T yrItem = slot . get ( stampHolder ) ;                          ranura con su
      int stamp = stampHolder ;                                        elemento guardado.
      switch ( stamp ) {
      case EMPTY : ... // Oculto para analisis                       • El hilo actual intenta
          case WAITING:                                                intercambiar los
            if ( slot . CAS ( yrItem , myItem , WAITING , BUSY ) )     elementos
              return yrItem ;
            break ;                                                    atómicamente
          case BUSY : break ;                                          transicionando de
          }
      }
                                                                       WAITING a BUSY.
  }                                                                  • Si el CAS tiene
                                                                       éxito, se concreta el
                                                                       encuentro y retorna
                                                                       el elemento del rival.
```

## Página 105

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=105)

```text
Soporte de Timeout en LockFreeExchanger
                                                           Gestión de Tiempo:
public T exchange ( T myItem , long timeout , TimeUnit        • Se calcula el límite de tiempo
    unit ) throws TimeoutException {
  long nanos = unit . toNanos ( timeout ) ;
                                                                (timeBound) antes de entrar al
   long timeBound = System.nanoTime() + nanos;
                                                                ciclo.
  int [] stampHolder = { EMPTY };                             • En estado WAITING se espera
  while ( true ) {                                              activamente mientras no se venza
     if (System.nanoTime() > timeBound)                         el plazo.
          throw new TimeoutException () ;
    // ... ( Lectura de slot y switch ) ...
                                                              • Si expira, intenta retirar su oferta
    case EMPTY :                                                restaurando a EMPTY.
    if ( slot . CAS ( yrItem , myItem , EMPTY , WAITING ) ) {
                                                              • Si el CAS de retiro falla, significa
        while (System.nanoTime() < timeBound) {
                                                                que otro hilo lo tomó en el último
          // ... ( Esperando al socio en BUSY ) ...
       }                                                        instante; se acepta el intercambio
         if (slot.CAS(myItem, null, WAITING, EMPTY))            y no se lanza excepción.
           throw new TimeoutException () ;
          else { // otro tomo el elemento
          ...
      }
```

## Página 106

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=106)

```text
Soporte de Timeout en LockFreeExchanger
                                                            Gestión de Tiempo:
public T exchange ( T myItem , long timeout , TimeUnit          • Cálculo inicial: Se calcula el
    unit ) throws TimeoutException {
  long nanos = unit . toNanos ( timeout ) ;
                                                                  límite de tiempo (timeBound)
     long timeBound = System.nanoTime() + nanos;
                                                                  antes de entrar al ciclo.
    int [] stampHolder = { EMPTY };                             • Spinning acotado: En estado
    while ( true ) {                                              WAITING se espera activamente
      // ... ( Lectura de slot y switch ) ...
      case EMPTY :                                                mientras no se venza el plazo.
      if ( slot . CAS ( yrItem , myItem , EMPTY , WAITING ) ) { •
            // ... ( Esperando al socio en BUSY ) ...
                                                                  Cancelación segura: Si expira,
           if (slot.CAS(myItem, null, WAITING, EMPTY))
                                                                  intenta retirar su oferta
             throw new TimeoutException () ;
                                                                  restaurando a EMPTY.
           else { // otro tomo el elemento                      • Carrera crítica: Si el CAS de
              yrItem = slot . get ( stampHolder ) ;
              slot . set ( null , EMPTY ) ;                       retiro falla, significa que un socio
              return yrItem ;                                     lo tomó en el último instante; se
         }
      }
                                                                  acepta el intercambio y no se
      break ;                                                     lanza excepción.
    }
}
```

## Página 107

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=107)

```text
Correctitud y Progreso

  Puntos de Linearización
    • Intercambio Exitoso: Ocurre cuando el segundo hilo cambia con éxito
      el estado de WAITING a BUSY.
    • Intercambio Fallido: Se lineariza en el instante en el que el temporizador
      expira y se lanza la excepción.
```

## Página 108

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=108)

```text
Correctitud y Progreso

  Puntos de Linearización
    • Intercambio Exitoso: Ocurre cuando el segundo hilo cambia con éxito
      el estado de WAITING a BUSY.
    • Intercambio Fallido: Se lineariza en el instante en el que el temporizador
      expira y se lanza la excepción.

  Progreso
    • Lock-Free: Si llamadas concurrentes a exchange() disponen de
      suficiente tiempo solo fallarán si otros intercambios están teniendo éxito
      de forma repetida.
    • Duración del Timeout: Un tiempo de espera demasiado corto puede
      provocar que un hilo nunca logre completar un intercambio.
```

## Página 109

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=109)

```text
Estructura de EliminationArray

 Principio de Diseño
 Un arreglo de objetos LockFreeExchanger donde cada hilo que intenta realizar
 una eliminación selecciona una posición al azar e invoca al método de intercam-
 bio.
```

## Página 110

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=110)

```text
Estructura de EliminationArray

 Principio de Diseño
 Un arreglo de objetos LockFreeExchanger donde cada hilo que intenta realizar
 una eliminación selecciona una posición al azar e invoca al método de intercam-
 bio.

  • Subrango Dinámico: Cada hilo selecciona una posición aleatoria dentro de
    un subrango específico (range). Este rango se ajusta dinámicamente según
    la carga y nivel de contención del sistema.
  • Generador de Números Aleatorios: Cada hilo debe usar su propio
    generador local.
  • Resultado: El método retorna el elemento provisto por otro hilo, o lanza
    una excepción cuando expira el tiempo límite.
```

## Página 111

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=111)

```text
Código de EliminationArray
public class EliminationArray <T > {
  private static final int duration = ...;
  LockFreeExchanger <T >[] exchanger ;                           • Selección de la posición:
                                                                     • Determina la posición en el
    public EliminationArray ( int capacity ) {
      exchanger = ( LockFreeExchanger <T >[])                          arreglo.
        new LockFreeExchanger [ capacity ];
      for ( int i = 0; i < capacity ; i ++) {
        exchanger [ i ] =
          new LockFreeExchanger <T >() ;
      }
    }

    public T visit ( T value , int range )
                     throws Exception {
        int slot = ThreadLocalRandom.current().nextInt(range);
        return exchanger[slot].exchange(value,
           duration, MILLISECONDS);
    }
}
```

## Página 112

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=112)

```text
Código de EliminationArray
public class EliminationArray <T > {
  private static final int duration = ...;
  LockFreeExchanger <T >[] exchanger ;                         • Delegación al Intercambiador:
    public EliminationArray ( int capacity ) {       • Invoca al elemento
      exchanger = ( LockFreeExchanger <T >[])                        LockFreeExchanger ubicado
        new LockFreeExchanger [ capacity ];
      for ( int i = 0; i < capacity ; i ++) {                        en la celda elegida.
        exchanger [ i ] =                                          • Le transfiere el valor local y un
          new LockFreeExchanger <T >() ;
      }
                                                                     tiempo de espera fijo
    }                                                                predefinido para resolver el
                                                                     encuentro de eliminación.
    public T visit ( T value , int range )
                      throws Exception {
      int slot = ThreadLocalRandom.current()
        return exchanger[slot].exchange(value,

           duration, MILLISECONDS);
    }
}
```

## Página 113

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=113)

```text
Pila con Eliminación y Backoff (EliminationBackoffStack)

  Principio de Operación
  Es una subclase de LockFreeStack que sobrescribe push() y pop(). En lugar
  de usar un retardo cuando falla el acceso a la pila, utiliza un EliminationArray
  para intentar cruzar las operaciones concurrentes.

   • Asimetría: Al invocar visit(), los hilos productores envían el elemento de
     tipo T, mientras que los consumidores envían la referencia null.
   • Verificación:
        • Si un push() concreta el intercambio y recibe null, finaliza con éxito.
        • Si un pop() concreta el intercambio y recibe un valor distinto de null,
          obtiene el dato del productor y finaliza.
   • Política de Rango (RangePolicy): Un objeto local por hilo regula
     dinámicamente el subrango de colisión. El rango se reduce si ocurren
     timeouts y se expande ante encuentros exitosos.
```

## Página 114

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=114)

```text
Método push() con Eliminación

 public void push ( T value ) {
   RangePolicy rangePolicy = policy . get () ;                  • Intento Alternativo:
   Node node = new Node ( value ) ;
   while ( true ) {                                                 • Si la inserción directa en el
     if ( tryPush ( node ) ) {                                        tope con tryPush() falla usa
       return ;
     } else try {                                                     el eliminationArray
              T otherValue = eliminationArray                         entregando su valor usando el
                                                                      rango que le dicta su política
                 .visit(value, rangePolicy.getRange());
                                                                      local.
               if (otherValue == null) {
                  rangePolicy.recordSuccess();
                  return ; // Exchanged with pop
               }
             } catch ( TimeoutException ex ) {
                rangePolicy . recordTimeout () ;
         }
     }
 }
```

## Página 115

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=115)

```text
Método push() con Eliminación

 public void push ( T value ) {
   RangePolicy rangePolicy = policy . get () ;                  • Intercambio Exitoso:
   Node node = new Node ( value ) ;
   while ( true ) {                                                 • Si el intercambiador le devuelve
     if ( tryPush ( node ) ) {                                        null, el hilo confirma que un
       return ;
     } else try {                                                     consumidor tomó su nodo.
        T otherValue = eliminationArray                             • Registra el éxito en la política
          .visit(value, rangePolicy.getRange());
                                                                      de rango y retorna sin haber
              if (otherValue == null) {
                                                                      tocado la pila central.
                  rangePolicy.recordSuccess();
                 return ; // Exchanged with pop
               }
             } catch ( TimeoutException ex ) {
               rangePolicy . recordTimeout () ;
         }
     }
 }
```

## Página 116

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=116)

```text
Método pop() con Eliminación

 public T pop () throws Exception {
   RangePolicy rangePolicy = policy . get () ;              • Si tryPop() falla debido a colisiones
   while ( true ) {
     Node returnNode = tryPop () ;
                                                              concurrentes en el tope de la pila, el
     if ( returnNode != null ) {                              consumidor redirige su esfuerzo
       return returnNode . value ;                            hacia el arreglo de eliminación.
     } else try {
            T otherValue = eliminationArray
                                                            • Invoca visit() ofreciendo null
                                                              como carnada para capturar un
            .visit(null, rangePolicy.getRange());
                                                              productor.
            if (otherValue != null) {
              rangePolicy.recordSuccess();
              return otherValue ;
           }
         } catch ( TimeoutException ex ) {
           rangePolicy . recordTimeout () ;
         }
     }
 }
```

## Página 117

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=117)

```text
Método pop() con Eliminación

 public T pop () throws Exception {
   RangePolicy rangePolicy = policy . get () ;              • Si obtiene una referencia de datos
   while ( true ) {
     Node returnNode = tryPop () ;
                                                              válida (distinta de null), la
     if ( returnNode != null ) {                              eliminación mutua se ha concretado.
       return returnNode . value ;
     } else try {
                                                            • Notifica la efectividad de la ranura a
        T otherValue = eliminationArray                       su política y retorna directamente el
        .visit(null, rangePolicy.getRange());
                                                              valor extraído.
            if (otherValue != null) {

               rangePolicy.recordSuccess();
             return otherValue ;
           }
         } catch ( TimeoutException ex ) {
           rangePolicy . recordTimeout () ;
         }
     }
 }
```

## Página 118

[Ver página original](teorica-05-pilas-colas-y-problema-aba.pdf#page=118)

```text
Linearizabilidad


  Puntos de Linealización
    • Acceso a la Pila Central: Cualquier llamada exitosa a push() o pop()
      que complete operando sobre LockFreeStack se lineariza en el cuando
      hace la modificación en la pila.
    • Operaciones Eliminadas: Cualquier par de llamadas concurrentes que
      colisionen y se eliminen en el arreglo se linearizan en el momento en que
      intercambian sus valores.
    • Independencia: Las operaciones completadas por eliminación no afectan
      la linearizabilidad de la pila central. Podrían haber ocurrido en cualquier
      estado de la LockFreeStack y, tras suceder, el estado de la pila central
      permanece idéntico.
```
