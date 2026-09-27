# Práctica 1: semántica, exclusión mutua y memoria real — resumen

- Fuente: [practica-01-introduccion-semantica-y-java.pdf](practica-01-introduccion-semantica-y-java.pdf) (104 páginas).
- [Transcripción completa](practica-01-introduccion-semantica-y-java.transcripcion.md).
- Síntesis editorial basada en este PDF; no reemplaza los enunciados ni constituye una fe de erratas oficial.

## Mapa de consulta

| Páginas | Contenido |
| --- | --- |
| [1–9](practica-01-introduccion-semantica-y-java.transcripcion.md#página-1) | Organización de cursada; no usar como calendario actualizado |
| [10–24](practica-01-introduccion-semantica-y-java.transcripcion.md#página-10) | Estados, semántica, interleavings, grafos y trazas |
| [25–40](practica-01-introduccion-semantica-y-java.transcripcion.md#página-25) | Exclusión mutua, atomicidad, contraejemplos e invariantes |
| [41–61](practica-01-introduccion-semantica-y-java.transcripcion.md#página-41) | Procesos/threads, scheduling, afinidad y modelos N:1, 1:1, N:M |
| [62–85](practica-01-introduccion-semantica-y-java.transcripcion.md#página-62) | DekkerLitmus, consistencia secuencial, store buffers, TSO y fences |
| [86–93](practica-01-introduccion-semantica-y-java.transcripcion.md#página-86) | Modelos más débiles y primitivas de hardware |
| [94–103](practica-01-introduccion-semantica-y-java.transcripcion.md#página-94) | Compilador, Java Memory Model, DRF y volatile |
| [104](practica-01-introduccion-semantica-y-java.transcripcion.md#página-104) | Bibliografía |

## Núcleo

- La semántica secuencial transforma estados; una ejecución concurrente admite distintos resultados por entrelazados. El grafo debe conservar posiciones de control además de valores (pp. 10–24).
- Para negar una propiedad universal alcanza un contraejemplo válido. Para probarla, una traza favorable no basta: argumentar mediante invariantes o todas las ejecuciones pertinentes (p. 38).
- En los análisis de exclusión mutua se asume que la sección crítica termina, que la no crítica no manipula sus variables y que el scheduler es weakly fair. Separar mutex, ausencia de deadlock/livelock y garantía de entrada (p. 31).
- Concurrencia no exige ejecución simultánea. Los modelos de threads distinguen entidades manejadas por el runtime y por el sistema operativo; el mapeo cambia costos, bloqueo y paralelismo (pp. 41–61).

## Del modelo abstracto a Java/hardware

- Consistencia secuencial (SC): las operaciones pueden explicarse por un orden global compatible con el orden de cada thread (pp. 69–72).
- Un store buffer puede hacer que una escritura todavía no sea visible a otro core. Store-to-load forwarding permite al propio core leer su escritura pendiente (pp. 75–79).
- En el modelo TSO de la presentación se preservan LL, LS y SS, pero un load puede adelantarse a la visibilidad de un store previo a otra dirección. El ejemplo obtiene `r1 == 0 && r2 == 0` por stores pendientes en ambos cores (pp. 80–81).
- Un fence impone orden; una operación read-modify-write atómica aporta indivisibilidad. No son la misma necesidad (pp. 83–93).
- El compilador también puede reordenar. Java define un contrato propio; no alcanza razonar sólo sobre el CPU (pp. 94–98).
- **DRF ⇒ SC:** un programa correctamente sincronizado puede analizarse como secuencialmente consistente. `happens-before` expresa las relaciones relevantes (pp. 97–102).
- `volatile` aporta garantías de publicación/orden para sus accesos. No transforma `x++` en una operación atómica; práctica 2, p. 67 lo contrasta explícitamente.

## Uso al estudiar

Esta práctica es especialmente relevante para el inciso 1(c) del modelo: distinguir prueba abstracta, atomicidad de operaciones y garantías de lenguaje/arquitectura. Esa vinculación es una orientación editorial, no una predicción del parcial.

## Erratas observadas

La p. 13 escribe una suma con `x*x/2`, mientras el bucle y la p. 14 indican la suma descendente; no memorizar esa fórmula sin verificarla. En p. 85 «nunca al revés» no significa que ninguna ejecución TSO sea SC: la inclusión es propia, como indica el diagrama. Se conserva el texto original en la transcripción.
