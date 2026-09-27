# Pilas, colas, ABA y eliminación — resumen

- Fuente: [teorica-05-pilas-colas-y-problema-aba.pdf](teorica-05-pilas-colas-y-problema-aba.pdf) (118 páginas).
- [Transcripción completa](teorica-05-pilas-colas-y-problema-aba.transcripcion.md).
- Síntesis editorial basada en este PDF; no reemplaza los enunciados ni constituye una fe de erratas oficial.

## Mapa de consulta

| Páginas | Contenido |
| --- | --- |
| [2–6](teorica-05-pilas-colas-y-problema-aba.transcripcion.md#página-2) | Pools, operaciones parciales/totales, rendezvous y orden de elementos |
| [7–30](teorica-05-pilas-colas-y-problema-aba.transcripcion.md#página-7) | Cola acotada con dos locks, contador y estados intermedios |
| [31–34](teorica-05-pilas-colas-y-problema-aba.transcripcion.md#página-31) | Cola no acotada con locks separados |
| [35–52](teorica-05-pilas-colas-y-problema-aba.transcripcion.md#página-35) | Cola lock-free, ayuda a tail y linealización |
| [53–69](teorica-05-pilas-colas-y-problema-aba.transcripcion.md#página-53) | Reciclaje de memoria, ABA y referencias con versión |
| [70–81](teorica-05-pilas-colas-y-problema-aba.transcripcion.md#página-70) | Cola síncrona y estructuras duales |
| [82–95](teorica-05-pilas-colas-y-problema-aba.transcripcion.md#página-82) | Pila lock-free y backoff |
| [96–118](teorica-05-pilas-colas-y-problema-aba.transcripcion.md#página-96) | Eliminación, exchanger y pila con eliminación |

## Núcleo

- Un pool permite insertar y retirar elementos. Una operación parcial puede esperar por una condición; una total devuelve resultado/error sin esperar esa condición. «Total» aquí no significa automáticamente wait-free (pp. 2–5).
- FIFO/LIFO describen el orden de los elementos, no garantizan por sí solos justicia entre threads (p. 6).
- La cola acotada divide locks entre productores y consumidores. Las notificaciones cruzadas adquieren el lock correspondiente después de soltar el propio, y la espera revalida su condición (pp. 11–16).
- En esta implementación `enq` se hace efectivo al enlazar `tail.next`, antes de actualizar tail y size. Por eso size puede ser temporalmente negativo; no es un error de extracción ni una propiedad de cualquier cola (pp. 18–30).
- La cola no acotada con dos locks separa inserciones de extracciones; extraer vacía produce excepción. El análisis de inanición depende de locks fair (pp. 31–34).

## Cola lock-free y ABA

- `head` apunta al centinela. `tail` puede estar retrasado; si hay sucesor, otro thread ayuda a adelantarlo. `head == tail` solo no alcanza para declarar vacía: también se inspecciona next y se valida lo observado (pp. 35–50).
- Inserción exitosa: CAS sobre `last.next`. Extracción exitosa: CAS que avanza head. La operación fallida por cola vacía tiene su punto dentro de la observación validada del centinela (p. 51).
- Los fallos de CAS se relacionan con modificaciones de otros threads: hay progreso global, no una cota individual para cada participante (p. 52).
- **ABA:** observar la misma referencia no prueba que nada cambió si un nodo pudo retirarse, reciclarse y volver. Un CAS puede aceptar una observación obsoleta (pp. 53–61).
- `AtomicStampedReference` compara referencia y versión conjuntamente. La versión cambia con las modificaciones relevantes. No confundir sello de versión con el bit de borrado lógico de las listas (pp. 62–69).

## Otros diseños

- Cola síncrona: el productor espera hasta que un consumidor tome su elemento, y viceversa; es rendezvous, no un buffer de capacidad uno ordinario (pp. 70–77).
- Estructuras duales separan el registro del pedido y su satisfacción (pp. 78–81).
- Pila lock-free: push/pop intentan modificar la cima con CAS. Backoff reduce contención mediante esperas, sin convertir la garantía global en individual (pp. 82–95).
- Eliminación: un push y un pop compatibles pueden intercambiar el valor sin modificar la pila central. El exchanger y su protocolo de timeout distinguen encuentro exitoso de retiro de una oferta; el punto de linealización es el intercambio correspondiente (pp. 96–118).

## Precauciones

La p. 71 llama «wake-ups espúreos» a despertares por notificación común que pueden no servir al destinatario. Distinguirlos de un retorno de wait sin notificación (práctica 3, pp. 14–15). No suponer que añadir CAS a una parte vuelve correcta una mezcla de operaciones con locks y sin locks: deben compartir un protocolo compatible.
