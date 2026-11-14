# Streaming: soluciones suministradas — transcripción

- Fuente: [guia-06a-mensajes-plataforma-streaming-2026-2c-soluciones.pdf](guia-06a-mensajes-plataforma-streaming-2026-2c-soluciones.pdf).
- Páginas: 6.
- SHA-256: `be8c8bdb966c0ab4abd2546a873cf8642e2e7fabfd6a9d9ee74f5c301ad2dc9e`.

> Texto por página con disposición aproximada. La extracción puede unir palabras o desplazar símbolos. Cotejar fórmulas, código y diagramas en el PDF.

## Página 1

[Ver página original](guia-06a-mensajes-plataforma-streaming-2026-2c-soluciones.pdf#page=1)

```text
                 Programación      Concurrente      y Paralela


                                 Ejercicio Mensajes
                               2do Cuatrimestre 2026


       Ejercicio 1. Queremos modelar a una persona que hace una transmisión de streaming, su
       plataforma, y a la gente que está subscripta a ella. Modelaremos el stream como una secuencia
       de elementos Package (que, en Go, serían un struct{ contenido : string }, para poder
       imprimirlo). La persona streamer tiene su plataformaStream que se encarga de enviar cada
       Package generado por la persona streamer a la gente que esté suscripta a la transmisión, hasta
       que esta se termine.
          Implementar utilizando comunicación por medio de mensajes:

         a) Un thread streamer y un thread plataformaStream. plataformaStream recibe una lista
           de canales de Package suscriptos a la transmisión. Ambos threads tienen un canal en
           común por el que streamer envía sincrónicamente cada package cada 0.5 segundos y
           plataformaStream lo replica a todos los canales en la lista de suscripciones. Por lo pronto,
           streamer genera Packages indefinidamente (se puede asumir que los suscriptores los irán
           consumiendo).


           Solución:

            platCh = create (channel Package, 0)

            thread streamer ((channel Package) platCh ) {

                Ticker ticker = new Ticker(0.5s)
                while (true) {
                    ticker.receive()
                    platCh.send(new Package()) //o funcion ficticia
                                               //que genere el package
                }
            }

            thread plataformaStream(list<channel Package> subs,

                                       (channel Package) platCh) {
                while(true) {
                    Package p = platCh.receive()
                    for (sCh : subs) {
                        sCh.send(p)
                    }
                }
            }

           Ausencia de Deadlock: platCh es sincrónico y arma un esquema productor-consumidor
           clásico: el send del streamer siempre encuentra receptor (la plataforma está en receive(),


                                         1
```

## Página 2

[Ver página original](guia-06a-mensajes-plataforma-streaming-2026-2c-soluciones.pdf#page=2)

```text
       Prog. Concurrente y Paralela                           Ejercicio Mensajes


           salvomientrasestéreplicando,yesareplicaciónterminasolacuandolossuscriptoresconsu-
           men), y la plataforma siempre encuentra emisor mientras el streamer esté vivo. Los envíos
           para la distribución, en cambio, exigen que los canales de la lista tengan lector: en este
           ítem la lista llega por parámetro, y la solución vale bajo esa suposición (hay lectores, o
           buffer suficiente). Si un canal de la lista no tuviera lector, la plataforma se bloquearía en

           ese envío y en consecuencia también el streamer.
         b) Modifique la solución anterior para que ahora plataformaStream no reciba la lista de
           suscripcionesporparámetro,sinoqueéstacomiencevacíaylaspersonassuscriptorasdeban
           unirse a ella por medio de mensajes. Suponga que habrán exactamente 3 suscripciones, y
           haga que el thread streamer comience la transmisión una vez las mismas hayan llegado.
           Agregue el thread suscriptor que se agregue como suscriptor e imprima los Packages (o

           bien sus contenido) a medida que vayan llegando.

           Solución:
            thread streamer ((channel Package) platCh, channel ()  startCh) {

                startCh.receive()
                Ticker ticker = new Ticker(0.5s)
                while (true) {
                    ticker.receive()
                    platCh.send(new Package())
                }
            }

            thread plataformaStream((channel (channel Package)) subCh,

                    (channel Package) platCh, channel () startCh) {
                List<channel Package> subs
                for (int i=0;i<3;i++) {
                    subs.append(subCh.receive())
                }
                startCh.send( () )
                while(true) {
                    Package p = platCh.receive()
                    for (sCh : subs) {

                        sCh.send(p)
                    }
                }
            }

            thread suscriptor (channel (channel Package) subCh) {
                miCanal = create (channel Package, 0)
                subCh.send(miCanal)
                while (true) {

                    print(miCanal.receive())


                                         2
```

## Página 3

[Ver página original](guia-06a-mensajes-plataforma-streaming-2026-2c-soluciones.pdf#page=3)

```text
       Prog. Concurrente y Paralela                           Ejercicio Mensajes


                }
            }

           Ausencia de Deadlock: El arranque no tiene espera circular porque cada envío tiene
           su correspondiente receptor activo: los tres subCh.send(miCanal) de los suscriptores se
           emparejan con los tres subCh.receive() de la plataforma; el startCh.send() de la plata-
           forma se empareja con el startCh.receive() del streamer (y la plataforma solo lo manda
           cuando ya tiene las tres suscripciones, que es justo lo que el streamer espera); y los sus-

           criptores, después de suscribirse, permanecen en miCanal.receive(). Con todo estable,
           vale lo mismo que en el ítem 1: platCh es productor-consumidor y la distribución siempre
           encuentra lectores. Notar que nadie espera nada que dependa de sí mismo, cada bloqueo
           se resuelve con el progreso independiente de otro thread.
         c) Modifique la solución para que ahora puedan seguir llegando suscripciones una vez comen-
           zada la transmisión, y que además sea posible dar de baja suscripciones (puede suponer

           que las primeras 3 acciones que llegarán son 3 suscripciones). Para el thread suscriptor,
           haga que reciba alguna cantidad aleatoria de paquetes (se puede suponer mayor que 1) y se
           desuscriba del stream. Puede suponer que el espacio entre Package y Package es suficiente
           como para que en el medio se pueda hacer el envío a los canales suscriptores y aplicar las
           suscripciones/desuscripciones de haberlas (Pregunta adicional, ¿Qué complicación podría
           darse si no asumiera esto?)

           Solución: Cambiamos el canal subCh para que sea un canal de un tipo Command.


            type Command = struct {accion string, (channel Package)  canal}

            thread streamer ((channel Package) platCh, channel ()  startCh) {
                startCh.receive()
                Ticker ticker = new Ticker(0.5s)

                while (true) {
                    ticker.receive()
                    platCh.send(new Package())
                }
            }

            thread plataformaStream((channel Command) subCh,
                    (channel Package) platCh, channel () startCh) {
                List<channel Package> subs

                for (int i=0;i<3;i++) {
                    Command suscripcion = subCh.receive()
                    subs.append(suscripcion.canal)
                }
                startCh.send( () )
                while(true) {
                    select {


                                         3
```

## Página 4

[Ver página original](guia-06a-mensajes-plataforma-streaming-2026-2c-soluciones.pdf#page=4)

```text
       Prog. Concurrente y Paralela                           Ejercicio Mensajes


                        case Package p = platCh.receive()
                            for (sCh : subs) {
                                sCh.send(p)
                            }
                        case Command c = subCh.receive()

                            if c.accion == "sub" {
                                subs.append(c.canal)
                            } else {
                                subs.remove(c.canal)
                            }
                    }
                }
            }


            thread suscriptor (channel (channel Package) subCh) {
                miCanal = create (channel Package, 0)
                subCh.send(Command{accion="sub",canal=miCanal})
                int cantidad = random() //por hipotesis: cantidad  > 1
                int recibidos = 0
                while (recibidos < cantidad) {
                    print(miCanal.receive())
                    recibidos++
                }

                subCh.send(Command{accion="unsub",canal=miCanal})
            }

           La complicación que requiere asumir que la actualización de la lista se puede hacer en el
           lapso entre dos paquetes es que un suscriptor que se desuscribe deja de leer su canal, pero
           esecanaltodavíapodríaestarensubscuandollegaelpróximopaquete.Conuntimingdes-
           afortunado, podría cerrarse una espera circular: la plataforma bloqueada en sCh.send(p)
           esperando que el suscriptor reciba, y el suscriptor bloqueado en subCh.send(unsub) espe-
           rando que la plataforma reciba. Bajo esa hipótesis del enunciado, el unsub ya fue procesado
           y el canal sacado de subs antes del próximo platCh.send() del streamer, con lo cual la
           distribución nunca envía a un canal sin lector. (Si quisiéramos no depender de la hipótesis
           del timing, podríamos considerar hacer que los canales de los suscriptores fueran asincró-

           nicos, estimándoles un tamaño lo suficientemente grande como para que puedan contener
           una cantidad de paquetes que soporte el desfasaje de tiempo hasta que la desuscripción
           fuera aceptada (y, aún más, se podría intentar asegurar el vaciado del canal de manejo de
           suscripciones antes del envío del siguiente paquete)).
         d) Finalmente, modifique la solución para que si, durante el lapso de la transmisión sucede

           que la cantidad de suscripciones es cero, se informe a la persona streamer y esta detenga la
           transmisión (no debería mandar más que un Package desde la última desuscripción hasta
           la anulación del stream, suponga que una vez que las suscripciones llegaron a 0, no habrán
           más suscripciones nuevas). Modele el fin de la transmisión como el cierre del canal entre


                                         4
```

## Página 5

[Ver página original](guia-06a-mensajes-plataforma-streaming-2026-2c-soluciones.pdf#page=5)

```text
       Prog. Concurrente y Paralela                           Ejercicio Mensajes


           streamer y plataformaStream.

           Solución: Hay que agregar el caso de 0 suscriptores, avisar al streamer, y cerrar la trans-
           misión.


            type Command = struct {accion string, (channel Package)  canal}

            channel () endCh = create (channel (), 1)

            thread streamer ((channel Package) platCh,

                    channel () startCh, channel () endCh) {
                startCh.receive()
                Ticker ticker = new Ticker(0.5s)
                bool fin = false
                while (!fin) {
                    select {
                        case endCh.receive():
                            fin = true
                            close(platCh)

                            ticker.Stop()
                        default:
                            ticker.receive()
                            platCh.send(new Package())
                    }
                }
            }

            thread plataformaStream((channel Command) subCh,

                    (channel Package) platCh, channel () startCh,
                    channel () endCh) {
                List<channel Package> subs
                for (int i=0;i<3;i++) {
                    Command suscripcion = subCh.receive()
                    subs.append(suscripcion.canal)
                }
                startCh.send( () )
                bool abierto = true

                while(abierto) {
                    select {
                        case Package p= platCh.receive()
                            if (p == EOF) {
                                abierto = false
                            } else {
                                for (sCh : subs) {


                                         5
```

## Página 6

[Ver página original](guia-06a-mensajes-plataforma-streaming-2026-2c-soluciones.pdf#page=6)

```text
       Prog. Concurrente y Paralela                           Ejercicio Mensajes


                                    sCh.send(p)
                                }
                            }
                        case Command c = subCh.receive()
                            if c.accion == "sub" {

                                subs.append(c.canal)
                            } else {
                                subs.remove(c.canal)
                                if subs.length==0 {
                                    endCh.send( () )
                                }
                            }
                    }
                }

            }

            thread suscriptor (channel (channel Package) subCh) {
                miCanal = create (channel Package, 0)
                subCh.send(Command{accion="sub",canal=miCanal})
                int cantidad = random() //por hipotesis: cantidad  > 1
                int recibidos = 0
                while (recibidos < cantidad) {
                    print(miCanal.receive())

                    recibidos++
                }
                subCh.send(Command{accion="unsub",canal=miCanal})
            }

           El canal endCh debe tener un buffer (en este caso puede ser de tamaño 1). Si no, puede
           haber deadlock entre streamer y plataformaStream: la plataforma queda bloqueada en
           endCh.send() esperando al streamer, y el streamer bloqueado en platCh.send() espe-
           rando que la plataforma lo reciba. El buffer de 1 hace que la señal no bloquee a quien la
           manda. Más allá de esto: (i) al hacer close(platCh), la plataforma recibe EOF y corta; (ii)
           el paquete que puede quedar en vuelo se procesa con la distribución sobre la lista vacía,
           que no hace ningún envío y por lo tanto no bloquea (por eso está bien que el streamer,

           que recién ve la señal de cierre cuando termina el envío en curso, mande a lo sumo un
           paquete más, como pide la consigna); (iii) como subs llegó a cero, no queda ningún sus-
           criptor bloqueado en receive() para siempre: todos se fueron al desuscribirse; (iv) sigue
           valiendo la hipótesis de timing del ítem anterior, que garantiza que el último unsub se
           procesa antes de la última distribución. Con eso todos los threads terminan: el streamer
           por fin, la plataforma por EOF, y cada suscriptor al desuscribirse.






                                         6
```
