# Guía 4: estructuras de datos y lock-free — resumen

- Fuente: [guia-04-estructuras-de-datos-y-lock-free.pdf](guia-04-estructuras-de-datos-y-lock-free.pdf) (4 páginas).
- [Transcripción completa](guia-04-estructuras-de-datos-y-lock-free.transcripcion.md).
- Síntesis editorial basada en este PDF; no reemplaza los enunciados ni constituye una fe de erratas oficial.

## Alcance y uso

Índice de todos los ejercicios, sin soluciones ni clasificación de dificultad inventada. Las descripciones permiten seleccionar; antes de resolver hay que leer el enunciado completo en la transcripción. La página indicada es la de inicio: algunos ejercicios continúan en la siguiente.

Varios ejercicios dependen de implementaciones vistas en clase. Consultar teoría de conjuntos/listas y de pilas/colas junto con el enunciado, en lugar de elegir cualquier algoritmo conocido.

## Catálogo

| Ejercicio | Inicio | Tema | Lo que pide |
| --- | --- | --- | --- |
| 1 | [p. 1](guia-04-estructuras-de-datos-y-lock-free.transcripcion.md#página-1) | Colisiones de hash | Cambios en las implementaciones de listas si las claves no son únicas. |
| 2 | [p. 1](guia-04-estructuras-de-datos-y-lock-free.transcripcion.md#página-1) | Granularidad fina: Figura | Concurrencia entre posición y tamaño; prevención de espera circular al combinar operaciones. |
| 3 | [p. 1](guia-04-estructuras-de-datos-y-lock-free.transcripcion.md#página-1) | Granularidad fina: array | assign y swap sobre posiciones distintas; analizar inanición. |
| 4 | [p. 2](guia-04-estructuras-de-datos-y-lock-free.transcripcion.md#página-2) | Lista optimista | Escenario de inanición y cambio de orden de locks en add. |
| 5 | [p. 2](guia-04-estructuras-de-datos-y-lock-free.transcripcion.md#página-2) | Actualización optimista de tabla | Sacar cálculo costoso del bloqueo y justificar progreso. |
| 6 | [p. 3](guia-04-estructuras-de-datos-y-lock-free.transcripcion.md#página-3) | Cola con dos pilas | Exhibir falla concurrente y corregir preservando concurrencia cuando out no está vacía. |
| 7 | [p. 3](guia-04-estructuras-de-datos-y-lock-free.transcripcion.md#página-3) | Contadores de cola acotada | Separar incrementos/decrementos para reducir sincronización sobre size. |
| 8 | [p. 3](guia-04-estructuras-de-datos-y-lock-free.transcripcion.md#página-3) | Pila acotada | Diseñar implementación; el enunciado no agrega restricciones detalladas. |
| 9 | [p. 3](guia-04-estructuras-de-datos-y-lock-free.transcripcion.md#página-3) | ABA en pila | Exhibir problema sin GC y modificar la solución. |
| 10 | [p. 4](guia-04-estructuras-de-datos-y-lock-free.transcripcion.md#página-4) | Contador lock-free | inc, get y reset; clasificar operaciones wait-free. |
| 11 | [p. 4](guia-04-estructuras-de-datos-y-lock-free.transcripcion.md#página-4) | Figura sin locks | Reformular las operaciones del ejercicio 2 sin locks. |

## Referencias de teoría

Teórica 4: pp. 26–29 (progreso), 41–104 (granularidades, optimista/lazy), 105–131 (CAS). Teórica 5: pp. 7–34 (colas con locks), 53–69 (ABA), 82–95 (pila lock-free). Ejercicios 2/11 forman una pareja con distintos mecanismos; 10 se relaciona temáticamente con el contador del modelo, pero no es el mismo problema.
