# Streaming por mensajes — solución

Fuente: [transcripción](guia-06a-mensajes-plataforma-streaming.transcripcion.md). Solución elaborada, no oficial. Se mantiene el supuesto del enunciado de que los receptores participan y hay tiempo para repartir cada paquete. La plataforma es la única dueña de su registro de suscripciones.

## a) Lista fija

```text
streamer(entrada):
    forever:
        Sleep(500 ms)
        send(entrada, producirPackage())

plataformaStream(entrada, suscripciones):
    forever:
        Package p = receive(entrada)
        for ch in suscripciones: send(ch,p)
```

Entrada y canales de suscripción son sincrónicos. Cada paquete se entrega una vez a cada suscriptor antes de tomar el siguiente. Un receptor lento produce backpressure; no hay memoria compartida entre registro y clientes. No hay terminación en este inciso.

## b) Tres suscripciones iniciales

Usar un canal de control que transporta el canal de cada suscriptor y un canal `inicio`. La plataforma recibe exactamente tres altas, luego cierra `inicio` y empieza el bucle del inciso a. El streamer ejecuta `<-inicio` antes de producir el primer paquete. Cada suscriptor envía su canal por control y luego recibe/imprime continuamente. El cierre de inicio funciona como broadcast de una única habilitación, no se necesitan tres permisos.

## c) Altas y bajas durante la transmisión

No basta hacer un select entre alta/baja y entrada seguido de un broadcast bloqueante. Un suscriptor puede dejar de recibir para enviar su baja mientras la plataforma intenta enviarle otro paquete: ambos quedarían esperando al otro.

La implementación de abajo procesa controles **también durante el reparto**. Se toma una foto de los destinatarios al recibir cada paquete; un alta durante ese reparto entra en el próximo paquete, y una baja cancela las entregas pendientes a ese id. IDs no se reutilizan durante la transmisión. Los primeros tres controles son altas distintas como permite asumir la consigna.

## d) Cancelación cuando no quedan suscripciones

Este código Go incluye c y d. Para c sin cancelación, quitar la rama que cierra `stop` y marca `terminando`. `inicio`, `stop`, `paquetes` y `control` se crean una vez en main; solo plataforma cierra inicio/stop, y solo streamer cierra paquetes.

```go
package streaming

import (
    "fmt"
    "time"
)

type Package struct { Contenido string }
type Control struct {
    ID int
    Alta bool
    Datos chan Package
    Ack chan struct{} // capacidad 1; una respuesta por comando
}

func Streamer(out chan<- Package, inicio, stop <-chan struct{}) {
    defer close(out)
    <-inicio
    ticker := time.NewTicker(500 * time.Millisecond)
    defer ticker.Stop()
    for n := 0; ; n++ {
        // Prioridad explícita al stop entre paquetes. Un select aislado
        // no garantizaría la cota de un único envío posterior a la baja.
        select { case <-stop: return; default: }
        select { case <-stop: return; case <-ticker.C: }
        p := Package{Contenido: fmt.Sprintf("Paquete %d", n)}
        select { case <-stop: return; case out <- p: }
    }
}

func Plataforma(in <-chan Package, control <-chan Control,
    inicio, stop chan struct{}) {
    activos := make(map[int]chan Package)
    for len(activos) < 3 {
        c := <-control // precondición: tres altas distintas
        activos[c.ID] = c.Datos
        c.Ack <- struct{}{}
    }
    close(inicio)
    pendiente := []int{}
    var paquete Package
    terminando := false
    for {
        // Eliminar de la foto quienes se dieron de baja.
        for len(pendiente) > 0 {
            if _, existe := activos[pendiente[0]]; existe { break }
            pendiente = pendiente[1:]
        }
        entrada := in
        if len(pendiente) > 0 { entrada = nil }
        var salida chan Package
        if len(pendiente) > 0 { salida = activos[pendiente[0]] }
        ctl := control
        if terminando { ctl = nil } // no llegarán más altas
        select {
        case c := <-ctl:
            if c.Alta { activos[c.ID] = c.Datos } else { delete(activos,c.ID) }
            c.Ack <- struct{}{}
            if len(activos) == 0 {
                close(stop)
                terminando = true
                pendiente = nil
            }
        case p, ok := <-entrada:
            if !ok { return }
            if terminando { continue } // drena como máximo un paquete extra
            paquete = p
            pendiente = pendiente[:0]
            for id := range activos { pendiente = append(pendiente,id) }
        case salida <- paquete:
            pendiente = pendiente[1:]
        }
    }
}

func Suscriptor(id, cantidad int, control chan<- Control) {
    datos := make(chan Package)
    ack := make(chan struct{},1)
    control <- Control{ID:id, Alta:true, Datos:datos, Ack:ack}
    <-ack
    for i := 0; i < cantidad; i++ {
        p := <-datos
        fmt.Println(id,p.Contenido)
    }
    control <- Control{ID:id, Alta:false, Ack:ack}
    <-ack
}
```

Sistema: `paquetes := make(chan Package)`, `control := make(chan Control)`, `inicio := make(chan struct{})`, `stop := make(chan struct{})`; lanzar Streamer y Plataforma y al menos los tres Suscriptores iniciales, cada uno con `cantidad > 1`. Las altas posteriores son opcionales. En Go main debe esperar al fin de plataforma (por un canal `fin` o WaitGroup); no basta retornar tras lanzar las goroutines. La guía abstrae esa diferencia en su pseudocódigo.

## Seguridad, terminación y límites

- Un único proceso modifica el mapa y el reparto: no hay race sobre ellos.
- Cada ID de la foto se retira después de un envío; no recibe dos veces el mismo paquete. Una baja elimina cualquier entrega pendiente; no se cierra su canal desde el suscriptor.
- Los ACK tienen capacidad uno y una única respuesta por petición: la plataforma no queda bloqueada esperando que quien se dio de baja atienda una notificación.
- Mientras un envío a datos está bloqueado, la plataforma puede recibir la baja por control. Eso elimina el ciclo de espera mencionado en c.
- El instante de la última baja es su procesamiento por la plataforma. A partir de ahí `stop` queda cerrado. Si el streamer ya pasó el chequeo prioritario puede completar un envío; al volver al inicio del bucle necesariamente observa stop. Por eso la cota es **a lo sumo un paquete adicional**, sin basarse en suerte del select.
- La plataforma sigue drenando entrada hasta que el streamer cierra paquetes. No cierra ella el canal de otro productor y no hay send sobre canal cerrado.
- No se garantiza progreso si un suscriptor desaparece sin recibir ni darse de baja. Para tolerar fallas habría que definir timeouts o expulsión, ajenos a este enunciado.

El código es una propuesta autocontenida; los ejemplos y las justificaciones se diferencian de los originales del PDF. Ver el registro de validación del repositorio para saber cuáles bloques se ejecutaron realmente.
