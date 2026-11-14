# Servidor con selección y servidor por turnos — transcripción

- Fuente: [practica-07a-servidor-seleccion.pdf](practica-07a-servidor-seleccion.pdf).
- Páginas: 10.
- SHA-256: `a185fbf3b435aff7b973f88a6049a6f23d789ac68508a98eaf98b8b4559c3f5c`.

> Texto por página con disposición aproximada. La extracción puede unir palabras o desplazar símbolos. Cotejar fórmulas, código y diagramas en el PDF.

## Página 1

[Ver página original](practica-07a-servidor-seleccion.pdf#page=1)

```text
   Versión A: el diagrama




                              0, P, P
                   pedido1              pedido2
         1, E, P         usar1     usar2          2, P, E
               resp1                          resp2
          usar2    0, U, P              0, P, U    usar1
                    pedido1             pedido2
         1, E, U         usar2     usar1          2, U, E
                    resp1                resp2
                              0, U, U




 Programación concurrente y paralela                        1/10
```

## Página 2

[Ver página original](practica-07a-servidor-seleccion.pdf#page=2)

```text
   Versión B: el diagrama



                        pedido2

         0, U, P usar1 0, P, P pedido1 1, E, P resp1 2, U, P
          usar2     usar2      usar2       usar2

         0, U, U usar1 0, P, U pedido1 1, E, U resp1 2, U, U usar1

          resp2     resp2                  usar1
         3, U, E usar1 3, P, E             2, P, U usar2 2, P, P

                                    pedido2



 Programación concurrente y paralela                        2/10
```

## Página 3

[Ver página original](practica-07a-servidor-seleccion.pdf#page=3)

```text
   Las proposiciones sobre los estados


    Cada proposición es una condición sobre una componente de la terna.
               Proposición Versión A  Versión B

               pide       cliente i en P cliente i en P
                  i
               espera     cliente i en E cliente i en E
                    i
               usa        cliente i en U cliente i en U
                  i
               atiende    servidor en 1 servidor en 1
                     1
               atiende    servidor en 2 servidor en 3
                     2
      • Recordemos: la traza de una ejecución es la sucesión de estados que
       visita. El LTS satisface φ si la satisfacen las trazas de todas sus
       ejecuciones.
      • Como no hay estados sin salida, todas las ejecuciones son infinitas.
 Programación concurrente y paralela                        3/10
```

## Página 4

[Ver página original](practica-07a-servidor-seleccion.pdf#page=4)

```text
   Recordemos  los operadores



       Fórmula Vale en la posición i si...

       □φ      φ vale en i y en todas las siguientes
       ♢φ      φ vale en i o en alguna siguiente

       ⃝φ      φ vale en i + 1
       φ U ψ   ψ vale en algún j ≥ i, y φ vale en todas las anteriores a j

       φ W ψ   como U, pero si ψ no llega nunca, alcanza con □φ

    φ W ψ ≡ (φ U ψ) ∨ □φ. Con lo cual U exige que ψ llegue, y W no.



 Programación concurrente y paralela                        4/10
```

## Página 5

[Ver página original](practica-07a-servidor-seleccion.pdf#page=5)

```text
   Recordemos: safety y liveness




     Safety

     Algo malo nunca pasa. Si una traza la viola, hay un momento finito en
     que ya se violó, y nada de lo que venga después lo arregla.

     Liveness

     Algo bueno termina pasando. Ningún prefijo finito la viola, porque lo
     bueno siempre puede llegar más adelante.




 Programación concurrente y paralela                        5/10
```

## Página 6

[Ver página original](practica-07a-servidor-seleccion.pdf#page=6)

```text
    □♢
   1.  usa  en A: no vale
           2
                              0, P, P
                      pedido1         pedido2
              1, E, P     usar1   usar2       2, P, E
                  resp1                   resp2
              usar2   0, U, P         0, P, U usar1
                      pedido1         pedido2
              1, E, U     usar2   usar1       2, U, E
                      resp1           resp2
                              0, U, U
                                  (cid:0)           (cid:1)ω
    El select puede elegir siempre al cliente 1: (0, P, P) (1, E, P) (0, U, P) . La
    vuelta nunca pasa por un estado con el cliente 2 en U, que se queda en P para
    siempre.
     Pregunta
     ¿Es válido este lazo bajo fair choice?
 Programación concurrente y paralela                        6/10
```

## Página 7

[Ver página original](practica-07a-servidor-seleccion.pdf#page=7)

```text
   2. □(pide → ♢ espera )
           1          1

                              0, P, P
                      pedido1         pedido2
              1, E, P     usar1   usar2       2, P, E
                  resp1                   resp2
              usar2   0, U, P         0, P, U usar1
                      pedido1         pedido2
              1, E, U     usar2   usar1       2, U, E
                      resp1           resp2
                              0, U, U
    En A no vale. Es lo mismo al revés. El select elige siempre al cliente 2,
    (cid:0)          (cid:1)ω
     (0, P, P) (2, P, E) (0, P, U) , y el cliente 1 se queda en P.
      • Bajo fair choice vale, por el mismo argumento: hay una sola componente
       terminal y tiene estados con el cliente 1 en E.

 Programación concurrente y paralela                        7/10
```

## Página 8

[Ver página original](practica-07a-servidor-seleccion.pdf#page=8)

```text
   3. □¬(usa ∧ usa ): no vale en ninguna
            1     2
                              0,P,P
                       pedido1       pedido2
                1,E,P      usar1  usar2     2,P,E
                    resp1                resp2
                 usar2 0,U,P         0,P,U   usar1
                       pedido1       pedido2
                1,E,U      usar2  usar1     2,U,E
                        resp1         resp2
                              0,U,U
    En palabras: nunca hay un estado en el que los dos clientes estén usando una
    respuesta a la vez.
    En A, un prefijo finito: (0, P, P) (1, E, P) (0, U, P) (2, U, E) (0, U, U). En B, la misma
    secuencia de acciones también llega a (0, U, U).
      • El servidor no espera a que un cliente termine de usar para atender al otro.
      • Fair choice no cambia nada. Es safety, el prefijo malo ya ocurrió, y se puede
       continuar de forma justa.
 Programación concurrente y paralela                        8/10
```

## Página 9

[Ver página original](practica-07a-servidor-seleccion.pdf#page=9)

```text
   4. □(espera → ⃝(espera  ∨ usa ))
              1           1     1

    En palabras: si el cliente 1 está esperando, en el estado siguiente sigue

    esperando o ya está usando. Nunca vuelve a P sin haber usado la
    respuesta.
     Pregunta

     ¿⃝ es “el próximo paso del cliente 1”?

    No. ⃝ es el próximo paso del sistema. Puede ser un paso del servidor, del
    cliente 1 o del cliente 2.

      • Es safety. Un contraejemplo sería un prefijo que termina en un paso
       que saca al cliente 1 de E hacia P.


 Programación concurrente y paralela                        9/10
```

## Página 10

[Ver página original](practica-07a-servidor-seleccion.pdf#page=10)

```text
   4. Vale en las dos


    Busquemos un paso que saque al cliente 1 de E hacia algo distinto de U.
    Usemos la regla de composición:
      • Si la acción no está en el alfabeto de C (pedido , resp , usar ), el
                                    1      2   2    2
       cliente 1 no se mueve. Sigue en E.
      • Si está en el alfabeto de C , tiene que ofrecerla C . Desde E, C solo
                           1               1         1
       ofrece resp , que lo lleva a U.
                1
    No usamos nada del servidor, con lo cual el argumento vale igual en A y
    en B. Como no hay estados sin salida, siempre hay un estado siguiente y
    ⃝ tiene sentido.
    Es la misma idea que la propiedad 2 con W. La fórmula equivale a
                    □(espera → (espera W usa ))
                           1        1    1
    es decir, desde que espera, sigue esperando hasta usar (o para siempre).
 Programación concurrente y paralela                       10/10
```
