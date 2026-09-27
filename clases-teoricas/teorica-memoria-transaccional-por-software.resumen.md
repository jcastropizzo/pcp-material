# Memoria transaccional por software y Haskell — resumen

- Fuente: [teorica-memoria-transaccional-por-software.pdf](teorica-memoria-transaccional-por-software.pdf) (83 páginas).
- [Transcripción completa](teorica-memoria-transaccional-por-software.transcripcion.md).
- Síntesis editorial basada en este PDF; no reemplaza los enunciados ni constituye una fe de erratas oficial.

## Mapa de consulta

| Páginas | Contenido |
| --- | --- |
| [2–9](teorica-memoria-transaccional-por-software.transcripcion.md#página-2) | Motivación, bloques atómicos, aislamiento y dificultades |
| [10–31](teorica-memoria-transaccional-por-software.transcripcion.md#página-10) | Efectos, tipos, Functor, Applicative, Monad y Maybe |
| [32–46](teorica-memoria-transaccional-por-software.transcripcion.md#página-32) | IO, do, return, lectura/escritura y combinadores |
| [47–53](teorica-memoria-transaccional-por-software.transcripcion.md#página-47) | IORef, carreras, forkIO y async |
| [54–63](teorica-memoria-transaccional-por-software.transcripcion.md#página-54) | STM, TVar, atomically y composición de transferencias |
| [64–70](teorica-memoria-transaccional-por-software.transcripcion.md#página-64) | retry, check, orElse y separación de IO |
| [71–83](teorica-memoria-transaccional-por-software.transcripcion.md#página-71) | Buffer, estructuras STM, barbero y fumadores |

## Núcleo de Haskell necesario

- Separar un valor puro `a` de una acción `IO a` o `STM a`. `<-` enlaza el resultado de una acción dentro de do; `let` define valores puros; `return` introduce un valor en el contexto y **no corta la ejecución** (pp. 31–46).
- `Functor` mapea una función; `Applicative` combina una función y un argumento en contexto; `Monad` permite que la acción siguiente dependa del resultado anterior. Maybe ilustra propagación de ausencia (pp. 19–30).
- `IORef` da estado mutable pero no vuelve atómica una secuencia leer/sumar/escribir. Crear un thread con `forkIO` no espera que termine; `async` y `wait` permiten sincronizar la finalización (pp. 47–53).

## Interfaz esencial

```haskell
newTVar   :: a -> STM (TVar a)
readTVar  :: TVar a -> STM a
writeTVar :: TVar a -> a -> STM ()
atomically :: STM a -> IO a
retry :: STM a
check :: Bool -> STM ()
```

- Una función de tipo `STM a` describe una computación transaccional componible. El límite de `atomically` determina qué conjunto de acciones se valida y se hace visible conjuntamente (pp. 56–63).
- En la transferencia, extraer y depositar se componen dentro de la misma transacción. Dos `atomically` separados no ofrecen esa atomicidad conjunta (pp. 60–63).
- `retry` descarta la tentativa y espera cambios en dependencias leídas. `check False = retry`; `check True` permite continuar. No es un bucle de consulta activa (pp. 64–67).
- `orElse a b` intenta a; si pide retry, prueba b descartando los efectos tentativos de a. Si ambas reintentan, espera cambios relevantes para las alternativas (pp. 68–69).
- El sistema de tipos separa STM de IO para evitar introducir directamente efectos externos que no podrían deshacerse durante un reintento (pp. 54–57, 70).

## Aplicaciones

- Buffer transaccional como `TVar [a]`: agregar modifica la lista; retirar de vacía ejecuta retry (pp. 71–73).
- TQueue es no acotada; TBQueue acotada; TMVar tiene lugar para un valor; TChan ofrece un canal; TArray es indexada (p. 74).
- Barbero: ocupación/cola se actualizan transaccionalmente, mientras el corte y las impresiones ocurren en IO. Fumadores: comprobar y retirar ambos ingredientes pertenece a una transacción (pp. 75–83).

## Precauciones del original

La p. 70 contiene «No es imposible inyectar...» aunque el ejemplo se presenta como error por mezclar IO y STM: se conserva en la transcripción y se marca aquí la contradicción. «Sin deadlocks» en la motivación se refiere al problema de adquirir locks explícitos; no es una promesa de terminación de cualquier programa que use retry ni de equidad. El ejemplo de contador no espera al fork antes de leer: atomicidad y finalización del thread son cuestiones distintas.
