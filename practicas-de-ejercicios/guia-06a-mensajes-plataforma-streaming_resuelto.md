# Streaming por mensajes — solución punto por punto

Fuente: [transcripción del enunciado](guia-06a-mensajes-plataforma-streaming.transcripcion.md). Elaboración de estudio, no oficial.

Cada inciso incluye **su programa completo e independiente**. Guardar el bloque elegido en un archivo `main.go`; no concatenar los cuatro bloques, porque repiten tipos y funciones. No hace falta copiar código de otro inciso ni aplicar modificaciones implícitas.

Los canales de paquetes son sincrónicos. Se mantiene el supuesto de la consigna de que hay tiempo suficiente entre paquetes para repartirlos y procesar controles. Un ticker no ofrece una garantía de tiempo real: si el receptor demora, el envío espera. El código no fue compilado ni ejecutado en esta revisión.

## a) Lista fija de suscripciones

**Objetivo:** streamer genera un paquete cada 500 ms y plataforma lo replica a una lista recibida como parámetro. La transmisión continúa indefinidamente.

```go
package main

import (
    "fmt"
    "time"
)

type Package struct { Contenido string }

func Streamer(out chan<- Package) {
    ticker := time.NewTicker(500 * time.Millisecond)
    defer ticker.Stop()
    for n := 0; ; n++ {
        <-ticker.C
        out <- Package{Contenido: fmt.Sprintf("Paquete %d", n)}
    }
}

func Plataforma(in <-chan Package, suscripciones []chan Package) {
    for p := range in {
        for _, ch := range suscripciones { ch <- p }
    }
}

// Receptor auxiliar para ejecutar la lista fija del inciso a.
func Receptor(id int, datos <-chan Package) {
    for p := range datos { fmt.Println(id, p.Contenido) }
}

func main() {
    paquetes := make(chan Package) // sin buffer: envío sincrónico
    suscripciones := []chan Package{
        make(chan Package), make(chan Package), make(chan Package),
    }
    for i, ch := range suscripciones { go Receptor(i+1, ch) }
    go Streamer(paquetes)
    Plataforma(paquetes, suscripciones)
}
```

**Por qué funciona:** plataforma toma un paquete y lo envía una vez a cada canal antes de recibir el siguiente. El canal compartido con streamer no tiene buffer, por lo que el envío sincroniza ambos procesos. Los receptores auxiliares permiten ejecutar el ejemplo; en este inciso aún no hay un protocolo de alta ni de baja.

## b) Tres altas antes de comenzar

**Objetivo:** la lista empieza vacía; exactamente tres suscriptores se registran mediante mensajes. Streamer espera a que plataforma confirme que llegaron los tres.

```go
package main

import (
    "fmt"
    "time"
)

type Package struct { Contenido string }

func Streamer(out chan<- Package, inicio <-chan struct{}) {
    <-inicio
    ticker := time.NewTicker(500 * time.Millisecond)
    defer ticker.Stop()
    for n := 0; ; n++ {
        <-ticker.C
        out <- Package{Contenido: fmt.Sprintf("Paquete %d", n)}
    }
}

func Plataforma(in <-chan Package, altas <-chan chan Package,
    inicio chan struct{}) {
    suscripciones := make([]chan Package, 0, 3)
    for len(suscripciones) < 3 {
        suscripciones = append(suscripciones, <-altas)
    }
    close(inicio)
    for p := range in {
        for _, ch := range suscripciones { ch <- p }
    }
}

func Suscriptor(id int, altas chan<- chan Package) {
    datos := make(chan Package)
    altas <- datos
    for p := range datos { fmt.Println(id, p.Contenido) }
}

func main() {
    paquetes := make(chan Package)
    altas := make(chan chan Package)
    inicio := make(chan struct{})
    go Streamer(paquetes, inicio)
    for id := 1; id <= 3; id++ { go Suscriptor(id, altas) }
    Plataforma(paquetes, altas, inicio)
}
```

**Por qué funciona:** cada suscriptor envía su canal por `altas`. Solo después de recibir los tres, plataforma cierra `inicio`, habilitando al streamer. El canal de paquetes sigue siendo sincrónico. Plataforma se ejecuta en main, que no retorna mientras la transmisión sigue activa.

## c) Altas y bajas durante la transmisión

**Objetivo:** después de las primeras tres altas pueden llegar nuevas altas y bajas. Cada suscriptor recibe una cantidad aleatoria mayor que uno y se retira.

Cada comando incluye un ID único y un canal de confirmación de capacidad uno. Los IDs no se reutilizan. Plataforma toma una foto de los destinatarios de cada paquete: un alta durante el reparto participa desde el paquete siguiente; una baja cancela las entregas pendientes a ese ID.

Es importante atender controles también durante el reparto. Si plataforma hiciera un envío bloqueante fuera del `select`, podría esperar a un suscriptor que dejó de recibir porque está intentando enviar su baja.

```go
package main

import (
    "fmt"
    "math/rand"
    "time"
)

type Package struct { Contenido string }
type Control struct {
    ID int
    Alta bool
    Datos chan Package
    Ack chan struct{} // capacidad 1; una respuesta por comando
}

func Streamer(out chan<- Package, inicio <-chan struct{}) {
    <-inicio
    ticker := time.NewTicker(500 * time.Millisecond)
    defer ticker.Stop()
    for n := 0; ; n++ {
        <-ticker.C
        p := Package{Contenido: fmt.Sprintf("Paquete %d", n)}
        out <- p
    }
}

func Plataforma(in <-chan Package, control <-chan Control,
    inicio chan struct{}) {
    activos := make(map[int]chan Package)
    for len(activos) < 3 {
        c := <-control // precondición: tres altas distintas
        activos[c.ID] = c.Datos
        c.Ack <- struct{}{}
    }
    close(inicio)
    pendiente := []int{}
    var paquete Package
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
        select {
        case c := <-ctl:
            if c.Alta { activos[c.ID] = c.Datos } else { delete(activos,c.ID) }
            c.Ack <- struct{}{}
        case p, ok := <-entrada:
            if !ok { return }
            paquete = p
            pendiente = pendiente[:0]
            for id := range activos { pendiente = append(pendiente,id) }
        case salida <- paquete:
            pendiente = pendiente[1:]
        }
    }
}

func Suscriptor(id int, control chan<- Control) {
    cantidad := 2 + rand.Intn(5) // entre 2 y 6 paquetes
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

func main() {
    paquetes := make(chan Package)
    control := make(chan Control)
    inicio := make(chan struct{})
    go Streamer(paquetes, inicio)
    for id := 1; id <= 3; id++ { go Suscriptor(id, control) }
    go func() {
        <-inicio
        time.Sleep(750 * time.Millisecond)
        Suscriptor(4, control) // alta durante la transmisión
    }()
    Plataforma(paquetes, control, inicio)
}
```

**Por qué funciona:**

- Solo plataforma modifica el mapa de suscripciones y la lista de entregas pendientes.
- Mientras quedan destinatarios, se deshabilita la recepción del siguiente paquete mediante un canal `nil`.
- El envío al próximo destinatario compite en el `select` con la recepción de controles. Una baja puede procesarse aunque ese destinatario ya no reciba.
- Cada envío exitoso retira un destinatario pendiente; las bajas también se descartan de la lista.
- Los ACK no bloquean plataforma porque tienen capacidad uno y el suscriptor espera cada confirmación antes de emitir otro comando.
- Cuando el registro queda vacío, **este inciso continúa transmitiendo**: plataforma recibe y descarta paquetes mientras espera posibles altas. Todavía no se pidió detener el sistema.

El ejemplo incorpora un cuarto suscriptor después de iniciada la transmisión. El programa es deliberadamente indefinido, igual que la consigna hasta este inciso.

## d) Detener el stream al quedar cero suscripciones

**Objetivo:** la última baja notifica a streamer; este puede enviar como máximo un paquete adicional y después debe cerrar el canal de paquetes. Se asume que no llegan nuevas altas después de quedar cero suscriptores.

Este programa repite íntegramente la solución con el protocolo de terminación incorporado.

```go
package main

import (
    "fmt"
    "math/rand"
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

func Suscriptor(id int, control chan<- Control) {
    cantidad := 2 + rand.Intn(5) // entre 2 y 6 paquetes
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

func main() {
    paquetes := make(chan Package)
    control := make(chan Control)
    inicio := make(chan struct{})
    stop := make(chan struct{})
    go Streamer(paquetes, inicio, stop)
    for id := 1; id <= 3; id++ { go Suscriptor(id, control) }
    // Las altas posteriores se admiten mientras haya suscriptores.
    // Este ejemplo lanza tres; no intenta nuevas altas después de cero.
    Plataforma(paquetes, control, inicio, stop)
    // Retorna cuando streamer cerró paquetes y plataforma observó el cierre.
}
```

**Por qué funciona y respeta la cota:**

1. La última baja se considera efectiva cuando plataforma la procesa. En ese momento cierra `stop`, descarta pendientes y deja de aceptar controles.
2. Streamer comprueba `stop` al comienzo de cada iteración, antes de generar otro paquete. Si ya había pasado esa comprobación cuando se cerró `stop`, el `select` de envío puede completar **un solo envío adicional**. Al comenzar la siguiente iteración encuentra necesariamente el canal cerrado y termina.
3. No se basa la cota en que un `select` elija siempre la cancelación: cuando envío y cancelación están habilitados, cualquiera puede ganar.
4. Solo streamer cierra `paquetes`, mediante `defer close(out)`. Plataforma sigue recibiendo hasta observar ese cierre; durante esa fase descarta cualquier paquete adicional.
5. Main ejecuta plataforma y espera su retorno, por lo que no termina prematuramente al lanzar las goroutines.

No se cierran los canales individuales de datos: cada suscriptor termina después de confirmar su baja. No se garantiza progreso frente a un suscriptor que desaparece sin recibir ni darse de baja; tolerar esa falla requeriría un protocolo adicional ajeno a la consigna.
