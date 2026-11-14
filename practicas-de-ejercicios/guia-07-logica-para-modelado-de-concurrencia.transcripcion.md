# Lógica para modelado de concurrencia — transcripción

- Fuente: [guia-07-logica-para-modelado-de-concurrencia.pdf](guia-07-logica-para-modelado-de-concurrencia.pdf).
- Páginas: 5.
- SHA-256: `8b219741f36e1d810901a11d92f3b0a87c584397dea513186695b2ac3c5e7a08`.

> Texto por página con disposición aproximada. La extracción puede unir palabras o desplazar símbolos. Cotejar fórmulas, código y diagramas en el PDF.

## Página 1

[Ver página original](guia-07-logica-para-modelado-de-concurrencia.pdf#page=1)

```text
                 Programación      Concurrente      y Paralela


                    Práctica 7: Lógica para modelado de concurrencia
                               2do Cuatrimestre 2026


       Ejercicio 1. Considere el programa incr(), que incrementa una variable x:

           int x = 0

           void incr() {
           p1:    int loc = x
           p2:    x = loc + 1
           }

          Cada línea etiquetada se ejecuta de forma atómica. Escriba el diagrama LTS para
       incr()||incr().

       Ejercicio 2. Considere el siguiente programa:
           Lock a, b

           void p() {                 void q() {
           p1: a.lock()               q1: b.lock()
           p2: b.lock()               q2: a.lock()
           p3: usar()                 q3: usar()
           p4: b.unlock()             q4: a.unlock()
           p5: a.unlock()             q5: b.unlock()
           }                          }

          Construya el LTS de p()||q() y encuentre el camino más corto a un deadlock. Proponga dos
       modificaciones que lo eliminen. En una, un thread se rinde y libera lo que tiene (considere la
       instrucción atómica tryLock(): para x.tryLock(), si x está libre lo toma y devuelve true, y si
       no devuelve false sin bloquearse.). En la otra, se cambia el orden de adquisición. Muestre que
       ninguno de los dos LTS resultantes tiene deadlock.

       Ejercicio 3. Considere el siguiente programa en Go:

           c1 := make(chan int)
           c2 := make(chan int)

           go func() { c2 <- 2; <-c1 }()
           c1 <- 1
           <-c2

          Modele el programa como composición de LTS, primero con los canales tal como están y
       despuéscreándolosconmake(chan int, 1).Determinesicadaversiónpuedequedarbloqueada.

       Ejercicio 4. Dadas las trazas
           σ = {p} {p,q} {q} ({p})ω
            1

                                         1
```

## Página 2

[Ver página original](guia-07-logica-para-modelado-de-concurrencia.pdf#page=2)

```text
       Prog. Concurrente y Paralela                           Práctica 7: Lógica


           σ = ({p} {q})ω
            2
           σ = {q} ∅ ({p,q})ω
            3
           σ = {p} ∅ ({p,q} {p} ∅)ω
            4
       decida cuáles satisfacen □♢q, ♢□p, pUq, □(p → ♢q), ⃝⃝q y □(pUq).
       Ejercicio 5. Decida cuáles de las siguientes equivalencias son correctas. Demuéstrelas o dé un
       contraejemplo.
         a) ♢(φ∧ψ) ≡ ♢φ∧♢ψ
         b) □(φ∧ψ) ≡ □φ∧□ψ

         c) ⃝♢φ ≡  ♢⃝φ
         d) □♢φ → □♢ψ ≡ □(φ → ♢ψ)

         e) □φ → ♢ψ ≡ φU(ψ ∨¬φ)
          Sugerencia para el ítem e: reescriba el lado izquierdo como una fórmula de la forma ♢χ, y
       después piense en la primera posición en la que vale χ.

       Ejercicio 6. Una fórmula es satisfacible si existe al menos una traza que la satisface, y es válida
       si todas las trazas la satisfacen. Para cada una de las siguientes fórmulas, decida si es satisfacible

       y si es válida.
         a) ⃝⃝a  → ⃝a

         b) ⃝(a∨♢a) → ♢a
         c) □a → ¬⃝(¬a∧□¬a)

         d) (□a)U(♢b) → □(aU♢b)
         e) ♢b → (aUb)

       Ejercicio 7. Dos threads acceden a una sección crítica protegida por un lock. Las proposiciones
       son request , que vale desde que el thread i llama a lock() hasta que libera el lock, critical ,
               i                                                          i
       que vale mientras el thread i está en la sección crítica, y release , que vale en el instante en que
                                                    i
       el thread i llama a unlock(). Escriba en LTL las siguientes propiedades y clasifique cada una
       como safety o liveness:
         a) Exclusión mutua: un solo thread está en la sección crítica a la vez.
         b) Permanencia finita: ningún thread se queda en la sección crítica para siempre.
         c) Ausencia de inanición: si un thread pide el lock, eventualmente entra a la sección crítica.
         d) Acceso alternado: los threads se turnan estrictamente para entrar a la sección crítica.

         e) Si el thread 1 pide el lock infinitas veces, entra a la sección crítica infinitas veces.
         f) Si a partir de algún momento el thread 1 pide el lock sin parar, entra a la sección crítica
           infinitas veces.

         g) Mientras el thread 1 espera, el thread 2 entra a la sección crítica a lo sumo una vez.



                                         2
```

## Página 3

[Ver página original](guia-07-logica-para-modelado-de-concurrencia.pdf#page=3)

```text
       Prog. Concurrente y Paralela                           Práctica 7: Lógica


       Ejercicio 8. Defina en LTL los siguientes operadores:
           φNψ  (“en el próximo”): la próxima vez que vale ψ, también vale φ.

           φMψ   (“mientras”): φ vale al menos mientras valga ψ.
           φBψ  (“antes”): si ψ vale alguna vez, φ vale antes.

       Ejercicio 9. Un thread usa un lock en un loop: lo toma, lo suelta, lo vuelve a tomar, y así
       para siempre. Con la proposición held, que vale mientras el thread tiene el lock, la propiedad “el
       thread alterna entre tener el lock y no tenerlo” se escribió como

                            (cid:16)                   (cid:17)
                          □  (¬held → ⃝held)∧(held → ⃝¬held)
       La fórmula vale para el thread solo, pero deja de valer si se lo compone con otro thread que
       nunca usa el lock, por ejemplo uno que solo incrementa un contador propio en un loop. Reescriba
       la propiedad para que no se vea afectada por esa composición.

       Ejercicio 10. El siguiente LTS abstrae a un thread que usa tryLock() con backoff. tryLock()
       intenta tomar el lock sin bloquearse: si está libre lo toma, y si no, devuelve false enseguida.
       Con backoff, cuando el intento falla el thread espera un rato antes de volver a intentar, para no

       competir todo el tiempo por el lock. Después de esperar puede reintentar (retry) o rendirse y
       volver al principio (giveup). En el LTS, BACKOFF es ese estado de espera. La competencia
       con los demás threads aparece como la elección entre acquire y fail.

                                      release




                   IDLE    request   TRYING    acquire  CRITICAL


                                  retry    fail



                          giveup    BACKOFF


          En las fórmulas, las proposiciones son los nombres de los estados: en cada posición de la traza
       vale el nombre del estado en el que está el thread. Decida si valen las siguientes fórmulas:
           □(TRYING → ♢CRITICAL)

           □(TRYING → ♢(CRITICAL∨IDLE))
           □(BACKOFF  → □¬CRITICAL)
           □(BACKOFF  → ♢TRYING)

       Para las que no valen, dé el contraejemplo. Identifique los conjuntos terminales y diga cuáles
       fórmulas pasan a valer bajo fair choice.


                                         3
```

## Página 4

[Ver página original](guia-07-logica-para-modelado-de-concurrencia.pdf#page=4)

```text
       Prog. Concurrente y Paralela                           Práctica 7: Lógica


       Ejercicio 11. Dibuje un LTS observador con estado de error para la siguiente propiedad: nadie
       usaelrecursosinhaberloadquirido,ynohaydosacquiresinunreleaseenelmedio.Elalfabeto
       es {acquire, use, release}. Escriba la misma propiedad en LTL, tomando como proposiciones
       las acciones: en cada posición de la traza vale solo la acción de ese paso.

       Ejercicio 12. Dos threads (i = 0,1) ejecutan:

           int s = 1
           bool y[2] = {false, false}

           loop {
             noncritical()
             (y[i], s) = (true, i)
             await (!y[1-i] || s == i)

             critical()
             y[i] = false
           }
          donde (y[i], s) = (true, i) es una asignación múltiple atómica.

         a) Construya el LTS del programa.
         b) Escriba en LTL exclusión mutua y ausencia de inanición, usando proposiciones sobre el
           estado, como critical , que vale cuando el thread i está en critical().
                          i
         c) VerifiqueambaspropiedadessobreelLTS.Paralasquefallen,déuncontraejemploydecida
           si pasan a valer bajo fair choice.

       Ejercicio 13. Dos threads ejecutan:
           int y1 = 0, y2 = 0


           thread 1:                           thread 2:
           loop {                              loop {
             noncritical()                       noncritical()
             y1 = y2 + 1                         y2 = y1 + 1
             await (y2 == 0 || y1 < y2)          await (y1 == 0 || y2 < y1)
             critical()                          critical()
             y1 = 0                              y2 = 0
           }                                  }

          Las asignaciones y1 = y2 + 1 y y2 = y1 + 1 son atómicas.

         a) Construya el LTS alcanzable restringido a y1 ≤ 2 y y2 ≤ 2.
         b) Muestre una ejecución que pruebe que el LTS completo es infinito.

         c) EscribaenLTLexclusiónmutua,ausenciadedeadlockyausenciadeinanición,usandopro-
           posiciones sobre el estado, como critical , que vale cuando el thread i está en critical().
                                       i
           Verifique las tres propiedades.


                                         4
```

## Página 5

[Ver página original](guia-07-logica-para-modelado-de-concurrencia.pdf#page=5)

```text
       Prog. Concurrente y Paralela                           Práctica 7: Lógica


       Ejercicio 14. Unservidoratiendeadosclientes.Lospedidoslleganporuncanal,ylasrespuestas
       vuelven por otro canal que comparten los dos clientes:

           requests := make(chan int)
           responses := make(chan int)

           // server
           for { i := <-requests; responses <- i  }

           // client i (i = 1, 2)
           for { requests <- i; r := <-responses;  use(r) }


         a) Modele el sistema como composición de LTS, con los canales tal como están y con
           responses creado con make(chan int, 1).
         b) Escriba en LTL, usando proposiciones sobre el estado, que cada cliente recibe siempre la
           respuesta a su propio pedido, y que todo cliente que manda un pedido termina recibiendo
           una respuesta.

         c) Verifique las dos propiedades en las dos versiones. Para las que fallen, dé un contraejemplo
           y decida si pasan a valer bajo fair choice.






























                                         5
```
