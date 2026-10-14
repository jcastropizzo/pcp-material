# Guía 6 — comunicación por mensajes: soluciones

Fuente: [transcripción](guia-06-comunicacion-por-mensajes.transcripcion.md). Pseudocódigo de estudio, no soluciones oficiales. Se usan `process`, `send`, `receive`, `select` y el `EOF` distinguido del enunciado. `canal(T, k)` abrevia la creación de un canal de T con capacidad k; `canal(T, ∞)` representa el canal asincrónico ideal, realizable mediante el adaptador del ejercicio 5. En Go real un canal bufferizado siempre es finito y la recepción usa `valor, ok`, no EOF.

Se supone planificación que permite ejecutar a los participantes, cálculos finitos y clientes que reciben las respuestas comprometidas. No se presume fairness de `select`. Un servidor inactivo esperando nuevos pedidos no se considera deadlock según la guía. El cierre lo realiza el único productor o un coordinador que sabe que terminaron todos los productores.

## Ejercicio 1 — Next y Count

```text
Srv(ch):
    int cuenta = 0
    loop:
        Cmd m = receive(ch)
        if m == EOF: return
        if m == Next: cuenta = cuenta + 1
        if m == Count:
            print(cuenta)
            cuenta = 0

Client(n, ch):                    // n >= 0
    forever:
        repetir n veces: send(ch, Next)
        send(ch, Count)

main:
    ch = canal(Cmd, 0)
    process Srv(ch)
    process Client(3, ch)
    process Client(5, ch)
```

a) El servidor es único dueño del contador; no necesita un lock. b) Cada cliente preserva su secuencia local. c) El valor impreso no tiene por qué ser 3 o 5: cuenta todos los Next recibidos desde el Count anterior, incluidos los del otro cliente. No hay ciclo de espera: el servidor vuelve a recibir después de cada pedido; un cliente puede sufrir postergación bajo una elección adversaria de emisores.

## Ejercicio 2 — canal de respuesta

```text
Cmd = Next | Count(channel int respuesta)

Srv(ch):
    int cuenta = 0
    loop:
        Cmd m = receive(ch)
        if m == EOF: return
        match m:
            Next: cuenta++
            Count(r):
                int valor = cuenta
                cuenta = 0
                send(r, valor)

Client(n, ch):
    r = canal(int, 1)
    forever:
        repetir n veces: send(ch, Next)
        send(ch, Count(r))
        print(receive(r))
```

La respuesta vuelve al cliente que emitió ese Count. Hay a lo sumo una respuesta pendiente por cliente, de modo que capacidad uno evita bloquear al servidor mientras el cliente comienza a recibir. El contador sigue siendo global, no por cliente. Si se permitieran múltiples Count pendientes por cliente, habría que dimensionar/gestionar su cola de respuestas o usar un canal nuevo por solicitud.

## Ejercicio 3 — pares de mensajes

```text
Srv(ch1, ch2):
    forever:
        int x = receive(ch1)
        int y = receive(ch2)
        print(x == y)
Cliente(ch):
    forever: send(ch, randInt(100))
main:
    a = canal(int, 0); b = canal(int, 0)
    process Srv(a,b)
    process Cliente(a)
    process Cliente(b)
```

La recepción secuencial alcanza: cada iteración consume exactamente un elemento de cada canal. El cliente 2 puede esperar mientras se atiende al 1, pero no retiene un recurso que impida al cliente 1 enviar. No hace falta select porque se requieren ambos, no cualquiera de los dos. Si uno deja de producir, no existe un par para procesar; no puede garantizarse avance sin ambos participantes.

## Ejercicio 4 — adivinanza

### a) Un cliente

```text
Server(intentos, respuestas):
    int secreto = randInt(11)            // 0..10 inclusive
    loop:
        int x = receive(intentos)
        bool acierto = (x == secreto)
        send(respuestas, acierto)
        if acierto: return

Cliente(intentos, respuestas):
    for int x in 0..10:
        send(intentos, x)
        if receive(respuestas): return

main:
    q = canal(int,0); r = canal(bool,0)
    process Server(q,r)
    process Cliente(q,r)
```

El cliente prueba todos los valores, así termina en no más de 11 intentos. Elegir al azar repetidamente daría terminación probabilística, no una cota determinista. Los canales separados evitan confundir intentos con respuestas.

### b) Sesiones secuenciales

```text
Sesion = {channel int intentos, channel bool respuestas}
ServidorSecuencial(conexiones):
    forever:
        Sesion s = receive(conexiones)
        Server(s.intentos, s.respuestas) // llamada, no nuevo process

ClienteNuevo(conexiones):
    q = canal(int,0); r = canal(bool,0)
    send(conexiones, Sesion(q,r))
    Cliente(q,r)
```

La recepción de una sesión habilita al cliente actual. Si conexiones es sincrónico, los siguientes esperan fuera hasta que termine. No se necesita compartir el secreto ni los canales entre sesiones.

### c) Sesiones concurrentes

```text
ServidorConcurrente(conexiones):
    forever:
        Sesion s = receive(conexiones)
        process Server(s.intentos, s.respuestas)
```

Cada handler genera su propio secreto y posee sus canales. Sesiones de clientes diferentes no mezclan sus respuestas. No hay una cota de handlers concurrentes; limitarla sería una extensión distinta.

## Ejercicio 5 — canal asincrónico ilimitado

```text
AsyncChan(entrada, salida):
    queue q = []
    channel entradaActiva = entrada
    loop:
        if entradaActiva == nil and empty(q):
            close(salida)
            return
        channel salidaActiva = nil
        any primero = nil
        if not empty(q):
            salidaActiva = salida
            primero = head(q)
        select:
            case any x = receive(entradaActiva):
                if x == EOF: entradaActiva = nil
                else: append(q,x)
            case send(salidaActiva, primero):
                removeHead(q)
```

Solo se usan canales físicos sincrónicos. El proceso intermedio es dueño de una cola local sin cota fija. El envío a salida se deshabilita cuando está vacía; el EOF deshabilita entrada y se drena lo pendiente antes de cerrar salida. Se preserva FIFO. El productor puede detenerse hasta que el adaptador sea planificado, pero no depende de que el consumidor haya recibido cada mensaje. Una implementación real tiene memoria finita: ilimitado es una abstracción del ejercicio, no una promesa física.

## Ejercicio 6 — intentos pendientes al acertar

No basta permitir buffer y hacer que el servidor desaparezca al primer acierto: pueden quedar intentos sin respuesta. Usar una sesión con canales asincrónicos y un protocolo de cierre:

```text
Intento = {int numeroSecuencia, int valor}
Respuesta = {int numeroSecuencia, Estado estado}
Estado = Fallo | Acierto | SesionTerminada

Handler(intentos, respuestas):
    int secreto = randInt(11)
    bool terminada = false
    loop:
        Intento q = receive(intentos)
        if q == EOF:
            close(respuestas)
            return
        if terminada: estado = SesionTerminada
        else if q.valor == secreto:
            estado = Acierto
            terminada = true
        else: estado = Fallo
        send(respuestas, Respuesta(q.numeroSecuencia,estado))
```

El cliente mantiene un proceso emisor y uno receptor (o un event loop que hace ambas cosas). Al recibir Acierto deja de generar intentos, coordina la terminación del único emisor y este cierra intentos; sigue drenando respuestas hasta EOF. Para una versión simple y finita puede emitir 0..10, cerrar y luego leer todas las respuestas: ilustra pipelining sin cierre concurrente. Cada intento ya aceptado tiene una respuesta y un identificador. Para sesiones secuenciales, el servidor espera el cierre/drain de la sesión antes de aceptar otra; para concurrentes cada handler es independiente.

## Ejercicio 7 — variable mediante servidor

```text
Req = Get(channel Valor reply) | Set(Valor nuevo, channel unit ack)
Variable(pedidos, inicial):
    Valor actual = inicial
    loop:
        Req r = receive(pedidos)
        if r == EOF: return
        match r:
            Get(reply): send(reply,actual)
            Set(v,ack): actual = v; send(ack,unit)

leer(pedidos):
    reply = canal(Valor,1)
    send(pedidos,Get(reply))
    return receive(reply)
escribir(pedidos,v):
    ack = canal(unit,1)
    send(pedidos,Set(v,ack))
    receive(ack)
```

a) Funciona con pedidos sincrónicos. b) También con pedidos asincrónicos: `escribir` espera confirmación de aplicación, no considera que encolar el mensaje ya modificó la variable. Las respuestas privadas evitan mezclas. El punto de linearización es la lectura/actualización de `actual` en el servidor. Este serializa operaciones; no garantiza una operación compuesta atómica de leer-modificar-escribir. Para incrementos atómicos debe agregarse un mensaje Incrementar que el servidor ejecute completo.

## Ejercicio 8 — timer

### a) Lista inicial

```text
Timer(t, subs):                    // t > 0
    reloj = Ticker(t milisegundos)
    forever:
        receive(reloj)
        for ch in subs: send(ch,unit)
```

Se usan canales asincrónicos ideales para notificar: un destinatario lento no bloquea a los demás. En Go, un buffer finito por sí solo no conserva esta garantía; usar un adaptador del ejercicio 5 por destinatario. No hay garantía de tiempo real: t describe la fuente periódica y pueden existir demoras de planificación.

### b) Altas dinámicas

```text
Timer(t, altas):
    set subs = {}
    reloj = Ticker(t milisegundos)
    forever:
        select:
            case channel nuevo = receive(altas): subs.add(nuevo)
            case receive(reloj):
                for ch in subs: send(ch,unit)
```

Una suscripción comienza cuando el timer procesa el alta. Si se exige que el cliente conozca ese instante, agregar ack privado. El conjunto evita notificaciones duplicadas por altas repetidas. No se pide baja ni cierre; agregar esos eventos requeriría definir quién puede cerrar cada canal. `select` no garantiza una latencia máxima de admisión.

## Ejercicio 9 — codificación y destino

### a) Un destino

```text
T(entrada,R):
    loop:
        string m = receive(entrada)
        if m == EOF: return
        send(R,codificar(m))
```

### b) Cambio de destinatario

```text
Cmd = Destino(channel string r) | Texto(string contenido)
T(entrada):
    channel destino = nil
    loop:
        Cmd m = receive(entrada)
        if m == EOF: return
        match m:
            Destino(r): destino = r
            Texto(s): send(destino,codificar(s))
```

El cliente empieza con Destino, como exige la consigna. Todos los comandos viajan por el mismo canal FIFO; separar avisos y textos en dos canales con select perdería el orden entre ellos. La variable destino es privada de T.

### c) Una clave por texto

```text
T(entrada,K):
    channel destino = nil
    loop:
        Cmd m = receive(entrada)
        if m == EOF: return
        match m:
            Destino(r): destino = r
            Texto(s):
                clave = canal(string,1)
                send(K,clave)
                string k = receive(clave)
                send(destino,codificar(s,k))
```

La clave se solicita en cuanto hay un texto que la necesita, no se reserva una clave mientras se espera indefinidamente un texto futuro. La respuesta bufferizada de capacidad uno permite que K la deposite sin esperar la recepción de T. El algoritmo es secuencial y conserva el orden de transmisión. Se puede agregar una cola de trabajos y superponer obtención de la próxima clave con el envío actual, conservando en cada trabajo **el destino capturado al recibirlo** y su número de orden; no hace falta esa complejidad para evitar el bloqueo innecesario de K. La disponibilidad real de R y K es una precondición del progreso del servicio.

## Ejercicio 10 — proxy con límite de concurrencia

### a) Una solicitud en S

Una solución que recibe un pedido, lo envía y luego se bloquea entregando la respuesta a C puede trabarse si C está enviando más pedidos antes de recibir. Usar la máquina de eventos detallada abajo con un servidor y límite 1. P mantiene una cola de entradas y otra estructura de respuestas; nunca deja de poder aceptar respuestas de S por estar esperando a C.

### b) Hasta N solicitudes en S

Usar la misma máquina con límite N ≥ 1. El contador incluye cada pedido enviado hasta que se recibió su resultado; no se libera un cupo al mero envío. Puede haber N cálculos activos y pedidos extra en cola local.

### c) Dos copias y resultados ordenados

Usar dos contadores, uno por copia, cada uno con límite N (o 1 si cada copia solo admite una operación). Se numeran los pedidos por orden de recepción y se retienen resultados fuera de orden. Devolverlos en orden global satisface también el orden de cada cliente; para evitar bloqueo entre clientes se puede mantener un orden por cliente si el protocolo incluye esa identidad. Aquí la consigna tiene un cliente C y `Req` solo trae entero y canal de respuesta.

```text
Req = {int input, channel int reply}
Job = {int secuencia, Req original}
Done = {int servidor, int secuencia, int resultado, channel int reply}

Proxy(pedidos, servidores[], limites[]):
    queue pendientes = []
    map listos = {}                    // secuencia -> Done
    int proximoPedido = 0
    int proximaRespuesta = 0
    int activos[len(servidores)] = {0,...,0}
    completos = canal(Done,0)

    forever:
        // Construir guardas. Un envío no habilitado usa nil channel.
        for j in indices(servidores):
            enviarA[j] = nil
            if not empty(pendientes) and activos[j] < limites[j]:
                enviarA[j] = servidores[j]
        responderA = nil
        valor = 0
        if listos.contains(proximaRespuesta):
            responderA = listos[proximaRespuesta].reply
            valor = listos[proximaRespuesta].resultado

        // Se crea un canal nuevo para el potencial despacho de esta iteración.
        // Si no se elige un despacho, se descarta sin lanzar ningún thread.
        interno = canal(int,1)

        select:
            case Req r = receive(pedidos):
                append(pendientes, Job(proximoPedido,r))
                proximoPedido++

            case send(enviarA[j], Req(head(pendientes).original.input,interno)):
                // Una alternativa por cada j; evaluar head solo si la cola existe.
                Job trabajo = popHead(pendientes)
                activos[j]++
                process con copias de (j,trabajo,interno):
                    int resultado = receive(interno)
                    send(completos,Done(j,trabajo.secuencia,resultado,
                                         trabajo.original.reply))

            case Done d = receive(completos):
                activos[d.servidor]--
                listos[d.secuencia] = d

            case send(responderA,valor):
                listos.delete(proximaRespuesta)
                proximaRespuesta++
```

El bloque `case ... j` representa un conjunto finito de alternativas, una por servidor; con dos copias se escriben dos cases. En una traducción a Go **no se puede evaluar `head` de una cola vacía aunque el canal sea nil**, porque las expresiones del select se evalúan antes: preparar previamente un `Req` dummy y reemplazarlo por el real solo si hay pendientes. El pseudocódigo especifica esa evaluación guardada. Se asume el servicio permanece abierto; si se desea terminar al cerrar pedidos, deshabilitar esa entrada y drenar todos los trabajos antes de retornar.

**Invariantes:** `0 ≤ activos[j] ≤ limite[j]`; cada secuencia está exactamente en uno de los estados pendiente/activa/lista/entregada; `proximaRespuesta` es la menor no entregada. La transición de despacho aumenta el contador una sola vez; la recepción de Done lo disminuye una sola vez. Cada respuesta interna tiene capacidad uno y único productor. Si un cliente tarda en recibir, P puede seguir aceptando pedidos y completando cálculos, evitando un ciclo de espera por entrega. Con memoria finita haría falta imponer backpressure y un protocolo de cliente que reciba concurrentemente; no se puede absorber una cantidad ilimitada de pedidos con memoria acotada.

No se promete wait-freedom ni una latencia máxima: hay espera por servicios y por el cliente. «Maximizar concurrencia» significa permitir todos los cupos disponibles, no garantizar que un scheduler mantenga físicamente ocupados todos los servidores en todo instante.
