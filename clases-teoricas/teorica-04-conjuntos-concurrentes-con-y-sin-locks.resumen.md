# Conjuntos concurrentes: correctitud, progreso y listas — resumen

- Fuente: [teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf](teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf) (131 páginas).
- [Transcripción completa](teorica-04-conjuntos-concurrentes-con-y-sin-locks.transcripcion.md).
- Síntesis editorial basada en este PDF; no reemplaza los enunciados ni constituye una fe de erratas oficial.

## Mapa de consulta

| Páginas | Contenido |
| --- | --- |
| [2–25](teorica-04-conjuntos-concurrentes-con-y-sin-locks.transcripcion.md#página-2) | Historias, especificación, consistencia secuencial y linealizabilidad |
| [26–29](teorica-04-conjuntos-concurrentes-con-y-sin-locks.transcripcion.md#página-26) | Progreso bloqueante y no bloqueante |
| [30–40](teorica-04-conjuntos-concurrentes-con-y-sin-locks.transcripcion.md#página-30) | Lista ordenada y lock global |
| [41–72](teorica-04-conjuntos-concurrentes-con-y-sin-locks.transcripcion.md#página-41) | Granularidad fina, lock coupling, contraejemplos y pruebas |
| [73–87](teorica-04-conjuntos-concurrentes-con-y-sin-locks.transcripcion.md#página-73) | Sincronización optimista y validación |
| [88–104](teorica-04-conjuntos-concurrentes-con-y-sin-locks.transcripcion.md#página-88) | Lista lazy, borrado lógico/físico y contains |
| [105–131](teorica-04-conjuntos-concurrentes-con-y-sin-locks.transcripcion.md#página-105) | Lista lock-free, referencias marcadas, ayuda y CAS |

## Correctitud y progreso

- Consistencia secuencial: existe un orden legal que respeta el orden de cada thread; no exige respetar todo orden temporal entre threads (pp. 10–20).
- Linealizabilidad: cada llamada parece actuar en un instante entre invocación y respuesta y respeta precedencias de tiempo real. Implica consistencia secuencial; la recíproca no vale (pp. 24–25).
- No confundir correctitud con progreso: una estructura puede preservar resultados pero impedir que una operación termine.
- `wait-free` garantiza progreso individual en pasos propios, independiente de los demás; `lock-free` garantiza progreso global y admite inanición individual. En las garantías bloqueantes también intervienen el scheduler y la liberación de locks (pp. 26–29).

## Comparación de implementaciones

| Diseño | Mecanismo | Obligación al razonar |
| --- | --- | --- |
| Grueso | Un lock para el conjunto | Exclusión de todas las operaciones; fairness del lock para entrada individual |
| Fino | Locks de nodos; tomar el siguiente antes de soltar el anterior | Mantener ventana pred/curr y orden común de adquisición |
| Optimista | Buscar sin locks, bloquear y validar | Alcanzabilidad de pred y adyacencia pred.next == curr; reintentar si falló |
| Lazy | Marcar borrado lógico y luego desenlazar | Validar nodos no marcados y adyacencia; distinguir pertenencia lógica de alcance físico |
| Lock-free | CAS sobre referencia y marca; ayuda al borrar | Validar lo observado, no insertar detrás de un nodo eliminado y justificar progreso por interferencias exitosas |

## Detalles que ahorran errores

- Un orden común de locks evita ciclos; por sí solo no demuestra ausencia de inanición. La lista fina analiza locks fair (pp. 69–70).
- En la lista optimista, tomar los locks después de buscar no hace válida la búsqueda previa: hay que revalidar. Incluso locks fair no evitan reintentos indefinidos (pp. 80–87).
- Validación lazy: `!pred.marked && !curr.marked && pred.next == curr`. El borrado lógico precede al físico (pp. 90–91).
- `contains` lazy puede recorrer nodos desconectados. El punto de linealización de una respuesta falsa requiere mirar la historia; no siempre coincide con una instrucción propia fija (pp. 96–103).
- En la lista lock-free, el CAS trata referencia y marca como una unidad. `find` coopera desenlazando nodos marcados (pp. 115–125).
- `add` exitoso se linealiza al enlazar; `remove` exitoso, al marcar lógicamente. Los fracasos y búsquedas requieren analizar lo observado y su validación (pp. 129–131).

## Alcance

Las garantías de `contains` y las pruebas de recorrido corresponden a estas representaciones, claves y supuestos; no concluir «todo recorrido sin locks es wait-free». Para reproducir una implementación usar el código completo de la transcripción, no esta tabla.
