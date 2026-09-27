# practica-04-listas-y-skip-lists-concurrentes — transcripción

- Fuente: [practica-04-listas-y-skip-lists-concurrentes.pdf](practica-04-listas-y-skip-lists-concurrentes.pdf)
- Páginas del PDF: 45.
- SHA-256 del PDF: `25a78f596010de947209a44ed8dc1efebd1980305cc423e0fb8e7b8fe2ea57ed`.
- Tipo: transcripción textual por página, con disposición espacial conservada y referencias al original para esquemas.

> Se conserva el contenido del original, incluidos ejemplos incorrectos, erratas y diapositivas progresivas. Los bloques `text` preservan columnas y sangría; no son código listo para compilar. En código repartido en columnas, seguir las indicaciones «continúa a la derecha». Las notas editoriales se distinguen del original. Para símbolos cuya extracción sea ambigua, consultar la página enlazada.

## Página 1

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=1)

```text
Programación concurrente y paralela
Práctica 4 - Listas y Skip Lists
Concurrentes

Segundo Cuatrimestre 2026
```

## Página 2

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=2)

```text
Repaso
```

## Página 3

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=3)

```text
Lock-Free, Wait-Free




        • Buscamos definir estructuras de datos que permitan admitan alguna
           equivalencia con el comportamiento secuencial.
        • En particular vimos la Consistencia Secuencial, la posibilidad de
           ordenar los eventos para representar comportamiento secuencial.
        • También, más fuerte, la lineazabilidad, poder definir un momento en
           la ejecución de nuestras operaciones donde consideramos que se
           realizó la acción.




Programación concurrente y paralela                                             3/45
```

## Página 4

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=4)

```text
Lock-Free, Wait-Free



     Podemos asignar el siguiente atributo a nuestras estructuras:
        • Una operación es Wait-Free si sabemos que siempre finalizará en
           una cantidad acotada de pasos.
        • Una estructura es Wait-Free si todas sus operaciones son Wait-free.
        • Una estructura es Lock-Free cuando siempre hay al menos algún
           proceso ejecutando una operación que terminará en una cantidad
           acotada de pasos (“hay progreso global”).




Programación concurrente y paralela                                             4/45
```

## Página 5

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=5)

```text
Implementación de Conjuntos con Listas




     Repasamos las diferentes implementaciones de conjuntos sobre listas:




Programación concurrente y paralela                                         5/45
```

## Página 6

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=6)

```text
Implementación de Granulado Grueso




     Se aplica un sobre toda la lista para operar (se puede hacer directamente
     con métodos synchronized)
     (obs: El entry set no es ordenado en Java, puede haber starvation)




Programación concurrente y paralela                                              6/45
```

## Página 7

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=7)

```text
Implementación de Granulado Fino




     Cada elemento tiene un Lock.

     Se toman los locks del elemento a usar y el anterior, la toma de locks se
     hace con el método hand over hand.




Programación concurrente y paralela                                              7/45
```

## Página 8

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=8)

```text
Implementación Optimista




     No se toman los locks hasta llegar a los elementos buscados, se verifica
     que una vez tomados los locks se tenga consistencia recorriendo la lista
     nuevamente.
     (obs: En este esquema hay threads que pueden quedar en starvation)




Programación concurrente y paralela                                             8/45
```

## Página 9

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=9)

```text
Implementación Lazy




     Se agrega un marcador de “borrado” a los nodos; esto evita tener que
     rechequear la lista para validarla (pero no impide que tengamos que
     volver a recorrerla si debemos reintentar).




Programación concurrente y paralela                                         9/45
```

## Página 10

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=10)

```text
Extra: Implementación “Granulado grueso lazy”




        • Idea: Puedo usar el concepto “Optimista” en granulado grueso, es
           decir, buscar los nodos que necesito, y luego tomar el lock de la lista.
        • El problema es, ¿No hace esto que de todas maneras tenga que
           re-chequear una vez tomado el lock?




Programación concurrente y paralela                                                   10/45
```

## Página 11

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=11)

```text
Extra: Implementación “Granulado grueso lazy”


     Podemos resolver esto con un nuevo atributo en la lista que sea el
     número de versión. Cada acción de agregado o borrado incrementa el
     número de versión.

     Luego, miramos la versión antes de la búsqueda, encontramos, tomamos
     el lock, y chequeamos que el número de versión sea el mismo que
     teníamos.

     La solución finalmente no gana tanto respecto al granulado grueso
     debido a que una modificación, aunque sea en elementos sin relación al
     que estamos operando, incrementa el número de versión.


Programación concurrente y paralela                                           11/45
```

## Página 12

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=12)

```text
Implementación Lock-Free




     Se eliminan los locks; se utiliza una instrucción atómica que, en una
     misma operación, verifica que una referencia es válida y a su vez chequea
     (y setea) el valor de un booleano: CompareAndSet.

     Usando esta instrucción, se puede garantizar que se está actualizando un
     nodo no-borrado y que apunta a la referencia correcta.




Programación concurrente y paralela                                              12/45
```

## Página 13

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=13)

```text
Skip Lists
```

## Página 14

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=14)

```text
Ventajas de Skip lists en concurrencia




        • Las estructuras arbóreas que preservan altura (por ej. AVLs) tienen
           que hacer rotaciones incrementando la contencion.
        • La idea es “simular” un arbol con una lista multinivel
        • Esto nos va a dar complejidad esperada O(log n) (donde n es la
           cantidad de elementos de la lista)




Programación concurrente y paralela                                             14/45
```

## Página 15

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=15)

```text
Skip lists, implementaciones




     Vamos a ver las ideas de dos implementaciones de Skip Lists:
        • LazySkipList, similar a la implementación Lazy de listas
        • LockFreeSkipList, similar a la implementación Lock-Free de listas

     En ambas, contains() es wait-free




Programación concurrente y paralela                                           15/45
```

## Página 16

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=16)

```text
Skip lists, ideas generales




     Mantenemos las reglas de la lista
        • Tenemos un hash perfecto (no hay dos objetos con el mismo hash)
        • La lista se mantiene ordenada por key (resultado de la f. hash)
        • Tenemos nodos centinelas head y tail con key MAX_INT y MIN_INT




Programación concurrente y paralela                                         16/45
```

## Página 17

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=17)

```text
Skip lists, reglas




        • Tenemos múltiples listas cada una con un nivel
        • skiplist property: Toda lista del nivel n + 1 está contenida en la lista de
           nivel n
        • Usamos el mismo nodo, que tiene referencias al próximo para cada
           nivel en el que está
        • Las listas de niveles superiores funcionan como “atajos” (skips) para
           recorrer la lista completa (la del nivel 0)




Programación concurrente y paralela                                                     17/45
```

**Información gráfica:** [consultar el diagrama o tabla de la página 17](practica-04-listas-y-skip-lists-concurrentes.pdf#page=17). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 18

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=18)

```text
Skip lists, reglas




     En esta versión ideal con 8 nodos, hay exactamente 1 de nivel 2 y 3 de
     nivel 1. Para buscar, empiezo en el nivel superior y voy bajando a medida
     que me voy pasando.

Programación concurrente y paralela                                              18/45
```

**Descripción editorial del esquema:** La skip list ordena las claves 2,5,8,9,11,15,18,25 entre centinelas -infinito y +infinito. El nivel 0 contiene todos los nodos; los niveles superiores enlazan subconjuntos y permiten saltos más largos. La búsqueda comienza arriba y desciende cuando el siguiente salto se pasa.

**Información gráfica:** [consultar el diagrama o tabla de la página 18](practica-04-listas-y-skip-lists-concurrentes.pdf#page=18). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 19

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=19)

```text
Skip lists, probabilidad



     En la práctica, las skip lists son probabilísitcas.
        • Se busca que la altura H sea el logaritmo de la longitud media
           esperado
        • Al insertar un nodo defino cuál es su nivel máximo i (0 < i ≤ H)
        • Fijando una probabilidad p (usualmente 0.5), el nivel máximo del
           nodo es i con probabilidad pi .
        • Si un nodo está en el nivel i, está en todo nivel 0 ≤ j < i
        • head y tail tienen altura H.



Programación concurrente y paralela                                          19/45
```

## Página 20

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=20)

```text
find y add




     Para recorrer la lista nos apoyamos en un método find, que no sólo nos
     dice si el elemento pertenece a la lista, también nos dice el nivel en el que
     lo encontró y, más importante, sus predecesores en todos los niveles y sus
     sucesores en niveles más altos.




Programación concurrente y paralela                                                  20/45
```

**Información gráfica:** [consultar el diagrama o tabla de la página 20](practica-04-listas-y-skip-lists-concurrentes.pdf#page=20). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 21

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=21)

```text
find y add




Programación concurrente y paralela   21/45
```

**Información gráfica:** [consultar el diagrama o tabla de la página 21](practica-04-listas-y-skip-lists-concurrentes.pdf#page=21). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 22

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=22)

```text
Representación de conjuntos con Skip List




     Como en la clase teórica, representamos conjuntos por medio de skip
     lists.




Programación concurrente y paralela                                        22/45
```

## Página 23

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=23)

```text
Implementación 1: Lazy



        • Como en listas lazy, se toman los locks y se verifica que no hayan
           habido cambios
        • Los nodos tienen dos booleanos: El marcador para borrado y
           fullyLinked, que indica que el nodo está seteado en todos su
           predecesores

     Inv. Rep: Un valor está en el conjunto si y sólo si la skiplist tiene un nodo
     sin marcar y fully linked cuya key es el hash del valor.




Programación concurrente y paralela                                                  23/45
```

## Página 24

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=24)

```text
Skip list Lazy: definición


  1 public final class LazySkipList<T> {              21 // constructor sentinela
  2   static final int MAX_LEVEL = ...;              22      public Node(int key) {
  3   final Node<T> head =                           23        this.item = null;
  4     new Node<T>(Integer.MIN_VALUE);              24        this.key = key;
  5   final Node<T> tail =                           25        next = new Node[MAX_LEVEL + 1];
  6     new Node<T>(Integer.MAX_VALUE);              26        topLevel = MAX_LEVEL;
  7   public LazySkipList() {                        27      }
  8     for (int i = 0; i < head.next.length; i++)   28
                {                                    29      public Node(T x, int height) {
   9            head.next[i] = tail;                 30        item = x;
 10         }                                         31       key = x.hashCode();
   11   }                                            32        next = new Node[height + 1];
  12                                                 33        topLevel = height;
  13    private static final class Node<T> {         34      }
 14       final Lock lock = new ReentrantLock();     35
  15      final T item;                              36      public void lock() {
 16       final int key;                             37        lock.lock();
 17       final Node<T>[] next;                      38      }
 18       volatile boolean marked = false;           39
 19       volatile boolean fullyLinked = false;      40      public void unlock() {
 20       private int topLevel;                      41        lock.unlock();
                                                     42      }
                                                     43    }
                                                     44 }



Programación concurrente y paralela                                                              24/45
```

## Página 25

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=25)

```text
Skip list Lazy: find



        • Recorre la lista usando prev y curr desde el nivel más alto, sin tomar
           locks.
        • Si el elemento no se encuentra en el nivel actual, baja (recorre desde
           prev un nivel más abajo), recordando el predecesor y el sucesor en el
           nivel actual (se le pasan dos arrays donde se guarda esta
           información).
        • Si encuentra el elemento, informa el nivel donde lo encontró (i.e., la
           altura del elemento).
        • Si llega al nivel 0 sin encontrar el elemento, devuelve −1.



Programación concurrente y paralela                                                25/45
```

## Página 26

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=26)

```text
Skip list Lazy: find



       1 int find(T x, Node<T>[] preds, Node<T>[] succs) {
      2    int key = x.hashCode();
      3    int lFound = -1;
      4    Node<T> pred = head;
      5    for (int level = MAX_LEVEL; level >= 0;
      6      level--) {
      7      volatile Node<T> curr = pred.next[level];
      8      while (key > curr.key) {
      9        pred = curr; curr = pred.next[level];
     10      }
      11     if (lFound == -1 && key == curr.key) {
     12        lFound = level;
     13      }
     14      preds[level] = pred;
     15      succs[level] = curr;
     16    }
     17    return lFound;
     18 }




Programación concurrente y paralela                          26/45
```

## Página 27

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=27)

```text
Skip list Lazy: contains




        • Se basa en find: Devuelve true en tanto find encuentra el nodo, y
           este está fullyLinked, y no está marcado.
        • No adquiere locks ni reinicia la búsqueda.




Programación concurrente y paralela                                           27/45
```

## Página 28

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=28)

```text
Skip list Lazy: contains




      1 boolean contains(T x) {
      2   Node<T>[] preds =
      3     (Node<T>[]) new Node[MAX_LEVEL + 1];
      4   Node<T>[] succs =
      5     (Node<T>[]) new Node[MAX_LEVEL + 1];
      6   int lFound = find(x, preds, succs); //linealizacion en el find
      7   return (lFound != -1
      8     && succs[lFound].fullyLinked
      9     && !succs[lFound].marked);
     10 }




Programación concurrente y paralela                                        28/45
```

## Página 29

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=29)

```text
Skip list Lazy: add


        • Usa find para ver si el elemento ya existe, de hacerlo y no estar
           marcado devuelve false; si cuando lo encuentra no tiene prendido
           el flag fullyLinked, lo espera.
        • Si el elemento existe pero está marcado, alguien lo está borrando,
           empieza de nuevo
        • Nivel por nivel (empezando del 0), toma los locks del predecesor en
           cada nivel, y verifica que no estén marcados y que el predecesor
           apunte al sucesor. Si este chequeo falla, se empieza de nuevo.
        • Tomados los locks, crea el nodo, actualiza referencias, y marca el flag
           fullyLinked.
        • Finalmente, libera los locks y devuelve true.

Programación concurrente y paralela                                                 29/45
```

## Página 30

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=30)

```text
Skip list Lazy: add


    1 boolean add(T x) {                                 26           succ = succs[level];
   2    int topLevel = randomLevel();                    27           pred.lock.lock();
   3    Node<T>[] preds =                                28           highestLocked = level;
   4      (Node<T>[]) newNode[MAX_LEVEL + 1];           29           valid = !pred.marked
   5    Node<T>[] succs =                                30             && !succ.marked
   6      (Node<T>[]) newNode[MAX_LEVEL + 1];            31            && pred.next[level]==succ;
   7    while (true) {                                   32           }
   8      int lFound = find(x, preds, succs);            33           if (!valid) continue;
   9      if (lFound != -1) {                            34           Node<T> newNode = newNode(x, topLevel);
 10         Node<T> nodeFound = succs[lFound];           35           for (int level = 0;
   11       if (!nodeFound.marked) {                     36             level <= topLevel; level++)
  12          while (!nodeFound.fullyLinked) {}          37             newNode.next[level] = succs[level];
  13          return false; //linealiza fallo            38           for (int level = 0;
 14         }                                            39             level <= topLevel; level++)
  15        continue; //el nodo estaba marcado           40             preds[level].next[level] = newNode;
 16       }                                              41           newNode.fullyLinked = true; //linealiza exito
 17       int highestLocked = -1;                        42           return true;
 18       try {                                          43         } finally {
 19         Node<T> pred, succ;                          44           for (int level = 0;
 20         boolean valid = true;                        45             level <= highestLocked; level++)
  21        for (int level = 0;                          46             preds[level].unlock();
 22           valid && (level <= topLevel); level++) {   47         }
 23           pred = preds[level];                       48     }
                                                         49 }



Programación concurrente y paralela                                                                                   30/45
```

## Página 31

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=31)

```text
Skip list Lazy: remove

        • Usa find para encontrar el elemento y chequea que tenga
          fullyLinked, no esté marcado, y que su topLevel coincida con el
          nivel donde find lo encontró. Si no, falla.
        • Toma el lock del elemento, y valida que aún no esté marcado. Lo
          marca.
        • Va subiendo nivel por nivel (hasta el nivel del elemento) tomando el
          lock del predecesor y verificando que este no esté marcado y que
          apunte al elemento a borrar. Si algo de esto falla, libera los locks de
          los predecesores tomados (no el del elemento a borrar) y comienza
          de nuevo.
        • Si consigue los locks, va de arriba hacia abajo reemplazando las
          referencias al próximo, haciendo efectivo el borrado.
        • Finalmente, libera los locks y devuelve true.
Programación concurrente y paralela                                                 31/45
```

## Página 32

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=32)

```text
Skip list Lazy: remove


    1 boolean remove(T x) {                           26     int highestLocked = -1;
   2    Node<T> victim = null;                        27     try {
   3    boolean isMarked = false;                     28       Node<T> pred, succ; boolean valid = true;
   4    int topLevel = -1;                            29       for (int level = 0; valid && (level <= topLevel);
   5    Node<T>[] preds =                             30         level++) {
   6      (Node<T>[]) new Node[MAX_LEVEL + 1];         31        pred = preds[level];
   7    Node<T>[] succs =                             32         pred.lock.lock();
   8      (Node<T>[]) new Node[MAX_LEVEL + 1];        33         highestLocked = level;
   9    while (true) {                                34         valid = !pred.marked
 10       int lFound = find(x, preds, succs);         35           && pred.next[level]==victim;
   11     if (lFound != -1) victim = succs[lFound];   36       }
  12      if (isMarked ||                             37       if (!valid) continue;
  13          (lFound != -1 &&                        38       for (int level = topLevel; level >= 0; level--) {
 14           (victim.fullyLinked                     39         preds[level].next[level] = victim.next[level];
  15          && victim.topLevel == lFound            40       }
 16           && !victim.marked))) {                  41       victim.lock.unlock();
 17         if (!isMarked) {                          42       return true;
 18           topLevel = victim.topLevel;             43       } finally {
 19           victim.lock.lock();                     44         for (int i = 0; i <= highestLocked; i++) {
 20           if (victim.marked) {                    45           preds[i].unlock();
  21            victim.lock.unlock();                 46         }
 22             return false; //linealiza fallo       47       }
 23           }                                       48         } else return false; //linealiza fallo
 24         victim.marked = true; //linealiza exito   49   }
 25         isMarked = true;                          50 }
 26       }
Programación concurrente y paralela                                                                                32/45
```

## Página 33

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=33)

```text
Skip list Lazy




Programación concurrente y paralela   33/45
```

**Información gráfica:** [consultar el diagrama o tabla de la página 33](practica-04-listas-y-skip-lists-concurrentes.pdf#page=33). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 34

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=34)

```text
Implementación 2: Lock-Free




        • Trae a Skip List las ideas de la implementación Lock-Free de listas.
        • Usa referencias atómicamente marcables y CompareAndSet.
        • Como no vamos a lockear, no podemos cambiar todos los niveles a la
           vez y por lo tanto romperemos la propiedad skiplist (podemos tener
           nodos que saltean niveles). No hay fullyLinkedFlag.
        • Inv. Rep: Un elemento está en el conjunto si y sólo si está (es
           alcanzable) en el nivel 0 de la estructura y no está marcado.




Programación concurrente y paralela                                              34/45
```

## Página 35

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=35)

```text
Implementación 2: Lock-Free




        • Los métodos add y remove se comportan como si cada nivel fuera su
           propia lista Lazy.
        • El método find se encarga de hacer el borrado efectivo de nodos
           marcados




Programación concurrente y paralela                                           35/45
```

**Información gráfica:** [consultar el diagrama o tabla de la página 35](practica-04-listas-y-skip-lists-concurrentes.pdf#page=35). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 36

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=36)

```text
Skip list Lock-Free: definición


  1 public final class LockFreeSkipList<T> {             20 //constructor para sentinelas
 2    static final int MAX_LEVEL = ...; //depende         21    public Node(int key) {
 3    final Node<T> head =                               22       value = null; key = key;
 4      new Node<T>(Integer.MIN_VALUE);                  23       next = (AtomicMarkableReference<Node<T>>[])
 5    final Node<T> tail =                               24         new AtomicMarkableReference[MAX_LEVEL + 1];
 6      new Node<T>(Integer.MAX_VALUE);                  25       for (int i = 0; i < next.length; i++) {
 7    public LockFreeSkipList() {                        26         next[i] = new AtomicMarkableReference<Node<T>>(
 8      for (int i = 0; i < head.next.length;                    null,false);
 9        i++) {                                         27       }
10        head.next[i] =                                 28       topLevel = MAX_LEVEL;
 11         new AtomicMarkableReference<                 29     }
12          LockFreeSkipList.Node<T>>(tail, false);      30 //constructor para nodos regulares
13      }                                                 31    public Node(T x, int height) {
14    }                                                  32       value = x;
15                                                       33       key = x.hashCode();
16    public static final class Node<T> {                34       next = (AtomicMarkableReference<Node<T>>[])
17      final T value; final int key;                    35         new AtomicMarkableReference[height + 1];
18      final AtomicMarkableReference<Node<T>>[] next;   36       for (int i = 0; i < next.length; i++) {
19      private int topLevel;                            37         next[i] = new AtomicMarkableReference<Node<T>>(
                                                                 null,false);
                                                         38       }
                                                         39       topLevel = height;
                                                         40     }
                                                         41   }
                                                         42 }


Programación concurrente y paralela                                                                              36/45
```

## Página 37

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=37)

```text
Skip list Lock-Free: add




        • Busca el elemento y los vectores de predecesores y sucesores con
           find (similar a la Lazy). De encontrarlo, devuelve false.
        • Si no, crea el nodo, setea sus sucesores, e intenta agregarlo al nivel 0
           con CompareAndSet. Si falla comienza de nuevo (encontró un nodo
           marcado, find lo eliminará).
        • Hecho esto, repite con los niveles superiores (hasta el nivel asginado
           al nodo)




Programación concurrente y paralela                                                  37/45
```

## Página 38

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=38)

```text
Skip list Lock-Free: add



    1 boolean add(T x) {                               19       Node<T> pred = preds[bottomLevel];
   2    int topLevel = randomLevel();                  20       Node<T> succ = succs[bottomLevel];
   3    int bottomLevel = 0;                            21      if (!pred.next[bottomLevel].compareAndSet(
   4    Node<T>[] preds =                              22        succ, newNode, false, false)) { //l. exito
   5      (Node<T>[]) newNode[MAX_LEVEL + 1];         23        continue;
   6    Node<T>[] succs =                              24       }
   7      (Node<T>[]) newNode[MAX_LEVEL + 1];         25       for (int level = bottomLevel+1;
   8    while (true) {                                 26         level <= topLevel; level++) {
   9      boolean found = find(x, preds, succs);       27         while (true) {
  10      if (found) { //linealizacion fallo           28           pred = preds[level];
   11       return false;                              29           succ = succs[level];
  12      } else {                                     30           if (pred.next[level].compareAndSet(
  13        Node<T> newNode = newNode(x, topLevel);    31            succ, newNode, false, false))
  14        for (int level = bottomLevel; level <=     32             break;
          topLevel;                                    33           find(x, preds, succs);
  15        level++) {                                 34         }
  16        Node<T> succ = succs[level];               35       }
  17        newNode.next[level].set(succ, false);      36       return true;
  18      }                                            37     }
                                                       38   }
                                                       39 }




Programación concurrente y paralela                                                                           38/45
```

## Página 39

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=39)

```text
Skip list Lock-Free: remove



        • Primero, invoca a find para determinar la existencia del elemento en
           el nivel 0; si no, devuelve falso.
        • Si lo encuentra, para cada nivel superior al 0 (bajando desde la altura
           del nodo) utiliza CompareAndSet para marcar la referencia al
           siguiente en el nivel (reintentando hasta lograrlo)
        • Finalmente, hace lo mismo en el nivel 0, quedándose con el valor de
           retorno de CaS para verificar si el borrado fue suyo.
        • No hace borrado físico, de eso se encargará find.



Programación concurrente y paralela                                                 39/45
```

## Página 40

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=40)

```text
Skip list Lock-Free: remove


     1 boolean remove(T x) {                               26         boolean[] marked = {false};
    2    int bottomLevel = 0;                              27         succ = nodeToRemove.next[bottomLevel].get(
    3    Node<T>[] preds =                                            marked);
    4      (Node<T>[]) new Node[MAX_LEVEL + 1];            28         while (true) {
    5    Node<T>[] succs =                                 29           //l. exito
    6      (Node<T>[]) new Node[MAX_LEVEL + 1];            30           boolean iMarkedIt =
    7    Node<T> succ;                                      31            nodeToRemove.next[bottomLevel]
    8    while (true) {                                    32             .compareAndSet(succ, succ,false, true);
    9      boolean found = find(x, preds, succs);          33           succ = succs[bottomLevel].next[
  10       if (!found) { //l. fallo                                   bottomLevel].get(marked);
    11       return false;                                 34           if (iMarkedIt) {
   12      } else {                                        35             find(x, preds, succs);
   13        Node<T> nodeToRemove = succs[bottomLevel];    36             return true;
  14         for (int level = nodeToRemove.topLevel;       37           }
   15          level >= bottomLevel+1; level--) {          38           else if (marked[0]) return false;
  16           boolean[] marked = {false};                 39         }
  17           succ = nodeToRemove.next[level]             40     }
  18             .get(marked);                             41   }
  19           while (!marked[0]) {                        42 }
  20             nodeToRemove.next[level].compareAndSet(
   21              succ, succ, false, true);
  22             succ = nodeToRemove.next[level]
  23             .get(marked);
  24           }
  25         }

Programación concurrente y paralela                                                                                 40/45
```

## Página 41

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=41)

```text
Skip list Lock-Free: find




        • Busca el nodo con el elemento bajando desde el nivel más alto.
        • Como en el caso anterior, va manteniendo los arrays de predecesores
           y sucesores.
        • Si encuentra una referencia con marca, significa que el nodo actual
           debe ser eliminado, por lo que lo enlaza el predecesor con el sucesor




Programación concurrente y paralela                                                41/45
```

## Página 42

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=42)

```text
Skip list Lock-Free: find



     1   boolean find(T x, Node<T>[] preds, Node<T>[] succs) {   22         if (curr.key < key){
    2    int bottomLevel = 0;                                    23           pred = curr; curr = succ;
    3    int key = x.hashCode();                                 24         } else {
    4    boolean[] marked = {false};                             25           break;
    5    boolean snip;                                           26         }
    6    Node<T> pred = null, curr = null, succ = null;          27       }
    7    retry:                                                  28       preds[level] = pred;
    8      while (true) {                                        29       succs[level] = curr;
    9        pred = head;                                        30     }
  10         for (int level = MAX_LEVEL; level >= bottomLevel;    31    return (curr.key == key);
    11           level--) {                                      32   }
   12          curr = pred.next[level].getReference();           33 }
   13          while (true) {
  14             succ = curr.next[level].get(marked);
   15            while (marked[0]) {
  16               snip = pred.next[level]
  17                 .compareAndSet(curr, succ, false, false);
  18               if (!snip) continue retry;
  19               curr = pred.next[level].getReference();
  20               succ = curr.next[level].get(marked);
   21            }




Programación concurrente y paralela                                                                       42/45
```

## Página 43

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=43)

```text
Skip list Lock-Free: contains




        • Hace la búsqueda desde el nivel más alto, bajando cuando se cruza
           con un elemento mayor o igual al buscado.
        • Si encuentra nodos marcados, no mira su clave (pero no los elimina)
        • Al llegar al nivel 0, responde según si la clave del elemento actual es
           la buscada.




Programación concurrente y paralela                                                 43/45
```

## Página 44

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=44)

```text
Skip list Lock-Free: contains


        1 boolean contains(T x) {
       2    int bottomLevel = 0;
       3    int v = x.hashCode();
       4    boolean[] marked = {false};
       5    Node<T> pred = head, curr = null, succ = null;
       6    for (int level = MAX_LEVEL; level >= bottomLevel; level--) {
       7      curr = curr.next[level].getReference();
       8      while (true) {
       9        succ = curr.next[level].get(marked);
     10         while (marked[0]) {
       11         curr = pred.next[level].getReference();
      12          succ = curr.next[level].get(marked);
      13        }
     14         if (curr.key < v){
      15          pred = curr;
     16           curr = succ;
     17         } else {
     18           break;
     19         }
     20       }
      21    }
     22     return (curr.key == v); //linealiza
     23 }




Programación concurrente y paralela                                        44/45
```

## Página 45

[Ver página original](practica-04-listas-y-skip-lists-concurrentes.pdf#page=45)

```text
Skip list Lock-Free




Programación concurrente y paralela   45/45
```

**Información gráfica:** [consultar el diagrama o tabla de la página 45](practica-04-listas-y-skip-lists-concurrentes.pdf#page=45). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.
