# Práctica 3: de semáforos a monitores, pools y promesas — resumen

- Fuente: [practica-03-de-semaforos-a-monitores.pdf](practica-03-de-semaforos-a-monitores.pdf) (30 páginas).
- [Transcripción completa](practica-03-de-semaforos-a-monitores.transcripcion.md).
- Síntesis editorial basada en este PDF; no reemplaza los enunciados ni constituye una fe de erratas oficial.

## Mapa de consulta

| Páginas | Contenido |
| --- | --- |
| [1–7](practica-03-de-semaforos-a-monitores.transcripcion.md#página-1) | Puente, molinete, fairness, paso de testigo y Lightswitch |
| [7–10](practica-03-de-semaforos-a-monitores.transcripcion.md#página-7) | MonitorCasero y condiciones mediante semáforos privados |
| [10–15](practica-03-de-semaforos-a-monitores.transcripcion.md#página-10) | Barrera por fases, Hoare/Mesa y despertares espurios |
| [15–20](practica-03-de-semaforos-a-monitores.transcripcion.md#página-15) | CoDep: tickets, varias condiciones y notificaciones |
| [20–23](practica-03-de-semaforos-a-monitores.transcripcion.md#página-20) | Obtención conjunta de recursos (variante de fumadores) |
| [23–26](practica-03-de-semaforos-a-monitores.transcripcion.md#página-23) | Thread pool y manejo de fallos de tareas |
| [26–30](practica-03-de-semaforos-a-monitores.transcripcion.md#página-26) | Promesas, resultados, errores y callbacks |

## Núcleo

- En el puente, el primer auto de un sentido toma el recurso y el último lo libera. El molinete evita que siga creciendo un grupo mientras otro espera, pero el apunte muestra que un molinete débil permite adelantamientos indefinidos (pp. 1–3).
- La nota de p. 3 advierte que una versión de código usa `Semaphore(1)` aunque la justificación de fairness necesita la versión fuerte. No ocultar esa diferencia al reutilizarla.
- El paso de testigo ordena salidas con un semáforo privado por participante. La cola representa el orden; el permiso se entrega al destinatario concreto (pp. 3–6).
- MonitorCasero combina un mutex y una cola de semáforos privados. `esperar` registra al thread bajo exclusión, libera mutex, espera su permiso y readquiere mutex. `avisar` no entrega el mutex: implementa signal y continúa (pp. 8–10).
- La barrera reutilizable guarda `miFase`, cuenta llegadas y avanza `fase` al completar el grupo. Evita confundir rondas aunque los despertados retomen más tarde (pp. 10–13).

## Esperas y señalización

- Dos razones diferentes para revalidar: (1) en Mesa otro thread puede cambiar el estado antes de que el despertado readquiera el lock; (2) Java permite despertares sin notificación. Quitar (2) no elimina (1) (pp. 13–15).
- CoDep usa tickets para decidir quién sigue. Con una cola común, notify puede despertar a alguien cuyo predicado sigue falso; notifyAll permite que todos revaliden. Condiciones separadas permiten dirigir avisos según motivo de espera (pp. 16–20).
- La combinación de recursos debe comprobarse y reservarse bajo la misma exclusión; tomar componentes por separado puede impedir que alguien complete el conjunto (pp. 20–23).
- El monitor del pool sólo protege la cola: ejecutar la tarea fuera del lock permite trabajar a varios workers. Un aviso puede bastar cuando cualquier worker sirve para una tarea (pp. 23–25).
- Una promesa se completa una sola vez, con valor o error. Hay que avisar a todos los que esperan ese resultado; copiar los callbacks bajo lock y ejecutarlos fuera evita retener el monitor durante código ajeno (pp. 26–29).

## Precauciones del original

- La descripción inicial usa condiciones FIFO, pero los ejercicios pueden cambiar esa hipótesis. El modelo de parcial usa colas no fair.
- La afirmación informal de que `entonces` no bloquea debe leerse como «no espera a que la promesa se resuelva»: si ejecuta sincrónicamente un callback ya disponible, su retorno depende de ese callback (pp. 27–29).
- El worker captura Throwable, pero el wrapper de Callable de p. 29 captura Exception. No afirmar que cualquier Error deja la promesa resuelta sólo por existir un catch externo.
- Los ejemplos y simplificaciones se conservan; el resumen no certifica que el código sea una implementación de producción completa.
