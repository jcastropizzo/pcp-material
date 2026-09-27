# Práctica 4: listas y skip lists concurrentes — resumen

- Fuente: [practica-04-listas-y-skip-lists-concurrentes.pdf](practica-04-listas-y-skip-lists-concurrentes.pdf) (45 páginas).
- [Transcripción completa](practica-04-listas-y-skip-lists-concurrentes.transcripcion.md).
- Síntesis editorial basada en este PDF; no reemplaza los enunciados ni constituye una fe de erratas oficial.

## Mapa de consulta

| Páginas | Contenido |
| --- | --- |
| [2–12](practica-04-listas-y-skip-lists-concurrentes.transcripcion.md#página-2) | Correctitud/progreso y repaso de listas |
| [13–22](practica-04-listas-y-skip-lists-concurrentes.transcripcion.md#página-13) | Motivación, niveles, búsqueda y representación de skip lists |
| [23–33](practica-04-listas-y-skip-lists-concurrentes.transcripcion.md#página-23) | LazySkipList: find, contains, add y remove |
| [34–45](practica-04-listas-y-skip-lists-concurrentes.transcripcion.md#página-34) | LockFreeSkipList: representación, operaciones y ayuda |

## Núcleo

- Separar linealizabilidad de las garantías de progreso. Wait-free es individual; lock-free permite que una llamada pierda indefinidamente mientras otras terminan (pp. 3–4).
- Granulado grueso serializa la estructura; fino usa locks por nodo; optimista busca antes de bloquear y valida; lazy agrega marca de borrado; lock-free combina referencia/marca mediante CAS (pp. 5–12).
- La variante con una versión global permite validar una búsqueda al tomar el lock, pero cualquier modificación cambia la versión, incluso lejos de la zona buscada (pp. 10–11).
- Una skip list usa niveles superiores como atajos. Cada nodo tiene referencias por nivel. En la representación ideal, el nivel superior es subconjunto del inferior; la búsqueda baja cuando seguir avanzando sobrepasaría la clave (pp. 13–21).
- Se asumen claves hash sin colisiones en la representación presentada. No trasladar esa suposición a un ejercicio que explícitamente la quite (p. 16).

## LazySkipList

- `find` recorre sin locks y construye arrays de predecesores/sucesores por nivel (pp. 25–26).
- Un nodo representa un elemento presente cuando está completamente enlazado (`fullyLinked`) y no marcado. `contains` no toma locks ni reinicia la búsqueda (pp. 27–28).
- `add` adquiere locks de predecesores y valida que no estén marcados y mantengan los enlaces esperados; luego enlaza y publica `fullyLinked` (pp. 29–30).
- `remove` marca al nodo y después lo desenlaza de arriba hacia abajo, validando los predecesores correspondientes (pp. 31–32).

## LockFreeSkipList

- Usa referencias marcables y CAS, sin fullyLinked. La pertenencia al conjunto la determina estar alcanzable y no marcado en el nivel 0 (p. 34).
- `add` inserta primero en el nivel 0 y luego trabaja en niveles superiores; `remove` marca primero arriba y termina marcando el nivel 0 (pp. 37–40).
- `find` ayuda a eliminar físicamente los nodos marcados. `contains` consulta sin hacer esa limpieza (pp. 41–44).
- No usar «no tiene locks» como prueba suficiente de wait-freedom: revisar los recorridos y reintentos de cada operación.

## Detalles para verificar

La p. 16 enumera los centinelas con MAX/MIN en un orden confuso; los constructores de pp. 24 y 36 muestran head con MIN_VALUE y tail con MAX_VALUE, consistente con los diagramas. En el constructor centinela de p. 36 aparece `key = key`; no copiarlo como si asignara `this.key`. La fórmula verbal de alturas aleatorias de p. 19 debe distinguir probabilidad de alcanzar un nivel de probabilidad de altura exacta; no inventar esa precisión dentro de la transcripción.
