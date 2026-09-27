# Guía 3 resuelta — Monitores

Fuente: [transcripción de la guía](guia-03-monitores.transcripcion.md). Apoyo: [teórica de monitores](../clases-teoricas/teorica-03-monitores.transcripcion.md). Soluciones de estudio, no oficiales.

## Convenciones

Salvo el análisis explícito del ejercicio 1, usamos **signal y continúa / Mesa**. Todos los métodos del monitor son mutuamente excluyentes. `wait` libera el monitor y lo readquiere antes de regresar; `signalAll` despierta a todos los que esperan en esa condición, pero no les transfiere inmediatamente el monitor. Toda espera se escribe `while (!predicado) wait()`.

Las condiciones **no acumulan permisos**. El estado persistente guarda el motivo por el que un hilo puede avanzar. Las llamadas auxiliares internas se ejecutan bajo el mismo monitor, sin volver a adquirir un lock no reentrante. Las acciones largas —cortar pelo, dar charla, navegar— se ejecutan afuera. Las garantías de progreso suponen admisión justa al monitor, scheduler justo, participantes necesarios presentes y acciones finitas. Las colas FIFO garantizan orden desde el registro en el monitor, no desde una llegada física imposible de observar antes de registrarse.

## Ejercicio 1 — Señales sin memoria y disciplina del monitor

### a) Signal y espera urgente: incorrecto

Si el hilo de `antesydespues` entra primero:

```text
hilo A: ejecuta antes
hilo A: signal(permiso), sin ningún hilo esperando => la señal se pierde
hilo A: ejecuta despues y sale
hilo B: entra a importante, hace wait(permiso) y queda bloqueado
```

La disciplina urgente sólo entrega el monitor a un **hilo ya esperando**. No crea un permiso futuro. Si B esperaba desde antes, sí se obtendría el orden buscado, pero el programa debe funcionar para todas las intercalaciones.

### b) Signal y continúa: tampoco es correcto

Incluso cuando B ya espera, A ejecuta `antes`, señala y continúa ejecutando `despues`; recién cuando A libera el monitor puede B ejecutar `importante`. El orden es `antes→despues→importante`. Si B llega tarde aparece, además, la señal perdida del inciso a.

Una reparación independiente de estos dos problemas necesita estado y un acuse:

```text
monitor M:
    bool antesHecho=false, importanteHecho=false
    condition cambio

    antesydespues():
        ejecutarAntes()
        antesHecho=true
        cambio.signalAll()
        while !importanteHecho: cambio.wait()
        ejecutarDespues()

    importante():
        while !antesHecho: cambio.wait()
        ejecutarImportante()
        importanteHecho=true
        cambio.signalAll()
```

Es para una pareja de llamadas, como el ejercicio; reutilizarlo exige identificar las rondas.

## Ejercicio 2 — Secuenciador ternario

```text
monitor SecuenciadorTernario:
    int turno=0
    condition cambio

    primero(): ejecutar(0)
    segundo(): ejecutar(1)
    tercero(): ejecutar(2)

    ejecutar(t):
        while turno!=t: cambio.wait()
        realizarAccion(t)
        turno=(turno+1)%3
        cambio.signalAll()
```

`realizarAccion` es la acción que se secuencia, y ocurre dentro del método. Si los métodos sólo otorgaran permisos para una acción posterior fuera del monitor, no ordenarían por sí solos esas acciones externas.

El invariante indica qué única operación puede pasar. Se usa signalAll porque con una condición única un signal podría despertar a alguien del tipo equivocado y dejar dormido al necesario. Si no llega una llamada del siguiente tipo, la espera es la especificada. No se promete FIFO entre llamadas del mismo tipo.

## Ejercicio 3 — Barrera

### a) Una sola vez

```text
monitor Barrera(N>0):
    int llegaron=0
    condition abierta

    esperar():
        if llegaron==N: return
        llegaron++
        if llegaron==N: abierta.signalAll()
        while llegaron<N: abierta.wait()
```

Nadie retorna antes de la llegada N. Después de esa llegada el predicado queda verdadero permanentemente, incluidas invocaciones posteriores.

### b) Reutilizable

```text
monitor BarreraCiclica(N>0):
    int llegaron=0
    long generacion=0
    condition cambio

    esperar():
        local g=generacion
        llegaron++
        if llegaron==N:
            llegaron=0
            generacion++
            cambio.signalAll()
        else:
            while generacion==g: cambio.wait()
```

Cada uno de los N participantes llama una vez por ronda. Un hilo despertado de la ronda g sólo espera que cambie su generación, no que `llegaron==N`: ese contador puede haberse reiniciado y tener llegadas de la ronda siguiente. Se supone generación sin overflow. La barrera no maneja abandonos de participantes.

## Ejercicio 4 — Atrapador

Un permiso global podría ser robado por una llegada nueva bajo Mesa. Por eso cada espera tiene su propio registro `r` con `liberado=false`, y una cola FIFO identifica **a quiénes** se libera.

### a) Liberar no espera a completar el grupo

```text
monitor Atrapador:
    cola atrapados=[]
    condition cambio

    esperar():
        r=Registro(liberado=false)
        atrapados.pushBack(r)
        while !r.liberado: cambio.wait()

    liberar(N):
        exigir N>=0
        if atrapados.size<N: return
        repetir N veces:
            r=atrapados.popFront()
            r.liberado=true
        cambio.signalAll()
```

Si hay menos de N, no cambia nada; N=0 no libera a nadie. «Nunca se bloquea» significa que no espera en una condición para reunir atrapados; puede esperar el acceso normal al monitor.

Una llegada posterior no puede usar el permiso de alguien ya liberado porque tiene otro registro. Puede físicamente retornar antes que un hilo antiguo ya habilitado si el scheduler retrasa a éste, pero no le roba su liberación ni lo deja esperando por otro `liberar`.

### b) Liberar espera a reunir N

También serializamos los pedidos de liberación mediante una cola para que uno grande no sea adelantado indefinidamente por otros pequeños:

```text
monitor AtrapadorBloqueante:
    cola atrapados=[], liberadores=[]
    condition cambio

    esperar():
        r=Registro(liberado=false)
        atrapados.pushBack(r)
        cambio.signalAll()               // Puede completar un pedido de liberación.
        while !r.liberado: cambio.wait()

    liberar(N):
        exigir N>=0
        if N==0: return
        pedido=Pedido(N)
        liberadores.pushBack(pedido)
        while liberadores.front!=pedido or atrapados.size<N:
            cambio.wait()
        repetir N veces:
            r=atrapados.popFront()
            r.liberado=true
        liberadores.popFront()
        cambio.signalAll()
```

Se habilitan los N juntos dentro de una única sección del monitor. No significa que N CPUs ejecuten exactamente a la vez, ni que liberar deba esperar a que todos hayan regresado de wait. Un pedido imposible por falta futura de participantes espera legítimamente; FIFO puede dejar pedidos pequeños detrás de él.

## Ejercicio 5 — Peluquería

Cada cliente genera un trabajo con una condición privada. El monitor asocia al thread peluquero su trabajo actual; no cambia la interfaz del enunciado.

### a) Varias condiciones, Mesa

```text
monitor Pelu:
    cola clientes=[]
    mapa actualPorPeluquero={}
    condition hayClientes
    // Trabajo contiene bool terminado=false y condition fin.

    cortarseElPelo():
        t=Trabajo()
        clientes.pushBack(t)
        hayClientes.signal()
        while !t.terminado: t.fin.wait()

    empezarCorte():
        id=threadActual()
        while clientes.empty: hayClientes.wait()
        t=clientes.popFront()
        actualPorPeluquero[id]=t
        // Retorna; este peluquero corta fuera del monitor.

    terminarCorte():
        id=threadActual()
        t=actualPorPeluquero.remove(id)
        t.terminado=true
        t.fin.signal()
```

```text
Peluquero: forever:
    pelu.empezarCorte()
    cortarPelo()                          // Trabajo asignado a este peluquero.
    pelu.terminarCorte()

Cliente:
    pelu.cortarseElPelo()
    irse()
```

Cada trabajo sale una vez de la cola y pertenece a un peluquero. El cliente sólo regresa tras el fin de **su** corte, no tras el de cualquier cliente. Dos peluqueros pueden cortar a la vez porque la acción larga está fuera del monitor. El mapa presupone que un peluquero llama empezar/terminar alternadamente como indica la guía.

### b) Una única condición

Sustituir `hayClientes` y todos los `t.fin` por `cambio`, conservando **exactamente los mismos predicados**. En cortarseElPelo, después de encolar, ejecutar `cambio.signalAll()`; en terminarCorte, después de marcar terminado, también. Ambas clases de espera usan `cambio.wait()` dentro de sus while.

No alcanza con reemplazar signal por signal sobre una condición común: se podría despertar a un cliente cuyo corte sigue pendiente y dejar a los peluqueros dormidos. signalAll permite que cada uno verifique su propia condición.

## Ejercicio 6 — Sala de charlas

### a) Una charla repetida, capacidad 50

Una llamada `asistir` representa entrar, esperar el final y retirarse. El identificador de sesión evita mezclar una charla terminada con la siguiente.

```text
monitor Sala:
    int dentro=0
    bool abierta=true
    long sesion=0, ultimaTerminada=-1
    condition cambio

    asistir():
        while !abierta or dentro==50: cambio.wait()
        local miSesion=sesion
        dentro++
        while ultimaTerminada<miSesion: cambio.wait()
        dentro--                         // Salida de esta persona.
        cambio.signalAll()

    intentarComenzar():
        if dentro==0: return false
        abierta=false
        return true

    terminar():
        ultimaTerminada=sesion
        cambio.signalAll()
        while dentro>0: cambio.wait()
        sesion++
        abierta=true
        cambio.signalAll()
```

```text
Orador:
    forever:
        if sala.intentarComenzar():
            darCharla()                 // Fuera del monitor.
            sala.terminar()
        descansar(5 minutos)

Asistente:
    sala.asistir()
```

Al llenarse, los demás no entran aunque la charla aún no haya comenzado. Al comenzar se cierra también para entradas por debajo de 50. Nadie sale antes de terminar. Se espera la salida de la sesión anterior antes de abrir la siguiente; esto evita que los contadores incluyan asistentes de dos sesiones. Se modela entrar/salir como cambios del estado protegido; si se modelan físicamente como acciones largas, se deben añadir reservas y acuses de salida.

### b) Tres charlas y umbral 40

Hay una sala y tres oradores. Como el enunciado no exige que cada asistente elija tema, se toma que **acepta la próxima charla disponible**. Cada orador registra su tema; la sala concede turnos FIFO y quien la obtiene espera hasta reunir al menos 40 personas. Si se quisieran preferencias por tema habría que añadir ese parámetro y sus colas; no se presupone aquí.

```text
monitor SalaTres:
    int dentro=0
    bool abierta=true
    bool reservada=false
    long sesion=0, ultimaTerminada=-1
    cola oradores=[]
    Tema temaActual
    condition cambio

    asistir():
        while !abierta or dentro==50: cambio.wait()
        local s=sesion
        dentro++
        cambio.signalAll()                 // El oyente 40 habilita al orador.
        while ultimaTerminada<s: cambio.wait()
        dentro--
        cambio.signalAll()

    comenzar(tema):
        pedido=Pedido(tema)
        oradores.pushBack(pedido)
        while reservada or oradores.front!=pedido: cambio.wait()
        reservada=true
        temaActual=tema
        abierta=true
        cambio.signalAll()
        while dentro<40: cambio.wait()
        abierta=false
        // Retorna para dar esta charla fuera del monitor.

    terminar():
        ultimaTerminada=sesion
        cambio.signalAll()
        while dentro>0: cambio.wait()
        sesion++
        oradores.popFront()
        reservada=false
        abierta=true
        cambio.signalAll()
```

```text
Orador(tema): forever:
    sala.comenzar(tema)
    darCharla(tema)
    sala.terminar()
    descansar(5 minutos)
```

La sesión empieza con entre 40 y 50 asistentes; puede haber más de 40 si entran antes de que el orador retome el monitor. Nunca hay dos oradores dueños. Si no llegan 40 personas, la charla no debe empezar: ese bloqueo es una exigencia del problema. Entre sesiones la sala queda abierta: las personas pueden entrar antes de que llegue el siguiente orador, respetando la capacidad.

## Ejercicio 7 — Sala de apuestas

### a) Thread jugador usando sólo las dos operaciones

```text
Jugador(juego):
    gane=false
    premio=0
    while !gane and !juego.concluido():
        palabra=elegirPalabra()
        monto=elegirMontoPositivo()
        if juego.apostar(palabra,monto):
            gane=true
            premio=10*monto
    print(gane, premio)
```

El monto mostrado aquí es el **premio obtenido**: 10 veces la apuesta ganadora, o cero. La interfaz booleana no informa si un false corresponde a apuesta perdida o a juego ya cerrado; por eso no se inventa un saldo neto de apuestas debitadas. Si se quisiera ese saldo, habría que especificar una interfaz/política de débito adicional.

### b) Monitor

```text
monitor Juego(secreto):
    bool fin=false
    Id ultimo=null
    condition cambio

    concluido(): return fin

    apostar(palabra, apuesta):
        exigir apuesta>0
        yo=threadActual()
        while !fin and ultimo==yo: cambio.wait()
        if fin: return false
        ultimo=yo
        if palabra==secreto:
            fin=true
            cambio.signalAll()
            return true
        cambio.signalAll()
        return false
```

Sólo una apuesta aceptada actualiza ultimo. El jugador que pierde no puede volver a apostar hasta que cambie ese id. La victoria despierta a todos, incluidos los que estaban prohibidos por ser el último jugador.

Existe una carrera válida entre `concluido()` y `apostar()`: otro puede acertar entre ambas llamadas. Por eso apostar vuelve a consultar fin, retorna false y no registra una apuesta nueva. Todos los jugadores terminan **cuando alguien acierta**, bajo justicia. No se garantiza que alguien acierte ni progreso de un único jugador que falló y carece de otro participante: exigir alternancia lo impide.

## Ejercicio 8 — Pizzas grandes o dos chicas

```text
monitor Barra:
    int grandes=0, chicas=0
    condition hayComida

    ponerGrande():
        grandes++
        hayComida.signalAll()

    ponerChica():
        chicas++
        hayComida.signalAll()

    tomar():
        while grandes==0 and chicas<2: hayComida.wait()
        if grandes>0:
            grandes--
            return UNA_GRANDE
        chicas-=2
        return DOS_CHICAS
```

```text
Pizzero: forever:
    tipo=elegirTipo()
    cocinar(tipo)
    if tipo==GRANDE: barra.ponerGrande()
    else: barra.ponerChica()

Cliente:
    pizzas=barra.tomar()
    pagar(pizzas)
```

La preferencia por grande se decide con el estado observado al tomar, dentro del monitor. Nadie toma una chica y retiene esa mitad mientras espera otra. No hay FIFO de clientes, como pide el enunciado; un cliente puede sufrir inanición bajo competencia ilimitada. Se necesita producción suficiente para satisfacer demandas.

## Ejercicio 9 — Bote con autorización

### a) Monitor básico

Se interpreta cada llamada `autorizar` como un permiso para **un** viaje. Se pueden guardar permisos adelantados; si la política externa sólo admite uno pendiente, reemplazar el contador por un booleano idempotente. En ambos casos el permiso se consume al partir.

```text
monitor Bote(N>0):
    int costa=0, aBordo=0, autorizaciones=0
    bool viajando=false, descargando=false
    long viaje=0
    condition cambio

    cruzarDesde(c):
        while viajando or descargando or costa!=c or aBordo==N:
            cambio.wait()
        local miViaje=viaje
        aBordo++
        cambio.signalAll()
        while viaje==miViaje: cambio.wait()
        aBordo--                          // Ya en destino, desciende.
        cambio.signalAll()

    autorizar():
        autorizaciones++
        cambio.signalAll()

    esperarSalida():
        while viajando or descargando or aBordo<N or autorizaciones==0:
            cambio.wait()
        autorizaciones--
        viajando=true
        return 1-costa

    llegar():
        costa=1-costa
        viajando=false
        descargando=true
        viaje++
        cambio.signalAll()
        while aBordo>0: cambio.wait()
        descargando=false
        cambio.signalAll()
```

```text
ThreadBote: forever:
    destino=bote.esperarSalida()
    navegarYAmarrar(destino)
    bote.llegar()

Persona(c): bote.cruzarDesde(c)
Autoridad: al emitir un permiso, bote.autorizar()
```

La costa es la última costa amarrada; viajando impide embarcar durante el movimiento. Las personas de un viaje se identifican por generación. Durante descargando nadie nuevo entra, así que aBordo puede bajar a cero y permitir la próxima carga. Se supone un único ThreadBote. Las acciones de entrada/salida se modelan en el monitor, igual que en el ejercicio 6.

### b) Orden de llegada

La versión anterior **no lo asegura** con Mesa: un recién llegado puede adelantarse a alguien despertado. Agregar dos colas, una por costa, y modificar cruzarDesde:

```text
cruzarDesde(c):
    pedido=Pedido()
    cola[c].pushBack(pedido)
    while viajando or descargando or costa!=c or aBordo==N or
          cola[c].front!=pedido:
        cambio.wait()
    cola[c].popFront()
    local miViaje=viaje
    aBordo++
    cambio.signalAll()                // Habilita al siguiente de la costa, si cabe.
    while viaje==miViaje: cambio.wait()
    aBordo--
    cambio.signalAll()
```

El resto no cambia. El orden se garantiza por costa y desde la incorporación a la cola. Alguien esperando en la costa opuesta no bloquea a quienes pueden abordar aquí.

## Ejercicio 10 — Administrador de recursos en Java

### a) Tomar y liberar uno

```java
import java.util.*;

public final class Recursos<T> {
    private final Deque<T> libres;

    public Recursos(Collection<T> iniciales) {
        libres = new ArrayDeque<>(iniciales);
    }

    public synchronized T tomar() throws InterruptedException {
        while (libres.isEmpty()) wait();
        return libres.removeFirst();
    }

    public synchronized void liberar(T recurso) {
        libres.addLast(recurso);
        notifyAll();
    }
}
```

Precondiciones del contrato: recursos no nulos, cada recurso se devuelve una vez y sólo después de haber sido tomado. Las operaciones de uso del recurso se realizan afuera del monitor. El orden FIFO de **recursos libres** no implica FIFO de hilos solicitantes.

### b) Pedidos de tamaño arbitrario como una unidad

La siguiente implementación cubre b con `fifo=false`. Toma exactamente k recursos juntos, nunca toma algunos para esperar el resto. Liberar un lote también es una sola acción del monitor.

```java
import java.util.*;

public final class RecursosLote<T> {
    private final Deque<T> libres;
    private final Deque<Object> pedidos = new ArrayDeque<>();
    private final int total;
    private final boolean fifo;

    public RecursosLote(Collection<T> iniciales, boolean fifo) {
        libres = new ArrayDeque<>(iniciales);
        total = libres.size();
        this.fifo = fifo;
    }

    public synchronized List<T> tomar(int k) throws InterruptedException {
        if (k < 0 || k > total) throw new IllegalArgumentException();
        if (k == 0) return List.of();
        Object pedido = new Object();
        pedidos.addLast(pedido);
        try {
            while (libres.size() < k || (fifo && pedidos.peekFirst() != pedido))
                wait();
            List<T> resultado = new ArrayList<>(k);
            for (int i = 0; i < k; i++) resultado.add(libres.removeFirst());
            return resultado;
        } finally {
            // También se quita si se interrumpió la espera.
            pedidos.remove(pedido);
            notifyAll();
        }
    }

    public synchronized void liberar(Collection<T> lote) {
        // Contrato: lote estable, sin nulls, recursos poseídos por quien devuelve.
        // Copiar y validar antes de modificar evita una devolución parcial por null.
        List<T> copia = new ArrayList<>(lote);
        for (T r : copia) Objects.requireNonNull(r);
        libres.addAll(copia);
        notifyAll();
    }
}
```

Con fifo=false un pedido de 5 puede esperar indefinidamente si pedidos de 1 consumen cada devolución antes de que se acumulen 5. notifyAll es necesario: una devolución puede satisfacer a alguno de varios tamaños y una selección arbitraria única puede despertar al que aún no cabe.

### c) No perjudicar pedidos grandes

Instanciar la misma clase con **`fifo=true`**. Sólo la cabeza de pedidos puede tomar recursos; los siguientes esperan aunque sus pedidos pequeños entren. Así se acumulan devoluciones hasta completar el pedido grande.

Cada pedido válido tiene un prefijo finito por delante. Si quienes tienen recursos los devuelven en tiempo finito y la admisión al monitor progresa, se atiende todo ese prefijo y luego el pedido. Se requiere `k≤total`; no se puede garantizar entrada a un pedido mayor que el universo.

La prueba también presupone que un hilo **no retiene recursos del mismo pool mientras hace otra toma bloqueante**: dos clientes que ya poseen parte del pool y ambos piden el resto pueden crear un deadlock externo al administrador. La toma en lote permite solicitar desde el principio el conjunto completo que necesitan. FIFO puede reducir utilización, pero evita que pedidos nuevos agoten permanentemente los recursos que necesita uno antiguo.
