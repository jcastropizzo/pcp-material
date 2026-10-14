# Guía 5 — memoria transaccional: soluciones

Fuente: [transcripción](guia-05-memoria-transaccional.transcripcion.md). Soluciones de estudio. Imports comunes: `Control.Concurrent.STM`, `Control.Concurrent`, `Control.Concurrent.MVar`, `Control.Monad` y, para proteger liberaciones, `Control.Exception (bracket_)`. Cada ejercicio puede ser un módulo independiente. Las funciones de estructuras devuelven `STM`; el cliente decide el límite de `atomically`.

**Convenciones:** no se usa IO dentro de STM. Un `retry` revierte toda la transacción y espera cambios en sus lecturas; no conserva una adquisición parcial. La serialización al commit proporciona los puntos de linearización de las operaciones exitosas. No se presume fairness ni ausencia de starvation.

## Ejercicio 1 — buffer de capacidad uno

### a) Versiones no bloqueantes

```haskell
getNB :: TVar (Maybe a) -> STM (Maybe a)
getNB b = do
  x <- readTVar b
  writeTVar b Nothing
  return x

putNB :: TVar (Maybe a) -> a -> STM Bool
putNB b x = do
  actual <- readTVar b
  case actual of
    Just _ -> return False
    Nothing -> writeTVar b (Just x) >> return True
```

Se eligieron nombres diferentes para comparar variantes. `putNB` no reemplaza un elemento existente. Un fallo devuelve `False` y no altera el estado; `getNB` devuelve `Nothing` si estaba vacío.

### b) Programa concurrente

```haskell
demo :: IO ()
demo = do
  b <- newTVarIO (Just (0 :: Int))
  fin <- newEmptyMVar
  _ <- forkIO $ atomically (putNB b 1) >>= putMVar fin
  resultado <- atomically (getNB b)
  print resultado
  _ <- takeMVar fin
  return ()
```

Siempre imprime `Just 0`. Si el put se serializa primero, encuentra el buffer lleno y falla; si se serializa después del get, escribe 1 cuando la lectura ya tomó 0. No puede imprimirse 1 con estas operaciones y ese estado inicial. La espera final evita perder el thread escritor al terminar el programa.

### c–d) Versiones bloqueantes y lectura sin consumo

```haskell
get :: TVar (Maybe a) -> STM a
get b = do
  x <- readTVar b
  case x of
    Nothing -> retry
    Just v -> writeTVar b Nothing >> return v

put :: TVar (Maybe a) -> a -> STM ()
put b v = do
  x <- readTVar b
  case x of
    Just _ -> retry
    Nothing -> writeTVar b (Just v)

readBuffer :: TVar (Maybe a) -> STM a
readBuffer b = readTVar b >>= maybe retry return
```

El estado es siempre vacío o contiene un solo elemento. La consulta de la condición y la actualización pertenecen a la misma transacción. `readBuffer` no vacía el buffer.

## Ejercicio 2 — semáforo

```haskell
type Sem = TVar Int
newSem :: Int -> IO Sem
newSem n | n < 0 = ioError (userError "Permisos negativos")
         | otherwise = newTVarIO n

acquire :: Sem -> STM ()
acquire s = do
  n <- readTVar s
  check (n > 0)
  writeTVar s (n-1)

release :: Sem -> STM ()
release s = modifyTVar' s (+1)

dosPermisos :: Sem -> IO () -> IO ()
dosPermisos s accion = bracket_
  (atomically (acquire s >> acquire s))
  (atomically (release s >> release s)) accion
```

Ambos permisos se toman en un único commit. Con un solo permiso disponible, el segundo `acquire` hace retry y el primero no queda adquirido. Separarlos en dos `atomically` cambia esa propiedad. Invariante: contador no negativo. Un semáforo que nunca puede llegar a dos permisos no permite completar esa solicitud.

## Ejercicio 3 — bolitas

```haskell
data Bolsa = Bolsa (TVar Int) (TVar Int) -- generadores, bolitas
nuevaBolsa :: IO Bolsa
nuevaBolsa = atomically $ Bolsa <$> newTVar 0 <*> newTVar 0

entrarGenerador :: Bolsa -> STM ()
entrarGenerador (Bolsa g _) = modifyTVar' g (+1)

generar :: Bolsa -> TVar Int -> STM ()
generar (Bolsa g b) puntos = do
  capacidad <- readTVar g
  n <- readTVar b
  check (n < capacidad)
  writeTVar b (n+1)
  modifyTVar' puntos (+1)

consumir :: Bolsa -> TVar Int -> STM ()
consumir (Bolsa _ b) puntos = do
  n <- readTVar b
  check (n >= 2)
  writeTVar b (n-2)
  modifyTVar' puntos (+1)

salirGenerador :: Bolsa -> STM ()
salirGenerador (Bolsa g b) = do
  capacidad <- readTVar g
  n <- readTVar b
  check (capacidad > 0 && n <= capacidad-1)
  writeTVar g (capacidad-1)
```

Cada generador se registra una vez antes de su ciclo `atomically (generar bolsa misPuntos)`. Cada consumidor repite `atomically (consumir bolsa misPuntos)`. Cada participante crea su contador con cero. Si no hay retiros, no se necesita `salirGenerador`; si se permiten, no puede disminuir la capacidad dejando un exceso de bolitas, por eso puede esperar antes de retirarse.

Invariante inductivo: `0 ≤ bolitas ≤ generadores`. Las transacciones actualizan puntos y bolsa juntas. **Límite del enunciado:** con un solo generador y una bolita nadie puede producir otra ni consumir un par. Esa configuración impide progreso por las propias reglas, no se puede prometer ausencia de bloqueos globales para toda cantidad posible de participantes. Tampoco se promete starvation-free.

## Ejercicio 4 — fumadores

```haskell
data Ingrediente = Tabaco | Papel | Fosforos deriving (Eq, Show)
type Mesa = TVar (Maybe Ingrediente)
-- Just i representa un par completo al que le falta i.
ponerPar :: Mesa -> Ingrediente -> STM ()
ponerPar mesa faltante = do
  m <- readTVar mesa
  case m of
    Nothing -> writeTVar mesa (Just faltante)
    Just _ -> retry

tomarPar :: Mesa -> Ingrediente -> STM ()
tomarPar mesa propio = do
  m <- readTVar mesa
  check (m == Just propio)
  writeTVar mesa Nothing
```

Inicialización: `mesa <- newTVarIO Nothing`. El proveedor elige el par fuera de STM y ejecuta `atomically (ponerPar mesa ingredienteFaltante)`. Cada fumador ejecuta `atomically (tomarPar mesa miIngrediente)` y luego arma/fuma en IO. No se debe consumir un ingrediente por separado. Solo el fumador complementario retira el par, y el proveedor no sobrescribe un par disponible. Para que cada fumador progrese se necesita también una elección adecuada del proveedor y planificación; STM no las impone.

## Ejercicio 5 — pizzería

```haskell
data Barra = Barra (TVar Int) (TVar Int) -- grandes, chicas
data Pedido = UnaGrande | DosChicas deriving (Eq, Show)

servirGrande :: Barra -> STM ()
servirGrande (Barra g _) = modifyTVar' g (+1)
servirChica :: Barra -> STM ()
servirChica (Barra _ c) = modifyTVar' c (+1)

tomarPizza :: Barra -> STM Pedido
tomarPizza (Barra g c) = grande `orElse` chicas
  where
    grande = do
      n <- readTVar g
      check (n > 0)
      writeTVar g (n-1)
      return UnaGrande
    chicas = do
      n <- readTVar c
      check (n >= 2)
      writeTVar c (n-2)
      return DosChicas
```

Se crea `Barra` con ambos contadores en cero. El pizzero cocina fuera de STM y publica una pizza por transacción; cada cliente toma su pedido con `atomically` y paga/come fuera. `orElse` prueba primero la alternativa grande, no elige al azar. Si hay grande y dos chicas en el estado serializado, toma la grande. Si ambas ramas hacen retry, se espera por cambios relevantes de ambas. Los contadores nunca son negativos; no se garantiza orden de atención.

## Ejercicio 6 — pila

```haskell
newtype Pila a = Pila (TVar [a])
newPila :: STM (Pila a)
newPila = Pila <$> newTVar []
push :: Pila a -> a -> STM ()
push (Pila p) x = modifyTVar' p (x:)
pop :: Pila a -> STM a
pop (Pila p) = do
  xs <- readTVar p
  case xs of
    [] -> retry
    x:resto -> writeTVar p resto >> return x
```

La cabeza representa el tope. Cada commit equivale a una operación LIFO secuencial. Se eligió `pop` bloqueante porque la guía no fija su política en vacío; una variante no bloqueante puede devolver `Maybe`.

## Ejercicio 7 — colas

```haskell
data Cola a = Cola (TVar [a])
newCola :: STM (Cola a)
newCola = Cola <$> newTVar []

encolar :: Cola a -> a -> STM ()
encolar (Cola q) x = modifyTVar' q (++ [x])
desencolar :: Cola a -> STM a
desencolar (Cola q) = do
  xs <- readTVar q
  case xs of
    [] -> retry
    x:resto -> writeTVar q resto >> return x
desencolarSi :: Cola a -> STM (Maybe a)
desencolarSi q = (Just <$> desencolar q) `orElse` return Nothing

encolarDesencolar :: Cola a -> Cola a -> IO ()
encolarDesencolar origen destino = atomically $ do
  x <- desencolar origen
  encolar destino x

data Prioridad = Alta | Media | Baja
data ColaPrioridad a = ColaPrioridad (Cola a) (Cola a) (Cola a)
encolarP :: ColaPrioridad a -> a -> Prioridad -> STM ()
encolarP (ColaPrioridad a _ _) x Alta = encolar a x
encolarP (ColaPrioridad _ m _) x Media = encolar m x
encolarP (ColaPrioridad _ _ b) x Baja = encolar b x
desencolarP :: ColaPrioridad a -> STM a
desencolarP (ColaPrioridad a m b) =
  desencolar a `orElse` desencolar m `orElse` desencolar b
```

En a), el final de lista es la entrada y la cabeza la salida: FIFO. Encolar cuesta O(n) con esta representación impuesta; una cola de dos listas puede mejorarla, pero sería otra representación. En b), ambos efectos se publican juntos; con la misma cola como origen/destino se rota el primer elemento al final. En c), FIFO se conserva dentro de cada prioridad y `orElse` impone prioridad estricta. Un flujo perpetuo de prioridad alta puede postergar las otras indefinidamente.

## Ejercicio 8 — lock lectores/escritor

La frase «crear un lock sin para» parece una errata del enunciado. Se implementan adquisición y liberación, sin inferir una garantía de fairness que no se pide explícitamente.

```haskell
data RWLock = RWLock (TVar Int) (TVar Bool)
newRWLock :: IO RWLock
newRWLock = atomically $ RWLock <$> newTVar 0 <*> newTVar False

readLock :: RWLock -> STM ()
readLock (RWLock lectores escritor) = do
  w <- readTVar escritor
  check (not w)
  modifyTVar' lectores (+1)
readUnlock :: RWLock -> STM ()
readUnlock (RWLock lectores _) = modifyTVar' lectores (subtract 1)

writeLock :: RWLock -> STM ()
writeLock (RWLock lectores escritor) = do
  r <- readTVar lectores
  w <- readTVar escritor
  check (r == 0 && not w)
  writeTVar escritor True
writeUnlock :: RWLock -> STM ()
writeUnlock (RWLock _ escritor) = writeTVar escritor False

conLectura :: RWLock -> IO a -> IO a
conLectura l = bracket_ (atomically (readLock l))
                       (atomically (readUnlock l))

data RWLimitado = RWLimitado Int RWLock
newRWLimitado :: Int -> IO RWLimitado
newRWLimitado k
  | k <= 0 = ioError (userError "El máximo debe ser positivo")
  | otherwise = RWLimitado k <$> newRWLock

readLockLimitado :: RWLimitado -> STM ()
readLockLimitado (RWLimitado k (RWLock lectores escritor)) = do
  r <- readTVar lectores
  w <- readTVar escritor
  check (not w && r < k)
  writeTVar lectores (r+1)
```

En b), liberación y operaciones de escritor delegan al `RWLock` interno. Se exige disciplina de uso: solo libera quien adquirió, exactamente una vez. Si hay escritor, lectores es cero; la adquisición conjunta comprueba esa exclusión. La acción protegida corre fuera de STM, entre adquisición y liberación. Un escritor puede sufrir starvation si siempre se superponen lectores; tener un máximo de lectores no evita por sí solo ese fenómeno.

## Ejercicio 9 — filósofos

```haskell
type Tenedor = TVar (Maybe ())
comerCon :: Tenedor -> Tenedor -> IO () -> IO ()
comerCon izq der comer = bracket_
  (atomically (get izq >> get der))
  (atomically (put izq () >> put der ()))
  comer

filosofo :: [Tenedor] -> Int -> IO () -> IO () -> IO ()
filosofo ts i pensar comer = forever $ do
  pensar
  comerCon (ts !! i) (ts !! ((i+1) `mod` length ts)) comer
```

Se crean N tenedores llenos con `newTVarIO (Just ())`, N ≥ 2, y un thread por filósofo. La adquisición de ambos es una transacción: si falta uno, no se retiene el otro. Se elimina la espera circular por retención parcial. Con planificación y comidas finitas hay progreso global, pero no garantía individual de entrada. Para N=1 ambos índices refieren al mismo tenedor y esta implementación esperaría indefinidamente; por eso se explicitó N ≥ 2. La ayuda que remite a 1.b parece referirse a las operaciones bloqueantes de 1.c.
