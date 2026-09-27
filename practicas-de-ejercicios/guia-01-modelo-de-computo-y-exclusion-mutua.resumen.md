# Guía 1: modelo de cómputo y exclusión mutua — resumen

- Fuente: [guia-01-modelo-de-computo-y-exclusion-mutua.pdf](guia-01-modelo-de-computo-y-exclusion-mutua.pdf) (7 páginas).
- [Transcripción completa](guia-01-modelo-de-computo-y-exclusion-mutua.transcripcion.md).
- Síntesis editorial basada en este PDF; no reemplaza los enunciados ni constituye una fe de erratas oficial.

## Alcance y uso

Índice de todos los ejercicios, sin soluciones ni clasificación de dificultad inventada. Las descripciones permiten seleccionar; antes de resolver hay que leer el enunciado completo en la transcripción. La página indicada es la de inicio: algunos ejercicios continúan en la siguiente.

El ejercicio 1 explicita unidades atómicas. Otros incisos cambian la atomicidad de funciones auxiliares: mantener separadas las variantes. El PDF conserva encabezado de primer cuatrimestre de 2026.

## Catálogo

| Ejercicio | Inicio | Tema | Lo que pide |
| --- | --- | --- | --- |
| 1 | [p. 1](guia-01-modelo-de-computo-y-exclusion-mutua.transcripcion.md#página-1) | Semántica y grafos | Tres programas; semántica de cada hilo y composición, grafo completo con atomicidad indicada. |
| 2 | [p. 1](guia-01-modelo-de-computo-y-exclusion-mutua.transcripcion.md#página-1) | Incrementos concurrentes | Construir trazas con resultados 2K y K; analizar si es posible menos de K. |
| 3 | [p. 2](guia-01-modelo-de-computo-y-exclusion-mutua.transcripcion.md#página-2) | Estados finales | Enumerar resultados posibles de asignaciones entre tres variables/hilos. |
| 4 | [p. 2](guia-01-modelo-de-computo-y-exclusion-mutua.transcripcion.md#página-2) | Tamaño del espacio de estados | Cota de nodos y número de trazas para N threads de K acciones sin bucles. |
| 5 | [p. 2](guia-01-modelo-de-computo-y-exclusion-mutua.transcripcion.md#página-2) | Búsqueda de una raíz | Comparar tres programas y justificar si ambos threads terminan al encontrarse una raíz. |
| 6 | [p. 3](guia-01-modelo-de-computo-y-exclusion-mutua.transcripcion.md#página-3) | Condición de bucle y salida | Posibles cantidades de impresiones y salida mínima con un contador concurrente. |
| 7 | [p. 3](guia-01-modelo-de-computo-y-exclusion-mutua.transcripcion.md#página-3) | Interleavings y terminación | Ejecución con una iteración de T1 y posibilidad de ejecución infinita. |
| 8 | [p. 3](guia-01-modelo-de-computo-y-exclusion-mutua.transcripcion.md#página-3) | Bandera y terminación | Estados finales y posible no terminación con alternancia de n. |
| 9 | [p. 4](guia-01-modelo-de-computo-y-exclusion-mutua.transcripcion.md#página-4) | Bakery para dos threads | Evaluar el protocolo dado y justificar exclusión mutua. |
| 10 | [p. 4](guia-01-modelo-de-computo-y-exclusion-mutua.transcripcion.md#página-4) | Protocolo de turnos | Propiedades violadas y trazas; repetir análisis con auxiliares atómicas. |
| 11 | [p. 5](guia-01-modelo-de-computo-y-exclusion-mutua.transcripcion.md#página-5) | Peterson extendido a N | Determinar qué requisitos se cumplen o fallan para N > 2. |
| 12 | [p. 5](guia-01-modelo-de-computo-y-exclusion-mutua.transcripcion.md#página-5) | Consulta de banderas | Analizar algunVerdadero no atómica y luego atómica. |
| 13 | [p. 6](guia-01-modelo-de-computo-y-exclusion-mutua.transcripcion.md#página-6) | Bakery modificado | Analizar eliminación del desempate, propiedades y contraejemplos con el código dado. |
| 14 | [p. 6](guia-01-modelo-de-computo-y-exclusion-mutua.transcripcion.md#página-6) | Fetch-and-add y tickets | Explicar falla, modificar implementación y justificar corrección. |
| 15 | [p. 7](guia-01-modelo-de-computo-y-exclusion-mutua.transcripcion.md#página-7) | Atomicidad de tomarFlag | Comparar dos threads con auxiliar no atómica y atómica. |

## Referencias de teoría

Teórica 1: pp. 32–58 (modelo, atomicidad, fairness), 59–90 (mutex y algoritmos). Práctica 1: pp. 25–40 (análisis) y 69–103 (memoria real). El ejercicio 13 debe leerse con su pseudocódigo exacto: no sustituirlo por una variante estándar recordada de Bakery.
