# Guía 3: monitores — resumen

- Fuente: [guia-03-monitores.pdf](guia-03-monitores.pdf) (4 páginas).
- [Transcripción completa](guia-03-monitores.transcripcion.md).
- Síntesis editorial basada en este PDF; no reemplaza los enunciados ni constituye una fe de erratas oficial.

## Alcance y uso

Índice de todos los ejercicios, sin soluciones ni clasificación de dificultad inventada. Las descripciones permiten seleccionar; antes de resolver hay que leer el enunciado completo en la transcripción. La página indicada es la de inicio: algunos ejercicios continúan en la siguiente.

La semántica de signal importa y varía según el ejercicio. No adoptar automáticamente FIFO ni Hoare; el ejercicio 5 pide explícitamente signal y continúa.

## Catálogo

| Ejercicio | Inicio | Tema | Lo que pide |
| --- | --- | --- | --- |
| 1 | [p. 1](guia-03-monitores.transcripcion.md#página-1) | Semántica de signal | Contraejemplo al orden antes/importante/después; Hoare y Mesa. |
| 2 | [p. 1](guia-03-monitores.transcripcion.md#página-1) | Secuenciador ternario | Repetir primero, segundo, tercero en orden cíclico. |
| 3 | [p. 1](guia-03-monitores.transcripcion.md#página-1) | Barrera | Versión de único uso y versión reutilizable para N threads. |
| 4 | [p. 2](guia-03-monitores.transcripcion.md#página-2) | Atrapador | Liberar N esperas juntas; liberador no bloqueante o bloqueante; adelantamientos. |
| 5 | [p. 2](guia-03-monitores.transcripcion.md#página-2) | Peluquería | Clientes/barberos, inicio y fin; Mesa con varias condiciones y luego una sola. |
| 6 | [p. 2](guia-03-monitores.transcripcion.md#página-2) | Conferencia | Capacidad, admisión, inicio y salida por fases; variantes de charlas/oradores. |
| 7 | [p. 3](guia-03-monitores.transcripcion.md#página-3) | Apuestas | No apostar dos veces seguidas, cierre al acertar y terminación de participantes. |
| 8 | [p. 3](guia-03-monitores.transcripcion.md#página-3) | Pizzas | Preferir una grande; en su defecto dos chicas; competencia sin cola de clientes. |
| 9 | [p. 3](guia-03-monitores.transcripcion.md#página-3) | Transbordador autorizado | Capacidad, costas, autorización antes/después de llenarse y variante de orden de llegada. |
| 10 | [p. 4](guia-03-monitores.transcripcion.md#página-4) | Asignación de recursos | Cola de recursos en Java; pedidos múltiples y evitar perjudicar pedidos grandes. |

## Referencias de teoría

Teórica 3: pp. 45–64 (Hoare/Mesa y fairness), 65–86 (buffers, condiciones y Java). Práctica 3: pp. 10–15 (barrera por fases), 15–20 (tickets/condiciones), 20–23 (recursos conjuntos). No resolver usando sólo un conteo si la consigna exige identificar turnos, rondas o destinatarios.
