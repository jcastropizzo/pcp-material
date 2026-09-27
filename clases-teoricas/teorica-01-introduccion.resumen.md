# Introducción: modelo de cómputo y exclusión mutua — resumen

- Fuente: [teorica-01-introduccion.pdf](teorica-01-introduccion.pdf) (90 páginas).
- [Transcripción completa](teorica-01-introduccion.transcripcion.md).
- Síntesis editorial basada en este PDF; no reemplaza los enunciados ni constituye una fe de erratas oficial.

## Mapa de consulta

| Páginas | Contenido |
| --- | --- |
| [4–14](teorica-01-introduccion.transcripcion.md#página-4) | IMP secuencial, semántica denotacional, equivalencia y contextos |
| [15–29](teorica-01-introduccion.transcripcion.md#página-15) | Concurrencia, entrelazados, no determinismo y límites de describir sólo resultados |
| [30–45](teorica-01-introduccion.transcripcion.md#página-30) | Interacción, atomicidad, pseudocódigo y grafos de transición |
| [46–58](teorica-01-introduccion.transcripcion.md#página-46) | Safety, liveness, fairness y ejecuciones infinitas |
| [59–80](teorica-01-introduccion.transcripcion.md#página-59) | Exclusión mutua: requisitos e intentos fallidos |
| [81–90](teorica-01-introduccion.transcripcion.md#página-81) | Dekker, Peterson, Bakery y primitivas atómicas de hardware |

## Núcleo

- Un estado asigna valores a variables. En el enfoque secuencial, un comando denota una función parcial entre estados: puede no terminar. La composición `C1; C2` aplica primero C1 y después C2 (pp. 6–10).
- El paralelismo de comandos permite entrelazados compatibles con el orden de cada thread. Tener las mismas transformaciones finales no basta para intercambiar componentes concurrentes: el entorno puede observar estados intermedios (pp. 15–29).
- Una acción atómica no presenta estados intermedios observables por otras acciones del modelo. Una línea de código no es necesariamente una acción atómica. Los ejemplos distinguen incremento completo de lectura en temporal y escritura posterior (pp. 32–45).
- Un estado de ejecución incluye memoria y posiciones de control. El grafo explora alternativas; una traza es un camino. No basta exhibir una ejecución favorable para probar corrección.
- **Safety:** nada malo ocurre. **Liveness:** el progreso exigido ocurre eventualmente. La corrección requiere las dos dimensiones (pp. 46–50).
- **Fairness débil:** una instrucción continuamente habilitada acaba ejecutándose. Es una hipótesis de las ejecuciones admitidas, no una garantía FIFO de los mecanismos de sincronización (pp. 51–55).
- El problema de exclusión mutua requiere: a lo sumo un thread en sección crítica, progreso de alguno si hay competidores y entrada eventual de cada competidor. No son la misma propiedad (p. 61).

## Algoritmos y operaciones

- Las tentativas con banderas o turno aislados sirven para construir contraejemplos; no copiar una tentativa como solución (pp. 63–80).
- Dekker y Peterson combinan banderas y desempate para dos procesos, bajo el modelo de la clase. Bakery extiende el protocolo mediante números y desempate por identificador (pp. 81–84).
- `test-and-set`, `exchange`, CAS y `fetch-and-add` son primitivas atómicas distintas. En esta presentación CAS devuelve el valor anterior; otras APIs pueden devolver un booleano (pp. 86–89).
- El ticket se obtiene incrementando `ticket`; al salir avanza `turno`. Todos estos esquemas usan espera activa (pp. 89–90).

## Precauciones para el tutor

No transportar una prueba bajo el modelo abstracto directamente a Java/hardware real: consultar práctica 1, pp. 69–103. La ausencia de inanición depende de las hipótesis de progreso y finalización de la sección crítica. Para fórmulas denotacionales consultar las imágenes de pp. 7–8: la extracción representa algunos corchetes semánticos como `J...K` y la actualización de estados como `7→`.
