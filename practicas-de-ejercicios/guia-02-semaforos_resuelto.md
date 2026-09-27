# Guía 2 resuelta — Semáforos

Fuente: [transcripción de la guía](guia-02-semaforos.transcripcion.md). Apoyo: [teórica de semáforos](../clases-teoricas/teorica-02-semaforos.transcripcion.md). Soluciones de estudio, no oficiales.

## Convenciones

`P(s)`/`V(s)` son wait/acquire y signal/release. Los semáforos indicados en cada ejercicio son compartidos; los temporales son locales. `print` es atómico. Los contadores, colas y decisiones compuestas se protegen por un mutex. Las operaciones físicas se hacen fuera del mutex salvo indicación contraria.

Se supone scheduler justo, acciones físicas finitas y ausencia de cancelaciones. Cuando se afirma ausencia de inanición se requieren semáforos fuertes/FIFO y colas explícitas donde se indique. No confundir una espera legítima por participantes que aún no llegaron con un deadlock del protocolo. La notación `V(s,k)` significa k señales, no una primitiva adicional indivisible.

## Ejercicio 1 — A antes que F antes que C

```text
semaphore aHecha=0, fHecha=0

T1:                         T2:
    print(A)                    print(E)
    V(aHecha)                   P(aHecha)
    print(B)                    print(F)
    P(fHecha)                   V(fHecha)
    print(C)                    print(G)
```

F consume una señal emitida después de A; C consume una emitida después de F. B y E/G conservan sólo las restricciones de orden propias de cada hilo. No hay ciclo de esperas.

## Ejercicio 2 — ACERO o ACREO

```text
semaphore aHecha=0, cHecha=0, eHecha=0

T1:                         T2:
    P(aHecha)                   print(A)
    print(C)                    V(aHecha)
    V(cHecha)                   P(cHecha)
    print(E)                    print(R)
    V(eHecha)                   P(eHecha)
                                print(O)
```

El orden parcial es `A<C`, `C<E`, `C<R`, `E<O`, `R<O`. E y R no están ordenadas entre sí; las dos y sólo las dos extensiones posibles son las pedidas.

## Ejercicio 3 — R I O OK OK OK

```text
semaphore rHecha=0, iHecha=0, permisoOK=0

T1:                         T2:                         T3:
    print(R)                    P(rHecha)                   P(iHecha)
    V(rHecha)                   print(I)                    print(O)
    P(permisoOK)                V(iHecha)                   V(permisoOK,3)
    print(OK)                   P(permisoOK)                P(permisoOK)
                                print(OK)                   print(OK)
```

Los tres OK deben consumir permisos publicados después de O. Cada hilo imprime exactamente uno; su identidad no afecta la salida.

## Ejercicio 4 — Desigualdades de cantidades

```text
semaphore creditoA=0, creditoE=0, creditoG=0

T1:                         T2:                         T3:
  forever:                    forever:                    forever:
    print(A)                    print(E)                    P(creditoE)
    V(creditoA)                 V(creditoE)                 print(H)
    print(B)                    P(creditoA)                 print(I)
    P(creditoG)                 print(F)
    print(C)                    print(G)
    print(D)                    V(creditoG)
```

Cada F exige un crédito distinto creado por un A anterior, cada H uno de E, cada C uno de G. Incluso entre consumir el permiso e imprimir se mantienen `#F≤#A`, `#H≤#E`, `#C≤#G`. Si T1 espera G, ya produjo A; T2 puede producir E, consumir ese A y producir G. T3 no impide esos avances.

## Ejercicio 5 — Dos productores de letras

### a) Diferencia absoluta a lo sumo 1

```text
semaphore puedeA=1, puedeB=1
T1: forever: P(puedeA); print(A); V(puedeB)
T2: forever: P(puedeB); print(B); V(puedeA)
```

Cada A, salvo el primero, necesita un B anterior, y recíprocamente. Así `#A≤#B+1` y `#B≤#A+1`. **No impone alternancia estricta:** `ABBA` es un prefijo válido. Una B puede usar su crédito inicial y otra el generado por A.

### b) ABAB…

Mismo código, pero `puedeA=1`, `puedeB=0`. Hay un único permiso que circula.

### c) ABBABB…

```text
semaphore puedeA=1, puedeB=0
T1: forever:
    P(puedeA)
    print(A)
    V(puedeB)

T2: forever:
    P(puedeB)
    print(B)
    print(B)
    V(puedeA)
```

El hilo de B agrupa sus impresiones de a dos. Si se quiere mantener una impresión por iteración, usar un contador **local** módulo 2 y devolver permiso a A sólo tras la segunda B; habilitar inicialmente dos permisos B por cada A.

## Ejercicio 6 — Suma cooperativa de impares, Java

`impar` contiene el **índice i**, pese a su nombre; el acumulador calcula `2*i−1`. Se usa un rendezvous por valor y un acuse para impedir sobrescribir el índice y para imprimir después de la última suma.

```java
import java.util.concurrent.Semaphore;

public final class SumaImpares {
    private final int n;
    private int impar;
    private long suma = 0;
    private final Semaphore listo = new Semaphore(0);
    private final Semaphore sumado = new Semaphore(0);

    public SumaImpares(int n) {
        if (n < 0 || n == Integer.MAX_VALUE)
            throw new IllegalArgumentException("N fuera de rango");
        this.n = n;
        this.impar = n; // Inicialización pedida por la guía.
    }

    private void generar() {
        for (int i = 1; i <= n; i++) {
            impar = i;
            listo.release();
            sumado.acquireUninterruptibly();
        }
        System.out.println(suma);
    }

    private void acumular() {
        for (int i = 1; i <= n; i++) {
            listo.acquireUninterruptibly();
            suma += 2L * impar - 1;
            sumado.release();
        }
    }

    public void ejecutar() throws InterruptedException {
        Thread g = new Thread(this::generar);
        Thread a = new Thread(this::acumular);
        g.start(); a.start();
        g.join(); a.join();
    }

    public static void main(String[] args) throws InterruptedException {
        new SumaImpares(100).ejecutar(); // 10000
    }
}
```

Los pares release/acquire publican los datos en Java; no hace falta volatile adicional. Tras i acuses, `suma=i²`. Para N=0 imprime 0 sin esperar. Para evitar overflow del contador del `for`, el contrato del ejemplo es `N<Integer.MAX_VALUE`; con ese rango N² cabe en long. Cada instancia se ejecuta una sola vez.

## Ejercicio 7 — Gimnasio

### a) Modelo

Agentes: un thread por cliente. Recursos: cuatro aparatos exclusivos y D discos intercambiables compartidos. Estado protegido: aparatos ocupados, discos disponibles y pedidos en espera. Cada paso de una rutina es `(aparato, cantidadDiscos)`; se valida `0≤cantidad≤D`. Una rutina puede repetir aparatos.

### b) Implementación Java sin deadlock ni livelock

Se reserva **aparato y discos conjuntamente** bajo un mutex; no se retiene uno mientras se espera el otro. Los pedidos forman una cola FIFO. El despachador concede pedidos desde la cabeza mientras sus recursos estén disponibles; puede admitir varios clientes que no compiten por recursos. El trabajo físico queda fuera del mutex.

```java
import java.util.*;
import java.util.concurrent.Semaphore;

public final class Gimnasio {
    public record Paso(int aparato, int discos) {}
    private static final class Pedido {
        final Paso paso;
        final Semaphore concedido = new Semaphore(0);
        Pedido(Paso paso) { this.paso = paso; }
    }

    private final Semaphore mutex = new Semaphore(1, true);
    private final boolean[] ocupado = new boolean[4];
    private final Deque<Pedido> cola = new ArrayDeque<>();
    private final int totalDiscos;
    private int disponibles;

    public Gimnasio(int discos) {
        if (discos < 0) throw new IllegalArgumentException();
        totalDiscos = disponibles = discos;
    }

    private void validar(Paso p) {
        if (p.aparato() < 0 || p.aparato() >= 4 ||
            p.discos() < 0 || p.discos() > totalDiscos)
            throw new IllegalArgumentException("Paso imposible");
    }

    // Sólo se invoca con mutex tomado.
    private void despachar() {
        while (!cola.isEmpty()) {
            Pedido p = cola.peekFirst();
            int a = p.paso.aparato(), d = p.paso.discos();
            if (ocupado[a] || disponibles < d) return;
            cola.removeFirst();
            ocupado[a] = true;
            disponibles -= d;
            p.concedido.release();
        }
    }

    private void tomar(Paso paso) {
        validar(paso);
        Pedido p = new Pedido(paso);
        mutex.acquireUninterruptibly();
        try { cola.addLast(p); despachar(); }
        finally { mutex.release(); }
        p.concedido.acquireUninterruptibly(); // Sin retener mutex.
    }

    private void devolver(Paso paso) {
        mutex.acquireUninterruptibly();
        try {
            ocupado[paso.aparato()] = false;
            disponibles += paso.discos();
            despachar();
        } finally { mutex.release(); }
    }

    public void hacerRutina(List<Paso> rutina) {
        // Validar todo antes de empezar la simulación.
        for (Paso p : rutina) validar(p);
        for (Paso p : rutina) {
            tomar(p);
            try {
                // Simula cargar discos, entrenar y descargar TODOS los discos.
                // Cada acción es finita. No se conserva nada para el paso siguiente.
                Thread.yield();
            } finally { devolver(p); }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Gimnasio g = new Gimnasio(12);
        List<List<Paso>> rutinas = List.of(
            List.of(new Paso(0, 6), new Paso(1, 4), new Paso(0, 8)),
            List.of(new Paso(1, 8), new Paso(2, 6)),
            List.of(new Paso(3, 4), new Paso(2, 12))
        );
        List<Thread> clientes = new ArrayList<>();
        for (List<Paso> r : rutinas) clientes.add(new Thread(() -> g.hacerRutina(r)));
        for (Thread t : clientes) t.start();
        for (Thread t : clientes) t.join();
    }
}
```

Invariantes: `disponibles + discosReservados = D`, disponibilidad nunca negativa y cada aparato se concede a lo sumo a un pedido activo. Las reservas incluyen clientes habilitados que aún no comenzaron físicamente.

No hay ciclo de espera: un cliente esperando su concesión no retiene recursos; uno que ya los tiene no pide otros antes de devolverlos. Tampoco hay reintentos activos que puedan originar livelock. Si la cabeza no puede entrar, algún usuario activo devolverá los recursos que necesita; cuando terminen los activos, cualquier pedido válido cabe.

### c) Inanición

Esta versión **ya la evita**: un pedido tiene una cantidad finita de predecesores y ninguno posterior puede adelantársele. Los tenedores actuales terminan, y la cabeza acaba siendo concedida. El mutex justo permite además ingresar a la cola. El costo es posible bloqueo de cabeza: pueden quedar recursos ociosos mientras el primer pedido espera. Una versión que salte siempre al primero para satisfacer pedidos pequeños tendría mejor utilización pero podría dejarlo esperando indefinidamente.

## Ejercicio 8 — Bolsa con capacidad igual al número de generadores

La cantidad de participantes no se conoce de antemano. Cada generador se registra al empezar y aporta un permiso de capacidad. Se considera que los participantes siguen jugando; el enunciado no especifica retiros. Si se permitieran, habría que retirar también capacidad sin violar la cota: no basta decrementar un contador de generadores.

```text
semaphore lugares=0, bolitas=0, mutexBolsa=1, tomarPar=1
int enBolsa=0

Generador():
    puntos=0
    V(lugares)                  // Registro de este generador: capacidad +1.
    forever:
        crearBolita()
        P(lugares)
        P(mutexBolsa)
        enBolsa++
        V(mutexBolsa)
        V(bolitas)
        puntos++

Consumidor():
    puntos=0
    forever:
        P(tomarPar)             // Un solo consumidor reserva un par a la vez.
        P(bolitas)
        P(bolitas)
        P(mutexBolsa)
        enBolsa -= 2            // Extracción del par como unidad.
        V(mutexBolsa)
        V(lugares,2)
        V(tomarPar)
        puntos++
        usarPar()
```

La reserva de permisos no retira físicamente bolitas: `enBolsa` se reduce de a dos. Por ello siempre es ≤ la cantidad de generadores registrados. Las bolitas reservadas siguen ocupando capacidad hasta extraer el par.

`tomarPar` evita que dos consumidores reserven una bolita cada uno y dejen la bolsa llena sin poder completar ninguno. Si el único reservante tiene una bolita y falta otra, con al menos dos generadores registrados existe capacidad para la segunda; los generadores no necesitan tomar `tomarPar`, así que pueden producirla. Si no hay consumidores o dejan de consumir, los generadores pueden esperar por bolsa llena: no hay protocolo que garantice progreso de producción ilimitada en ese escenario. La garantía pedida presupone al menos un consumidor activo además de los dos generadores.

## Ejercicio 9 — Transbordador con semáforos

Cada persona tiene un semáforo **privado** `llegue=0`. Así, un recién embarcado no puede consumir la notificación de llegada de un pasajero del viaje anterior.

Estado común:

```text
semaphore embarque[2] = {0,0}
semaphore plazas=N, sentados=0, bajaron=0, mutex=1
lista manifiesto = []
```

Persona que empieza en costa c:

```text
Persona(c):
    local llegue = Semaphore(0)
    P(embarque[c])
    P(plazas)
    subirYAcomodarse()          // Una acción física finita.
    P(mutex)
    manifiesto.agregar(llegue)
    V(mutex)
    V(sentados)
    P(llegue)
    bajarCompletamente()
    V(plazas)
    V(bajaron)                  // Sólo necesario en variante a.
```

### a) Primero bajan todos

```text
Transbordador():
    costa=0
    forever:
        V(embarque[costa],N)
        repetir N veces: P(sentados)
        P(mutex)
        pasajeros=manifiesto
        manifiesto=[]
        V(mutex)
        viajarYAmarrarEn(1-costa)
        costa=1-costa
        para s en pasajeros: V(s)
        repetir N veces: P(bajaron)
```

No se abren los permisos de la siguiente costa hasta recibir los N acuses de descenso. Los N acuses `sentados` impiden viajar con alguien todavía acomodándose.

### b) Subida y bajada concurrentes

Mismo código, **eliminar la espera final por `bajaron` y también sus señales en Persona**. Después de avisar la llegada, se abre inmediatamente el embarque de la nueva costa.

`plazas` se libera sólo después de bajar completamente y se adquiere antes de subir. Luego hay a lo sumo N personas a bordo, incluyendo reservas para subir. Para que se sienten N pasajeros nuevos, deben haberse liberado N plazas: eso obliga a que hayan bajado todos los anteriores antes del próximo viaje. No hace falta que las bajadas terminen antes de comenzar las subidas.

En ambos casos, faltar pasajeros para completar un viaje es espera especificada. Las colas de semáforos débiles no garantizan orden de llegada; la guía no lo exige aquí.

## Ejercicio 10 — Planta de refinamiento

Hay 8 threads máquina y 4 threads vehículo. Cada máquina tiene capacidad para un lote: recibe, procesa y entrega. Se separan los puertos de carga y descarga; un vehículo que espera cargar no bloquea a quien viene a descargar.

Por máquina m:

```text
semaphore puedeDescargar[m]=1, pedidoDescarga[m]=0
semaphore puedeCargar[m]=0, pedidoCarga[m]=0
Pedido descarga[m], carga[m]
// Pedido = (vehiculo, Semaphore fin=0), uno nuevo por operación.
```

```text
Maquina(m):
    forever:
        P(pedidoDescarga[m])
        p = descarga[m]
        lote = descargarFisicamente(p.vehiculo)
        V(p.fin)
        procesado = procesar(lote)                 // Fuera de cualquier mutex global.
        V(puedeCargar[m])
        P(pedidoCarga[m])
        p = carga[m]
        cargarFisicamente(p.vehiculo, procesado)
        V(p.fin)
        V(puedeDescargar[m])
```

```text
Vehiculo(id, ruta):
    para (lugar, operacion) en ruta:
        desplazarseHasta(lugar)
        si lugar es plataformaRecepcion:
            cargarMateriaPrima()                  // Precondición: vehículo vacío.
        sino si lugar es plataformaEntrega:
            descargarProductoFinal()              // Precondición: vehículo cargado.
        sino si operacion == DESCARGAR:
            m=lugar
            P(puedeDescargar[m])
            p=Pedido(esteVehiculo)
            descarga[m]=p
            V(pedidoDescarga[m])
            P(p.fin)                      // No se desplaza antes del fin físico.
        sino:                                     // CARGAR en una máquina.
            m=lugar
            P(puedeCargar[m])
            p=Pedido(esteVehiculo)
            carga[m]=p
            V(pedidoCarga[m])
            P(p.fin)
```

Los permisos de acceso conceden un único vehículo por operación de puerto, por eso las referencias no necesitan un mutex adicional. Una segunda descarga no se admite hasta haber entregado el lote anterior. Cada señal final va a un semáforo privado: otro vehículo de una ronda posterior no puede robar un acuse todavía no consumido. Cada vehículo recibe el acuse antes de continuar su ruta.

El procesamiento de una máquina, las operaciones de otras y los desplazamientos son concurrentes. Se suponen rutas materialmente válidas (cargar estando vacío, descargar estando cargado). **Las condiciones del enunciado no alcanzan para garantizar ausencia de bloqueo para cualquier ruta imaginable:** si los cuatro vehículos piden cargar en máquinas inicialmente vacías y nadie lleva materia prima, todos esperan. Eso es una dependencia de las rutas, no una exclusión accidental entre los puertos. La solución no promete eliminar ese caso imposible sin cambiar las rutas.

## Ejercicio 11 — Baño y limpieza: tres políticas distintas

Usamos un administrador con mutex y un semáforo privado por pedido. Eso permite expresar exactamente a qué personas alcanza cada prioridad, sin confundirlas con el orden de un semáforo de capacidad.

Estado y código comunes:

```text
semaphore mutex=1
int dentro=0
bool limpiando=false
cola usuarios=[]                 // Pedidos en orden de registro.
Pedido pedidoLimpieza=null       // Un solo thread de limpieza.
// Cada Pedido contiene Semaphore listo=0 y numero de llegada.
int siguiente=0, corte=0

entrarUsuario():
    p=Pedido()
    P(mutex)
    p.numero=siguiente; siguiente++
    usuarios.agregar(p)
    despachar()
    V(mutex)
    P(p.listo)                   // Se reserva su toilette antes de despertarlo.

salirUsuario():
    P(mutex)
    dentro--
    despachar()
    V(mutex)

pedirLimpieza():
    p=Pedido()
    P(mutex)
    pedidoLimpieza=p
    corte=siguiente              // Frontera entre personas previas y posteriores.
    despachar()
    V(mutex)
    P(p.listo)

terminarLimpieza():
    P(mutex)
    limpiando=false
    despachar()
    V(mutex)

Usuario:
    entrarUsuario(); usarToilette(); salirUsuario()

Limpieza:
    forever:
        esperarHastaProximaLimpieza()
        pedirLimpieza(); limpiar(); terminarLimpieza()
```

`despachar` siempre corre bajo mutex y nunca espera. Su código depende de la política; `concederUsuario()` quita la cabeza, incrementa dentro y señala su `listo`; `concederLimpieza()` pone `limpiando=true`, quita pedidoLimpieza y señala su `listo`. El incremento de dentro es la admisión/reserva de toilette, aun si el hilo tarda en comenzar físicamente.

### a) Limpieza espera también a quienes esperan toilette

```text
despachar():
    si limpiando: return
    mientras dentro<8 y usuarios no vacía:
        concederUsuario()
    si dentro==0 y usuarios vacía y pedidoLimpieza!=null:
        concederLimpieza()
```

Una corriente continua de usuarios puede postergar la limpieza indefinidamente: esta prioridad admite inanición del limpiador. Nunca se limpia con una reserva activa ni se admite un usuario durante la limpieza.

### b) Prioridad sobre personas que llegan después del limpiador

```text
despachar():
    si limpiando: return
    mientras dentro<8 y usuarios no vacía y
              (pedidoLimpieza==null o usuarios.primero.numero<corte):
        concederUsuario()
    si pedidoLimpieza!=null y dentro==0 y
          (usuarios vacía o usuarios.primero.numero>=corte):
        concederLimpieza()
```

Las personas ya registradas antes del pedido de limpieza conservan su turno, incluso si esperaban capacidad. Las que llegan después no entran hasta que termine. Como hay un prefijo finito previo a corte, la limpieza llega a ejecutarse, suponiendo que todos los usuarios admitidos terminan y que el mutex es justo.

### c) Limpieza antes incluso de quienes ya esperaban un toilette

```text
despachar():
    si limpiando: return
    si pedidoLimpieza!=null:
        si dentro==0: concederLimpieza()
        return
    mientras dentro<8 y usuarios no vacía:
        concederUsuario()
```

Se deja terminar sólo a los que ya fueron admitidos; nadie que aún espera en `usuarios` entra por delante del limpiador. El corte de llegada deja de ser necesario. Es diferente de b: en b los usuarios anteriores en cola sí debían atenderse.

En las tres variantes `0≤dentro≤8` y `limpiando ⇒ dentro=0`. Sólo el administrador entrega permisos, así que ni despertares ni carreras entre hilos alteran la política. Una secuencia ilimitada de pedidos prioritarios puede perjudicar al grupo no prioritario; no se afirma justicia entre grupos donde no fue exigida.

## Ejercicio 12 — Puente

### a) Concurrencia en una misma dirección

```text
semaphore puente=1
semaphore mutex[2]={1,1}
int grupo[2]={0,0}

entrarDireccion(d):
    P(mutex[d])
    grupo[d]++
    si grupo[d]==1: P(puente)
    V(mutex[d])

salirDireccion(d):
    P(mutex[d])
    grupo[d]--
    si grupo[d]==0: V(puente)
    V(mutex[d])

Auto(d):
    entrarDireccion(d)
    cruzar()
    salirDireccion(d)
```

Sólo el primer miembro de una dirección toma el puente; sólo el último lo libera. El primer miembro opuesto puede esperar reteniendo **su propio mutex**, que no necesitan los autos de la dirección activa para salir. Los miembros de una misma dirección cruzan concurrentemente.

### b) A lo sumo tres autos

Agregar `semaphore plazas=3` y cambiar únicamente el cuerpo de Auto:

```text
Auto(d):
    entrarDireccion(d)
    P(plazas)
    cruzar()
    V(plazas)
    salirDireccion(d)
```

Es importante adquirir plazas **después** de pertenecer al grupo autorizado. No se permite que autos de la dirección bloqueada consuman las plazas del puente. El contador grupo incluye los autos de la dirección activa esperando una plaza, para que no se ceda prematuramente el puente.

### c) Inanición

**No es libre de inanición**, aun con semáforos FIFO. Si llegan suficientes autos del sentido 0 para que `grupo[0]` nunca baje a cero, un auto del sentido 1 puede esperar para siempre en puente. La justicia del semáforo no ayuda mientras nadie lo libera.

Una corrección posible es cerrar nuevas admisiones de la dirección activa cuando aparezca una petición opuesta y alternar lotes finitos, o administrar una cola FIFO de solicitudes y admitir hasta tres consecutivas compatibles de su cabeza. Es una modificación de política; no es una propiedad de la solución anterior.
