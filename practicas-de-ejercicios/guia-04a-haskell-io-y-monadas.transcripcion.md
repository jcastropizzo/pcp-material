# guia-04a-haskell-io-y-monadas — transcripción

Fuente: [guia-04a-haskell-io-y-monadas.pdf](guia-04a-haskell-io-y-monadas.pdf).

Conversión textual por página. Se conserva el texto disponible, los ejemplos y las diapositivas progresivas. No es código compilable ni una corrección del original. Los diagramas, fórmulas, colores y columnas deben cotejarse en el PDF cuando afecten una conclusión. No se modificó el archivo fuente.



## Página 1

[Ver página del PDF](guia-04a-haskell-io-y-monadas.pdf#page=1)

```text
Programación Concurrente y Paralela
                      Práctica opcional: Haskell, IO y mónadas
                               2do Cuatrimestre 2026

   Esta guía es opcional. Es un warm-up para la Práctica 5. Los ejercicios se pueden resolver
en ghci o compilando con ghc.


Warm-up
Ejercicio 1. Funciones sobre listas. Definir de manera recursiva, sin usar las funciones equiva-
lentes del Prelude:
  a) longitud :: [a] -> Int
  b) sumatoria :: [Int] -> Int
  c) pertenece :: Eq a => a -> [a] -> Bool
  d) quitar :: Eq a => a -> [a] -> [a], que quita solo la primera aparición del elemento.

Ejercicio 2. Funciones de alto orden.
  a) Definir cuadrados :: [Int] -> [Int] usando map y positivos :: [Int] -> [Int]
     usando filter.
  b) Definir cantidad :: (a -> Bool) -> [a] -> Int que cuenta cuántos elementos cum-
     plen el predicado, usando foldr.

Ejercicio 3. Considerar el siguiente tipo para árboles binarios de búsqueda:
data Arbol a = Hoja | Nodo ( Arbol a ) a ( Arbol a )
   Definir:
  a) insertar :: Ord a => a -> Arbol a -> Arbol a, que no agrega elementos repetidos.
  b) estaEn :: Ord a => a -> Arbol a -> Bool
  c) enOrden :: Arbol a -> [a], que devuelve los elementos ordenados de menor a mayor.
  d) desdeLista :: Ord a => [a] -> Arbol a, que construye un árbol insertando todos los
     elementos de la lista. Usar foldr.

Ejercicio 4. Funciones parciales con Maybe.
  a) safeHead :: [a] -> Maybe a, que devuelve el primer elemento de la lista, o Nothing si
     la lista es vacía.
  b) buscar :: Eq k => k -> [(k, v)] -> Maybe v, que devuelve el valor asociado a la cla-
     ve, o Nothing si la clave no está.
  c) dividir :: Int -> Int -> Maybe Int, que devuelve Nothing si el divisor es 0.
  d) Dado el tipo

                                               1
```

## Página 2

[Ver página del PDF](guia-04a-haskell-io-y-monadas.pdf#page=2)

```text
Prog. Concurrente y Paralela                                Práctica opcional: Haskell y mónadas


     data Cuenta = Cuenta { titular :: String , saldo :: Int }

     definir las siguientes funciones. extraer devuelve Nothing si el saldo no alcanza.
     depositar :: Int -> Cuenta -> Cuenta
     extraer   :: Int -> Cuenta -> Maybe Cuenta


Mónadas
Ejercicio 5. Maybe y Either como mónadas.
  a) Definir calcular :: Int -> Int -> Int -> Maybe Int tal que calcular a b c de-
     vuelve (a / b) / c + 1 usando dividir del Ejercicio 4. Dar tres versiones: con case,
     con >>= y con do.
  b) Usando la notación do y las funciones del Ejercicio 4, definir
     transferir :: Int -> Cuenta -> Cuenta -> Maybe ( Cuenta , Cuenta )

     que devuelve las cuentas de origen y destino luego de transferir el monto, o Nothing si el
     saldo de origen no alcanza.
  c) Dar versiones de dividir, extraer y transferir que devuelvan Either String con un
     mensaje que explique el error. ¿Cuánto cambia el código de transferir?

Ejercicio 6. La lista como mónada.
  a) Definir con notación do la función pares :: [a] -> [b] -> [(a, b)] que devuelve todos
     los pares posibles.
  b) Definir con notación do la función pitagoricas :: Int -> [(Int, Int, Int)] que de-
     vuelve las ternas (a, b, c) con a ≤ b ≤ c ≤ n y a2 + b2 = c2 , sin usar listas por comprensión.

Ejercicio 7. Las siguientes funciones de Control.Monad sirven para cualquier mónada. Defi-
nirlas sin usar las de la biblioteca, y probarlas con Maybe y listas.
  a) when :: Monad m => Bool -> m () -> m (), que ejecuta la acción solo si la condición
     es verdadera. Si es falsa, no hace nada.
  b) replicateM_ :: Monad m => Int -> m a -> m (), que ejecuta la acción la cantidad de
     veces indicada, una después de la otra, y descarta los resultados.
  c) mapM :: Monad m => (a -> m b) -> [a] -> m [b], que aplica la función a cada ele-
     mento de la lista, ejecuta las acciones en orden y devuelve la lista de resultados. ¿Qué
     devuelve mapM (dividir 100) [1, 0, 5]?
  d) forM_ :: Monad m => [a] -> (a -> m b) -> m (), que es como mapM con los argumen-
     tos invertidos, pero descarta los resultados.
  e) forever :: Monad m => m a -> m b, que ejecuta la acción una y otra vez, sin terminar.
     ¿Qué devuelve forever Nothing?


                                                 2
```

## Página 3

[Ver página del PDF](guia-04a-haskell-io-y-monadas.pdf#page=3)

```text
Prog. Concurrente y Paralela                             Práctica opcional: Haskell y mónadas


Entrada y salida
Ejercicio 8. Programas interactivos.
  a) Escribir un programa que pregunte el nombre del usuario y lo salude por su nombre.
  b) Definir sumarHastaCero :: IO Int, que lee números de la entrada (uno por línea) hasta
     leer un 0 y devuelve la suma de los números leídos. Escribir un main que la use e imprima
     el resultado.

Ejercicio 9. Una expresión de tipo IO a es una acción: un valor que describe un efecto, que se
ejecuta solo cuando forma parte de main. Sea
hola :: IO ()
hola = putStrLn " hola "

  a) Indicar qué imprime cada uno de los siguientes programas y justificar.

     programa1 = do                                programa3 = do
         let x = hola                                  n <- return 5
         putStrLn " chau "                             return 10
                                                       print n
     programa2 = do
         let x = hola                              programa4 = do
         x                                             let as = [ putStrLn " a " ,
         x                                                        putStrLn " b " ,
                                                                  putStrLn " c " ]
                                                       as !! 2
                                                       head as


  b) Definir repetir :: Int -> IO () -> IO () que ejecuta una acción la cantidad de veces
     indicada.

Ejercicio 10. Definir adivinar :: Int -> IO (), que recibe un número secreto y le pide al
usuario que lo adivine. Después de cada intento, el programa le dice si el número secreto es
más alto o más bajo. Cuando el usuario acierta, el programa muestra cuántos intentos le llevó y
termina. Escribir un main que la use.




                                              3
```
