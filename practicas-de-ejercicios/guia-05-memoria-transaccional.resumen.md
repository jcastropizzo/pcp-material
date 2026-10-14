# guia 05 memoria transaccional — resumen

Fuente: [guia-05-memoria-transaccional.pdf](guia-05-memoria-transaccional.pdf). [Transcripción completa](guia-05-memoria-transaccional.transcripcion.md).

Síntesis editorial para estudio; el original prevalece ante diferencias.

## Mapa de ejercicios
| Ej. | Problema |
|---|---|
|1|Buffer de una posición: operaciones no bloqueantes y bloqueantes; lectura sin extracción.|
|2|Semáforos STM y adquisición conjunta.|
|3|Bolsa de bolitas, generadores y extracción de pares.|
|4|Fumadores e ingredientes.|
|5|Pizzería y elección entre pedidos.|
|6|Pila transaccional.|
|7|Colas FIFO, transferencia y prioridades.|
|8|Lectores/escritores y límites de acceso.|
|9|Filósofos con recursos adquiridos transaccionalmente.|

## Núcleo para estudiar
Las primitivas de la estructura devuelven `STM`; quien las combina decide el límite de `atomically`. `retry` aborta los cambios tentativos y espera cambios en las variables leídas; no confirma registros previos. `orElse` prueba alternativas con preferencia por la izquierda, no es una elección justa.

## Decisiones que importan
Explicitar invariantes, variables observadas por cada espera y qué cambios se confirman juntos. Efectos externos y demoras van fuera de STM. Atomicidad y ausencia de espera circular no implican ausencia de starvation. Revisar las referencias cruzadas de los incisos del buffer contra el PDF.

## Soluciones separadas

[Desarrollo de los ejercicios](guia-05-memoria-transaccional_resuelto.md) — elaboración de estudio, no oficial.
