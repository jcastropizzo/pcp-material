# Cine York — solución STM

Fuente: [transcripción](guia-05a-stm-reservas-cine-york.transcripcion.md). Solución elaborada, no oficial.

## Interpretación y decisiones

El tamaño `k` es la cantidad de **personas del grupo**, no la cantidad de grupos; la última frase del PDF es ambigua al respecto. Se exige `1 ≤ k ≤ capacidad total`. El orden dentro de una fila será de izquierda a derecha y el de filas es el dado por `Sala`. Primero se busca cualquier bloque contiguo de k en una fila; solo si no existe, se toman las primeras k libres en orden global.

R3 exige especial cuidado. Una implementación que hace `retry` después de leer todas las butacas suscribe al grupo a cambios de toda la sala. Aquí cada grupo pendiente espera **solo en una variable de respuesta privada**. Quien libera butacas también asigna lugares a los pendientes que ahora pueden entrar. Así el grupo recibe una reserva ya realizada y nunca se despierta para comprobar nuevamente una sala que sigue sin alcanzarle.

Esta garantía se refiere a los despertares de la espera de aplicación. Ningún programa sobre STM general puede garantizar literalmente cero reintentos internos por conflictos del runtime durante cualquier transacción. Tampoco se modelan cancelaciones, fallas de threads o llamadas duplicadas a liberar. Para soportarlas habría que agregar identificación de reserva y cancelación de pedidos pendientes.

## Implementación

```haskell
import Control.Concurrent.STM
import Control.Exception (bracket)
import Control.Monad (replicateM, forM, forM_, when)
import Data.List (tails)

type Butaca = TVar Bool
type Fila = [Butaca]
type Sala = [Fila]
type Entrada = TVar (Maybe [Butaca])
data Pedido = Pedido Int Entrada
data Cine = Cine Sala (TVar [Pedido])

crearCine :: [Int] -> IO Cine
crearCine largos
  | null largos || any (<=0) largos =
      ioError (userError "La sala debe tener filas no vacías")
  | otherwise = atomically $ do
      sala <- mapM (\n -> replicateM n (newTVar False)) largos
      cola <- newTVar []
      return (Cine sala cola)

primero :: [a] -> Maybe a
primero [] = Nothing
primero (x:_) = Just x

elegir :: Int -> Sala -> STM (Maybe [Butaca])
elegir k sala = do
  foto <- forM sala $ \fila -> forM fila $ \b -> do
    ocupado <- readTVar b
    return (b, ocupado)
  let bloques =
        [map fst bloque | fila <- foto, sufijo <- tails fila,
          let bloque = take k sufijo,
          length bloque == k, all (not . snd) bloque]
      libres = [b | fila <- foto, (b, ocupado) <- fila, not ocupado]
  return $ case primero bloques of
    Just bs -> Just bs
    Nothing -> if length libres >= k then Just (take k libres)
               else Nothing

asignar :: Entrada -> [Butaca] -> STM ()
asignar respuesta bs = do
  forM_ bs $ \b -> writeTVar b True
  writeTVar respuesta (Just bs)

registrar :: Int -> Cine -> STM Entrada
registrar k (Cine sala cola) = do
  respuesta <- newTVar Nothing
  opcion <- elegir k sala
  case opcion of
    Just bs -> asignar respuesta bs
    Nothing -> modifyTVar' cola (++ [Pedido k respuesta])
  return respuesta

reservar :: Int -> Cine -> IO [Butaca]
reservar k cine@(Cine sala _)
  | k <= 0 || k > sum (map length sala) =
      ioError (userError "Tamaño de grupo imposible")
  | otherwise = do
      respuesta <- atomically (registrar k cine)
      atomically $ readTVar respuesta >>= maybe retry return

-- Se invoca dentro de la misma transacción que libera los lugares.
atender :: Sala -> [Pedido] -> STM [Pedido]
atender _ [] = return []
atender sala (p@(Pedido k respuesta):ps) = do
  opcion <- elegir k sala
  case opcion of
    Just bs -> asignar respuesta bs >> atender sala ps
    Nothing -> (p:) <$> atender sala ps

liberar :: Cine -> [Butaca] -> IO ()
liberar (Cine sala cola) bs = atomically $ do
  forM_ bs $ \b -> writeTVar b False
  pendientes <- readTVar cola
  restantes <- atender sala pendientes
  writeTVar cola restantes

grupo :: Cine -> Int -> IO () -> IO ()
grupo cine k verPelicula = bracket
  (reservar k cine)
  (liberar cine)
  (\_ -> verPelicula)
```

Sistema: crear `cine <- crearCine [8,8,6]`, lanzar un thread por grupo con `grupo cine k verPelicula`, y esperar sus terminaciones desde el programa principal. La acción `verPelicula` debe terminar para que ese grupo libere sus butacas. `bracket` protege la liberación después de obtener la reserva; se mantiene el supuesto de no cancelar solicitudes mientras esperan (su cancelación exige retirar el pedido o devolver una asignación concurrente).

## Justificación de R1

Elegir y marcar butacas es una única transacción. Dos reservas que pretenden la misma butaca entran en conflicto y no pueden publicar ambas la misma selección. La selección contiene exactamente k referencias diferentes. Cada butaca pertenece una sola vez a la sala, y cada grupo libera exactamente las referencias de su reserva. El commit de asignación es el punto de linearización, tanto si lo realiza el grupo al llegar como si lo realiza `liberar` en su nombre.

## Justificación de R2

No se guarda una elección tentativa de butacas cuando el pedido no cabe. `atender` ejecuta de nuevo la política completa con el estado vigente, incluyendo los cambios de la liberación y las reservas atendidas antes en esa transacción. En el ejemplo, si primero se liberan dos sueltas, la familia de cuatro sigue pendiente; cuando aparece el bloque de cuatro, se selecciona ese bloque antes que butacas dispersas. No queda atrapada en una rama de fallback elegida antes de dormir.

## Justificación de R3 y progreso

La espera de cada grupo solo lee `respuesta`; esa TVar pasa de `Nothing` a `Just reserva` una vez y solo cuando se le asignaron k butacas. Modificar otra reserva, agregar otro pedido o liberar un número insuficiente de lugares no escribe su TVar y no lo despierta. El coordinador puede tener que reevaluar pendientes, pero no son los threads de los grupos los que se despiertan sin resultado.

Se recorre la cola en orden, pero se saltean grupos que aún no caben. Esto evita que un grupo grande detenga a todos; permite starvation de grupos grandes bajo una sucesión adversaria de solicitudes pequeñas. R1–R3 no piden fairness. Se preserva la política espacial para cada asignación; no se agrega una política FIFO estricta que no fue solicitada.

## Casos para comprobar

- Una fila `[libre,libre,ocupada,libre]`, k=2: devuelve sus dos primeras butacas.
- Filas `[libre,ocupada]` y `[libre,libre]`, k=2: elige el bloque de la segunda, no dos dispersas anteriores.
- No hay bloque, pero sí k libres: devuelve las primeras k en orden global.
- Menos de k libres: se registra el pedido sin ocupar butacas.
- Liberaciones insuficientes: la TVar privada sigue `Nothing`.
- Dos pedidos compiten por el mismo bloque: solo uno lo recibe; el otro se reevalúa sobre el estado actualizado.
