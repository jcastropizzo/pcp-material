# Modelado con LTS, FSP y lógicas — transcripción

- Fuente: [teorica-09-lts-y-logicas.pdf](teorica-09-lts-y-logicas.pdf).
- Páginas: 59.
- SHA-256: `d9b2937b100e9604fa575e9c583fbd0ebd4b3974ed7084988cedba6057c788f6`.

> Texto por página con disposición aproximada. La extracción puede unir palabras o desplazar símbolos. Cotejar fórmulas, código y diagramas en el PDF.

## Página 1

[Ver página original](teorica-09-lts-y-logicas.pdf#page=1)

```text
             ¿Por             qué           modelar                     y     analizar




                 programas                            concurrentes?









              •  Son     notoriamente          difíciles    de    construir      correctamente.



              •  Los    errores      son    difíciles    de   replicar.




              •  Pero     también…




                  •  Establecer         un   modelo       matemático          que    los

                     caracteriza        es   interesante        a  nivel    disciplina
```

## Página 2

[Ver página original](teorica-09-lts-y-logicas.pdf#page=2)

```text
                                                          Therac-25
```

## Página 3

[Ver página original](teorica-09-lts-y-logicas.pdf#page=3)

```text
                  Preguntas                                de           Interés




                                      (Para  Programas      Concurrentes)






                 Compartidas      con   programas                  Particulares    de  programas

                           secuenciales                                     concurrentes



                  • Post-condiciones                          • Deadlock


                                                              • Livelock
                  • Invariante de ciclo
                                                              • Interferencia
                  • Aserciones en código

                                                              • Fairness
                  • Terminación
                                                              • Atomicidad
                  • …

                                                              • Propiedades de dominio específico

                                                              • ….
```

## Página 4

[Ver página original](teorica-09-lts-y-logicas.pdf#page=4)

> Nota editorial sobre la imagen. Imagen del premio Turing 1980 a Antony Hoare por sus contribuciones al diseño y la definición de lenguajes de programación.

## Página 5

[Ver página original](teorica-09-lts-y-logicas.pdf#page=5)

> Nota editorial sobre la imagen. Imagen del premio Turing 2013 a Leslie Lamport por sus contribuciones a los sistemas distribuidos y concurrentes.

## Página 6

[Ver página original](teorica-09-lts-y-logicas.pdf#page=6)

> Nota editorial sobre la imagen. Imagen del premio Turing 1996 a Amir Pnueli por el análisis de programas mediante lógica temporal.

## Página 7

[Ver página original](teorica-09-lts-y-logicas.pdf#page=7)

```text
                          https://dl.acm.org/doi/pdf/10.1145/1592761.1592781
```

## Página 8

[Ver página original](teorica-09-lts-y-logicas.pdf#page=8)

```text
                Modelos                       de        Concurrencia







                   •  Cual   sería   el WHILE     de   programas      concurrentes?



                   •  Algebras     de   Proceso.



                       •  Communicating         Sequential      Processes      (CSP).



                           • Tony    Hoare.



                           • Go   language



                       •  Finite  State   Processes      (FSP)



                           • Una    variante    de  CSP    que   garantiza     modelos      finitos.
```

## Página 9

[Ver página original](teorica-09-lts-y-logicas.pdf#page=9)

```text
                        Sintaxis,                    Semántica                           y




                                        Denotación











                                                 Sistemas     de

                FSP                               Transición                          Equivalencia
                                                 Etiquetadas
















                                           Sistemas     Concurrentes
```

## Página 10

[Ver página original](teorica-09-lts-y-logicas.pdf#page=10)

```text
                                               MTSA







               •  Originalmente  LTSA   - The Labelled  Transition System   Analyser. Imperial
                  College.



               •  Compila  FSP  en  sistemas  de transición  etiquetadas  y provee  múltiples
                  formas  de análisis.


               •  Evolución  en dc.uba.ar  para:



                   • Modal   Transition Systems


                   • Markov   Decision  Processes


                   • Síntesis de  Controladores   de Eventos   Discretos





               •  http://mtsa.dc.uba.ar/
```

## Página 11

[Ver página original](teorica-09-lts-y-logicas.pdf#page=11)

```text
                  Sistemas              de     Transición              Etiquetadas







                • Las   etiquetas   modelan     interacciones     de  un  proceso    con  su  entorno


                • Hay   una   acción   especial   “tau”  que   modela    cómputo     interno   del

                  proceso,    que   no  es  observable     desde   el entorno.



                • Hay   una   cantidad    finita de estados



















                  •  Usaremos       el término    LTS   (Labelled     Transition    System)
```

## Página 12

[Ver página original](teorica-09-lts-y-logicas.pdf#page=12)

```text
                                            Un            LTS







            PHIL =  <{0 1, 2, 3, 4, 5, 6},

                      {sitdown, right.get, left.get, eat, left.put, right.put, arise},
                      {(0, sitdown, 1), (1, right.get, 2), (2, left.get, 3), (3, eat, 4), (5, left.put, 6), (6, arise, 0)},

                      0}





















             0, sitdown, 1, right.get, 2, left.get, 3, eat, 4, left.put, 5, right.put, 6, arise, 0, sitdown, …

             sitdown, right.get, left.get, eat, left.put, right.put, arise, sitdown, …
```

## Página 13

[Ver página original](teorica-09-lts-y-logicas.pdf#page=13)

```text
                    Modelado                                 Sistemas





                                Concurrentes









                                                  Parte     3/5
```

## Página 14

[Ver página original](teorica-09-lts-y-logicas.pdf#page=14)

```text
                               Lenguaje                              FSP




























    Quick      reference:

    https://www.doc.ic.ac.uk/~jnm/book/ltsa/Appendix-A-2e.html



    Sintaxis      y semántica

    https://www.doc.ic.ac.uk/~jnm/LTSdocumention/FSP-notation.html
```

## Página 15

[Ver página original](teorica-09-lts-y-logicas.pdf#page=15)

```text
            FSP             -   El       proceso                       Deadlock







                           STOP    es un  proceso   (y palabra  reservada)   que  describe

                           el proceso   que  es incapaz   de interactuar  con  su  entorno.






                                             NadaDeNada        =   STOP.














                                            lts(STOP) =  <{s},{}, {}, s>
```

## Página 16

[Ver página original](teorica-09-lts-y-logicas.pdf#page=16)

```text
                       FSP               -    action                    prefix







                 Si x es  una  acción  y P un  proceso   entonces   (x-> P)  describe  un  proceso
                 que  inicialmente   interactúa  a través de  la acción  x y luego  se comporta

                 como   P.





                                         UnicaVez      =  (uno   ->   STOP).













                                lts(UnicaVez)    = <{s, p},{uno},  {(s, uno, p)}, s>





                        Dosveces            =   (uno       ->    dos      ->    STOP).
```

## Página 17

[Ver página original](teorica-09-lts-y-logicas.pdf#page=17)

```text
                           FSP               -    Recursión









             Repetición   de comportamiento     se  modela   con  recursión:







            SWITCH     =  (on->off->SWITCH).












                          lts(Switch)   = <{s, p},{on, off}, {(s, on, p), (p, off, s)}, s>
```

## Página 18

[Ver página original](teorica-09-lts-y-logicas.pdf#page=18)

```text
                               Sub-Procesos





                                    a.k.a. Definiciones  locales  a un  Proceso





                                                                                   La coma    introduce
            SWITCH    =  OFF,
                                                                                    un  subproceso.
            OFF       =  (on    ->  ON),
            ON        =  (off->     OFF).


                                                                                     El punto   termina   la
           SWITCH     =  OFF,
                                                                                       definición   de  un
           OFF        =  (on   ->(off->OFF)).
                                                                                            proceso


           SWITCH     =  (on->off->SWITCH).





      Acciones/eventos   comienzan
      con minúscula.

      Procesos  comienzan   con
      mayúscula
```

## Página 19

[Ver página original](teorica-09-lts-y-logicas.pdf#page=19)

```text
                           FSP              -     Alternativas












        Si x  e y son  acciones  entonces


        (x->  P | y-> Q)

        describe   un proceso   que  inicialmente

        es  capaz  de  interactuar  a través  de

        las acciones   x o y.

        El proceso   pasa  a  comportarse    como
        P  o Q  según  ocurra  x o  y.






          DRINKS     =  (red->coffee->DRINKS

                        |blue->tea->DRINKS

                        ).
```

## Página 20

[Ver página original](teorica-09-lts-y-logicas.pdf#page=20)

```text
                           FSP              -     Alternativas













        DRINKS     =  (red->coffee->DRINKS

                      |blue->tea->DRINKS
                      ).













                                                Ejecuciones     y Trazas?





           Quién   elige  que              No  hay   una  distinción            Qué   significa  que   “red”

          alternativa   tomar?              entre  output  e  input?            no esté  habilitado   en  1?
```

## Página 21

[Ver página original](teorica-09-lts-y-logicas.pdf#page=21)

```text
                      FSP          -  Procesos                   y   acciones



                                            indexadas

             Un  buffer   de  1 lugar que  almacena    valores  de  0 a 3:




                     BUFF    =  (in[i:0..3]->out[i]->             BUFF).



             equivalente   a


                     BUFF    =  (in[0]->out[0]->BUFF

                                |in[1]->out[1]->BUFF
                                |in[2]->out[2]->BUFF

                                |in[3]->out[3]->BUFF

                                ).


             o usando   un  parámetro    de  proceso   con un  valor por  defecto:




                     BUFF(N=3)      =  (in[i:0..N]->out[i]->             BUFF).




                                                                                                  Azúcar

                                                                                                 sintáctico
```

## Página 22

[Ver página original](teorica-09-lts-y-logicas.pdf#page=22)

```text
                FSP        -   Declaración                   de      Constantes                  y



                                                 Rangos







        Indices  pueden    usarse  para

        modelar   cómputo:












        const    N  =  1

        range    T  =  0..N

        range    R  =  0..2*N


        SUM              =  (in[a:T][b:T]->TOTAL[a+b]),
        TOTAL[s:R]       =  (out[s]->SUM).


                                                                                                  Azúcar

                                                                                                 sintáctico
```

## Página 23

[Ver página original](teorica-09-lts-y-logicas.pdf#page=23)

```text
                              FSP               -    Guardas






                La  alternativa (when    B x -> P  | y -> Q) significa que  cuando   la guarda  B  es

          verdadera    entonces   tanto x como   y pueden    ser elegidas,  caso  contrario,  si B es falso

                                   entonces   la acción  x no puede   ser elegida.




          COUNT    (N=3)       =  COUNT[0],
          COUNT[i:0..N]        =  (when(i<N)       inc->COUNT[i+1]

                                  |when(i>0)       dec->COUNT[i-1]

                                  ).















                                                                                                  Azúcar

                                                                                                 sintáctico
```

## Página 24

[Ver página original](teorica-09-lts-y-logicas.pdf#page=24)

```text
                              FSP               -    Guardas







           Una  alarma   que cuenta   N  ticks  y hace  beep   y que puede   ser  parado   en

           cualquier  momento



          COUNTDOWN       (N=3)    =  (start->COUNTDOWN[N]),

          COUNTDOWN[i:0..N]           =
                            (when(i>0)       tick->COUNTDOWN[i-1]

                            |when(i==0)beep->STOP

                            |stop->STOP
                            ).














                                                                                                  Azúcar

                                                                                                 sintáctico
```

## Página 25

[Ver página original](teorica-09-lts-y-logicas.pdf#page=25)

```text
                ¿Vínculo        con     teoría     de    lenguajes          y  autómatas?
```

## Página 26

[Ver página original](teorica-09-lts-y-logicas.pdf#page=26)

```text
                Composición                                   en         paralelo








              Si  P y  Q  son   procesos    entonces     (P||Q)   representa     la ejecución

              concurrente      de  P  and  Q.



                    ITCH       =   (scratch->STOP).

                    CONVERSE        =   (think->talk->STOP).



                    ||CONVERSE_ITCH              =   (ITCH     ||    CONVERSE).


                                      lts(P||Q)      =  lts(P)   || lts(Q)




              La  composición       paralela   es  una   forma    de  construir   un  modelo     que

              tiene  todas    las ejecuciones      posibles    de  la ejecucíon     paralela   de

              dos   procesos.     Una   vez  compuesto,       es  un  proceso    sequencial.
```

## Página 27

[Ver página original](teorica-09-lts-y-logicas.pdf#page=27)

```text
                                  Composición                              en



                                   paralelo                  de       LTS
```

## Página 28

[Ver página original](teorica-09-lts-y-logicas.pdf#page=28)

```text
                                   Interleaving










                                                                                          2 estados



         2 estados                                     3 estados


                                                           scratch
                           CONVERSE_ITCH

                           2 x 3 estados

                                                            scratch




                                     think       talk       scratch



                                0           1           2           3           4           5
         ITCH
                                       CONVERSE
                             (0,0)       (0,1)       (0,2)       (1,2)        (1,1)       (1,0)

                                                                         talk        think
```

## Página 29

[Ver página original](teorica-09-lts-y-logicas.pdf#page=29)

```text
               Paralelismo                          vs      Concurrencia






                              Concurrencia:
                                                                         A
                      Procesamiento lógicamente simultáneo.
                   No implica múltiples unidades de procesamiento.
                                                                         B
                     Requiere ejecución “interleaved” en un UP.
                                                                         C
                                Paralelismo
                       Procesamiento físicamente simultáneo.
                                                                                              Tiempo
                            Involucra múltiples UPs.







                                  El modelo   de  concurrencia    de FSP   es uno

                                    de “interleaving”.  No  puede   modelar   la

                                  ocurrencia   de  dos  eventos  en  simultáneo.







                  Modelos   para  “true concurrency”   ver redes  de  petri o estructuras de  kripke.
```

## Página 30

[Ver página original](teorica-09-lts-y-logicas.pdf#page=30)

```text
                Acciones                             compartidas








                           MAKER         =   (make->ready->MAKER).

                           USER          =   (ready->use->USER).




                           ||MAKER_USER                 =   (MAKER         ||     USER).
```

## Página 31

[Ver página original](teorica-09-lts-y-logicas.pdf#page=31)

```text
                Modelado                           de         Handshake



           Un  handshake   es una acción  para la cuál  se pide  un acknowledge



             MAKERv2           =   (make->ready->used->MAKERv2).

             USERv2            =   (ready->use->used                      ->USERv2).




             ||MAKER_USERv2                   =   (MAKERv2            ||    USERv2).



                                                                                        3 x 3 = 9
                                                                                        estados?








                                                                              La  interacción

                                                                              restringe  el
                                                                              comportamiento

                                                                              global.
```

## Página 32

[Ver página original](teorica-09-lts-y-logicas.pdf#page=32)

```text
                              Equivalencia                         de       LTS





                                       (MAKERv2           ||     USERv2)


      (0,0)         (1,0)        (2,1)        (2,2)









       (0,1)        (0,2)       (1,1)        (1,2)
                                         use
               use
                                                7
          4            5           6



             (2,0)


                8




                         Son     equivalentes                 estos        dos      LTS?
```

## Página 33

[Ver página original](teorica-09-lts-y-logicas.pdf#page=33)

```text
               Sincronización                                           Múltiple







              MAKE_A            =   (makeA->ready->used->MAKE_A).

              MAKE_B            =   (makeB->ready->used->MAKE_B).

              ASSEMBLE          =   (ready->assemble->used->ASSEMBLE).


              ||FACTORY           =   (MAKE_A          ||    MAKE_B        ||    ASSEMBLE).
```

## Página 34

[Ver página original](teorica-09-lts-y-logicas.pdf#page=34)

```text
                 Procesos                             compuestos




           Un  proceso   compuesto    es  la composición   en  paralelo  de procesos.   Un  proceso

           compuesto    puede   usarse   en nuevas   composiciones.





                 ||MAKERS            =   (MAKE_A           ||    MAKE_B).



                 ||FACTORY             =   (MAKERS           ||    ASSEMBLE).







           Por  sustitución  y aplicando   conmutatividad    y asociatividad  obtenemos:



             ||FACTORY            =    (MAKE_A          ||     MAKE_B         ||


             ASSEMBLE).
```

## Página 35

[Ver página original](teorica-09-lts-y-logicas.pdf#page=35)

```text
                    Modelado                                 Sistemas





                                Concurrentes









                                                  Parte     4/5
```

## Página 36

[Ver página original](teorica-09-lts-y-logicas.pdf#page=36)

```text
            Elección                        no-determinística






                         El  proceso      (x->   P | x  ->  Q)   describe      una

                          elección     no   determinística       entre    P  or  Q.









       COIN    =   (toss->HEADS         |  toss->TAILS),
       HEADS     =  (heads->COIN),

       TAILS     =  (tails->COIN).













        Ojo,  No  determinismo      no  es lo mismo    que
                         equi-probable
```

## Página 37

[Ver página original](teorica-09-lts-y-logicas.pdf#page=37)

```text
                          No           determinismo









           COIN   =  (toss->HEADS|toss->TAILS),

           HEADS=    (heads->COIN),
           TAILS=    (tails->COIN).




           PLAY   =  (toss->    (heads    ->  HEADS1    |  tails    ->  TAILS1)),
           TAILS1    =  (toss   ->   (heads   ->   HEADS1    | tails    ->  TAILSWIN)),

           TAILSWIN     =  (twoTails     ->  end   ->  STOP),
           HEADS1     =  (toss    ->  (heads    ->  HEADSWIN     |  tails   ->  TAILS1)),

           HEADSWIN     =  (twoHeads     ->  end   ->  STOP).


           ||JUEGO    =  (PLAY    ||  COIN).
```

## Página 38

[Ver página original](teorica-09-lts-y-logicas.pdf#page=38)

```text
                    Juego                 con           moneda                      (v2)









      COIN   =  (toss->HEADS|toss->TAILS),
      HEADS=    (heads->COIN),

      TAILS=    (tails->COIN).





      REPETICIONES(MAX        =  3)  =  (toss->    (heads    ->   HEADS[1]    |  tails    ->  TAILS[1])),
      TAILS[i:1..MAX-1]         =  (toss   ->  (heads    ->  HEADS[1]     |  tails   ->   TAILS[i+1])),

      TAILS[MAX]      =  (fin   ->  STOP),
      HEADS[i:1..MAX-1]          =  (toss   ->   (heads    ->  HEADS[i+1]     |  tails    ->  TAILS[1])),

      HEADS[MAX]      =  (fin   ->  STOP).


      ||JUEGO     = (REPETICIONES(2)        ||   COIN).
```

## Página 39

[Ver página original](teorica-09-lts-y-logicas.pdf#page=39)

```text
            Ocultamiento                                     de         acciones



        En  FSP no se puede  usar el literal “tau” para representar la acción que representa cómputo interno.



                  Charlatan        =   (talk     ->   think      ->   STOP)\{think}.


                  Philosopher          =  (think      ->   talk     ->   STOP)@{talk}.
```

## Página 40

[Ver página original](teorica-09-lts-y-logicas.pdf#page=40)

> Nota editorial sobre la imagen. La imagen define la composición paralela M ∥ N sobre el producto cartesiano de estados, con alfabeto unión y estado inicial (s₀ᴹ,s₀ᴺ). Una transición propia de M mantiene fijo el estado de N, y viceversa. Cuando la etiqueta pertenece a ambos alfabetos, ambos LTS deben avanzar con esa etiqueta. La relación es la menor que satisface las tres reglas.

## Página 41

[Ver página original](teorica-09-lts-y-logicas.pdf#page=41)

```text
                Composición                                          Paralela









        Charlatan        =   (talk     ->   think      ->   STOP)\{think}.

        Philosopher          =  (think      ->    talk    ->    STOP)@{talk}.


        ||Debate        =  (Philosopher           ||   Charlatan).
```

## Página 42

[Ver página original](teorica-09-lts-y-logicas.pdf#page=42)

```text
                                                                    Minimización           que

                                                                  preserva        semántica
```

## Página 43

[Ver página original](teorica-09-lts-y-logicas.pdf#page=43)

```text
                       Etiquetado                       de        Procesos






               a:P   prefija    a  cada     acción     en    el alfabeto      de   P   con    a.





           Dos    instancias      de   un  proceso     switch:


               SWITCH      =     (on->off->SWITCH).


               ||TWO_SWITCH           =   (a:SWITCH         ||   b:SWITCH).













            Un   vector    de   instancias      del  proceso     switch:


               ||SWITCHES(N=3)                =   (forall[i:1..N]                s[i]:SWITCH).

               ||SWITCHES(N=3)                =   (s[i:1..N]:SWITCH).
```

## Página 44

[Ver página original](teorica-09-lts-y-logicas.pdf#page=44)

```text
                         Prefijado                   de       Procesos








                {a1,..,ax}::P    remplaza      cada   acción    n  del  alfabeto   de   P  con

                etiquetas     a1.n,…,ax.n.      Además,      cada    transición

                (n->X)    en  P  se  remplaza      con  ({a1.n,…,ax.n}       ->X).





           Prefijado    de  procesos    es  util para  modelar    recursos    compartidos       resources:





                 RESOURCE        =   (acquire->release->RESOURCE).

                 USER     =   (acquire->use->release->USER).



                 ||RESOURCE_SHARE              =   (a:USER       ||    b:USER
                                                           ||   {a,b}::RESOURCE).
```

## Página 45

[Ver página original](teorica-09-lts-y-logicas.pdf#page=45)

```text
                        Compartiendo                               recursos
















         Como   asegura  el modelo que  el

         mismo  usuario  que  adquiere un
         recurso es el que lo libera?
```

## Página 46

[Ver página original](teorica-09-lts-y-logicas.pdf#page=46)

```text
            Diagrama          arquitectónico            - recursos        compartidos





                                                            PRINTER_SHARE
                                   a:USER

                                       printer

                                                                     printer:

                                                                     RESOURCE

                                                                 acquire
                                  b:USER
                                                                 release
                                        printer







                        RESOURCE      =  (acquire->release->RESOURCE).

                        USER    =        (printer.acquire->use
                                           ->printer.release->USER)\{use}.



                        ||PRINTER_SHARE
                           =  (a:USER||b:USER||{a,b}::printer:RESOURCE).
```

## Página 47

[Ver página original](teorica-09-lts-y-logicas.pdf#page=47)

```text
                                  Verificación                           vía




               Bisimulación                              y     Abstracción






                                     ack
                                                                                        entrega
                                                                           ack
                    msg
                          Emisor
                                                                                Receptor
                                                  error
                                    error
                         envío                         Canal
                                                                                      recv
                                                  entrada     salida
                                envío                                           recv



                        ||ProtocoloAbstracto         = Protocolo@{msg,entrega}.







                                                                               ≈
```

## Página 48

[Ver página original](teorica-09-lts-y-logicas.pdf#page=48)

```text
                                        Deadlock






                                                    RESOURCE       =  (get->put->RESOURCE).
                         SYS
           p:P                    printer:
                printer           RESOURCE          P   =  (printer.get->scanner.get

                                  get
                                                            ->copy
              scanner
                                  put
                                                            ->printer.put->scanner.put->P).
           q:Q
                                  scanner:
                                                    Q   =  (scanner.get->printer.get
                printer           RESOURCE
                                                            ->copy
                                  get
              scanner
                                  put                       ->scanner.put->printer.put->Q).
                                                    ||SYS     =  (p:P||q:Q
                                                         ||{p,q}::printer:RESOURCE

                                                         ||{p,q}::scanner:RESOURCE

                                                         ).
```

## Página 49

[Ver página original](teorica-09-lts-y-logicas.pdf#page=49)

```text
                       Deadlock                               Analysis




























        Una     búsqueda           breadth       first   search      desde       el  estado       inicial


                     encuentra         el  camino        más     corto     a  deadlock.




                                           Complejidad:             |Δ|
```

## Página 50

[Ver página original](teorica-09-lts-y-logicas.pdf#page=50)

```text
                                                       Los              Filósofos






















                               FORK    =  (get    ->   put   ->   FORK).

                               PHIL    =  (sitdown->right.get->left.get
                                            ->eat->right.put->left.put

                                            ->arise->PHIL).




                               ||DINERS(N=5)=          forall     [i:0..N-1]
                                    (phil[i]:PHIL         ||

                                    {phil[i].left,phil[((i-1)+N)%N].right}::FORK

                                    ).
```

## Página 51

[Ver página original](teorica-09-lts-y-logicas.pdf#page=51)

```text
                  Explosión                             de          Estados











               •   La   composición            paralela       de    LTS    produce         una


                   explosión        de    estados        que    en    el  peor     caso     es

                   S^C      con    S   el  numero        de   estados        de    una


                   componente             y  C  el  número         de   componentes.




               •   Existen      algoritmos         que     evitan     construir       todo     el

                   espacio        de   estados.        C.f.   Partial     order     reduction
```

## Página 52

[Ver página original](teorica-09-lts-y-logicas.pdf#page=52)

```text
                                        Deadlock






                                                    RESOURCE       =  (get->put->RESOURCE).
                         SYS
           p:P                    printer:
                printer           RESOURCE          P   =  (printer.get->scanner.get

                                  get
                                                            ->copy
              scanner
                                  put
                                                            ->printer.put->scanner.put->P).
           q:Q
                                  scanner:
                                                    Q   =  (scanner.get->printer.get
                printer           RESOURCE
                                                            ->copy
                                  get
              scanner
                                  put                       ->scanner.put->printer.put->Q).
                                                    ||SYS     =  (p:P||q:Q
                                                         ||{p,q}::printer:RESOURCE

                                                         ||{p,q}::scanner:RESOURCE

                                                         ).
```

## Página 53

[Ver página original](teorica-09-lts-y-logicas.pdf#page=53)

```text
                Solución                           de           Deadlock






                                                   Timeouts




                       RESOURCE      =  (get->put->RESOURCE).



                       P                =  (printer.get->         GETSCANNER),
                       GETSCANNER       =  (scanner.get->copy->printer.put

                                                                       ->scanner.put->P
                                           |timeout      ->   printer.put->P

                                           )\{timeout}.

                       Q                =  (scanner.get->         GETPRINTER),
                       GETPRINTER       =  (printer.get->copy->printer.put

                                                                       ->scanner.put->Q
                                           |timeout      ->   scanner.put->Q

                                           )\{timeout}.


                       ||SYS     =  (p:P||q:Q

                            ||{p,q}::printer:RESOURCE
                            ||{p,q}::scanner:RESOURCE

                            ).
```

## Página 54

[Ver página original](teorica-09-lts-y-logicas.pdf#page=54)

```text
                Solución                           de           Deadlock




                                      Preemption         vía  Watchdog




                      RESOURCE      =  (get->put->RESOURCE).


                      P                =  (printer.get->         GETSCANNER),
                      GETSCANNER       =  (scanner.get->copy->printer.put

                                                                      ->scanner.put->P

                                          |abort     ->  printer.put->P
                                          ).

                      Q                =  (scanner.get->         GETPRINTER),
                      GETPRINTER       =  (printer.get->copy->printer.put

                                                                      ->scanner.put->Q

                                          |abort     ->  scanner.put->Q
                                          ).


                      WATCHDOG      =  (abort     ->  WATCHDOG).



                      ||SYS    =  (p:P||q:Q
                           ||{p,q}::printer:RESOURCE

                          ||{p,q}::scanner:RESOURCE
                           ||{p,q}::WATCHDOG

                          ).
```

## Página 55

[Ver página original](teorica-09-lts-y-logicas.pdf#page=55)

```text
                Solución                           de           Deadlock




                             Parametrización           para    romper      simetría





                     PHIL(I=0)

                        =  (when    (I%2==0)      sitdown

                                  ->left.get->right.get
                                  ->eat

                                 ->left.put->right.put
                                 ->arise->PHIL

                           |when    (I%2==1)      sitdown

                               ->right.get->left.get
                               ->eat

                                ->left.put->right.put

                                ->arise->PHIL
                           ).




                     ||DINERS(N=5)=          forall     [i:0..N-1]
                          (phil[i]:PHIL          ||

                          {phil[i].left,phil[((i-1)+N)%N].right}::FORK

                          ).
```

## Página 56

[Ver página original](teorica-09-lts-y-logicas.pdf#page=56)

```text
                     Puente                       3.0           -     Turnos






              const    True    =  1

              const    False    =  0
              range    B  =  False..True



              BRIDGE    =  BRIDGE[0][0][0][0][True],

              BRIDGE[nr:T][nb:T][wr:T][wb:T][bt:B]                     =
                 (red[ID].request           ->   BRIDGE[nr][nb][wr+1][wb][bt]

                 |when    (nb==0     &&  (wb==0||!bt))

                     red[ID].enter          ->   BRIDGE[nr+1][nb][wr-1][wb][bt]

                 |red[ID].exit              ->   BRIDGE[nr-1][nb][wr][wb][True]
                 |blue[ID].request          ->   BRIDGE[nr][nb][wr][wb+1][bt]

                 |when    (nr==0     &&  (wr==0||bt))

                     blue[ID].enter         ->   BRIDGE[nr][nb+1][wr][wb-1][bt]

                 |blue[ID].exit             ->   BRIDGE[nr][nb-1][wr][wb][False]

                 ).





                                 Algoritmo    de  Peterson   para   exclusión   mutua
```

## Página 57

[Ver página original](teorica-09-lts-y-logicas.pdf#page=57)

```text
                         Lógica                       Temporal





                                    Lineal                    (LTL)
```

## Página 58

[Ver página original](teorica-09-lts-y-logicas.pdf#page=58)

```text
                               Lógica                       Modal













            •
                Estudia      la  formalización         del   razonamiento           que

                involucra       aserciones        con    modalidades,           tales    como

                “necesariamente”              o  “posiblemente”




            •
                Un    enfoque       tradicional       es  complementar             los
                operadores         “clásicos”        con    operadores         modales:

                –
                   [] : Necesidad     (Box)
                –
                   <>  : Posibilidad     (Diamond)
```

## Página 59

[Ver página original](teorica-09-lts-y-logicas.pdf#page=59)

> Nota editorial sobre la imagen. La imagen define la sintaxis booleana y temporal de LTL y su semántica en una traza infinita de conjuntos de proposiciones. X evalúa en i+1. G y F cuantifican, respectivamente, sobre todas o alguna posición j≥i. P U Q requiere una Q en j≥i y P en todas las posiciones desde i hasta antes de j. P W Q permite también que P valga siempre. La satisfacción de una traza se evalúa en la posición 0.
