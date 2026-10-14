# parcial 01 2026 2c — resumen

Fuente: [parcial-01-2026-2c.pdf](parcial-01-2026-2c.pdf). [Transcripción completa](parcial-01-2026-2c.transcripcion.md).

Síntesis editorial para estudio; el original prevalece ante diferencias.

## Identificación
Primer parcial real de 2026-2C: conservar separado del modelo y de las guías. Este resumen cataloga preguntas; no es una corrección oficial.

## Mapa de ejercicios
1. Exclusión mutua con turnos en arreglo circular: operaciones atómicas/no atómicas, cantidad de participantes y garantías.
2. Telecabinas: capacidad, estaciones, orden de circulación y sincronización con semáforos.
3. Victoria y colaboradores: monitor, composición de piezas y condiciones de espera/despertar.
4. Cuenta con depósitos y extracciones separados: granularidad, consulta de saldo, linearizabilidad y alternativa con operaciones atómicas.

## Uso para estudiar
Practicar trazas completas, invariantes y justificaciones aplicadas al código. En particular, un saldo calculado leyendo dos componentes por separado necesita una prueba de snapshot consistente: que cada lectura sea atómica no alcanza. Explicitar las hipótesis de memoria y progreso de cada inciso. No extrapolar la dificultad o los temas de este parcial a una evaluación posterior sin su programa.
