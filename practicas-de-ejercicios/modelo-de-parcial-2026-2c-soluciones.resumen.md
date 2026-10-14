# modelo de parcial 2026 2c soluciones — resumen

Fuente: [modelo-de-parcial-2026-2c-soluciones.pdf](modelo-de-parcial-2026-2c-soluciones.pdf). [Transcripción completa](modelo-de-parcial-2026-2c-soluciones.transcripcion.md).

Síntesis editorial para estudio; el original prevalece ante diferencias.

## Identificación
PDF aportado con enunciados y soluciones del examen simulacro. No es una resolución nueva generada en este procesamiento. Se mantiene separado del modelo sin respuestas.

## Mapa de lectura
- Páginas 1–2: exclusión mutua, atomicidad y turnos.
- Páginas 3–5: transferencias bancarias, exclusión por cuenta, coordinación global y orden de locks.
- Páginas 6–9: monitor de mesas, orden de atención, condiciones compartidas/privadas y despertares.
- Páginas 10–12: contador distribuido, consultas, CAS y garantías de progreso.

## Ideas de mayor utilidad
Justificar por qué una traza viola la propiedad buscada; establecer un orden global al tomar varios locks; distinguir una condition no fair de una cola FIFO implementada explícitamente; distinguir `signalAll` necesario en una condición compartida de señales dirigidas a condiciones privadas. Para el contador, no confundir lecturas individualmente atómicas con una lectura global linearizable.

## Precaución
Las soluciones dependen de los supuestos del enunciado. Consultar el desarrollo completo para sus hipótesis; este resumen no certifica automáticamente todas las implementaciones ni reemplaza una prueba de linearizabilidad.
