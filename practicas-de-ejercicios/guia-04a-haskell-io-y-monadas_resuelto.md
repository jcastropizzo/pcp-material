# Guía 04a — Haskell, IO y mónadas: soluciones

Fuente: [transcripción](guia-04a-haskell-io-y-monadas.transcripcion.md). Soluciones de estudio, no oficiales. El número 04a ubica este warm-up opcional antes de la guía 5; no modifica la numeración impresa. Las funciones se pueden reunir en un módulo, ocultando del Prelude los nombres redefinidos (`mapM`, `sum`, etc. según cuáles se usen). Las alternativas de `main` son programas separados.

## Ejercicio 1 — listas

```haskell
longitud :: [a] -> Int
longitud [] = 0
longitud (_:xs) = 1 + longitud xs

sumatoria :: [Int] -> Int
sumatoria [] = 0
sumatoria (x:xs) = x + sumatoria xs

pertenece :: Eq a => a -> [a] -> Bool
pertenece _ [] = False
pertenece y (x:xs) = y == x || pertenece y xs

quitar :: Eq a => a -> [a] -> [a]
quitar _ [] = []
quitar y (x:xs)
  | x == y = xs
  | otherwise = x : quitar y xs
```

En `quitar`, al encontrar la primera coincidencia se devuelve la cola sin seguir quitando. Ejemplo: `quitar 2 [1,2,2] = [1,2]`. La recursión termina sobre listas finitas porque consume una celda por paso.

## Ejercicio 2 — alto orden

```haskell
cuadrados :: [Int] -> [Int]
cuadrados = map (\x -> x*x)
positivos :: [Int] -> [Int]
positivos = filter (>0)
cantidad :: (a -> Bool) -> [a] -> Int
cantidad p = foldr (\x n -> if p x then n+1 else n) 0
```

`map` conserva cantidad y orden; `filter` conserva el orden de los que pasan. `cantidad` suma exactamente una unidad por elemento que satisface `p`.

## Ejercicio 3 — árbol de búsqueda

```haskell
data Arbol a = Hoja | Nodo (Arbol a) a (Arbol a)
  deriving (Eq, Show)

insertar :: Ord a => a -> Arbol a -> Arbol a
insertar x Hoja = Nodo Hoja x Hoja
insertar x (Nodo iz y de)
  | x < y = Nodo (insertar x iz) y de
  | x > y = Nodo iz y (insertar x de)
  | otherwise = Nodo iz y de

estaEn :: Ord a => a -> Arbol a -> Bool
estaEn _ Hoja = False
estaEn x (Nodo iz y de)
  | x < y = estaEn x iz
  | x > y = estaEn x de
  | otherwise = True

enOrden :: Arbol a -> [a]
enOrden Hoja = []
enOrden (Nodo iz x de) = enOrden iz ++ [x] ++ enOrden de

desdeLista :: Ord a => [a] -> Arbol a
desdeLista = foldr insertar Hoja
```

Invariante: claves izquierdas menores y derechas mayores, sin duplicados. Insertar una clave igual conserva el nodo. `foldr` inserta desde la derecha de la lista; puede producir una forma distinta de `foldl`, aunque representa el mismo conjunto. `enOrden` es ordenado si se respeta el invariante, no para cualquier árbol arbitrario construido a mano.

## Ejercicio 4 — operaciones con Maybe

```haskell
safeHead :: [a] -> Maybe a
safeHead [] = Nothing
safeHead (x:_) = Just x

buscar :: Eq k => k -> [(k,v)] -> Maybe v
buscar _ [] = Nothing
buscar k ((k',v):xs)
  | k == k' = Just v
  | otherwise = buscar k xs

dividir :: Int -> Int -> Maybe Int
dividir _ 0 = Nothing
dividir x y = Just (x `div` y)

data Cuenta = Cuenta { titular :: String, saldo :: Int }
  deriving (Eq, Show)

depositar :: Int -> Cuenta -> Cuenta
depositar n c = c { saldo = saldo c + n }

extraer :: Int -> Cuenta -> Maybe Cuenta
extraer n c
  | saldo c < n = Nothing
  | otherwise = Just (c { saldo = saldo c - n })
```

Se supone que montos y saldos representan cantidades no negativas y que no hay overflow de `Int`. La guía no define qué hacer con montos negativos; esta es una precondición explícita, no una validación oculta. `dividir` es división entera (con `div`, hacia menos infinito), no división real.

## Ejercicio 5 — Maybe y Either

```haskell
calcularCase :: Int -> Int -> Int -> Maybe Int
calcularCase a b c = case dividir a b of
  Nothing -> Nothing
  Just x -> case dividir x c of
    Nothing -> Nothing
    Just y -> Just (y+1)

calcularBind :: Int -> Int -> Int -> Maybe Int
calcularBind a b c = dividir a b >>= \x ->
  dividir x c >>= \y -> return (y+1)

calcular :: Int -> Int -> Int -> Maybe Int
calcular a b c = do
  x <- dividir a b
  y <- dividir x c
  return (y+1)

transferir :: Int -> Cuenta -> Cuenta -> Maybe (Cuenta, Cuenta)
transferir n origen destino = do
  origen' <- extraer n origen
  return (origen', depositar n destino)

dividirE :: Int -> Int -> Either String Int
dividirE _ 0 = Left "Divisor cero"
dividirE x y = Right (x `div` y)

extraerE :: Int -> Cuenta -> Either String Cuenta
extraerE n c
  | n < 0 = Left "Monto negativo"
  | saldo c < n = Left "Saldo insuficiente"
  | otherwise = Right (c { saldo = saldo c - n })

transferirE :: Int -> Cuenta -> Cuenta -> Either String (Cuenta, Cuenta)
transferirE n origen destino = do
  origen' <- extraerE n origen
  return (origen', depositar n destino)
```

El flujo de `transferir` prácticamente no cambia: `do` propaga tanto `Nothing` como `Left`. La variante `Either` agrega diagnóstico y valida explícitamente montos negativos. Estas son cuentas inmutables: no se ejecuta ninguna actualización parcial cuando falla. Para referirse a una misma cuenta bancaria por identidad haría falta otro modelo; aquí se transforman dos valores.

## Ejercicio 6 — lista como mónada

```haskell
pares :: [a] -> [b] -> [(a,b)]
pares xs ys = do
  x <- xs
  y <- ys
  return (x,y)

pitagoricas :: Int -> [(Int,Int,Int)]
pitagoricas n = do
  a <- [1..n]
  b <- [a..n]
  c <- [b..n]
  if a*a+b*b == c*c then return (a,b,c) else []
```

Cada elección produce una rama. `[]` elimina una rama, mientras `return` produce una lista unitaria. Se toman ternas positivas; si se admitiera cero, hay que cambiar el primer rango y se incluirían ternas degeneradas. Los rangos garantizan `a ≤ b ≤ c ≤ n`.

## Ejercicio 7 — combinadores monádicos

```haskell
when :: Monad m => Bool -> m () -> m ()
when True accion = accion
when False _ = return ()

replicateM_ :: Monad m => Int -> m a -> m ()
replicateM_ n accion
  | n <= 0 = return ()
  | otherwise = accion >> replicateM_ (n-1) accion

mapM :: Monad m => (a -> m b) -> [a] -> m [b]
mapM _ [] = return []
mapM f (x:xs) = do
  y <- f x
  ys <- mapM f xs
  return (y:ys)

forM_ :: Monad m => [a] -> (a -> m b) -> m ()
forM_ [] _ = return ()
forM_ (x:xs) f = f x >> forM_ xs f

forever :: Monad m => m a -> m b
forever accion = accion >> forever accion
```

- `mapM (dividir 100) [1,0,5] = Nothing`: falla en el divisor cero; no retorna una lista parcial.
- `forever Nothing = Nothing`: el bind de `Maybe` corta sin evaluar la continuación. No toda aplicación de `forever` diverge.
- `when False Nothing = Just ()` al instanciar en `Maybe`.
- `replicateM_ 2 [1,2]` produce cuatro resultados `()`: la secuenciación de listas combina elecciones.
- `mapM (\x -> [x,-x]) [1,2]` produce `[[1,2],[1,-2],[-1,2],[-1,-2]]`.
- `forM_ [1,0] (\x -> dividir 10 x) = Nothing`.
- `forever [] = []`; `forever [()]` no produce un resultado terminado.

## Ejercicio 8 — IO

```haskell
saludar :: IO ()
saludar = do
  putStrLn "¿Cómo te llamás?"
  nombre <- getLine
  putStrLn ("Hola, " ++ nombre)

sumarHastaCero :: IO Int
sumarHastaCero = do
  n <- readLn
  if n == 0 then return 0 else do
    resto <- sumarHastaCero
    return (n + resto)

-- Alternativa A: main = saludar
-- Alternativa B: main = sumarHastaCero >>= print
```

Se supone entrada válida de enteros. Una versión para usuarios reales debería tratar errores de parseo; eso no cambia la secuenciación solicitada.

## Ejercicio 9 — acciones como valores

1. Imprime solo `chau`: `let x = hola` liga un valor, no ejecuta la acción.
2. Imprime `hola` dos veces: usar `x` dos veces secuencia dos ejecuciones.
3. Imprime `5`: `return 10` no es un salto de control ni termina la función; su resultado se descarta y después se ejecuta `print n`.
4. Imprime `c` y luego `a`: construir la lista no ejecuta las acciones; se ejecutan las dos seleccionadas.

```haskell
repetir :: Int -> IO () -> IO ()
repetir n accion
  | n <= 0 = return ()
  | otherwise = accion >> repetir (n-1) accion
```

## Ejercicio 10 — adivinar

```haskell
adivinar :: Int -> IO ()
adivinar secreto = intentar 1
  where
    intentar n = do
      putStrLn "Ingresá un número:"
      propuesta <- readLn
      case compare propuesta secreto of
        EQ -> putStrLn ("Acertaste en " ++ show n ++ " intentos")
        LT -> putStrLn "El secreto es mayor" >> intentar (n+1)
        GT -> putStrLn "El secreto es menor" >> intentar (n+1)

-- En un archivo ejecutable separado:
-- main :: IO ()
-- main = adivinar 42
```

El contador empieza en uno porque la primera entrada ya es un intento. Termina cuando se acierta; no se presupone que un usuario arbitrario vaya a acertar.
