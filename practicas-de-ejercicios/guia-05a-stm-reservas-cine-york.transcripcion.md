# guia-05a-stm-reservas-cine-york — transcripción

Fuente: [guia-05a-stm-reservas-cine-york.pdf](guia-05a-stm-reservas-cine-york.pdf).

Conversión textual por página. Se conserva el texto disponible, los ejemplos y las diapositivas progresivas. No es código compilable ni una corrección del original. Los diagramas, fórmulas, colores y columnas deben cotejarse en el PDF cuando afecten una conclusión. No se modificó el archivo fuente.



## Página 1

[Ver página del PDF](guia-05a-stm-reservas-cine-york.pdf#page=1)

```text
Programación Concurrente - STM
                                     Ejercicio: Cine York

El Cine York, en Olivos, es una sala municipal donde las entradas son gratis. Eso tiene una
consecuencia previsible: cuando hay un ciclo bueno, la cola empieza una hora antes y da vuelta
la manzana. Hay jubilados que van todas las semanas, familias enteras, grupos de estudiantes y
vecinos que pasaban por ahí y se sumaron. Como no se cobra, el municipio no quiere poner más
personal en la puerta y decidió reemplazar al acomodador por un sistema de reserva en el que
cada grupo es un thread.




                                       Cine York, Olivos.

Cada grupo de k personas quiere sentarse junto: k butacas contiguas en una misma fila, lo más
adelante posible en el orden de preferencia. Si eso no es posible, acepta k butacas cualesquiera,
también respetando la preferencia. Si ni siquiera hay k libres, espera en la vereda. Cuando
termina la película, el grupo libera sus butacas.
La sala se modela así:
  type Butaca = TVar Bool       -- True = ocupada
  type Fila   = [Butaca]
  type Sala   = [Fila]          -- de la fila más buscada a la menos buscada


Requisitos
El sistema tiene que cumplir lo siguiente:

                                               1
```

## Página 2

[Ver página del PDF](guia-05a-stm-reservas-cine-york.pdf#page=2)

```text
R1. Nunca se asigna una butaca dos veces y ningún grupo queda con menos butacas que
    personas.
R2. Un grupo que estuvo esperando no queda atado a la situación de cuando empezó a esperar.
    Cuando finalmente consigue lugar, se le aplica la misma política que a un grupo recién
    llegado, con las butacas libres en ese momento.
    Por ejemplo: una familia de 4 llega y la sala está llena, así que espera en la vereda. Primero
    se liberan 2 butacas sueltas en distintas filas, que no le alcanzan. Después termina la función
    de otro grupo y se libera un bloque de 4 butacas juntas en la fila 3. La familia tiene que
    sentarse en ese bloque, no repartida entre las butacas sueltas y otras de la fila 3.
R3. Un grupo nunca debe despertarse ni re-ejecutarse por cambios que no pueden afectarlo.

Se pide
  a) Implementar el sistema completo, incluidas la creación de la sala y del estado compartido.
     Implementar una funcion reservar, que de la cantidad de grupos y una sala, realiza la
     reserva.




                                                2
```
