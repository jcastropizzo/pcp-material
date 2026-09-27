# Modelo de parcial: formato, exigencias y mapa de temas — resumen

- Fuente: [modelo-de-parcial-2026-2c.pdf](modelo-de-parcial-2026-2c.pdf) (4 páginas).
- [Transcripción completa](modelo-de-parcial-2026-2c.transcripcion.md).
- Síntesis editorial basada en este PDF; no reemplaza los enunciados ni constituye una fe de erratas oficial.

## Naturaleza del documento

Modelo de evaluación de PCP, segundo cuatrimestre de 2026. Tiene **4 ejercicios en 4 páginas**. No es un examen histórico rendido. Este resumen no contiene soluciones ni afirma que el próximo parcial copie sus temas o ponderaciones.

## Instrucciones explícitas (p. 1)

Cada ejercicio se entrega en hojas separadas, numeradas e identificadas. El código/pseudocódigo y toda suposición deben justificarse. Las notas son I/A. El texto dice «al menos dos ejercicios calificados como bien y el restante al menos regular», y exige que alguno de los bien sea monitores o semáforos.

**Ambigüedad del original:** la regla usa «el restante» aunque el documento contiene cuatro ejercicios. No convertirla en una regla de aprobación para cuatro sin confirmación de la cátedra. No se especifica una duración en estas páginas.

## Ejercicios y demandas (sin resolución)

| Ejercicio | Páginas | Tema y trabajo exigido |
| --- | --- | --- |
| 1, a–c | [1–2](modelo-de-parcial-2026-2c.transcripcion.md#página-1) | Analizar un algoritmo de exclusión mutua para N threads: auxiliares no atómicas, luego atómicas y finalmente ejecución real en Java/arquitectura. Justificar propiedades y restricciones necesarias. |
| 2, a–c | [2](modelo-de-parcial-2026-2c.transcripcion.md#página-2) | Diseñar transferencias bancarias con semáforos, informe consistente, concurrencia entre cuentas disjuntas; analizar adelantamientos y extender a múltiples destinos. |
| 3, a–b | [2–3](modelo-de-parcial-2026-2c.transcripcion.md#página-2) | Asignación de mesas a grupos mediante monitor Mesa; garantizar servicio eventual; explicar adaptación a Hoare. |
| 4, a–e | [3–4](modelo-de-parcial-2026-2c.transcripcion.md#página-3) | Contador distribuido: granularidad fina, linealizabilidad/progreso, incremento con CAS, lectura por doble recorrido y wait-freedom. |
| Extra de 4 | [4](modelo-de-parcial-2026-2c.transcripcion.md#página-4) | Analizar mezcla de lectura con locks e incrementos CAS. El documento lo declara fuera del parcial. |

## Restricciones que no se pueden perder

- **1:** comparar por separado las tres hipótesis de ejecución; el inciso de Java pide nivel lenguaje y nivel arquitectura.
- **2:** transferencias independientes llegan sin control centralizado; cuentas disjuntas deben poder operar a la vez; límite de descubierto `-L`; se descarta lo que no cumple el saldo. El informe debe corresponder a un estado real y un informe que aún no empezó no debe demorar transferencias. Se asumen semáforos débiles salvo indicación contraria; las primitivas permitidas se especifican como constructor/acquire/release.
- **3:** grupos indivisibles, mesas de distintas capacidades, cada grupo ocupa toda su mesa. Existe una mesa compatible para cada grupo; quien se sienta se va eventualmente. Se piden Mesa, colas de condición **no fair** y **sin spurious wakeups**. Servicio eventual es una condición explícita adicional a no deadlock/no races.
- **4:** incrementar una celda atómicamente no resuelve por sí solo la especificación de lectura del total. El inciso (c) permite dejar get fuera del análisis de esa implementación, pero (d) y (e) lo vuelven a introducir. Mantener las variantes separadas.

## Cómo calibrar simulacros nuevos (orientación editorial)

Seleccionar problemas que requieran construcción o contraejemplos **y justificación**, no sólo recordar una definición. Buscar cobertura de: atomicidad y memoria; sincronización con varias restricciones; monitores con progreso individual; objetos concurrentes y garantías de progreso. Las guías permiten aproximar esas demandas, pero un ejercicio con el mismo tema no tiene automáticamente complejidad equivalente. Identificar siempre fuente e incisos, y no incluir el extra como obligatorio.
