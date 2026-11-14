# Análisis de algoritmos paralelos — transcripción

- Fuente: [practica-08-analisis-de-algoritmos-paralelos.pdf](practica-08-analisis-de-algoritmos-paralelos.pdf).
- Páginas: 70.
- SHA-256: `5fae62d26d6713b0a5020a1749fd042a353b050dc9686dcf03c0df2193a7a369`.

> Texto por página con disposición aproximada. La extracción puede unir palabras o desplazar símbolos. Cotejar fórmulas, código y diagramas en el PDF.

## Página 1

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=1)

```text
    Programación concurrente y paralela
    Práctica 8 - Analizando algoritmos

    paralelos


    Segundo Cuatrimestre 2026
```

## Página 2

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=2)

```text
                Recordemos:  fork-join y métricas
```

## Página 3

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=3)

```text
   Recordemos: tareas fork-join en Java



     • Un ForkJoinPool tiene un grupo fijo de threads que ejecutan tareas
       (unidades cortas de trabajo).
     • Una tarea que devuelve un valor extiende RecursiveTask<T>. Una tarea con
       efectos (escribe en un arreglo) extiende RecursiveAction. Las dos
       implementan compute().

     task.fork() // correr la tarea (eventualmente) en el pool
     task.join() // espera a que termine y da el resultado
     task.compute() // hace el trabajo en el propio worker
     pool.invoke(t) // corre t en el pool y espera a que termine
     • El patrón de siempre: si el problema es chico, resolverlo directamente. Si no,
       partirlo, hacer fork() de una parte, compute() de la otra, y join() al final.


 Programación concurrente y paralela                        3/1
```

## Página 4

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=4)

```text
   Recordemos: work, span y paralelismo


    Vemos el cómputo como un DAG de pasos. Una arista va de un paso a otro
    cuando el segundo tiene que esperar al primero.
     • Work T : la cantidad total de pasos. Es lo que tarda con un solo procesador.
            1
     • Span T : la longitud del camino más largo del DAG (el camino crítico). Es lo
            ∞
       que tarda con infinitos procesadores.
     • Speedup (aceleración) con P procesadores: T /T .
                                      1 P
     • Paralelismo: T /T . Es el speedup máximo posible.
                 1 ∞
     • Cuando dos partes van en secuencia, sus works se suman y sus spans
       también se suman.
     • Cuando van en paralelo, sus works se suman, pero el span es el máximo de
       los dos.
     • Así analizamos la suma y el producto de matrices en la teórica.
 Programación concurrente y paralela                        4/1
```

## Página 5

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=5)

```text
   Recordemos: las cotas de T
                       P

     • Ley del work: T ≥ T /P. No se puede repartir mejor que en partes iguales.
                 P   1
     • Ley del span: T ≥ T . No se puede ganarle al camino más largo.
                 P   ∞
     Teorema (scheduler greedy)
     Un scheduler greedy (en cada paso ejecuta todo lo que esté listo, hasta P cosas)
     tarda un tiempo T∗ que cumple
                 P
                              T
                           T∗ ≤ 1 + T .
                           P  P   ∞
     • Con lo cual, si T /P es mucho más grande que T , el tiempo es casi T /P y el
                 1                     ∞              1
       speedup es casi P.

 Programación concurrente y paralela                        5/1
```

## Página 6

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=6)

```text
   Recordemos: partir un rango a la mitad


    Para procesar un rango de m elementos independientes, lo partimos a la mitad
    hasta llegar a un umbral U, y las hojas lo procesan en secuencia.
                              [0, 8)

                      [0, 4)          [4, 8)


                  [0, 2)  [2, 4)  [4, 6)  [6, 8)
            m = 8, U = 2. Las hojas (en verde) procesan su rango en secuencia.
     • El árbol tiene profundidad log(m/U). Bajar (los fork()), procesar una hoja y
       subir (los join()) cuesta Θ(log(m/U) + U) de span.
     • Hay m/U hojas de work Θ(U), y cada nodo interno agrega Θ(1). En total,
       Θ(m).
     • Con U constante, el span queda Θ(log m).

 Programación concurrente y paralela                        6/1
```

## Página 7

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=7)

```text
   Lo que vamos a hacer hoy


    Para cada algoritmo, las mismas preguntas:
     1. ¿Qué partes tienen que ir en secuencia? ¿Por qué?
     2. ¿Qué partes son independientes?

     3. ¿Cuánto cuestan el work, el span y el paralelismo?
     4. ¿Qué es lo que limita el span? ¿Se puede mejorar?
     • Empezamos con tres algoritmos sobre matrices, cada uno un poco más

       complicado que el anterior.
     • Al final, un algoritmo recursivo, quicksort.





 Programación concurrente y paralela                         7/1
```

## Página 8

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=8)

```text
                   Producto matriz por vector
```

## Página 9

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=9)

```text
   Producto matriz por vector



     El problema
     Dados A ∈ Rn×n y x ∈ Rn, calcular y = A · x, con y = (cid:80)n−1 a · x .
                                      i   j=0 ij j

     void matVec(double[][] a, double[] x, double[] y) {
       int n = x.length;
       for (int i = 0; i < n; i++) {
        double sum = 0;
        for (int j = 0; j < n; j++)
         sum += a[i][j] * x[j];
        y[i] = sum;
       }
     }
     • Las filas son independientes. La fila i solo escribe y[i] y lee a y x, que nadie
       modifica. Las procesamos partiendo el rango de filas a la mitad.
 Programación concurrente y paralela                        9/1
```

## Página 10

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=10)

```text
   Armando la tarea


    Busquemos qué tiene que hacer cada tarea.
     • Una tarea se encarga de un rango de filas [lo, hi). Al terminar, dejó
       calculados y , . . . , y .
               lo   hi−1
     • La tarea inicial es la de todas las filas, [0, n).
    • Caso base: una sola fila. Hacemos      [0, 4)
      lo mismo que el ciclo de adentro
                                        [0, 2)    [2, 4)
      del código secuencial.
    • Si no: partimos el rango a la mitad.
                                       y0   y1  y2   y3
      Hacemos fork() de las filas de Con n = 4. Cada hoja calcula una fila.
      arriba, compute() de las de abajo, y
      join() al final.

 Programación concurrente y paralela                        10/1
```

## Página 11

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=11)

```text
   Matriz por vector en fork-join


     class MatVecTask extends RecursiveAction {
       final double[][] a; final double[] x, y;
       final int lo, hi; // filas [lo, hi), constructor omitido
       public void compute() {
        if (hi - lo == 1) {    // una fila: en secuencia
         double sum = 0;
         for (int j = 0; j < x.length; j++)
           sum += a[lo][j] * x[j];
         y[lo] = sum;
         return;
        }
        int mid = (lo + hi) / 2;
        MatVecTask top = new MatVecTask(a, x, y, lo, mid);
        top.fork();            // filas de arriba
        new MatVecTask(a, x, y, mid, hi).compute(); // filas de abajo
        top.join();
       }
     }
 Programación concurrente y paralela                        11/1
```

## Página 12

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=12)

```text
   ¿Por qué funciona?


     • Arrancamos con la tarea de todas las filas:

        pool.invoke(new MatVecTask(a, x, y, 0, n));
     • El umbral es una fila, que ya son n multiplicaciones.
     • ¿Hay condiciones de carrera? No. Los rangos son disjuntos, con lo cual dos

       tareas nunca escriben la misma posición de y.
     • ¿Cuándo está listo y? Cada tarea hace join() de la que lanzó, con lo cual la
       tarea [0, n) termina después que todas las demás. Y invoke() espera a que
       termine.





 Programación concurrente y paralela                        12/1
```

## Página 13

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=13)

```text
   Matriz por vector: work


    Sumamos lo que cuesta cada tarea, nivel por nivel. Con n = 8:

                                     1 tarea × Θ(1)

                                     2 tareas × Θ(1)

                                     n/2 tareas × Θ(1)

               y0 y1 y2 y3 y4 y5 y6 y7 n hojas × Θ(n)
     Cada hoja y calcula una fila. Las demás tareas solo parten el rango y hacen fork() y join().
           i
              T (n) = (1 + 2 + · · · + n/2) ·Θ(1) + n · Θ(n) = Θ(n2)
               1
                    (cid:124) (cid:123)(cid:122) (cid:125)
                         <n
    Es el work del secuencial.
 Programación concurrente y paralela                        13/1
```

## Página 14

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=14)

```text
   Matriz por vector: span


    El span es el camino más largo. Las tareas de un mismo nivel van en paralelo,
    con lo cual el camino pasa por una sola tarea de cada nivel.

                               Θ(1)
                                    log n niveles, fork() al bajar
                               Θ(1)
                                    y join() al subir
                               Θ(1)

         y0 y1 y2 y3 y4 yy55 y6 y7 Θ(n): una fila en secuencia

               T (n) = log n · Θ(1) + Θ(n) + log n · Θ(1) = Θ(n)
                ∞
                     (cid:124) (cid:123)(cid:122) (cid:125) (cid:124) (cid:123)(cid:122) (cid:125) (cid:124) (cid:123)(cid:122) (cid:125)
                       bajar  una fila subir
 Programación concurrente y paralela                        14/1
```

## Página 15

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=15)

```text
   Matriz por vector: paralelismo


    Sin contar el árbol, que es chico, el trabajo son n filas independientes de largo n.
    Con n = 8:
                         cada fila: Θ(n)

                 y0
                 y1
                 y2
                 y3                      n filas
                 y4                      a la vez
                 y5
                 y6
                 y7
                                         tiempo
                         T   Θ(n2)
                          1 =    = Θ(n)
                        T    Θ(n)
                         ∞
    El paralelismo es la cantidad de filas. Con más procesadores que filas no
    ganamos nada, porque una fila no se reparte.
 Programación concurrente y paralela                        15/1
```

## Página 16

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=16)

```text
   ¿Qué limita el span?


    En el span, lo que pesa es la hoja: una fila hecha en secuencia, Θ(n). ¿Por qué va
    en secuencia?
     • Las multiplicaciones p = a · x son independientes.
                      j   ij j
     • Lo que encadena es la suma. Cada sum += usa el sum anterior.
                        +

                     +                     +
                  +                    +      +
                p0 p1 p2 p3           p0 p1 p2 p3
             En secuencia: n − 1 niveles En árbol: log n niveles

    La suma en R es asociativa (con double, solo hasta los últimos dígitos), con lo cual

    podemos reagrupar. Calcular y es un reduce, con span Θ(log n).
                         i
 Programación concurrente y paralela                        16/1
```

## Página 17

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=17)

```text
   El reduce de una fila


     class RowSumTask extends RecursiveTask<Double> {
       final double[] row, x;
       final int lo, hi; // columnas [lo, hi), constructor omitido
       protected Double compute() {
        if (hi - lo == 1) return row[lo] * x[lo]; // un producto
        int mid = (lo + hi) / 2;
        RowSumTask left = new RowSumTask(row, x, lo, mid);
        left.fork();
        double right = new RowSumTask(row, x, mid, hi).compute();
        return left.join() + right;
       }
     }
     • Es la misma forma que MatVecTask, pero parte columnas y devuelve la suma.
       Por eso es una RecursiveTask.
     • La hoja de MatVecTask ahora hace
       y[lo] = new RowSumTask(a[lo], x, 0, n).compute().
 Programación concurrente y paralela                        17/1
```

## Página 18

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=18)

```text
   Paralelismo anidado: el span


    Ahora debajo de cada fila cuelga un árbol de reduce. Con n = 4:

                                   log n niveles de filas

             y0    y1    y2   y3
                                   log n niveles del reduce

                    p0 p1 p2 pp33
    El camino baja por las filas, baja por el reduce, y sube por los dos. Cada nivel
    cuesta Θ(1):
                  T (n) = Θ(log n) + Θ(log n) = Θ(log n)
                   ∞
                        (cid:124) (cid:123)(cid:122) (cid:125) (cid:124) (cid:123)(cid:122) (cid:125)
                          filas reduce
    El work sigue siendo Θ(n2). El paralelismo sube de Θ(n) a Θ(n2/ log n). Para
    n = 1000, de 103 a 105.
 Programación concurrente y paralela                        18/1
```

## Página 19

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=19)

```text
   ¿Conviene el reduce en cada fila?


    Para n = 1000, T ≈ 106. Sin el reduce T ≈ 103, y con el reduce
               1              ∞
    T ≈ 4 log n ≈ 40. Por la cota greedy, el speedup es por lo menos
     ∞     2
    T /(T /P + T ):
     1 1    ∞
                       P  sin reduce con reduce
                       8    ≈ 7,9    ≈ 8
                       64   ≈ 60    ≈ 64
                     1000  ≈ 500    ≈ 960
     • Con 8 o 64 núcleos casi no cambia nada. Solo paga cuando P se acerca a n.
     • Y el reduce arma un árbol de tareas por fila, que agrega work.
     Para llevarse
     Alcanza con que el paralelismo sea bastante más grande que P. Más paralelismo
     que eso casi no mejora el speedup, y suele costar work.
 Programación concurrente y paralela                        19/1
```

## Página 20

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=20)

```text
                       Sistema triangular
```

## Página 21

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=21)

```text
   Sistemas triangulares



     El problema
     Dados L ∈ Rn×n triangular inferior con unos en la diagonal y b ∈ Rn, calcular
     x ∈ Rn tal que L · x = b.


   Un ejemplo con n = 3:        Cada fila de L es una ecuación:
       1 0 0   x 0   4                   x 0 = 4
      2  1 0 · x 1  = 10             2x 0 + x 1 = 10
        4 3 1    x     24
                  2                    4x + 3x + x = 24
                                         0   1  2
    Como L es triangular, la primera ecuación ya nos da x .
                                         0

 Programación concurrente y paralela                        21/1
```

## Página 22

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=22)

```text
   Forward substitution



    // Al terminar, b contiene x.    • En el paso j, b[j] ya es x j .
    void forward(double[][] l,       • El ciclo en i lo resta de las
             double[] b) {
      int n = b.length;                ecuaciones de abajo.
      for (int j = 0; j < n; j++)
       for (int i = j + 1; i < n; i++)
        b[i] -= l[i][j] * b[j];
    }






 Programación concurrente y paralela                        22/1
```

## Página 23

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=23)

```text
   ¿Qué va en secuencia y qué no?


    Sigamos el vector b en el ejemplo de antes, paso por paso.

                  paso 0       paso 1      paso 2
               b 0 x 0 = 4      4            4
               b 1 10 → 2      x 1 = 2       2
               b 2 24 → 8      8 → 2       x 2 = 2

       En naranja, la incógnita que se resuelve en cada paso. En verde, las restas de ese paso.
     • En secuencia: el paso 1 usa b = 2, que lo dejó el paso 0 (la flecha). Y el paso
                           1
       2 usa lo que dejó el paso 1. Cada paso espera al anterior, con lo cual los pasos
       forman una cadena.
     • Independiente: dentro del paso 0, b y b reciben cada uno su resta, y
                                1  2
       ninguna usa a la otra. Las restas de un mismo paso pueden ir en paralelo.
 Programación concurrente y paralela                        23/1
```

## Página 24

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=24)

```text
   Sistema triangular en fork-join: la tarea


    El ciclo en j queda en secuencia. Las restas de un paso son independientes,
    como las filas de matriz por vector, y las repartimos igual: una tarea se encarga
    del paso j en un rango de filas [lo, hi).
     class SubtractTask extends RecursiveAction {
       final double[][] l; final double[] b;
       final int j, lo, hi; // paso j, filas [lo, hi), constructor omitido
       public void compute() {
        if (hi - lo == 1) { b[lo] -= l[lo][j] * b[j]; return; } // una fila
        int mid = (lo + hi) / 2;
        SubtractTask top = new SubtractTask(l, b, j, lo, mid);
        top.fork();            // filas de arriba
        new SubtractTask(l, b, j, mid, hi).compute(); // filas de abajo
        top.join();
       }
     }
 Programación concurrente y paralela                        24/1
```

## Página 25

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=25)

```text
   Sistema triangular en fork-join: el programa


     void parallelForward(double[][] l, double[] b, ForkJoinPool pool) {
       int n = b.length;
       for (int j = 0; j < n - 1; j++) // el ultimo paso no tiene restas
        pool.invoke(new SubtractTask(l, b, j, j + 1, n));
     }
     • ¿Hay condiciones de carrera? No. Cada tarea escribe solo sus b[i]. Y b[j],
       que leen todas, nadie lo escribe en este paso.
     • ¿Cuándo empieza el paso j + 1? invoke() espera a que terminen todas las
       restas del paso j. Con lo cual b[j+1] ya es x j+1 .






 Programación concurrente y paralela                        25/1
```

## Página 26

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=26)

```text
   Sistema triangular: work y span


    Cada paso es un árbol de tareas, como el de matriz por vector, pero con hojas de
    Θ(1) (una resta). Con n = 5, los pasos tienen m = 4, 3, 2, 1 restas:





                 m = 4     m = 3    m = 2  m = 1

     • Work: el paso con m restas tiene m hojas (en verde), y cada nodo cuesta
       Θ(1). Sumando los pasos, T = (cid:80)n−1 Θ(m) = Θ(n2).
                         1   m=1
     • Span: en cada paso, el camino naranja baja y sube la altura del árbol,
       Θ(log m). Los pasos van en cadena (las flechas), con lo cual se suman:
       T =
           (cid:80)n−1
               Θ(log m).
        ∞    m=1
 Programación concurrente y paralela                        26/1
```

## Página 27

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=27)

```text
   Sistema triangular: ¿cuánto da el span?


    Nos quedó
                   T  = log 1 + log 2 + · · · + log(n − 1).
                    ∞
    Si dibujamos cada término como una barra de altura log m, la suma es el área de
    todas las barras. Con n = 8:



                                        m
                      1  2 3 4  5 6 7

    Esta suma no tiene una fórmula directa. Pero no necesitamos el valor exacto,
    solo el orden. Alcanza con encontrar algo más grande y algo más chico que
    sean fáciles de calcular.

 Programación concurrente y paralela                        27/1
```

## Página 28

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=28)

```text
   El span: una cota por arriba


            log n               • Ninguna barra pasa de log n. La más
                                  alta es log(n − 1).
                                • Y hay menos de n barras.

                                • Con lo cual las barras entran en el
                        m         rectángulo punteado, de ancho n y
      1  2 3 4  5 6 7
                                  alto log n.
                           T  ≤ n log n
                            ∞





 Programación concurrente y paralela                        28/1
```

## Página 29

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=29)

```text
   El span: una cota por abajo


                                • Nos quedamos solo con la mitad

    log n                         derecha, de m = n/2 en adelante. Son
      2
                                  n/2 barras.
                                • Cada una mide por lo menos log(n/2).
                         m
        1 2  3 4 5  6 7         • Con lo cual el rectángulo naranja
                                  entra debajo de las barras.
                               n   n
                           T ≥  log
                            ∞
                               2   2


 Programación concurrente y paralela                        29/1
```

## Página 30

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=30)

```text
   El span: juntando las dos cotas


                       n   n
                        log  ≤ T ≤ n log n
                               ∞
                       2   2
     • La de arriba es Θ(n log n).
     • La de abajo es n(log n − 1), que para n grande es más o menos la mitad de
                 2
       n log n. También es Θ(n log n).
    Como T queda entre dos cosas del mismo orden,
         ∞
                             T    Θ(n2)   (cid:18) n (cid:19)
              T = Θ(n log n), 1 =       = Θ     .
               ∞
                            T   Θ(n log n)  log n
                             ∞
 Programación concurrente y paralela                        30/1
```

## Página 31

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=31)

```text
   Mismo work, distinto paralelismo


     Matriz por vector (con reduce)    Sistema triangular


                                         n pasos en cadena
             todo a la vez
   span Θ(log n), paralelismo Θ(n2/ log n) span Θ(n log n), paralelismo Θ(n/ log n)
     • Los dos hacen Θ(n2) cuentas. Para n = 1000, el primero tiene paralelismo
       del orden de 105 y el segundo de unas decenas.
     • La diferencia es la cadena de pasos.

     Para llevarse
     Si un algoritmo tiene n pasos en cadena, el span es por lo menos n veces lo
     que cuesta cada paso. Lo único que podemos achicar es el costo de cada paso.


 Programación concurrente y paralela                        31/1
```

## Página 32

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=32)

```text
                        Factorización LU
```

## Página 33

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=33)

```text
   ¿Y si la matriz no es triangular?



     El problema
     Dada A ∈ Rn×n, encontrar L, U ∈ Rn×n tales que A = L · U, con L triangular
     inferior con unos en la diagonal y U triangular superior. Es la factorización LU.

    Si la tenemos, Ax = b es L(Ux) = b. Llamamos y = Ux:

     1. Resolver Ly = b, el sistema triangular que acabamos de ver.
     2. Resolver Ux = y, igual pero de abajo hacia arriba.
     • La factorización es la parte cara. Vamos a ver que cuesta Θ(n3), contra Θ(n2)
       de cada sistema triangular.

     • Se hace una sola vez, y después sirve para muchos b distintos.


 Programación concurrente y paralela                        33/1
```

## Página 34

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=34)

```text
   Eliminación gaussiana: el paso 0


    Recordemos la eliminación gaussiana de álgebra. El pivote del paso 0 es a = 2,
                                                      00
    y anulamos lo que está debajo de él.
                                 
                             2 1 1
                           4  3 3
                             8 7 9
     • Fila 1. El multiplicador es ℓ = 4/2 = 2.
                         10
                       (4, 3, 3) − 2 · (2, 1, 1) = (0, 1, 1)

     • Fila 2. El multiplicador es ℓ = 8/2 = 4.
                         20
                       (8, 7, 9) − 4 · (2, 1, 1) = (0, 3, 5)

 Programación concurrente y paralela                        34/1
```

## Página 35

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=35)

```text
   Eliminación gaussiana: el paso 1


    Después del paso 0 la matriz quedó así. El pivote del paso 1 es a = 1.
                                                11
                                 
                             2 1 1
                           0  1 1
                             0 3 5
     • Fila 2. El multiplicador es ℓ = 3/1 = 3.
                         21
                       (0, 3, 5) − 3 · (0, 1, 1) = (0, 0, 2)
     • Ya no queda nada debajo de la diagonal. Lo que obtuvimos es U:

                                    
                                2 1 1
                           U = 0 1 1
                                0 0 2
 Programación concurrente y paralela                        35/1
```

## Página 36

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=36)

```text
   ¿Y dónde está L?


    L se arma con los multiplicadores. El multiplicador ℓ va en la fila i, columna k:
                                        ik
                                      
                         1  0 0     1 0 0
                    L = ℓ
                         10
                            1 0 = 2 1 0
                        ℓ  ℓ   1    4 3  1
                         20 21
    (Es la L del sistema triangular de antes.) Verifiquemos una fila. La fila 2 de L · U es
                4 · (2, 1, 1) + 3 · (0, 1, 1) + 1 · (0, 0, 2) = (8, 7, 9),
    que es justo la fila 2 de A. La eliminación le restó a la fila 2 primero 4·fila y
                                                     0
    después 3·fila . Multiplicar por L deshace esas restas.
             1
 Programación concurrente y paralela                        36/1
```

## Página 37

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=37)

```text
   Guardar todo en la misma matriz


     • Cada multiplicador ℓ se calcula justo cuando a pasa a valer cero. Con lo
                     ik                 ik
       cual lo podemos guardar en el lugar de ese cero.
                                         
                 2 1 1       2 1 1       2 1 1
                        paso 0      paso 1
                4 3 3 −−−−→ 2 1 1 −−−→ 2 1 1
                 8 7 9       4 3 5       4 3 2
    En general, en el paso k (pivote a ), para cada fila i > k:
                           kk
                    a
            a  ← ℓ = ik , a ← a − ℓ · a para cada j > k.
             ik  ik        ij  ij ik kj
                    a
                     kk
    Suponemos que nunca aparece un pivote nulo, para poder dividir.
 Programación concurrente y paralela                        37/1
```

## Página 38

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=38)

```text
   LU secuencial




    void lu(double[][] a) {          • k recorre los pasos.
      int n = a.length;              • i recorre las filas debajo del
      for (int k = 0; k < n; k++)
       for (int i = k + 1; i < n; i++) { pivote.
        a[i][k] /= a[k][k];          • Primero el multiplicador,
        for (int j = k + 1; j < n; j++)
          a[i][j] -= a[i][k] * a[k][j]; después la resta en cada
       }
                                       columna j.
    }
     • En el paso k hay m = n − k − 1 filas, y cada una hace una división y m restas.
       El paso cuesta m(m + 1).
     • Sumando, T = (cid:80)n−1 m(m + 1) = (n−1) n (n+1) ≈ n3 . Con lo cual es Θ(n3).
                   m=0            3     3
 Programación concurrente y paralela                        38/1
```

## Página 39

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=39)

```text
   ¿Qué va en secuencia?


    Sigamos la matriz del ejemplo, paso por paso.

                 al empezar    paso 0        paso 1
            fila 0 2 1 1        2 1 1        2 1 1
             fila 1 4 3 3       2 1 1        2 1 1
             fila 2 8 7 9       4 3 5        4 3 2

    En naranja, la fila del pivote de cada paso. En verde, las filas que ese paso actualiza. En negrita, los
                           multiplicadores.
     • El paso 1 usa como pivote la fila 1. Pero no la original, sino la que dejó el paso
       0 (la flecha).
     • En general, el paso k lee la fila k, que se termina de escribir en el paso k − 1.

       Cada paso espera al anterior, igual que en el sistema triangular: los pasos
       forman una cadena.
 Programación concurrente y paralela                        39/1
```

## Página 40

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=40)

```text
   El paso k en un dibujo


                                • Gris: ya terminado. No se toca más.
           terminado
                                • Azul: la fila del pivote. En este paso la
          akk          fila k     leen todas las filas, pero nadie la
                                  escribe.
                                • Naranja: donde se guardan los
              se actualiza        multiplicadores.
                                • Verde: la submatriz que se actualiza.
                                  Cada franja horizontal es una fila, que

          ℓ                       solo escribe su franja.
           ik
    Con lo cual, dentro de un paso, las filas son independientes, como en matriz por
    vector.

 Programación concurrente y paralela                        40/1
```

## Página 41

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=41)

```text
   LU en fork-join: armando la tarea


    El ciclo en k queda en secuencia. En cada paso, procesamos las filas
    k + 1, . . . , n − 1 como en matriz por vector.
    • Una tarea se encarga del paso k en      [1, 5)
      un rango de filas [lo, hi).
                                         [1, 3)   [3, 5)
    • Caso base: una sola fila. Calculamos
      su multiplicador y hacemos sus
                                       fila 1 fila 2 fila 3 fila 4
      restas.
                                        El paso k = 0 con n = 5.
    • Si no: partimos el rango a la mitad.
      Hacemos fork() de las filas de arriba,
      compute() de las de abajo, y join() al
      final.

 Programación concurrente y paralela                        41/1
```

## Página 42

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=42)

```text
   LU en fork-join: el código


     void eliminateRow(double[][] a, int k, int i) {
       a[i][k] /= a[k][k];          // multiplicador
       for (int j = k + 1; j < a.length; j++)
        a[i][j] -= a[i][k] * a[k][j]; // restas
     }
     class EliminateRows extends RecursiveAction {
       final double[][] a;
       final int k, lo, hi; // paso k, filas [lo, hi), constructor omitido
       public void compute() {
        if (hi - lo == 1) { eliminateRow(a, k, lo); return; } // una fila
        int mid = (lo + hi) / 2;
        EliminateRows top = new EliminateRows(a, k, lo, mid);
        top.fork();                 // filas de arriba
        new EliminateRows(a, k, mid, hi).compute(); // filas de abajo
        top.join();
       }
     }
 Programación concurrente y paralela                        42/1
```

## Página 43

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=43)

```text
   LU en fork-join: el programa


     void parallelLu(double[][] a, ForkJoinPool pool) {
       int n = a.length;
       for (int k = 0; k < n - 1; k++) // el ultimo paso no tiene filas
        pool.invoke(new EliminateRows(a, k, k + 1, n));
     }
     • ¿Hay condiciones de carrera? No. Cada tarea escribe solo sus filas. Y la fila k,
       que leen todas, nadie la escribe en este paso.
     • ¿Cuándo empieza el paso k + 1? invoke() espera a que terminen todas las
       filas del paso k. Con lo cual la fila k + 1 ya está lista.






 Programación concurrente y paralela                        43/1
```

## Página 44

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=44)

```text
   LU: un paso


    El paso con m filas es como matriz por vector: m filas independientes, y cada una
    hace Θ(m) cuentas. Con m = 4:


                                log m niveles, Θ(1) cada uno


             fila fila fila fila m filas, Θ(m) cada una

     • Work del paso: m filas de Θ(m), más menos de m nodos internos de Θ(1).
       En total, Θ(m2).
     • Span del paso: bajar y subir, Θ(log m), más la fila de la hoja, Θ(m). En total,
       Θ(m).


 Programación concurrente y paralela                        44/1
```

## Página 45

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=45)

```text
   LU: todos los pasos


    Los pasos van en cadena. Cada uno trabaja sobre una submatriz más chica:



                    m = 4  m = 3  m = 2 m = 1
                   Con n = 5, los pasos tienen m = 4, 3, 2, 1 filas.

    Como van en cadena, tanto los works como los spans se suman:
                n−1                  n−1
                (cid:88)             (cid:88)
             T =   Θ(m2) = Θ(n3), T =  Θ(m) = Θ(n2).
              1                   ∞
                m=1                  m=1
                         T   Θ(n3)
                          1 =    = Θ(n)
                        T    Θ(n2)
                         ∞
 Programación concurrente y paralela                        45/1
```

## Página 46

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=46)

```text
   LU: speedup


    Contando una unidad por operación, T ≈ n3/3 y T ≈ n2/2. Para n = 1000, con
                               1      ∞
    la cota greedy:
                   P    T /P  T  speedup garantizado
                        1     ∞
                   8 4,2 · 107 5 · 105     ≈ 7,9
                  64  5,2 · 106 5 · 105     ≈ 58
                 1000 3,3 · 105 5 · 105    ≈ 400
                10000 3,3 · 104 5 · 105    ≈ 625
    Recordemos que el speedup garantizado es T
                              1
                              /(T
                                1
                                /P + T∞ ), y nunca supera el paralelismo
    T
     1
     /T∞ ≈ 667.
     • Con 8 y 64 procesadores, T /P domina y el speedup es casi P.
                         1
     • Con 10000, casi todo el tiempo es T . Agregar procesadores casi no sirve.
                               ∞
 Programación concurrente y paralela                        46/1
```

## Página 47

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=47)

```text
   El speedup en un gráfico


     speedup
             lineal
                     paralelismo ≈ 667
                                      • Al principio la curva sigue a la
     600                               recta lineal.
                                      • Después se aplana y nunca
                                       pasa el paralelismo.
     400
                                      • Para un speedup casi lineal, P
                                       tiene que estar bastante por
     200
                                       debajo del paralelismo (unas
                                       10 veces).
                              P
             1000   2000   3000
 Programación concurrente y paralela                        47/1
```

## Página 48

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=48)

```text
   ¿Alcanza con Θ(n)?


     • Para n = 1000 el paralelismo es de unos 667. Una máquina común tiene
       entre 8 y 64 núcleos, así que el speedup es casi lineal.
     • Pero con n = 100 el paralelismo es de unos 67, y con 64 núcleos ya no

       alcanza.
     ¿Se puede conseguir más paralelismo?

     Recordemos que en el producto matriz por vector, hacer la suma de cada fila
     con un reduce bajó el span. ¿Pasa algo parecido acá?






 Programación concurrente y paralela                        48/1
```

## Página 49

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=49)

```text
                     LU: mejoras (avanzado)
```

## Página 50

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=50)

```text
   Dentro de una fila


    El span de cada paso es Θ(m) porque la hoja procesa una fila entera en
    secuencia. ¿Hace falta? Miremos qué hace la fila i en el paso k, con m = 3
    columnas a actualizar:

                           ℓ = a /a
                            ik ik kk

           a   −= ℓ · a  a   −= ℓ · a  a   −= ℓ · a
            i,k+1 ik k,k+1 i,k+2 ik k,k+2 i,k+3 ik k,k+3
     • El multiplicador va primero, porque todas las restas lo usan.
     • Pero las restas no se usan entre sí. La de la columna j solo lee a , ℓ y a .
                                                  ij ik kj
    Con lo cual las restas de una misma fila también son independientes, y las
    podemos partir igual que las filas.

 Programación concurrente y paralela                        50/1
```

## Página 51

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=51)

```text
   Partir filas y columnas: dos fases


    Ya vimos que en el paso k las restas dependen del multiplicador de su fila, pero
    no entre sí. Con lo cual cada paso se puede hacer en dos fases, una después de la
    otra:





                        fase 1       fase 2
                     multiplicadores restas

     1. Multiplicadores: uno por fila, ℓ = a /a . Son m divisiones independientes.
                            ik ik kk
     2. Restas: una por entrada de la submatriz, a −= ℓ · a . Son m2 restas
                                    ij   ik kj
       independientes, y cada una solo necesita que la fase 1 haya terminado.
 Programación concurrente y paralela                        51/1
```

## Página 52

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=52)

```text
   Partir filas y columnas: cada fase






   Fase 1: como las filas de matriz por Fase 2: como la suma de matrices de

   vector. Partimos el rango de filas a la la teórica. Partimos la submatriz en
   mitad, y cada hoja hace una división. cuatro cuadrantes, cada uno en cuatro,
                                 hasta llegar a una entrada.
     • En las dos fases, cada nivel parte a la mitad (las filas, o el lado de la
       submatriz). Son log m niveles de Θ(1), con lo cual cada fase tiene span
       Θ(log m).
     • Las dos fases van en secuencia. El span del paso es
       Θ(log m) + Θ(log m) = Θ(log m). Antes era Θ(m).

 Programación concurrente y paralela                        52/1
```

## Página 53

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=53)

```text
   Partir filas y columnas: todos los pasos


    Los pasos siguen yendo en cadena, pero ahora cada uno cuesta Θ(log m) de
    span. Es la misma suma que en el sistema triangular:

                         n−1
                         (cid:88)
                    T  =   Θ(log m) = Θ(n log n).
                     ∞
                         m=1
    El work no cambia, sigue siendo Θ(n3). Con lo cual
                     T     Θ(n3)   (cid:18) n2 (cid:19)
                      1 =        = Θ     .
                     T   Θ(n log n)  log n
                      ∞
    Partir solo las filas daba paralelismo Θ(n). Partir también las columnas lo sube a
    Θ(n2/ log n).
 Programación concurrente y paralela                        53/1
```

## Página 54

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=54)

```text
   ¿Conviene partir las filas?


     • Para n = 1000, el paralelismo pasa de ≈ 103 a ≈ 105.
     • El precio: muchas más tareas, cada una con muy poco trabajo. En la práctica,
       las hojas tienen que ser bloques de un tamaño razonable.

     Para llevarse

     Igual que en matriz por vector, más paralelismo solo paga cuando P se acerca
     al que ya teníamos. Por ejemplo, con n = 100 y 64 núcleos.







 Programación concurrente y paralela                        54/1
```

## Página 55

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=55)

```text
                           Quicksort
```

## Página 56

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=56)

```text
   Recordemos quicksort


    Para ordenar un arreglo:
     1. Elegimos un pivote (por ejemplo, el último elemento).
     2. Partimos el arreglo. Los menores al pivote van a la izquierda, los demás a la

       derecha, y el pivote queda en el medio, en su lugar definitivo.
     3. Ordenamos cada lado recursivamente.
    Con pivote 4:
                             partir
                [5, 2, 8, 1, 9, 3, 7, 4] −−−→ [3, 2, 1, 4, 9, 5, 7, 8]
                                  (cid:124) (cid:123)(cid:122) (cid:125) (cid:124) (cid:123)(cid:122) (cid:125)
                                   <4     ≥4
     • Después de partir, los dos lados no comparten ningún elemento. Cada uno
       se ordena sin mirar al otro.


 Programación concurrente y paralela                        56/1
```

## Página 57

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=57)

```text
   La partición



    // parte a[lo, hi)                • i avanza desde la izquierda y
    int partition(int[] a, int lo, int hi) { j desde la derecha.
      int pivot = a[hi - 1];
      int i = lo, j = hi - 2;         • Un menor a la izquierda ya
      while (i <= j) {                 está bien. Un mayor o igual a
       if (a[i] < pivot) i++;
                                       la derecha también.
       else if (a[j] >= pivot) j--;
       else { swap(a, i, j); i++; j--; } • Si los dos están mal puestos,
      }
      swap(a, i, hi - 1);              se intercambian.
      return i;                       • Cuando se cruzan, i es el
    }
                                       primer mayor. Ahí va el pivote.
     • Recorre el rango una vez, con lo cual cuesta Θ(n).
     • Y es un recorrido en secuencia. Cada vuelta depende de dónde quedaron i
       y j en la anterior.
 Programación concurrente y paralela                        57/1
```

## Página 58

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=58)

```text
   Quicksort secuencial




     void quicksort(int[] a, int lo, int hi) {
       if (hi - lo <= 1) return;
       int p = partition(a, lo, hi);
       quicksort(a, lo, p); // menores
       quicksort(a, p + 1, hi); // mayores o iguales
     }
     • El pivote ya quedó en su lugar (a[p]), con lo cual no entra en ninguna de las
       dos llamadas.
     • Las dos llamadas trabajan sobre rangos disjuntos. Ninguna lee ni escribe lo
       que toca la otra.



 Programación concurrente y paralela                        58/1
```

## Página 59

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=59)

```text
   El peor caso


    Partir un rango de m elementos cuesta Θ(m). Con lo cual el costo total es la
    suma de los tamaños de todos los rangos que se parten.
     • Si el pivote es el mayor de su rango, todos los demás van a la izquierda y el
       lado derecho queda vacío. Por ejemplo, con el arreglo ya ordenado:
                     [1, 2, 3, 4, 5] → [1, 2, 3, 4], 5, [ ]

                       [1, 2, 3, 4] → [1, 2, 3], 4, [ ] . . .
     • Cada partición saca un solo elemento. Los rangos miden n, n − 1, . . . , 1, y
       n + (n − 1) + · · · + 1 = n(n+1) = Θ(n2).
                        2
     • Lo mismo pasa con el arreglo ordenado al revés, o con todos iguales.
     • En el mejor caso el pivote parte a la mitad. Hay log n niveles que suman n
       cada uno, con lo cual Θ(n log n).
     • Con el pivote al azar no siempre parte a la mitad, pero en promedio también
       cuesta Θ(n log n).
 Programación concurrente y paralela                        59/1
```

## Página 60

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=60)

```text
   Quicksort en fork-join


     class QuickSortTask extends RecursiveAction {
       static final int THRESHOLD = 1000;
       final int[] a;
       final int lo, hi; // ordena a[lo, hi), constructor omitido
       public void compute() {
        if (hi - lo <= THRESHOLD) {
         Arrays.sort(a, lo, hi); // chico: en secuencia
         return;
        }
        int p = partition(a, lo, hi); // primero partir
        QuickSortTask left = new QuickSortTask(a, lo, p);
        left.fork();           // lado izquierdo
        new QuickSortTask(a, p + 1, hi).compute(); // lado derecho
        left.join();
       }
     }
     • Lo nuevo es el umbral y el fork() de la llamada izquierda. Como los rangos
       son disjuntos, no hay condiciones de carrera.
 Programación concurrente y paralela                        60/1
```

## Página 61

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=61)

```text
   Work


    La versión fork-join hace las mismas cuentas que la secuencial. Con lo cual su
    work es el del quicksort secuencial.
     • Peor caso (el pivote siempre en un extremo):

                             T = Θ(n2).
                              1
     • En promedio:
                            T = Θ(n log n).
                            1
     • Nada de esto es nuevo. Paralelizar no cambió las cuentas, solo el orden en
       que se pueden hacer.




 Programación concurrente y paralela                        61/1
```

## Página 62

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=62)

```text
   Span


              partir n
                                  • Los dos lados van en paralelo. Su
           partir    partir        span es el del más largo.
                                  • Que queden desparejos no deja
             partir
                                   threads ociosos. El work stealing
                .
                .                  reparte las tareas.
                .
     • La partición de la raíz recorre los n elementos en secuencia, y no hay ningún
       fork() antes de que termine. Con lo cual, para cualquier entrada,
                              T  ≥ n.
                               ∞


 Programación concurrente y paralela                        62/1
```

## Página 63

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=63)

```text
   ¿Cuánto mide el camino más largo?


    Una llamada sobre m elementos primero parte, en secuencia, y después ordena
    los dos lados, en paralelo.

                       partir m elementos: Θ(m)


                ordenar el lado corto ordenar el lado largo

     • Partir y ordenar van en secuencia: sus spans se suman.
     • Los dos lados van en paralelo: queda el máximo, que es el del lado más largo.
                  T (m) = Θ(m) + T (lado más largo)
                   ∞            ∞
    Por eso el camino más largo baja siempre por el lado más largo, y suma lo que
    cuesta partir en cada nivel.

 Programación concurrente y paralela                        63/1
```

## Página 64

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=64)

```text
   El camino en el peor caso


    El pivote queda siempre en un extremo: un lado vacío y el otro con m − 1
    elementos. Con n = 8, cada barra es una partición del camino:
                                       8
                                     7
                                   6
                                 5
                               4
                             3
                           2
                         1
    El camino pasa por todas las particiones:
                                   n(n + 1)
                T = n + (n − 1) + · · · + 1 = = Θ(n2).
                ∞
                                     2
    Igual que el work. No hay nada en paralelo.
 Programación concurrente y paralela                        64/1
```

## Página 65

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=65)

```text
   El camino partiendo a la mitad


    El pivote parte a la mitad: el lado más largo tiene m/2 elementos. Con n = 8,
    ponemos las particiones del camino una al lado de la otra:

                       n          n/2   n/4

                              2n

     • Cada partición es la mitad de la anterior. Cada una llena la mitad de lo que
       falta hasta 2n, así que nunca lo alcanzan:

                            n  n
                         n +  +  + · · · < 2n.
                            2  4
     • Y la partición de la raíz sola ya cuesta n. Con lo cual T = Θ(n).
                                           ∞
    Con el pivote al azar, en promedio también vale T = Θ(n).
                                      ∞
 Programación concurrente y paralela                        65/1
```

## Página 66

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=66)

```text
   Paralelismo


     • Peor caso: Θ(n2)/Θ(n2) = Θ(1). Nada en paralelo.
     • En promedio: Θ(n log n)/Θ(n) = Θ(log n).

     • No hace falta la cuenta exacta para ver que es poco. Por la ley del span,
       T ≥ T ≥ n. Con lo cual el speedup es a lo sumo T /n, tenga los núcleos
        P  ∞                              1
       que tenga.
     • Para n = 106 es T ≈ n log n ≈ 2 · 107. El speedup no pasa de unos 20.
                  1     2






 Programación concurrente y paralela                        66/1
```

## Página 67

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=67)

```text
   ¿Se puede mejorar?


    Esto no es el foco de la clase. Lo dejamos como resumen, por curiosidad.
    Lo que limita el span es la partición, que recorre los elementos en secuencia.
    Pero se puede hacer en tres pasos paralelos:

     1. Marcar los elementos menores al pivote. Cada marca es independiente.
     2. Calcular la posición de cada uno: cuántos menores tiene antes. Es una suma
       de prefijos, que se hace en árbol con span Θ(log n).
     3. Escribir todos en su posición a la vez.

    La partición queda con span Θ(log n). El span de quicksort baja de Θ(n) a
    Θ(log2 n), y el paralelismo sube de Θ(log n) a Θ(n/ log n).




 Programación concurrente y paralela                        67/1
```

## Página 68

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=68)

```text
                           Resumen
```

## Página 69

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=69)

```text
   Resumen


           Algoritmo            T      T     Paralelismo
                                1       ∞
           Matriz por vector (filas) Θ(n2) Θ(n) Θ(n)
           Matriz por vector (reduce) Θ(n2) Θ(log n) Θ(n2/ log n)
           Sistema triangular  Θ(n2) Θ(n log n) Θ(n/ log n)
           LU (filas)          Θ(n3)  Θ(n2)    Θ(n)
           LU (filas y columnas) Θ(n3) Θ(n log n) Θ(n2/ log n)
           Quicksort (pivote al medio) Θ(n log n) Θ(n) Θ(log n)
     • Si hay n pasos en cadena, el span es por lo menos n veces el costo de cada
       paso. Lo que se puede achicar es ese costo.
     • En un algoritmo recursivo, lo que se hace antes o después de las llamadas
       puede dominar el span (la partición de quicksort).



 Programación concurrente y paralela                        69/1
```

## Página 70

[Ver página original](practica-08-analisis-de-algoritmos-paralelos.pdf#page=70)

```text
                               Temporary page!
          LATEX was unable to guess the total number of pages correctly. As there was some
          unprocessed data that should have been added to the final page this extra page
          has been added to receive it.
          If you rerun the document (without altering it) this surplus page will go away,
          because LATEX now knows how many pages to expect for this document.
```
