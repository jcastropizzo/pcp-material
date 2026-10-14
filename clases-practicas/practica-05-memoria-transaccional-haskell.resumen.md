# practica 05 memoria transaccional haskell — resumen

Fuente: [practica-05-memoria-transaccional-haskell.pdf](practica-05-memoria-transaccional-haskell.pdf). [Transcripción completa](practica-05-memoria-transaccional-haskell.transcripcion.md).

Síntesis editorial para estudio; el original prevalece ante diferencias.

## Ubicación y numeración
Apunte de STM: el encabezado dice «Clase práctica 6». Se usa `practica-05` para ubicarlo entre estructuras concurrentes y la práctica de mensajes, manteniendo intacto el documento original.

## Mapa de lectura
- Páginas 1–4: entorno Haskell, paquetes, threads, acciones IO y composición.
- Páginas 5–8: `TVar`, `STM`, `atomically`, `retry`, `check`, `orElse`.
- Páginas 9–14: carrera de corredores, barrera de salida y espera de terminación.
- Páginas 15–21: lista ordenada transaccional, búsqueda, inserción, eliminación y composición.

## Núcleo
`STM a` describe una operación componible; `atomically` la lleva a IO con un límite transaccional. Las escrituras tentativas se descartan al reintentar. `retry` permite esperar una condición sobre estado transaccional leído. `orElse` agrega alternativas, no fairness.

## Ejemplo que merece atención
Registrar que un corredor está listo y esperar la señal de salida dentro de una misma transacción puede impedir que el organizador vea ese registro. Separar la confirmación del registro de la espera. En una lista, devolver operaciones STM permite agrupar varias modificaciones en una sola transacción; envolver cada una por separado en `atomically` cambia la garantía.
