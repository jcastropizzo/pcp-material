# Simulacro del segundo parcial 2026-2C — soluciones suministradas — transcripción

- Fuente: [modelo-segundo-parcial-2026-2c-soluciones.pdf](modelo-segundo-parcial-2026-2c-soluciones.pdf).
- Páginas: 10.
- SHA-256: `37713392c72bc6295fba715e19e82d8829f8434306828cd0f32420b935a18257`.

> Texto por página con disposición aproximada. La extracción puede unir palabras o desplazar símbolos. Cotejar fórmulas, código y diagramas en el PDF.

## Página 1

[Ver página original](modelo-segundo-parcial-2026-2c-soluciones.pdf#page=1)

```text
        Programación  Concurrente  y
                                          Nombre y apellido:
        Paralela
                                          No orden:   L.U.:     Cant. hojas:
        Departamento de Computación – FCEyN – UBA
                                                         1   2  3   4  Nota
        Segundo cuatrimestre de 2026
        Simulacro 2do. parcial – 2do. cuat. de 2026

            Cada ejercicio debe realizarse en hojas separadas y numeradas. Debe identificarse cada hoja con nombre, apellido, LU y
            número de orden. Hojas sin identificar no serán corregidas.
            Entregue esta hoja junto al examen, la misma no se incluye en la cantidad total de hojas entregadas.
            Cada código o pseudocódigo debe estar bien explicado y justificado. ¡Obligatorio!

            Toda suposición o decisión que tome deberá justificarla adecuadamente. Si la misma no es correcta o no se condice con el
            enunciado no será tomada como válida y será corregida acorde.
            La devolución de los exámenes corregidos es personal. Los pedidos de revisión se realizarán por escrito, antes de retirar el
            examen corregido del aula.
            Los parciales tienen dos notas: I (Insuficiente) y A (Aprobado).
            Para tener la nota aprobada es necesario tener al menos dos ejercicios calificados como bien y el restante al menos regular.
            Además, alguno de los ejercicios calificados como bien debe ser el de STM o el de mensajes.
        Ejercicio 1: Centro de atención. Una empresa de internet tiene una línea de atención al cliente que colapsa
        en cada corte masivo, cuando a las consultas de siempre se suman todos los que se quedaron sin servicio. Para
        el próximo corte, decidió atender con agentes de IA, y antes de ponerlos a funcionar quiere simular la línea con
        STM.
          En la simulación, las llamadas son de dos tipos, urgentes (clientes sin servicio) y comunes (consultas de
        facturación). Cada tipo tiene su propia cola de espera, con lugar para K llamadas. Cada cliente que llama es un
        thread que ejecuta llamar y deja su llamada en la cola del tipo que corresponde. Si la cola está llena, el cliente
        espera a que se libere un lugar. Una vez en la cola, espera en la línea hasta que un agente toma su llamada, y
        recién ahí llamar retorna.
          Cada agente es un thread que ejecuta agente. Atiende de a una llamada por vez y siempre prefiere una
        urgente, si hay. Para que las comunes no queden relegadas, un agente que atendió 3 urgentes seguidas tiene que
        atender una común antes de volver a una urgente, si hay alguna común esperando.
          Se cuenta con lo siguiente:
        1 data Tipo = Urgente | Comun
        2 type Llamada = Int
        3 atender :: Llamada -> IO () -- El agente atiende la llamada
          Se cuenta además con el siguiente esqueleto:

        1 data Centro = ...
        2
        3 nuevoCentro :: Int -> IO Centro
        4 nuevoCentro k = ...
        5
        6 llamar :: Centro -> Tipo -> Llamada -> IO ()
        7 llamar centro tipo llamada = ...
        8
        9 agente :: Centro -> IO ()
        10 agente centro = ...
          a) Complete el esqueleto usando STM, incluida la representación del centro. La solución no debe tener
            deadlocks ni race conditions. Justifique todas las decisiones tomadas.
          b) Indique si su solución puede sufrir starvation, justificando a partir de cómo implementa Haskell las
            transacciones. Si puede, describa una traza que lleve a esa situación.
          c) La empresa contrata un centro tercerizado como respaldo, con sus propias colas y agentes. Todos los
            clientes aceptan que los atienda el primero de los dos centros que tome su llamada. Explique, sin escribir
            código, cómo adaptaría su solución para estos clientes. Justifique.



                                       1 de 10
```

## Página 2

[Ver página original](modelo-segundo-parcial-2026-2c-soluciones.pdf#page=2)

```text
        Segundo cuatrimestre de 2026 Programación Concurrente y Paralela – Simulacro 2do. parcial


          Solución.

          1 import Control.Concurrent.STM
          2 import Control.Monad
          3
          4 type EnLinea = (Llamada, TVar Bool)
          5
          6 data Centro = Centro Int (TVar [EnLinea]) (TVar [EnLinea])
          7
          8 nuevoCentro :: Int -> IO Centro
          9 nuevoCentro k = do
          10 urgentes <- newTVarIO []
          11 comunes <- newTVarIO []
          12 return (Centro k urgentes comunes)
          13
          14 encolar :: Int -> TVar [EnLinea] -> EnLinea -> STM ()
          15 encolar capacidad cola enLinea = do
          16 esperando <- readTVar cola
          17 when (length esperando >= capacidad) retry
          18 writeTVar cola (esperando ++ [enLinea])
          19
          20 esperarAtencion :: TVar Bool -> STM ()
          21 esperarAtencion atendida = do
          22 listo <- readTVar atendida
          23 when (not listo) retry
          24
          25 llamar :: Centro -> Tipo -> Llamada -> IO ()
          26 llamar (Centro k urgentes comunes) tipo llamada = do
          27 atendida <- newTVarIO False
          28 case tipo of
          29   Urgente -> atomically (encolar k urgentes (llamada, atendida))
          30   Comun -> atomically (encolar k comunes (llamada, atendida))
          31 atomically (esperarAtencion atendida)
          32
          33 tomarDe :: TVar [EnLinea] -> STM Llamada
          34 tomarDe cola = do
          35 esperando <- readTVar cola
          36 case esperando of
          37   [] -> retry
          38   ((llamada, atendida) : resto) -> do
          39    writeTVar cola resto
          40    writeTVar atendida True
          41    return llamada
          42
          43 tomarUrgente :: Centro -> TVar Int -> STM Llamada
          44 tomarUrgente (Centro _ urgentes _) seguidas = do
          45 llamada <- tomarDe urgentes
          46 n <- readTVar seguidas
          47 writeTVar seguidas (n + 1)
          48 return llamada
          49
          50 tomarComun :: Centro -> TVar Int -> STM Llamada
          51 tomarComun (Centro _ _ comunes) seguidas = do
          52 llamada <- tomarDe comunes
          53 writeTVar seguidas 0
          54 return llamada
          55
          56 elegir :: Centro -> TVar Int -> STM Llamada
          57 elegir centro seguidas = do
          58 n <- readTVar seguidas
          59 if n < 3
          60   then orElse (tomarUrgente centro seguidas) (tomarComun centro seguidas)
          61   else orElse (tomarComun centro seguidas) (tomarUrgente centro seguidas)
          62
          63 agente :: Centro -> IO ()
          64 agente centro = do
                                       2 de 10
```

## Página 3

[Ver página original](modelo-segundo-parcial-2026-2c-soluciones.pdf#page=3)

```text
        Programación Concurrente y Paralela – Simulacro 2do. parcial Segundo cuatrimestre de 2026


          65 seguidas <- newTVarIO 0
          66 forever (do
          67   llamada <- atomically (elegir centro seguidas)
          68   atender llamada)

        Ejercicio 2: Trapitos. Si uno va a un partido de fútbol en auto, no puede escaparle a una constante en la vida
        del hincha: los trapitos. Pero estamos en el siglo XXI, la barra se modernizó y quiere un simulador para probar
        qué tan efectivos son distintos modelos de organización. A cambio de un palco vitalicio, la barra nos encargó
        que simulemos el siguiente sistema de trapitos y lugares.
          Los lugares son finitos y están contados. La barra controla N lugares de estacionamiento que se alquilan a
        los autos que van llegando. ¿Cómo se hace eso?
          Cada hincha motorizado que llega pide un lugar y espera. Un trapito al azar (hay una cantidad arbitraria)
        toma el pedido y busca un lugar libre donde estacionar. Si no hay, espera; si hay, reserva el lugar, le avisa a la
        barra que ese lugar fue reservado y le avisa al hincha qué lugar le fue asignado. La barra, que es única, registra
        la reserva y cada 5 minutos suma 1 al costo del alquiler del lugar. Arranca en 1.
          Al terminar el partido (o al querer irse, si se va antes de que termine), el hincha le pregunta a la barra
        cuánto tiene que pagar e intenta pagar ese monto. Si en el medio pasaron otros 5 minutos y el precio aumentó,
        tiene que volver a consultar y reintentar, porque no puede retirar el auto mientras pague menos de lo debido.
        Cuando el pago alcanza, la barra lo deja retirarse y libera el lugar para otro hincha.
          Implementar utilizando comunicación por medio de mensajes, con las reglas vistas en clase1:
          a) Los threads trapito, hincha y barra. Habrá T trapitos, H hinchas y 1 barra. Pueden usar funciones y
            threads auxiliares. Para simular al hincha viendo el partido, llamen a una función imaginaria verPartido
            () después de que el hincha haya estacionado y antes de retirar el auto. La solución debe ser libre de
            deadlocks y se tiene que justificar. Se valora el uso de diagramas para dicha explicación.
          b) (Esta parte NO entra en el parcial, pero la pusimos porque nos gusta la discusión.) ¿Qué pasaría si, en
            vez de una barra, hubiera muchas? ¿Qué cambios harían?

          Solución. Si uno va a un partido de fútbol en auto, no puede escaparle a una constante en la vida del hincha:
          los trapitos. Pero estamos en el siglo XXI, la barra se modernizó y quiere un simulador para probar qué tan
          efectivos son distintos modelos de organización. A cambio de un palco vitalicio, la barra nos encargó que
          simulemos el siguiente sistema de trapitos y lugares.
            Los lugares son finitos y están contados. La barra controla N lugares de estacionamiento que se alquilan
          a los autos que van llegando. ¿Cómo se hace eso?
            Cada hincha motorizado que llega pide un lugar y espera. Un trapito al azar (hay una cantidad arbitraria)
          toma el pedido y busca un lugar libre donde estacionar. Si no hay, espera; si hay, reserva el lugar, le avisa
          a la barra que ese lugar fue reservado y le avisa al hincha qué lugar le fue asignado. La barra, que es única,
          registra la reserva y cada 5 minutos suma 1 al costo del alquiler del lugar. Arranca en 1.
            Al terminar el partido (o al querer irse, si se va antes de que termine), el hincha le pregunta a la barra
          cuánto tiene que pagar e intenta pagar ese monto. Si en el medio pasaron otros 5 minutos y el precio aumentó,
          tiene que volver a consultar y reintentar, porque no puede retirar el auto mientras pague menos de lo debido.
          Cuando el pago alcanza, la barra lo deja retirarse y libera el lugar para otro hincha.
            Implementar utilizando comunicación por medio de mensajes, con las reglas vistas en clase2:

           a) Los threads trapito, hincha y barra. Habrá T trapitos, H hinchas y 1 barra. Pueden usar funciones y
             threads auxiliares. Para simular al hincha viendo el partido, llamen a una función imaginaria verPartido
             () después de que el hincha haya estacionado y antes de retirar el auto. La solución debe ser libre de
             deadlocks y se tiene que justificar. Se valora el uso de diagramas para dicha explicación.
           b) (Esta parte NO entra en el parcial, pero la pusimos porque nos gusta la discusión.) ¿Qué pasaría si,
             en vez de una barra, hubiera muchas? ¿Qué cambios harían?

            Solución:

          1 package main
          2
          3 import (
          1Sin memoria compartida y con el nil propio de cada tipo. Los canales se comportan igual que en Go.

                                       3 de 10
```

## Página 4

[Ver página original](modelo-segundo-parcial-2026-2c-soluciones.pdf#page=4)

```text
        Segundo cuatrimestre de 2026 Programación Concurrente y Paralela – Simulacro 2do. parcial


          4    "strconv"
          5    "sync"
          6    "time"
          7 )
          8
          9 func lugares(cant int, disponibles chan<- int, liberados <-chan int) {
          10   for i := 0; i < cant; i++ {
          11     disponibles <- i
          12   }
          13   for lugar := range liberados {
          14     disponibles <- lugar
          15   }
          16 }
          17
          18 type PedidoEstacionamiento struct {
          19   cliente      string
          20   lugarEstacionamiento chan int
          21 }
          22
          23 type Estacionamiento struct {
          24   cliente      string
          25   lugarEstacionamiento int
          26 }
          27
          28 type ConsultaMonto struct {
          29   cliente string
          30   monto chan int
          31 }
          32
          33 type Pago struct {
          34   cliente string
          35   monto int
          36   ok   chan bool // true: pago aceptado, false: monto insuficiente
          37 }
          38
          39 func trapito(estacionar <-chan PedidoEstacionamiento, disponibles <-chan int, barra chan<-
               Estacionamiento) {
          40   for pedido := range estacionar {
          41     lugar := <-disponibles
          42     barra <- Estacionamiento{pedido.cliente, lugar}
          43     pedido.lugarEstacionamiento <- lugar
          44     println("Trapito: Hincha", pedido.cliente, "estacionado en lugar", lugar)
          45   }
          46 }
          47
          48 func barra(trapitos <-chan Estacionamiento, consultas <-chan ConsultaMonto, pagos <-chan Pago,
               liberados chan<- int) {
          49   // Diccionario cliente -> {lugar, monto}
          50   montos := make(map[string]int)
          51   lugares := make(map[string]int)
          52   recaudacionTotal := 0
          53   // Ticker para sumar 1 al monto de cada cliente cada 5 minutos
          54   ticker := time.NewTicker(5 * time.Minute)
          55   defer ticker.Stop()
          56
          57   for {
          58     select {
          59     case trapito := <-trapitos:
          60        montos[trapito.cliente] += 1
          61        lugares[trapito.cliente] = trapito.lugarEstacionamiento
          62     case consulta := <-consultas:
          63        consulta.monto <- montos[consulta.cliente]
          64     case pago := <-pagos:
          65        if pago.monto < montos[pago.cliente] {
          66          pago.ok <- false
          67          continue
                                       4 de 10
```

## Página 5

[Ver página original](modelo-segundo-parcial-2026-2c-soluciones.pdf#page=5)

```text
        Programación Concurrente y Paralela – Simulacro 2do. parcial Segundo cuatrimestre de 2026


          68        }
          69        recaudacionTotal += pago.monto
          70        println("Barra: Hincha", pago.cliente, "pagó", pago.monto, "y se retiró del lugar",
               lugares[pago.cliente])
          71        liberados <- lugares[pago.cliente]
          72        delete(montos, pago.cliente)
          73        delete(lugares, pago.cliente)
          74        pago.ok <- true
          75     case <-ticker.C:
          76        for cliente := range montos {
          77          montos[cliente] += 1
          78        }
          79     }
          80   }
          81 }
          82
          83 // verPartido es la función imaginaria que simula al hincha viendo el partido
          84 func verPartido() {}
          85
          86 func hincha(nombre string, estacionar chan<- PedidoEstacionamiento, consultas chan<-
               ConsultaMonto, pagos chan<- Pago) {
          87   lugarEstacionamiento := make(chan int)
          88   estacionar <- PedidoEstacionamiento{nombre, lugarEstacionamiento}
          89   lugar := <-lugarEstacionamiento
          90   println("Hincha", nombre, "estacionado en lugar", lugar)
          91   verPartido()
          92   // Consultar monto y pagar hasta que la barra acepte
          93   montoChan := make(chan int)
          94   ok := make(chan bool)
          95   var monto int
          96   for {
          97     consultas <- ConsultaMonto{nombre, montoChan}
          98     monto = <-montoChan
          99     pagos <- Pago{nombre, monto, ok}
         100     if <-ok {
         101        break
         102     }
         103   }
         104   println("Hincha", nombre, "pagó", monto, "y se retiró del lugar", lugar)
         105 }
         106
         107 func main() {
         108   cantTrapitos := 3
         109   cantLugares := 5
         110   cantClientes := 10
         111   disponibles := make(chan int, cantLugares)
         112   estacionar := make(chan PedidoEstacionamiento)
         113   avisarBarra := make(chan Estacionamiento)
         114   liberados := make(chan int)
         115   consultas := make(chan ConsultaMonto)
         116   pagos := make(chan Pago)
         117   go lugares(cantLugares, disponibles, liberados)
         118   go barra(avisarBarra, consultas, pagos, liberados)
         119   for i := 0; i < cantTrapitos; i++ {
         120     go trapito(estacionar, disponibles, avisarBarra)
         121   }
         122   var wg sync.WaitGroup
         123   for i := 0; i < cantClientes; i++ {
         124     wg.Go(func() {
         125        hincha("Hincha"+strconv.Itoa(i), estacionar, consultas, pagos)
         126     })
         127   }
         128   wg.Wait()
         129 }
                                       5 de 10
```

## Página 6

[Ver página original](modelo-segundo-parcial-2026-2c-soluciones.pdf#page=6)

```text
        Segundo cuatrimestre de 2026 Programación Concurrente y Paralela – Simulacro 2do. parcial


        Ejercicio 3.Un servidor atiende a dos clientes. El cliente i manda sus pedidos por el canal pedidoi y recibe las
        respuestas por respi , todos de tipo chan bool y sin buffer. Se consideran dos versiones del servidor, A y B:
        1 for { // cliente 1             6 for { // cliente 2
        2  pedido1 <- true               7  pedido2 <- true
        3  <-resp1                       8  <-resp2
        4  usar()                        9  usar()
        5 }                             10 }
        11 // servidor, versión A       18 // servidor, versión B
        12 for {                        19 for {
        13 select {                     20  <-pedido1; resp1 <- true
        14 case <-pedido1: resp1 <- true 21 <-pedido2; resp2 <- true
        15 case <-pedido2: resp2 <- true 22 }
        16 }
        17 }
          a) Modele las dos versiones del sistema como composición de LTS. Indique en qué estados valen las propo-
            siciones pide , espera , usa y atiende , que se usan en los ítems siguientes.
                   i    i  i      i
          b) Escriba en LTL las siguientes propiedades:
             1. Todo cliente que pide termina usando una respuesta.
             2. Un cliente que está esperando su respuesta no vuelve a pedir sin haberla usado antes.
             3. Entre dos atenciones al cliente 1, el servidor atiende al cliente 2.
          c) Determine si cada versión satisface cada una de las siguientes fórmulas. Para las que no, dé una traza que
            la viole y decida si pasa a valer bajo fair choice.
            1. □♢usa 2. □(pide → ♢espera ) 3. □¬(usa ∧ usa ) 4. □(espera → ⃝(espera ∨ usa ))
                 2        1      1         1   2          1        1   1
          Solución.
           a) Los componentes. Cada cliente tiene tres estados: P (por pedir), E (espera la respuesta) y U (usa).

             Sus acciones son P − p − e − d − id − o →i E − r − es − p →i U − u − s − ar →i P.
             El servidor A tiene tres estados: 0 (en el select), 1 y 2 (recibió el pedido del cliente 1 o 2 y le debe la
                                  pedido resp  pedido resp
             respuesta). Sus transiciones son 0 −−−−−→1 1 −−−→1 0 y 0 −−−−−→2 2 −−−→2 0.
                                         pedido resp pedido resp
             El servidor B tiene cuatro estados en ciclo: 0 −−−−−→1 1 −−−→1 2 −−−−−→2 3 −−−→2 0.
             La composición. Los canales no tienen buffer, con lo cual pedido y resp sincronizan al servidor con
                                                     i    i
             el cliente i. usar es local. Cada estado es la terna (servidor, cliente 1, cliente 2), y se parte de (0, P, P).
                       i
             Las proposiciones. pide , espera y usa valen cuando el cliente i está en P, E y U. En la versión A,
                             i    i   i
             atiende vale cuando el servidor está en i. En la versión B, atiende vale en el estado 1 y atiende en
                  i                                   1                 2
             el estado 3.
             Versión A (8 estados)
              Estado Transiciones
              (0, P, P) pedido → (1, E, P), pedido → (2, P, E)
                         1            2
              (1, E, P) resp → (0, U, P)
                       1
              (2, P, E) resp → (0, P, U)
                       2
              (0, U, P) usar → (0, P, P), pedido → (2, U, E)
                        1           2
              (0, P, U) pedido → (1, E, U), usar → (0, P, P)
                         1          2
              (2, U, E) usar → (2, P, E), resp → (0, U, U)
                        1          2
              (1, E, U) resp → (0, U, U), usar → (1, E, P)
                       1           2
              (0, U, U) usar → (0, P, U), usar → (0, U, P)
                        1          2
             Versión B (12 estados)
              Estado Transiciones
              (0, P, P) pedido → (1, E, P)
                         1
              (1, E, P) resp → (2, U, P)
                       1
              (2, U, P) usar → (2, P, P), pedido → (3, U, E)
                        1           2
              (2, P, P) pedido → (3, P, E)
                         2
              (3, U, E) usar → (3, P, E), resp → (0, U, U)
                        1          2
              (3, P, E) resp → (0, P, U)
                       2
              (0, U, U) usar → (0, P, U), usar → (0, U, P)
                        1          2
              (0, P, U) pedido → (1, E, U), usar → (0, P, P)
                         1          2
              (0, U, P) usar 1 → (0, P, P) 6 de 10
              (1, E, U) resp → (2, U, U), usar → (1, E, P)
                       1           2
              (2, U, U) usar → (2, P, U), usar → (2, U, P)
                        1          2
              (2, P, U) usar → (2, P, P)
                        2
```

## Página 7

[Ver página original](modelo-segundo-parcial-2026-2c-soluciones.pdf#page=7)

```text
        Programación Concurrente y Paralela – Simulacro 2do. parcial Segundo cuatrimestre de 2026


             Ninguna de las dos versiones tiene estados sin salida.
           b)  1. □(espera → ♢usa ) ∧ □(espera → ♢usa ). También se acepta con pide en lugar de espera , si
                      1     1       2    2                  i           i
                 se interpreta “pide” como “quiere pedir”.
               2. □(espera → (¬pide W usa )) ∧ □(espera → (¬pide W usa )). Se usa W porque la propiedad
                      1      1   1        2      2   2
                 no exige que la respuesta llegue, solo que no se vuelva a pedir antes. Si escriben U, la fórmula
                 exige además que la use, que es más fuerte de lo pedido.
               3. La atención al cliente 1 puede durar varios estados (mientras tanto se mueve el cliente 2), así que
                 hay que separar tres fases: la atención actual, el tramo sin atender al 1 y la atención al 2.
                              □(cid:0)
                               atiende → (atiende W (¬atiende W atiende ))
                                                             (cid:1)
                                    1      1        1      2
                 También es correcta
                             □(cid:0)
                               (atiende ∧ ⃝¬atiende ) → ⃝(¬atiende W atiende )
                                                               (cid:1)
                                                               . El error típico es
                                    1       1          1      2
                 escribir □(atiende → (¬atiende W atiende )), que es falsa en cuanto atiende vale, porque en
                           1        1      2                    1
                 ese mismo estado ya no vale ¬atiende . En la versión B la propiedad vale, y en la A no.
                                       1
           c)  1. □♢usa .
                     2
                                                             pedido    resp
                 Versión A: no vale. El select puede elegir siempre al cliente 1: (0, P, P) −−−−−→1 (1, E, P) −−−→1
                 (0, U, P) − u − sa − r →1 (0, P, P) · · · y así para siempre. El cliente 2 se queda en P. Bajo fair choice vale.
                 En esa traza, pedido está habilitada infinitas veces (en (0, P, P) y en (0, U, P)), con lo cual se
                             2
                 tiene que tomar. A partir de ahí el servidor solo puede responderle al cliente 2 (el cliente 1, como
                 mucho, termina de usar y queda bloqueado en su pedido), y el cliente 2 pasa a U.
                 En Go el select elige al azar entre los casos listos, con lo cual en la práctica no elige siempre al
                 mismo. Pero el LTS lo modela como no determinismo, y para concluir que el cliente 2 es atendido
                 hace falta suponer fairness.
                 Versión B: vale, sin fairness. Cuando el servidor está en 2 (espera el pedido del cliente 2), el
                 cliente 1 a lo sumo termina de usar y se bloquea en pedido . Lo único que puede pasar es que el
                                                    1
                 cliente 2 termine de usar (si estaba en U) y mande su pedido, y después el servidor está obligado
                 a responderle. Como el servidor pasa por 2 en cada vuelta, el cliente 2 usa una respuesta infinitas
                 veces.
               2. □(pide → ♢espera ).
                     1      1
                 Versión A: no vale, por lo mismo, al revés. El select elige siempre al cliente 2 y el cliente 1 se
                 queda en P. Bajo fair choice vale, por el mismo argumento que la anterior.
                 Versión B: vale, sin fairness. Es el argumento simétrico: cuando el servidor está en 0, el único
                 pedido que puede recibir es el del cliente 1.
               3. □¬(usa ∧ usa ).
                     1   2
                                                pedido      resp      pedido
                 No vale en ninguna de las dos. En A, (0, P, P) −−−−−→1 (1, E, P) −−−→1 (0, U, P) −−−−−→2
                       resp
                 (2, U, E) −−−→2 (0, U, U). En B, la misma secuencia de acciones lleva a (0, U, U). El servidor
                 no espera a que un cliente termine de usar para atender al otro. Fair choice no cambia nada.
                 Es una propiedad de safety, y la violación es un prefijo finito. Cualquier forma de continuar esa
                 traza sigue violándola.
               4. □(espera → ⃝(espera ∨ usa )).
                      1       1   1
                 Vale en las dos. El siguiente paso es del sistema, no del cliente 1, así que puede ser una acción
                 del cliente 2 o del servidor con el cliente 2. En ese caso el cliente 1 sigue en E. Si el paso es del
                 cliente 1, la única acción que tiene desde E es resp , que lo lleva a U. Como no hay estados sin
                                               1
                 salida, siempre hay un siguiente paso. El error típico es leer ⃝ como “el siguiente paso del cliente
                 1” y concluir que la fórmula dice que la respuesta llega enseguida.
        Ejercicio 4.
          Consideramos dos algoritmos divide and conquer secuenciales para calcular la función maxSubArray que, dado
        un arreglo de enteros A de longitud n (puede asumir n potencia de 2), determina el valor de la máxima suma
        de un subarreglo contíguo en A. Es decir, si por ejemplo tenemos el arreglo
                                 [−2, 1, −3, 4, −1, 2, 1, −5, 4]
        la suma máxima es 6, dada por el subarreglo [4, −1, 2, 1]. Se pide:
          a) Adapte el pseudocódigo de estos algoritmos para que trabajen bajo un esquema fork-join.
          b) Para ambos, calcule el work, el span, y el paralelismo.
                                       7 de 10
```

## Página 8

[Ver página original](modelo-segundo-parcial-2026-2c-soluciones.pdf#page=8)

```text
        Segundo cuatrimestre de 2026 Programación Concurrente y Paralela – Simulacro 2do. parcial


          c) En función de los resultados obtenidos, concluya cuál de las dos implementaciones es más conveniente
            (desde el punto de vista teórico) para su uso en una computadora de alta capacidad de paralelismo
            (explique el speedup alcanzable, por ejemplo, para una entrada de tamaño 210 en una computadora con
            64 procesadores, basándose en la aproximación T ≈ T /P + T ).
                                         P   1   ∞
          Resolvemos este problema de las siguientes maneras:
          a) Variante (I):
          1 int maxSubArray(int[] A, int lo, int hi) {
          2   if (hi - lo == 1) {
          3      return A[lo];
          4   }
          5   int mid = (lo + hi) / 2;
          6   int low = maxSubArray(A, lo, mid);
          7   int high = maxSubArray(A, mid, hi);
          8   int cruza = mejorSumaLineal(A, mid-1, lo-1) // mejor sufijo de la parte baja
          9          + mejorSumaLineal(A, mid, hi-1); // mejor prefijo de la parte alta
         10   return max(low, high, cruza);
         11 }
            Donde la respuesta es maxSubArray(A, 0, len(A)-1). La función mejorSumaLineal(A, desde, hasta) reco-
            rre secuencialmente, en Θ(|desde − hasta|) pasos, las posiciones desde desde hacia hasta, y devuelve la
            máxima suma de un segmento que arranca en la posición desde y avanza hacia hasta (por lo que hacia la
            derecha da el mejor prefijo y hacia la izquierda da el mejor sufijo del subarreglo).
          b) Variante (II):
          1 Tupla mejoresCasos(int[] A, int lo, int hi) {
          2   // Tupla(total, optimo, prefijo, sufijo)
          3   if (hi - lo == 1) {
          4      return new Tupla(A[lo], A[lo], A[lo], A[lo]);
          5   }
          6   int mid = (lo + hi) / 2;
          7   Tupla b = mejoresCasos(A, lo, mid);
          8   Tupla d = mejoresCasos(A, mid, hi);
          9   int total = b.total + d.total;
         10   int optimo = max(b.optimo, d.optimo, b.sufijo + d.prefijo);
         11   int prefijo = max(b.prefijo, b.total + d.prefijo);
         12   int sufijo = max(d.sufijo, d.total + b.sufijo);
         13   return new Tupla(total, optimo, prefijo, sufijo);
         14 }
            Donde la respuesta es mejoresCasos(A, 0, len(A)-1).optimo.
          Solución.
           a) En ambos casos replicamos la dinámica del FibTask de la teórica: se lanza la recursión sobre una mitad
             con fork(), la otra mitad se ejecuta en la propia tarea con compute(), y el resultado de la lanzada
             se obtiene con join() (tratando de demorarlo lo más posible). La parte de combinación queda igual.
             Notar que la adaptación no altera el cómputo.
             Convertimos las funciones a tareas (RecursiveTask) para poder hacerles el fork y el join que necesita-
             mos (no es necesario usar la sintaxis exacta).
             Variante (I).
            1
            2 class MaxSubArrayTask extends RecursiveTask<Integer> {
            3   int [] A;
            4   int lo;
            5   int hi;
            6
            7   public MaxSubArrayTask(int[] A, int lo, int hi){
            8      //setea los valores
            9   }
           10
           11   protected Integer compute() {
                                       8 de 10
```

## Página 9

[Ver página original](modelo-segundo-parcial-2026-2c-soluciones.pdf#page=9)

```text
        Programación Concurrente y Paralela – Simulacro 2do. parcial Segundo cuatrimestre de 2026


           12      if (hi - lo == 1) {
           13        return A[lo];
           14      }
           15      int mid = (lo + hi) / 2;
           16      MaxSubArrayTask low = new MaxSubArrayTask(A, lo, mid);
           17      low.fork();
           18      int high = new MaxSubArrayTask(A, mid, hi).compute();
           19      int cruza = mejorSumaLineal(A, mid-1, lo-1) // mejor sufijo de la parte baja
           20            + mejorSumaLineal(A, mid, hi-1); // mejor prefijo de la parte alta
           21      return max(low.join(), high, cruza);
           22   }
           23
           24 }
             La solución se obtiene con (new MaxSubArrayTask(A, 0, len(A)-1)).compute()
             Variante (II).
            1 // Se asume Tupla(total, optimo, prefijo, sufijo)
            2 class MejoresCasosTask extends RecursiveTask<Tupla> {
            3   int [] A;
            4   int lo;
            5   int hi;
            6
            7   public MejoresCasosTask(int[] A, int lo, int hi) {
            8      //setea los valores
            9   }
           10
           11   protected Tupla compute() {
           12      if (hi - lo == 1) {
           13        return new Tupla(A[lo], A[lo], A[lo], A[lo]);
           14      }
           15      int mid = (lo + hi) / 2;
           16      MejoresCasosTask low = new MejoresCasosTask(A, lo, mid);
           17      low.fork();
           18      Tupla r = new MejoresCasosTask(A, mid, hi).compute();
           19      Tupla l = low.join();
           20      int total = l.total + r.total;
           21      int optimo = max(l.optimo, r.optimo, l.sufijo + r.prefijo);
           22      int prefijo = max(l.prefijo, l.total + r.prefijo);
           23      int sufijo = max(r.sufijo, l.sufijo + r.total);
           24      return new Tupla(total, optimo, prefijo, sufijo);
           25   }
           26
           27 }
           b) Work, span y paralelismo.
             Variante (I).
             Work: Cada llamada parte el rango en dos mitades y las combina con dos recorridos lineales que juntas
             cubren el segmento completo. El Work esencialmente da lo mismo que hacer la ejecución secuencial:
                              T (n) = 2 T (n/2) + Θ(n), T (1) = Θ(1).
                               1     1            1
             Caso 2 del teorema maestro (Tanto CLRS3 como CLRS4) (log 2 = 1 = f(n)):
                                                   2
                                      T (n) = Θ(n log n).
                                       1
             Span: Las dos mitades corren en paralelo (aportan un máximo, no una suma), pero los recorridos
             lineales del combine son secuenciales y van enteros al camino crítico, igual que el merge secuencial del
             mergesort:
                         T (n) = T (n/2) + Θ(n) = Θ(n + n/2 + n/4 + . . . ) = Θ(n).
                          ∞     ∞
             Esta derivación del span también sale con el caso 3 del teorema maestro; sólo hay que tener en cuenta
             que la condición de ese caso es f(n) ∈ Ω(nlog b a+ε), y que hay que hacer el chequeo de regularidad:
             tiene que existir c < 1 tal que af(n/b) ≤ cf(n). Para la recurrencia del span, a = 1, b = 2 (con lo cual
             nlog b a = n0 = 1) y f(n) = Θ(n), que es Ω(n) con ε = 1; para la regularidad, af(n/2) = n/2 ≤ cn, que
             ciertamente vale para c = 1.
                             2
                                       9 de 10
```

## Página 10

[Ver página original](modelo-segundo-parcial-2026-2c-soluciones.pdf#page=10)

```text
        Segundo cuatrimestre de 2026 Programación Concurrente y Paralela – Simulacro 2do. parcial


             Nota: Se podría pensar en crear una tarea para mejorSumaLineal, lo cual permitiría paralelizar el
             avance en ambas direcciones. Eso mejoraría el cómputo, pero sólo en una constante; para el análisis
             asintótico mantiene el work y el span. Sin embargo, es posible hacer esta búsqueda de prefijo/sufijo
             de manera paralela: el algoritmo que lo hace se llama escaneo de Blelloch (usándolo, el span bajaría a
             Θ(log2 n), pero el work seguiría siendo Θ(n log n)).
             Paralelismo:
                                  T (n) Θ(n log n)
                                   1   =       = Θ(log n).
                                  T (n)   Θ(n)
                                   ∞
             Variante (II).
             Work: Cada llamada parte en dos mitades y combina en Θ(1) (constantes comparaciones y sumas):
                              T (n) = 2 T (n/2) + Θ(1), T (1) = Θ(1).
                               1     1            1
             Caso 1 del teorema maestro:
                                        T (n) = Θ(n).
                                        1
             Span: Las dos mitades corren en paralelo y la combinación es Θ(1):
                                 T (n) = T (n/2) + Θ(1) = Θ(log n).
                                 ∞     ∞
             Es el caso 2 del teorema maestro (nuevamente, aplica para CLRS3 y CLRS4).
             Paralelismo:
                                      T (n)
                                       1  = Θ(n/ log n).
                                     T (n)
                                      ∞
           c) La variante (II) domina a la variante (I) en las tres métricas: menos pasos totales (Θ(n) contra
             Θ(n log n)), camino crítico más corto (Θ(log n) contra Θ(n)) y mucho más paralelismo (Θ(n/ log n)
             contra Θ(log n)).
             Para el caso n = 210 = 1024 (claramente log n = 10):
                                        2
                 Variante (I): work Θ(n log n) = 1024 · 10 pasos, span Θ(n) = 1024 pasos, paralelismo Θ(log n) =
                 10. Aunque hubiera infinitos procesadores, la cadena de recorridos lineales del camino crítico
                 requiere al menos 1024 pasos. El speedup máximo teórico es de unas 10 veces, y encima el trabajo
                 total es 10 veces el de la variante (II).
                 Variante (II): work Θ(n) = 1024 pasos, span Θ(log n) = 10 pasos, paralelismo Θ(n/ log n) ≈ 102:
                 hay trabajo paralelo suficiente para aprovechar razonablemente hasta unos cientos de procesado-
                 res.
             Usando la aproximación greedy T ≈ T /P + T , con P = 64 = 26 procesadores (tomando las
                                  P   1     ∞
             constantes en Θ igual a 1):
                    1024 · 10                              1024
             (I): T ≈    + 1024 = 160 + 1024 = 1184 contra (II): T ≈ + 10 = 16 + 10 = 26.
                 64   26                               64  26
             Es decir, con 64 procesadores la variante (II) tarda 45 veces menos pasos. En términos del speedup
             alcanzable (respecto de la ejecución secuencial de cada variante, es decir, T /T ): la variante (I) logra
                                                          1 64
             10240/1184 ≈ 8,6, menos incluso que su paralelismo teórico de 10, mientras que la variante (II) logra
             1024/26 ≈ 39, y puede seguir creciendo con P hasta el límite que marca su paralelismo (≈ 102). Por
             eso (II) es la indicada para una computadora de alta capacidad de paralelismo. Esencialmente en la
             variante (I) la combinación secuencial Θ(n) aplasta el paralelismo (un poco lo que pasa en el ejercicio
             de la práctica de mergesort con merge secuencial), mientras que en la variante (II) cada nivel del árbol
             recursivo se computa en paralelo y solo se paga Θ(log n) de profundidad.




                                      10 de 10
```
