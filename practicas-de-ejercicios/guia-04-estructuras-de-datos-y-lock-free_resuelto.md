# Guía 4 resuelta — Estructuras concurrentes y lock-free

Fuente: [transcripción de la guía](guia-04-estructuras-de-datos-y-lock-free.transcripcion.md). Apoyo: [listas y conjuntos](../clases-teoricas/teorica-04-conjuntos-concurrentes-con-y-sin-locks.transcripcion.md) y [pilas, colas y ABA](../clases-teoricas/teorica-05-pilas-colas-y-problema-aba.transcripcion.md). Soluciones de estudio, no oficiales.

## Convenciones

Un lock protege un invariante, no sólo una instrucción. Un método es linealizable si parece ocurrir en un instante entre su invocación y su respuesta. **Lock-free** garantiza progreso global frente a contención; **wait-free**, terminación de cada operación en una cantidad acotada de pasos propios. Las garantías basadas en locks requieren justicia del lock y del scheduler donde se indique; no son garantías lock-free.

Los pseudocódigos atómicos usan memoria secuencialmente consistente. En Java se emplean locks, synchronized y referencias/campos atómicos o volatile para publicar correctamente. Se supone ausencia de overflow de contadores usados como secuencias. El código abstracto de cómputo del enunciado se supone terminante y sin excepciones, salvo donde se especifica un contrato más fuerte.

## Ejercicio 1 — Colisiones de hash en las listas de conjuntos

**No se puede decidir pertenencia con `curr.key == hash(x)`**: dos objetos distintos pueden compartir hash. Hay que comparar igualdad real, manteniendo la propiedad `x.equals(y) ⇒ hash(x)==hash(y)` y claves estables mientras estén almacenadas.

Una adaptación uniforme es tener **un nodo por valor hash y un bucket de objetos distintos por equals en ese nodo**. La lista exterior sigue ordenada por hash. Para evitar la carrera «modificar bucket mientras otro elimina su nodo», esta solución **conserva el nodo de hash aun cuando su bucket quede vacío**. Eso consume memoria por cada hash usado alguna vez, pero simplifica y hace correctos todos los casos.

Operaciones lógicas:

```text
contains(x): localizar nodo hash(x); si no existe, false;
             buscar por equals dentro de su bucket.
add(x):      crear nodo/bucket si aún no existe ese hash;
             si un igual está en el bucket, false;
             agregar x al bucket, true.
remove(x):   si nodo o igual no existen, false;
             quitar el igual del bucket, true; NO quitar el nodo exterior.
```

| Implementación de clase | Cambio concreto |
|---|---|
| Lock global / coarse-grained | Proteger con ese lock tanto recorrido como consulta/modificación del bucket. |
| Locks finos / hand-over-hand | Recorrer como antes; el lock de curr protege su bucket. Para crear un hash nuevo mantener la ventana pred/curr bloqueada en orden de lista. |
| Optimista | Buscar sin locks; tomar pred/curr y revalidar alcance y adyacencia; recién después consultar/modificar el bucket. No confiar en el bucket leído antes de validar. |
| Lazy | Mantener la disciplina de inserción y validación de ventanas. Como ya no se borran nodos exteriores, remove actualiza sólo el bucket. Para contains sin locks, el bucket debe ser una instantánea inmutable publicada atómicamente, no una colección mutable común. |
| Lock-free | Mantener CAS para insertar nodos de hash y representar cada bucket con `AtomicReference<BucketInmutable>`. Actualizar el bucket por CAS; al fallar, releer y reintentar. No reemplazar esto por un mutex, que perdería la propiedad lock-free. |

En las variantes con contains sin lock, **todos** los cambios de bucket deben publicar una nueva instantánea; no se muta la que un lector está recorriendo. En las bloqueantes puede usarse también esa representación o proteger todos los accesos con el lock correspondiente.

Para el bucket lock-free:

```text
addEnBucket(nodo,x):
    forever:
        viejo = nodo.bucket.get()
        if viejo.contienePorEquals(x): return false
        nuevo = copiaInmutable(viejo union {x})
        if CAS(nodo.bucket, viejo, nuevo): return true

removeEnBucket(nodo,x):
    forever:
        viejo = nodo.bucket.get()
        if !viejo.contienePorEquals(x): return false
        nuevo = copiaInmutable(viejo sin x)
        if CAS(nodo.bucket, viejo, nuevo): return true
```

Cada CAS exitoso es el punto de linealización de una modificación de un bucket existente. La creación de un nodo con su primer elemento se linealiza al enlazarlo. Una consulta utiliza una instantánea. Un CAS fallido evidencia un cambio exitoso de otro hilo, lo que sustenta progreso global, suponiendo copia/búsqueda finitas y asignación de memoria con las garantías requeridas.

Si se quisiera recuperar nodos de hash vacíos, habría que coordinar atómicamente cierre del bucket, validación y desvinculación: borrar el nodo sin ese protocolo puede perder un add concurrente. Otra opción es un orden total secundario **estable y compatible con equals**; no sirve asignar un nuevo id arbitrario a cada consulta del mismo objeto lógico.

## Ejercicio 2 — Locks separados en Figura

Los pares `(x,y)` y `(alto,ancho)` son invariantes independientes. Basta un lock por par:

```text
class Figura:
    Lock posicion, tamano
    int x=0, y=0, alto=0, ancho=0

    ajustarPosicion():
        posicion.lock()
        try:
            x=algunX()
            y=algunY()
        finally: posicion.unlock()

    ajustarTamano():
        tamano.lock()
        try:
            alto=algunAlto()
            ancho=algunAncho()
        finally: tamano.unlock()

    ajustarAmbos():
        posicion.lock()
        try:
            tamano.lock()
            try:
                actualizarPosicionYTamano()
            finally: tamano.unlock()
        finally: posicion.unlock()
```

Se elimina synchronized de los métodos separados; mantenerlo volvería a serializarlos por el monitor del objeto. Los lectores también deben tomar el lock del par que consultan si necesitan una instantánea coherente.

Para operaciones sobre ambos pares, **todas** toman primero posicion y luego tamano. Ningún camino puede tomar tamano y después posicion, ni llamar desde ese contexto a una función que lo haga. El orden global elimina espera circular. Es una solución bloqueante; el orden solo no asegura ausencia de inanición si los locks son injustos.

## Ejercicio 3 — Assign y swap con granularidad por posición

### a) Implementación Java

```java
import java.util.concurrent.locks.ReentrantLock;

public final class RecursosPorPosicion {
    private final Object[] recursos;
    private final ReentrantLock[] locks;

    public RecursosPorPosicion(int n) {
        if (n < 0) throw new IllegalArgumentException();
        recursos = new Object[n];
        locks = new ReentrantLock[n];
        for (int i = 0; i < n; i++) locks[i] = new ReentrantLock(true);
    }

    private boolean valida(int p) { return 0 <= p && p < recursos.length; }

    public void assign(int pos, Object o) {
        if (!valida(pos)) return;
        locks[pos].lock();
        try { recursos[pos] = o; }
        finally { locks[pos].unlock(); }
    }

    public void swap(int i, int j) {
        if (!valida(i) || !valida(j) || i == j) return;
        int a = Math.min(i, j), b = Math.max(i, j);
        locks[a].lock();
        try {
            locks[b].lock();
            try {
                Object aux = recursos[i];
                recursos[i] = recursos[j];
                recursos[j] = aux;
            } finally { locks[b].unlock(); }
        } finally { locks[a].unlock(); }
    }
}
```

Las operaciones disjuntas no comparten locks. Un swap bloquea ambas posiciones durante todo el intercambio, por lo que ninguna operación que respete este protocolo observa la mitad del swap. Una lectura de varias posiciones deberá tomar todos sus locks en orden ascendente.

Para índices no negativos válidos se conserva el comportamiento del enunciado. Se completó la validación de negativos, que en el original podía producir una excepción por acceso al array. El array y su capacidad no cambian después de construirlo.

### b) Inanición

Con los **locks justos** elegidos, scheduler justo y cuerpos finitos, sí hay entrada eventual. Toda cadena de espera de locks tiene índices estrictamente crecientes; no puede ciclar y, en un array finito, termina. La cola justa de cada lock impide que solicitudes nuevas adelanten indefinidamente una pendiente. Sin la opción justa, el orden de adquisición garantiza ausencia de deadlock pero no excluye inanición.

## Ejercicio 4 — Lista optimista

### a) Reintentos infinitos de remove

Lista inicial `head→10→30→tail`, y T quiere `remove(30)`:

1. T recorre sin locks y obtiene pred=10, curr=30.
2. U inserta 20, quedando `10→20→30`.
3. T toma los locks de 10 y 30; la validación `pred.next==curr` falla.
4. T suelta los locks y reintenta. Antes de su próximo recorrido, U elimina 20.
5. Repetir la misma secuencia indefinidamente.

30 permanece en la lista todo el tiempo. T obtiene incluso todos los locks que pide y ejecuta pasos infinitos, pero nunca valida una ventana vigente. **Locks fair no evitan esta inanición causada por interferencia optimista.**

### b) Invertir el orden de locks sólo en add

Misma lista. `add(20)` y `remove(30)` encuentran ambos pred=10 y curr=30.

```text
add(20), modificado: toma lock(30).
remove(30), normal: toma lock(10).
add(20): intenta lock(10), espera a remove.
remove(30): intenta lock(30), espera a add.
```

Se produce deadlock. La validación posterior nunca se alcanza. Se debe preservar un orden común, típicamente pred antes que curr en la lista ordenada.

## Ejercicio 5 — Actualización optimista de una tabla

### a) Capturar, computar afuera y validar

No basta comparar el valor con equals: otro hilo podría cambiarlo y volver a un valor igual (ABA). Se usa una **identidad de versión**: cada escritura crea una nueva Entrada y nunca reutiliza una anterior.

```java
import java.util.*;
import java.util.concurrent.locks.ReentrantLock;
import java.util.function.UnaryOperator;

public final class TablaOptimista {
    private record Entrada(Object valor) {}
    private final Map<Integer, Entrada> tabla = new HashMap<>();
    private final ReentrantLock lock = new ReentrantLock(true);
    private final UnaryOperator<Object> computo;

    public TablaOptimista(UnaryOperator<Object> computo) {
        this.computo = Objects.requireNonNull(computo);
    }

    public void poner(int i, Object valor) {
        lock.lock();
        try { tabla.put(i, new Entrada(valor)); }
        finally { lock.unlock(); }
    }

    public void actualizarEntrada(int i) {
        for (;;) {
            Entrada vieja;
            lock.lock();
            try {
                vieja = tabla.get(i);
                if (vieja == null) throw new NoSuchElementException("Clave inexistente");
            } finally { lock.unlock(); }

            Object nuevo = computo.apply(vieja.valor()); // SIN lock de la tabla.

            lock.lock();
            try {
                if (tabla.get(i) != vieja) continue;   // Comparación de identidad.
                tabla.put(i, new Entrada(nuevo));
                return;
            } finally { lock.unlock(); }
        }
    }
}
```

Contrato: los valores capturados son inmutables, o se copia profundamente bajo lock la porción que se computará. El cómputo es puro/repetible: puede ejecutarse varias veces si hay interferencia. Todos los escritores deben pasar por el mismo protocolo de publicar una Entrada nueva; una mutación interna silenciosa no sería detectada.

El cambio se linealiza en el put validado. La computación cara no bloquea accesos a otras claves ni las lecturas breves. Se exige que la clave exista, condición que el ejercicio no precisa; acá se responde explícitamente con excepción si no existe. No hace falta hacer remove antes de put.

### b) Inanición

No está garantizada. Otro hilo puede cambiar la misma entrada entre cada captura y validación, forzando infinitos reintentos aun con lock justo. Actualizaciones de **otras** claves no invalidan esta captura, porque la versión es por entrada.

## Ejercicio 6 — Cola con dos pilas

### a) Contraejemplo

Partir de dos enqueues completados `enq(a); enq(b)`: in tiene b arriba de a y out está vacía.

1. D1 comienza deq y transfiere sólo b de in a out; queda suspendido.
2. D2 comienza deq, ve out no vacía y devuelve b.
3. a, que fue encolado antes, sigue en in.

No hay ninguna linealización FIFO posible: la primera extracción de la historia debía devolver a. Sincronizar individualmente pop/push no hace atómica la transferencia ni protege su estado intermedio. También hay una carrera entre isEmpty y pop de distintos consumidores.

### b) Solución con un lock por pila y transferencia protegida

```java
import java.util.*;
import java.util.concurrent.locks.ReentrantLock;

public final class ColaDosPilas<T> {
    private final Deque<T> in = new ArrayDeque<>(), out = new ArrayDeque<>();
    private final ReentrantLock inLock = new ReentrantLock();
    private final ReentrantLock outLock = new ReentrantLock();

    public void enq(T x) {
        Objects.requireNonNull(x);
        inLock.lock();
        try { in.push(x); }
        finally { inLock.unlock(); }
    }

    public T deq() {
        outLock.lock();
        try {
            if (out.isEmpty()) {
                inLock.lock();
                try {
                    while (!in.isEmpty()) out.push(in.pop());
                    if (out.isEmpty()) throw new NoSuchElementException();
                } finally { inLock.unlock(); }
            }
            return out.pop();
        } finally { outLock.unlock(); }
    }
}
```

Se toma outLock antes de inLock cuando hacen falta ambos; enq nunca toma outLock. No hay ciclo. Si out no está vacía, deq sólo usa outLock y corre concurrentemente con enq. La transferencia invierte el prefijo pendiente completo sin intercalaciones que expongan una inversión parcial. Los elementos de out son anteriores a todos los de in. Una cola vacía produce excepción, como corresponde a esta versión no bloqueante respecto de datos.

## Ejercicio 7 — Cola acotada con dos contadores

La clase de la teórica tiene enqLock/deqLock, condiciones de no llena/no vacía, lista con centinela y un AtomicInteger size compartido por ambas operaciones. Reemplazamos el read-modify-write contendido de size por dos contadores **monótonos**:

- `E`: inserciones contabilizadas; sólo se modifica bajo enqLock.
- `D`: extracciones contabilizadas; sólo se modifica bajo deqLock y se publica como volatile.
- `dVista`: última copia de D leída por un productor; protegida por enqLock.

Para decidir si hay lugar, `E-dVista` es una **cota superior conservadora** de la ocupación cuando el productor tiene enqLock. Como D sólo crece, una copia vieja puede hacer creer que está llena, pero no habilita una inserción que exceda capacidad. Sólo al parecer llena se refresca D. El consumidor puede comprobar directamente `head.next`, como en la teórica.

Además hacen falta notificaciones correctas: no se elimina size y se deja sin reemplazo su función de despertar esperas de frontera.

```text
class ColaAcotada(C>0):
    Lock enqLock, deqLock
    Condition noLlena = enqLock.newCondition()
    Condition noVacia = deqLock.newCondition()
    Nodo centinela = new Nodo()
    Nodo head=centinela, tail=centinela
    // Cada Nodo tiene valor inmutable y next VOLATILE, inicialmente null.
    long E=0, dVista=0               // Sólo productores, bajo enqLock.
    volatile long D=0               // Escritor: deqLock. Lector: productores.
    volatile int esperanP=0         // Modificado sólo bajo enqLock.
    volatile int esperanC=0         // Modificado sólo bajo deqLock.

    enq(x):
        n=new Nodo(x)
        enqLock.lock()
        try:
            while E-dVista >= C:
                esperanP++
                try:
                    dVista=D        // Registrarse ANTES de refrescar el predicado.
                    if E-dVista >= C:
                        noLlena.await()
                finally: esperanP--
            tail.next=n
            tail=n
            E++
        finally: enqLock.unlock()

        if esperanC>0:
            deqLock.lock()
            try: noVacia.signalAll()
            finally: deqLock.unlock()

    deq():
        deqLock.lock()
        try:
            while head.next==null:
                esperanC++
                try:
                    if head.next==null:  // Revalidar después de registrarse.
                        noVacia.await()
                finally: esperanC--
            n=head.next
            resultado=n.valor
            head=n
            D=D+1                    // Un solo escritor a la vez: no requiere RMW.
        finally: deqLock.unlock()

        if esperanP>0:
            enqLock.lock()
            try: noLlena.signalAll()
            finally: enqLock.unlock()
        return resultado
```

Las esperas liberan y readquieren su lock correspondiente. No se permiten cancelaciones entre la publicación de un nodo/extracción y la notificación posterior. Si se implementa await interrumpible, se propaga la interrupción sin alterar la estructura; los finally mantienen los contadores de esperantes.

### Por qué respeta la capacidad

Mientras un productor decide, ningún otro productor puede agregar nodos. Los consumidores sólo reducen ocupación. En ese punto E cuenta todos los enlaces publicados por productores anteriores; D puede ir atrasado respecto de una extracción física, pero no adelantado. Así `ocupación ≤ E-D ≤ E-dVista`. Si la última expresión es menor que C, cabe uno más. Si no, se refresca D para evitar esperar por información vieja.

### Por qué no pierde despertares

Un consumidor se registra antes de la última lectura de `head.next`; un productor espera de forma análoga respecto de D. Si el cambio habilitante ocurre antes de ese registro, la relectura lo observa. Si ocurre después, el otro hilo ve un contador de esperantes positivo y toma el lock de la condición antes de señalar: no puede colar su señal entre la comprobación y el await, que libera el lock atómicamente con la espera. Se usan **contadores**, no un booleano que un despertado podría bajar mientras otros siguen dormidos.

Las notificaciones cruzadas se hacen **después de soltar el lock propio**, para no introducir el ciclo enqLock→deqLock→enqLock. signalAll evita dejar dormidos a otros consumidores/productores cuando hay capacidad o elementos para varios.

### Linealización y costo

Enq se linealiza al escribir `tail.next`; deq, al avanzar head. Como en la implementación de clase, un consumidor puede extraer el nodo antes de que el productor incremente E: `E-D` puede ser momentáneamente negativo. No es la ocupación exacta en todo instante. La prueba usa la cota sólo cuando el productor vuelve a estar en su sección de decisión, sin una inserción previa a medio contabilizar.

En el camino normal, E sólo lo toca el lado productor, D sólo tiene un escritor serializado por el lado consumidor, y no existe un RMW común para ambos. Leer D se reserva a la frontera de lleno; tomar el lock ajeno se reserva a la existencia de esperantes. Siguen siendo necesarios la publicación de enlaces y los indicadores de espera: dos contadores no eliminan toda comunicación. Para rendimiento real conviene separar físicamente los contadores de distintos lados para evitar false sharing. Esta cola es bloqueante, no lock-free.

## Ejercicio 8 — Pila acotada

La guía no exige lock-free ni una política especial para llena/vacía. Elegimos push bloqueante cuando está llena y pop bloqueante cuando está vacía.

```java
import java.util.*;

public final class PilaAcotada<T> {
    private final int capacidad;
    private final Deque<T> pila = new ArrayDeque<>();

    public PilaAcotada(int capacidad) {
        if (capacidad <= 0) throw new IllegalArgumentException();
        this.capacidad = capacidad;
    }

    public synchronized void push(T x) throws InterruptedException {
        Objects.requireNonNull(x);
        while (pila.size() == capacidad) wait();
        pila.push(x);
        notifyAll();
    }

    public synchronized T pop() throws InterruptedException {
        while (pila.isEmpty()) wait();
        T x = pila.pop();
        notifyAll();
        return x;
    }
}
```

El invariante `0≤size≤capacidad` se conserva en cada modificación protegida. Push y pop se linealizan en la operación correspondiente de la deque, que funciona como LIFO. Con una condición implícita común, notifyAll es necesario para no depender de despertar al tipo correcto de espera. No se garantiza orden FIFO de threads ni ausencia de inanición bajo un monitor injusto. Si se quisiera una API que falle en vez de esperar, se sustituyen las esperas por excepciones; sería otra especificación.

## Ejercicio 9 — ABA en una pila lock-free sin GC

### a) Contraejemplo

Pila inicial `head=A`, `A.next=B`, `B.next=C`. Los nombres denotan **direcciones de memoria**.

1. T1 comienza pop, lee head=A y next=B; queda suspendido antes de `CAS(head,A,B)`.
2. T2 hace pop de A y de B. Queda head=C, y libera ambos nodos.
3. Un nuevo push reutiliza la dirección A para un nodo con `next=C`; head vuelve a ser A.
4. T1 ejecuta su CAS: compara sólo la dirección A, tiene éxito y coloca head=B.

B ya estaba fuera de la pila y puede estar liberado/reutilizado. El CAS no detectó la historia `A→…→A`, y reinstala un enlace viejo, perdiendo el nodo actualmente publicado en A. Además del ABA, sin recuperación segura un hilo podría desreferenciar memoria ya liberada antes incluso de llegar al CAS.

### b) Solución con hazard pointers y retiro diferido

Una solución completa es impedir que un nodo observado por un pop sea liberado/reutilizado hasta que ese pop abandone la observación. Cada hilo tiene un hazard pointer atómico. Push crea un nodo nuevo; **no reintroduce deliberadamente nodos retirados que aún no son reclamables**.

```text
protegerHead(hilo):
    forever:
        p=head.load()
        hazard[hilo].store(p)
        // Publicación y relectura con orden de memoria adecuado (aquí SC).
        if head.load()==p: return p

pop(hilo):
    forever:
        p=protegerHead(hilo)
        if p==null:
            hazard[hilo].store(null)
            return VACIA
        siguiente=p.next
        valor=p.valor
        if CAS(head,p,siguiente):
            hazard[hilo].store(null)
            retirar(p)           // NO free inmediato.
            return valor
        hazard[hilo].store(null)

push(x):
    p=crearNodoNuevo(x)
    forever:
        viejo=head.load()
        p.next=viejo
        if CAS(head,viejo,p): return

retirar(p):
    agregar p a la lista local de retirados
    periódicamente:
        observar todos los hazard pointers
        liberar sólo retirados cuya dirección no esté protegida
```

`protegerHead` no desreferencia p antes de publicar protección y revalidar. Si alguien retiró p antes de la protección, la revalidación impide usar una observación desactualizada; si volvió a aparecer la misma dirección antes de revalidar, aún no leímos sus campos viejos, y se protege su nueva aparición. Una vez revalidado, p no puede ser reciclado mientras siga protegido.

Si head deja de ser p después de haberlo protegido, no puede volver a p por reciclado antes de nuestro CAS. Por tanto no ocurre el ABA del inciso a. La protección también permite leer next/valor sin use-after-free. Los campos de un nodo publicado son inmutables hasta su retiro y reclamación.

El esquema requiere una implementación correcta de publicación/escaneo de hazards, un registro de participantes y reclamación que no bloquee el progreso global. Un hilo detenido puede retener memoria protegida, pero no debe impedir otros pushes/pops. La asignación de memoria y el escaneo deben tener las garantías asumidas para afirmar lock-freedom de la implementación completa.

Una referencia etiquetada `(puntero,versión)` con CAS doble también detecta cambios A→B→A, si no se repite la versión. **La etiqueta sola no hace segura la desreferenciación de memoria liberada**: se debe combinar con un mecanismo de reclamación segura. GC elimina la reutilización prematura de direcciones alcanzables, pero tampoco justifica reinsertar manualmente el mismo nodo mutable sin analizar ABA.

## Ejercicio 10 — Contador lock-free

```java
import java.util.concurrent.atomic.AtomicInteger;

public final class Contador {
    private final AtomicInteger valor = new AtomicInteger(0);

    public void inc() {
        for (;;) {
            int anterior = valor.get();
            if (valor.compareAndSet(anterior, anterior + 1)) return;
        }
    }

    public int get() { return valor.get(); }

    public void reset() { valor.set(0); }
}
```

Puntos de linealización: CAS exitoso de inc, lectura atómica de get, escritura atómica de reset. Si inc lee un valor, otro hilo hace reset y el CAS falla, debe reintentar sobre el estado actual. Si el valor vuelve a ser igual y el CAS tiene éxito, no hay un ABA perjudicial: el contador no conserva enlaces ni invariantes sobre historia; puede linealizarse ese incremento después del reset.

| Operación | Lock-free | Wait-free en el modelo de primitivas atómicas |
|---|---|---|
| inc con loop CAS | Sí: fallas por interferencia implican modificaciones de otros | No: puede perder todos sus CAS |
| get | Sí | Sí: una lectura |
| reset con store atómico | Sí | Sí: una escritura |

Se usa CAS fuerte, sin fallas espurias. Las garantías se refieren al modelo algorítmico, suponiendo primitivas atómicas no bloqueantes; no son una promesa de latencia física de JVM/OS. El contador sigue la aritmética int de Java, con overflow modular. Para un contador matemático ilimitado habría que cambiar representación y análisis de costo.

## Ejercicio 11 — Figura sin locks

Se publican **pares inmutables**, no cuatro enteros independientes. De otro modo un lector podría ver una coordenada nueva y otra vieja, o un ajuste concurrente podría sobrescribir parte de otro.

Como las dependencias están separadas, usar dos referencias atómicas permite modificar posición y tamaño sin interferencia entre esos grupos:

```text
record Posicion(x,y)             // Inmutable.
record Tamano(alto,ancho)        // Inmutable.
AtomicReference posicion = Posicion(0,0)
AtomicReference tamano = Tamano(0,0)

ajustarPosicion():
    forever:
        p=posicion.get()
        nx=algunX(p.x,p.y)
        ny=algunY(nx,p.y)
        nueva=Posicion(nx,ny)
        if CAS(posicion,p,nueva): return

ajustarTamano():
    forever:
        t=tamano.get()
        na=algunAlto(t.alto,t.ancho)
        nn=algunAncho(na,t.ancho)
        nuevo=Tamano(na,nn)
        if CAS(tamano,t,nuevo): return

leerPosicion(): return posicion.get()
leerTamano(): return tamano.get()
```

Se refactorizaron las funciones para operar sobre un estado local explícito. El orden importa: en el original primero se asigna x y **después** se calcula y, que podría depender del nuevo x. Por eso algunY recibe nx, no necesariamente p.x. Análogamente para alto/ancho. Si las funciones no dependen del estado previo, pueden calcularse una sola vez y publicarse con un store atómico del par.

Contrato necesario para el loop optimista: cálculos puros, finitos y repetibles, sin efectos externos irreversibles. El enunciado sólo separa dependencias entre grupos; si esas funciones tuvieran efectos que no se pudieran repetir, no se puede simplemente ejecutar este CAS-loop sin refactorizarlas o cambiar el contrato.

Cada ajuste se linealiza en el CAS exitoso. Una falla indica que otro ajuste del mismo grupo publicó un cambio: hay progreso global, pero no garantía wait-free para ajustes que dependen del estado previo. Leer cada par es wait-free en el modelo atómico.

Dos lecturas separadas no proporcionan una instantánea atómica de la figura completa. Si se agrega una operación que exige modificar/leer ambos grupos como una sola transacción, una alternativa es una única `AtomicReference<EstadoCompletoInmutable>` y un CAS sobre las cuatro componentes; cada ajuste conserva del snapshot las componentes que no modifica. Eso mantiene lock-freedom pero introduce competencia entre ajustes que en esta solución eran independientes.
