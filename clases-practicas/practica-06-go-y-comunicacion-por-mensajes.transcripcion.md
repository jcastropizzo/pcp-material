# practica-06-go-y-comunicacion-por-mensajes — transcripción

Fuente: [practica-06-go-y-comunicacion-por-mensajes.pdf](practica-06-go-y-comunicacion-por-mensajes.pdf).

Conversión textual por página. Se conserva el texto disponible, los ejemplos y las diapositivas progresivas. No es código compilable ni una corrección del original. Los diagramas, fórmulas, colores y columnas deben cotejarse en el PDF cuando afecten una conclusión. No se modificó el archivo fuente.



## Página 1

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=1)

```text
Programación concurrente y paralela
Práctica 6 - Mensajes

Segundo Cuatrimestre 2026
```

## Página 2

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=2)

```text
Do not communicate by sharing memory;
            instead, share memory by communicating.




Programación concurrente y paralela                   2/28
```

## Página 3

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=3)

```text
Fundamentos de Go
```

## Página 4

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=4)

```text
Variables, inferencia y constantes



     1 var count int = 10                            1 const   Pi = 3.14159       // sin tipo
     2 var total = 0           // int inferido      2 const    MaxUsers int = 100 // tipada
     3 var ready bool          // false             3
     4                                              4 const (
     5 func   main() {                              5     Pending = iota // 0
     6       name := "Mario Santos"                 6     Running        // 1
     7       count = 20      // mismo tipo          7     Done           // 2
     8}                                             8)




                 var: en cualquier scope           :=: solo dentro de funciones
     Sin inicializador ⇒ valor cero              const ⇒ valor de compilación


Programación concurrente y paralela                                                             4/28
```

## Página 5

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=5)

```text
Loops


     1 // Forma clasica                         1// Recorrer una coleccion
     2 for i := 0; i < 5; i++ {                 2for i, value := range values {
     3     fmt.Println(i)                      3     if value < 0 {
     4}                                        4         continue
     5                                         5     }
     6 // Como un while                        6     fmt.Println(i, value)
     7 for pending > 0 {                       7}

     8     foo()                                8

     9     pending--                            9 for { // loop infinito
    10 }                                       10     break
                                               11 }



              Go solo tiene for       range produce índice y copia del valor
     break: termina el loop           continue: avanza a la siguiente iteración
Programación concurrente y paralela                                               5/28
```

## Página 6

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=6)

```text
Funciones también tienen tipo

      1 func   add(a, b int) int {                            1 type   Transform func(int) int
     2        return a + b                                   2
     3}                                                      3 func    apply(f Transform, x int) int {
     4                                                       4        return f(x)
     5 func    div(a, b float64) (float64, error) {          5}
     6        if b == 0 {                                    6
     7            return 0, errors.New("zero")               7 var    double = func(x int) int {
     8        }                                              8        return 2 * x
     9        return a / b, nil                              9}
     10 }                                                    10 var   result = apply(double, 3)
                                                             11
                                                             12 func   greeting() {
                                                             13       defer fmt.Println("Chau.")
                                                             14       fmt.Println("Hola!") // luego "Chau."
                                                             15 }


            Parámetros y retornos tipados             Múltiples retornos          Funciones como valores
      defer ejecuta la llamada al salir de la función; varios se resuelven en orden LIFO




Programación concurrente y paralela                                                                           6/28
```

## Página 7

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=7)

```text
Structs



     1 type User struct {                            1 ravenna   := User{Name: "Emilio Ravenna"}
                                                     2
     2     Name   string
                                                     3 ravenna.Active   = true
     3     Active bool                               4
     4}                                              5 cozzetti   := ravenna // copia del struct
     5                                               6 cozzetti.Name   = "Maximo Cozzetti"
                                                     7
     6   var nobody User // "", false                8 fmt.Println(ravenna.Name)




          Campos con tipo        Valor cero utilizable   Literales con campos nombrados
     Los structs son valores: asignarlos o pasarlos crea una copia



Programación concurrente y paralela                                                                7/28
```

## Página 8

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=8)

```text
Funciones vs. métodos


     Función                                        Método

     1func Activate(u *User) {                      1func (u *User) Activate() {
     2    u.Active = true                           2    u.Active = true
     3}                                             3}

     4                                              4

     5   Activate(&ravenna)                         5   ravenna.Activate()

           Todos los argumentos son                         u *User es el receiver.
                   explícitos.

     Los métodos se declaran fuera del struct y se asocian a un tipo nombrado.
          Receiver T: copia       Receiver *T: puede modificar   Métodos ⇒ interfaces

Programación concurrente y paralela                                                     8/28
```

## Página 9

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=9)

```text
Interfaces

     1type Greeter interface {                     1func Welcome(g Greeter) {
     2    Greet() string                           2    fmt.Println(g.Greet())
     3}                                            3}

     4                                            4

     5 func (u User) Greet() string {              5   var g Greeter = ravenna
     6     return "Hola, " + u.Name                6   Welcome(g)
     7}


          User tiene el método requerido:          El compilador verifica
                  Greet() string                   La asignación es válida porque el
                                                   conjunto de métodos de User
                                                   contiene Greet() string.
                         No existe implements: la satisfacción es implícita.
     Firma exacta           Métodos adicionales permitidos       Interfaces pequeñas


Programación concurrente y paralela                                                    9/28
```

## Página 10

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=10)

```text
Punteros




     1func increment(x *int) {        Como en C
     2    *x = *x + 1
     3}
                                        &x obtiene la dirección
     4
                                           *p accede al valor
     5 n := 10
     6 increment(&n)

     7
                                      Diferencia clave
     8   fmt.Println(n) // 11             No hay aritmética de
                                               punteros




Programación concurrente y paralela                               10/28
```

## Página 11

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=11)

```text
Arrays, slices y views

     1 array := [4]int{1, 2, 3, 4}                     Array: [N]T
     2 slice := array[:]     // len=4, cap=4
     3 view  := slice[1:3] // [2 3]                    Tamaño fijo; N forma parte del tipo.
     4                                                 Sus elementos son modificables.
     5   view[0] = 9                                   Asignarlo o pasarlo copia sus
     6   fmt.Println(array)     // [1 9 3 4]
                                                       elementos.
     7

     8   // cap lleno: crea otro backing array
     9   slice = append(slice, 5)
                                                       Slice: []T
                                                       Descriptor {ptr, len, cap}.
                                                       Sus elementos son modificables.
                            1 9 3 4
                                                       append puede cambiar el backing
             view comparte los elementos resaltados.   array.

                                                       View: s[i:j]
                                                       También es un slice: comparte
                                                       memoria.
                                                       Modificarla puede afectar a s y al array.
Programación concurrente y paralela                                                                11/28
```

## Página 12

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=12)

```text
Goroutines

     1 func work(id int) {                            Green threads: modelo M:N
     2     fmt.Println("inicio", id)
     3     compute()                                          G_1      G_2    G_3      ···    G_M
     4     fmt.Println("fin", id)                                   ⇓ scheduler del runtime ⇓
     5}                                                             T_1    T_2     ···    T_N
     6
                                                      M goroutines se multiplexan sobre N threads del SO.
     7   func main() {
      8      go work(1)
                                                      Implicancias para el programa
      9      go work(2)
     10      work(0)                                    • El orden de ejecución no está garantizado.
      11 }
                                                        • go f() no espera ni ofrece un join.
      go f() crea una goroutine y continúa              • Comparten memoria: pueden aparecer data
     sin esperar su resultado. Al retornar main,
                                                          races.
         termina el proceso y todas las goroutines.     • Sincronización y finalización explícitas.
                                                       Requiere correctitud para cualquier interleaving
                                                                           válido.

Programación concurrente y paralela                                                                         12/28
```

## Página 13

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=13)

```text
Channels

      type Message struct {
      1
                                               Canal tipado
     2    Text string
     3}                                        chan Message transporta solamente
     4                                         valores de tipo Message.
     5    messages := make(chan Message)
     6
                                               Operaciones bloqueantes
     7 go func() {
     8     messages <- Message{Text: "hola"}   ch <- value: espera hasta poder enviar.
     9 }()
                                               value := <-ch: espera hasta poder
     10

     11   msg := <-messages                    recibir.

                                               Dos funciones
                                               Comunicar un valor y establecer un
                                               punto de sincronización.




Programación concurrente y paralela                                                      13/28
```

## Página 14

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=14)

```text
Victoria Canalizada
```

## Página 15

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=15)

```text
Victoria Canalizada



     Victoria se mudó a San Martín de los Andes,
     así que sus colaboradores ya no pueden
     acercarse al taller.
     Le envían los paquetes por Correo Argentino,
     con las mismas reglas que el problema
     original: una carrocería y 2 faros, lo justo.   Famosa marca Italiana

     Los colaboradores esperan a que Victoria
     termine el auto antes de mandar más.



Programación concurrente y paralela                                          15/28
```

## Página 16

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=16)

```text
Generación Paralela
```

## Página 17

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=17)

```text
Generación Paralela

     Cayó una auditoría en la materia y no pasamos la
     inspección. Por suerte, el inspector nos indicó que si
     contribuimos a su ONG, podía reconsiderar su postura.
     Imbuido de una recientemente adquirida sabiduría
     china, Hernán nos propuso un novedoso sistema de
     inversión: periódicamente hacés una contribución
     monetaria a la persona que te invitó. A cambio, podés
     invitar a otras 4 personas más. Recibirás comisiones
     por cada envío de dinero, multiplicando tus ganancias
     y todos ganando en el proceso.
     Participar es obligatorio para presentarse al final y la   Novedosa propuesta de inversión
     nota es proporcional a la cantidad de familiares y
     amigos que invites.


Programación concurrente y paralela                                                               17/28
```

## Página 18

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=18)

```text
Unbuffered channels

      1func produce(out chan string) {      Encuentro entre goroutines
     2     fmt.Println("P: antes")
     3     out <- "dato"                         Productor: out <- "dato"
     4     fmt.Println("P: despues")              ⇓ espera al consumidor ⇓
     5}

     6                                           Consumidor: msg := <-ch
     7 func main() {
     8     ch := make(chan string)          Cuando se encuentran
     9     go produce(ch)
    10
                                            El valor se transfiere y ambas
     11       fmt.Println("C: esperando")   goroutines pueden continuar.
     12       msg := <-ch
     13       fmt.Println("C:", msg)           Capacidad cero: no hay valores
    14    }                                            pendientes.




Programación concurrente y paralela                                             18/28
```

## Página 19

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=19)

```text
Streams: dirección, cierre e iteración

      1   func produce() <-chan int {                 1   values := produce()
     2        out := make(chan int)                   2

     3                                                3for value := range values {
     4        go func() {                            4     fmt.Println(value)
     5            defer close(out)                    5}

     6            for i := 1; i <= 3; i++ {          6

     7                out <- i                       7    value, ok := <-values
     8            }                                  8    // value == 0, ok == false
     9        }()
     10
                                                      close es una señal
     11       return out
     12   }                                           Indica que no habrá más valores. Los
                                                      valores buffered pendientes se
                   chan<- T: solo envío
                                                      reciben antes de terminar el range.
                 <-chan T: solo recepción
      Cierra quien produce, intentar enviar luego del cierre produce panic.



Programación concurrente y paralela                                                          19/28
```

## Página 20

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=20)

```text
Parte I: los inversores



        • Hay un array con los nombres de los inversores. Hagan una goroutine
           que exponga todos los nombres en un canal, numerados para evitar
           repeticiones.
        • Pueden usar fmt.Sprintf("( %d) %s", i, nombre) para
           numerarlos.
        • En main, completen la función invitar: cada asociado invita hasta
           4 subordinados, y así sucesivamente, hasta que no haya más
           personas en la lista.




Programación concurrente y paralela                                             20/28
```

## Página 21

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=21)

```text
Wait Groups: esperar a que todas las goroutines terminen

      1 func work(id int,
     2      wg *sync.WaitGroup) {                   Un contador de tareas pendientes
     3      defer wg.Done()                         Add(1): registra una tarea.
     4      maybeHalt(id)
     5}                                             Done(): indica que una tarea terminó.
     6
     7 func  main() {                               Wait(): bloquea hasta llegar a cero.
     8      var wg sync.WaitGroup
     9
     10     for id := 0; id < 3; id++ {             Qué garantiza
     11         wg.Add(1)
     12         go work(id, &wg)                       • Después de Wait(), todas las tareas
     13     }
     14
                                                         registradas hicieron Done().
            wg.Wait()
     15
     16     fmt.Println("Todos terminaron")
                                                       • El orden de finalización no está
     17 }                                                garantizado.
                             TL;DR: Add antes de go y defer Done() al entrar.
      Compartir el mismo WaitGroup por puntero.


Programación concurrente y paralela                                                            21/28
```

## Página 22

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=22)

```text
Parte II: la pirámide




        • Usen wait groups (sync.WaitGroup) para que ahora sí se puedan
           generar todos los inversores posibles.




Programación concurrente y paralela                                       22/28
```

## Página 23

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=23)

```text
select: esperar múltiples eventos

     1  select {                                   1 select {

     2  case job := <-urgent:                      2 case value := <-results:

      3     handle(job)                            3     use(value)
      4 case job := <-normal:                      4 default:

      5     handle(job)                            5     doOtherWork()
      6 case <-time.After(time.Second):            6}

      7     // time.After(...) sends the timestamp
      8     // when the timer fires
                                                   default
      9     fmt.Println("zzz")
     10 }                                          Se ejecuta inmediatamente si ningún
                                                     otro caso está listo: convierte el
     Selección                                       select en no bloqueante.
     Espera hasta que algún caso pueda avanzar.
                                                     Una selección
     Si hay varios listos, elige uno sin prioridad
     por orden en la expresión.                      select elige una vez; para repetir
                                                     debe estar dentro de un loop.

Programación concurrente y paralela                                                       23/28
```

## Página 24

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=24)

```text
Worker pool: productor–consumidores

     1 func worker(                            1   jobs := make(chan Job)
     2     id int,                            2    results := make(chan Result)
     3     jobs <-chan Job,                   3

     4     results chan<- Result,             4for id := 0; id < 3; id++ {
     5) {                                     5    go worker(id, jobs, results)
     6     for job := range jobs {            6}

     7         results <- process(job)        7

     8     }                                  8   go func() {
     9}                                       9       defer close(jobs)
                                              10      for _, job := range input {
     Cada worker consume hasta que jobs        11         jobs <- job
                   se cierra.                 12      }
                                              13 }()

                                              14

                                              15 for range input {
                                              16     fmt.Println(<-results)
                                              17 }


                           input → jobs → workers → results → main
     La topología define trabajo, backpressure y terminación.
Programación concurrente y paralela                                                 24/28
```

## Página 25

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=25)

```text
Buffer: cola acotada y backpressure

     1   jobs := make(chan Job, 2)      Estado del buffer: capacidad 2
     2

     3 jobs <- jobA // no bloquea           jobA jobB        jobC → espera
     4 jobs <- jobB // no bloquea
     5 jobs <- jobC // bloquea: lleno
                                          • Envío bloquea cuando el buffer está
                                            lleno.
     Creación
                                          • Recepción bloquea cuando está
     make(chan T): capacidad cero.
                                            vacío.
     make(chan T, N): hasta N valores     • Los valores salen en orden FIFO.
     pendientes.                          • El productor puede adelantarse
                                            hasta la capacidad.

                                        No es “asíncrono”
                                        El buffer absorbe ráfagas, pero conserva
                                        backpressure.
Programación concurrente y paralela                                                25/28
```

## Página 26

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=26)

```text
Channels: errores frecuentes

                                                 • Un range no termina si nadie cierra el
     Deadlock
                                                   canal.
     1 ch := make(chan int)                      • Enviar o recibir sobre un canal nil
     2 ch <- 1 // nadie recibe                     bloquea siempre.
                                                 • Un consumidor que abandona puede
     Canal cerrado                                 dejar al productor bloqueado.
                                                 • Agregar buffer no corrige un
     1   close(ch)                                 protocolo incorrecto.
     2   ch <- 2 // panic
                                               Pregunta de diseño
                                                 ¿Quién produce, quién consume, quién
                                                  cierra y cómo termina cada goroutine?
     Todo canal necesita propiedad clara; toda goroutine, una condición de salida.




Programación concurrente y paralela                                                         26/28
```

## Página 27

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=27)

```text
Parte III: Recaudación


     Ahora sí, toca hacer el sistema de contribuciones. Funciona así:
        • Todos los inversores arrancan con 1000 yuanes.
        • Si tengo asociados subordinados:
              • Si no tengo jefe, me lo quedo.
              • Si tengo jefe, le reenvío los pagos.
        • Si me quedé sin asociados subordinados:
              • Cada 100 ms, envío 100 yuanes.
              • Si tengo menos de 100 yuanes, envío un pago de 0 (que indica que me
                 cierro) y retorno.
              • Si soy el jefe principal (no tengo jefe), en cuanto me quedo sin
                 subordinados imprimo el total obtenido y retorno.


Programación concurrente y paralela                                                   27/28
```

## Página 28

[Ver página del PDF](practica-06-go-y-comunicacion-por-mensajes.pdf#page=28)

```text
Parte IV: Teoría del Derrame



     Finalmente, queda hacer el sistema de comisiones, el que permite
     volvernos ricos a todos. Funciona así:
        • Al recibir pagos, el jefe principal (el que no tiene jefe) se queda con el
           50 % y envía el restante a sus subordinados por partes iguales.
        • Si recibo una comisión de mi jefe, me quedo con el 50 % y envío el
           restante en partes iguales a mis subordinados.
        • Si recibo una comisión y no tengo subordinados, me quedo con el
           total.




Programación concurrente y paralela                                                    28/28
```
