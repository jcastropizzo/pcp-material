# Guía 2: semáforos — resumen

- Fuente: [guia-02-semaforos.pdf](guia-02-semaforos.pdf) (4 páginas).
- [Transcripción completa](guia-02-semaforos.transcripcion.md).
- Síntesis editorial basada en este PDF; no reemplaza los enunciados ni constituye una fe de erratas oficial.

## Alcance y uso

Índice de todos los ejercicios, sin soluciones ni clasificación de dificultad inventada. Las descripciones permiten seleccionar; antes de resolver hay que leer el enunciado completo en la transcripción. La página indicada es la de inicio: algunos ejercicios continúan en la siguiente.

La guía declara `print` atómico. No fija una hipótesis universal de semáforos fuertes: revisar la consigna y la convención aplicable. El PDF conserva encabezado de primer cuatrimestre de 2026.

## Catálogo

| Ejercicio | Inicio | Tema | Lo que pide |
| --- | --- | --- | --- |
| 1 | [p. 1](guia-02-semaforos.transcripcion.md#página-1) | Precedencia | Imponer A antes que F y F antes que C. |
| 2 | [p. 1](guia-02-semaforos.transcripcion.md#página-1) | Salidas permitidas | Permitir exactamente ACERO y ACREO. |
| 3 | [p. 1](guia-02-semaforos.transcripcion.md#página-1) | Orden y sincronización de tres hilos | Obtener R I O OK OK OK. |
| 4 | [p. 1](guia-02-semaforos.transcripcion.md#página-1) | Restricciones de conteo | Mantener F <= A, H <= E y C <= G simultáneamente. |
| 5 | [p. 2](guia-02-semaforos.transcripcion.md#página-2) | Patrones A/B | Diferencia máxima 1, alternancia AB y patrón ABB; tres incisos. |
| 6 | [p. 2](guia-02-semaforos.transcripcion.md#página-2) | Generador/acumulador | Sumar impares, coordinar finalización e imprimir desde el generador; Java. |
| 7 | [p. 2](guia-02-semaforos.transcripcion.md#página-2) | Gimnasio | Modelar agentes/recursos; Java; exclusión, deadlock/livelock e inanición. |
| 8 | [p. 2](guia-02-semaforos.transcripcion.md#página-2) | Bolitas | Productores unitarios, consumidores de pares, capacidad ligada a generadores; al menos dos generadores. |
| 9 | [p. 3](guia-02-semaforos.transcripcion.md#página-3) | Transbordador | N pasajeros, costas y fases; descenso completo antes de subir o subida/bajada concurrentes. |
| 10 | [p. 3](guia-02-semaforos.transcripcion.md#página-3) | Planta de refinamiento | Máquinas, vehículos y rutas; sincronizar cargas/descargas sin serializar movimiento y procesamiento. |
| 11 | [p. 4](guia-02-semaforos.transcripcion.md#página-4) | Baño y limpieza | Capacidad 8; versiones con prioridades distintas y tratamiento de quienes esperan. |
| 12 | [p. 4](guia-02-semaforos.transcripcion.md#página-4) | Puente | Mismo sentido concurrente, capacidad máxima 3 e inanición. |

## Referencias de teoría

Teórica 2: pp. 9–54 (primitivas y garantías), 60–85 (buffers), 86–114 (recursos y prioridades). Práctica 2: pp. 13–38 (patrones y semáforos privados). Práctica 3: pp. 1–7 (puente y orden). Los ejercicios 7–12 contienen problemas de modelado completos; 1–5 trabajan restricciones más localizadas. Esta distinción describe las consignas, no predice dificultad de examen.
