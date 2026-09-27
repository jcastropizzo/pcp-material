# teorica-memoria-transaccional-por-software — transcripción

- Fuente: [teorica-memoria-transaccional-por-software.pdf](teorica-memoria-transaccional-por-software.pdf)
- Páginas del PDF: 83.
- SHA-256 del PDF: `44faa399d18f14dd4172148c747973576975068755d00a65596871bf34508fc0`.
- Tipo: transcripción textual por página, con disposición espacial conservada y referencias al original para esquemas.

> Se conserva el contenido del original, incluidos ejemplos incorrectos, erratas y diapositivas progresivas. Los bloques `text` preservan columnas y sangría; no son código listo para compilar. En código repartido en columnas, seguir las indicaciones «continúa a la derecha». Las notas editoriales se distinguen del original. Para símbolos cuya extracción sea ambigua, consultar la página enlazada.

## Página 1

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=1)

```text
Memoria Transaccional por Software
Software Transactional Memories (STM)
```

## Página 2

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=2)

```text
Idea Central



   • Reemplazar locks por bloques atómicos: atomic {bloque}
   • Garantiza:
       • Atomicidad: Los efectos se hacen visibles todos juntos (los estados
         intermedios no son visibles a otros threads).
       • Aislamiento (Isolation): La ejecución de un bloque no se ve afectada por
         otros threads.
   • Funciona de manera análoga a las transacciones ACID.
```

## Página 3

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=3)

```text
Ventajas de STM




  • No introduce deadlocks (ya que no se manejan locks explícitos).
  • Son composicionales.
  • La gestión de errores es simple (basta con levantar una excepción en el
    código).
```

## Página 4

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=4)

```text
Motivación

 class Cuenta {
   private double saldo = 0;

     synchronized void depositar ( double monto ) {
       // pre : monto > 0
       saldo += monto ;
     }
     synchronized void extraer ( double monto ) {
       // pre : monto > 0
       if ( saldo < monto )
          throw new SaldoInsuficiente () ;
       saldo -= monto ;
     }

     synchronized void transferir ( Cuenta cuenta ,
       double monto ) {
       cuenta . extraer ( monto ) ;
       depositar ( monto ) ;
     }
 }
```

## Página 5

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=5)

```text
Motivación

 class Cuenta {
   private double saldo = 0;                                 Posibilidad de Deadlock
     synchronized void depositar ( double monto ) {          Dos transferencias concu-
       // pre : monto > 0
       saldo += monto ;                                      rrentes en sentido opuesto
     }                                                       entre las mismas cuentas
     synchronized void extraer ( double monto ) {
       // pre : monto > 0                                    pueden producir un dead-
       if ( saldo < monto )                                  lock.
          throw new SaldoInsuficiente () ;
       saldo -= monto ;
     }

     synchronized void transferir ( Cuenta cuenta ,
       double monto ) {
       cuenta . extraer ( monto ) ;
       depositar ( monto ) ;
     }
 }
```

## Página 6

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=6)

```text
Problema en el uso de Locks




   • El uso tradicional de locks y variables de condición no es composicional.
   • Esto afecta y perjudica a la programación modular.
```

## Página 7

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=7)

```text
Idea Principal e Intuición de STM


 Idea
 Introducir un constructor atómico
 para delimitar el bloque de código:

    atomic { bloque de código }


  void transferir ( Cuenta cuenta ,
      double monto ) {
    atomic {
      cuenta . extraer ( monto ) ;
      depositar ( monto ) ;
    }
  }
```

## Página 8

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=8)

```text
Idea Principal e Intuición de STM


 Idea                                  Intuición sobre la ejecución:
 Introducir un constructor atómico       • Ejecutar el bloque sin tomar locks.
 para delimitar el bloque de código:     • Registrar cada lectura y escritura en un
                                           log local de cada thread.
    atomic { bloque de código }
                                         • Escribir únicamente en el log local (no
                                           en la memoria global).
  void transferir ( Cuenta cuenta ,      • Al finalizar, se valida el log:
      double monto ) {
    atomic {                                  • Si es válido: Se actualiza
      cuenta . extraer ( monto ) ;              atómicamente la memoria principal.
      depositar ( monto ) ;
    }                                         • Si no es válido: Se descartan los
  }                                             cambios y se recomienza desde el
                                                principio.
```

## Página 9

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=9)

```text
Dificultad para la implementación




   • Problema: Loggear los efectos secundarios.
   • En ciertos lenguajes funcionales se distinguen de forma estricta las
     expresiones puras de aquellas que tienen efectos.
   • Esto representa una excelente oportunidad para las mónadas.
```

## Página 10

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=10)

```text
Haskell: Efectos


   • Funciones puras: Solo calculan y retornan un resultado basado
     estrictamente en sus parámetros.
        • una función aplicada a los mismos argumentos, retorna el mismo resultado.
   • Ausencia de efectos secundarios (Side Effects): Evaluación de las
     expresiones no pueden alterar el entorno. .
  Transparencia referencial:
  El valor de una expresión depende sólo del valor de sus subexpresiones (no de-
  pende de la historia de la ejecución ni del orden de evaluación)
```

## Página 11

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=11)

```text
¿Cómo hace Haskell para I/O?


   • Operaciones de I/O no son puras (escriben/leen archivo, pantallas, teclado)
   • La solución de Haskell: Expresiones separado por el sistema de tipos.

  El Mundo Puro                           El Mundo Impuro
    • Libre de efectos secundarios.         • Se encarga del “trabajo sucio”.
    • Permite razonar con                   • Interactúa con el teclado, la
      transparencia referencial.              pantalla y el exterior.
```

## Página 12

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=12)

```text
¿Cómo hace Haskell para I/O?


   • Operaciones de I/O no son puras (escriben/leen archivo, pantallas, teclado)
   • La solución de Haskell: Expresiones separado por el sistema de tipos.

  El Mundo Puro                           El Mundo Impuro
    • Libre de efectos secundarios.         • Se encarga del “trabajo sucio”.
    • Permite razonar con                   • Interactúa con el teclado, la
      transparencia referencial.              pantalla y el exterior.
  Tipos Monádicos (Mónadas)
  El tipo de las expresiones impuras.
```

## Página 13

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=13)

```text
Tipos monádicos: Intuición

  ¿Qué es un Tipo Monádico?
  Un tipo común dice los posibles valores (un entero, un texto, un booleano) de
  una expresión tienes. Un tipo monádico dice los posibles valores Y ADEMÁS
  en qué contexto existe ese valor.
```

## Página 14

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=14)

```text
Tipos monádicos: Intuición

  ¿Qué es un Tipo Monádico?
  Un tipo común dice los posibles valores (un entero, un texto, un booleano) de
  una expresión tienes. Un tipo monádico dice los posibles valores Y ADEMÁS
  en qué contexto existe ese valor.

  Ejemplos
    • Int: Es un entero puro ordinario.
    • Maybe Int: Es un tipo monádico. Es un entero que habita en el contexto de la
      opcionalidad (el cómputo puede fallar).
    • [Int]: Es un tipo monádico. Es un entero que habita en el contexto de
      múltiples valores posibles (no-determinismo).
    • IO Int: Es un tipo monádico. Es un entero que habita en el contexto de una
      computación imperativa.
```

## Página 15

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=15)

```text
Constructores de Tipos


  Tipos Concretos
    • Representan valores directos en memoria.
    • Tipos como Int, Char o Bool.
    • Su clasificación o Kind es *.
```

## Página 16

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=16)

```text
Constructores de Tipos


  Tipos Concretos
    • Representan valores directos en memoria.
    • Tipos como Int, Char o Bool.
    • Su clasificación o Kind es *.

  Constructores de Tipos (Fábricas)
    • Plantillas de tipos. Necesitan aplicarse a un tipo para crear un tipo
      concreto.
    • Constructores como Maybe, [] (Lista) o (Bool,).
    • Su Kind es * -> *.
```

## Página 17

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=17)

```text
Abstracción de Comportamiento: Clases de Tipos



 Clase de Tipos (Typeclass)
 Es una interfaz que define un conjun-
 to de operaciones

  • Permite lograr polimorfismo
    ad-hoc (sobrecarga de funciones).
  • Un tipo de dato concreto puede
    elegir “implementar” esta interfaz
    creando una instancia.
```

## Página 18

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=18)

```text
Abstracción de Comportamiento: Clases de Tipos


                                         Ejemplo: La clase Eq (Igualdad)
 Clase de Tipos (Typeclass)               -- Definicion de la interfaz
                                          class Eq a where
 Es una interfaz que define un conjun-        (==) :: a -> a -> Bool
                                              (/=) :: a -> a -> Bool
 to de operaciones                            { - # MINIMAL (==) | (/=) # -}

                                          -- Instancia para un tipo propio
  • Permite lograr polimorfismo           data Estado = Activo | Inactivo
    ad-hoc (sobrecarga de funciones).     instance Eq Estado where
  • Un tipo de dato concreto puede            Activo   == Activo   = True
                                              Inactivo == Inactivo = True
    elegir “implementar” esta interfaz        _        == _        = False
    creando una instancia.
```

## Página 19

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=19)

```text
La Clase Functor: Contenedores mapeables


type Functor :: (* -> *) -> Constraint
                                               Instancias comunes:
class Functor f where                           -- Maybe
  fmap :: ( a -> b ) -> f a -> f b              fmap (+1) ( Just 5)   -- Just 6
  ...                                           fmap (+1) Nothing     -- Nothing
  { - # MINIMAL fmap # -}

 • Es una clase de tipos que representa a
   estructuras o contenedores que pueden
   ser mapeados.
 • fmap
     • Toma una función pura (a -> b).
     • Desempaque un valor (f a)
     • aplica la función al valor y devuelve
       el resultado empaquetado con tipo
       (f b).
```

## Página 20

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=20)

```text
La Clase Functor: Contenedores mapeables


type Functor :: (* -> *) -> Constraint
                                               Instancias comunes:
class Functor f where                           -- Maybe
  fmap :: ( a -> b ) -> f a -> f b              fmap (+1) ( Just 5)   -- Just 6
  ...                                           fmap (+1) Nothing     -- Nothing
  { - # MINIMAL fmap # -}

 • Es una clase de tipos que representa a
                                                -- Listas ( fmap es map )
   estructuras o contenedores que pueden        fmap (+1) [1 , 2 , 3] -- [2 , 3 , 4]
   ser mapeados.
 • fmap
     • Toma una función pura (a -> b).
     • Desempaque un valor (f a)
     • aplica la función al valor y devuelve
       el resultado empaquetado con tipo
       (f b).
```

## Página 21

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=21)

```text
La Clase Functor: Contenedores mapeables


type Functor :: (* -> *) -> Constraint
                                               Instancias comunes:
class Functor f where                           -- Maybe
  fmap :: ( a -> b ) -> f a -> f b              fmap (+1) ( Just 5)   -- Just 6
  ...                                           fmap (+1) Nothing     -- Nothing
  { - # MINIMAL fmap # -}

 • Es una clase de tipos que representa a
                                                -- Listas ( fmap es map )
   estructuras o contenedores que pueden        fmap (+1) [1 , 2 , 3] -- [2 , 3 , 4]
   ser mapeados.
 • fmap                                         -- Pairs ( es applicar a snd )
                                                fmap (+1) ( True , 2) -- ( True , 3)
     • Toma una función pura (a -> b).
     • Desempaque un valor (f a)
     • aplica la función al valor y devuelve
       el resultado empaquetado con tipo
       (f b).
```

## Página 22

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=22)

```text
La Clase Functor: Contenedores mapeables


type Functor :: (* -> *) -> Constraint
                                               Instancias comunes:
class Functor f where                           -- Maybe
  fmap :: ( a -> b ) -> f a -> f b              fmap (+1) ( Just 5)    -- Just 6
  ...                                           fmap (+1) Nothing      -- Nothing
  { - # MINIMAL fmap # -}

 • Es una clase de tipos que representa a
                                                -- Listas ( fmap es map )
   estructuras o contenedores que pueden        fmap (+1) [1 , 2 , 3] -- [2 , 3 , 4]
   ser mapeados.
 • fmap                                         -- Pairs ( es applicar a snd )
                                                fmap (+1) ( True , 2) -- ( True , 3)
     • Toma una función pura (a -> b).
     • Desempaque un valor (f a)                -- Flecha ( es composici ó n )
     • aplica la función al valor y devuelve    fmap 3) id 10 – 30
       el resultado empaquetado con tipo
       (f b).
```

## Página 23

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=23)

```text
Functores aplicativos

type Applicative :: (* -> *) ->               -- Maybe
     Constraint
class Functor f = > Applicative f where       pure 5 :: Maybe Int -- Just 5
  pure :: a -> f a
  ( <* >) :: f ( a -> b ) -> f a -> f b       Just (+3) <* > Just 2 -- Just 5
  ...                                         Nothing   <* > Just 2 -- Nothing
                                              Just (+3) <* > Nothing -- Nothing
• Todo tipo Applicative debe ser un
  Functor.
• Es un functor que permite aplicar
  funciones que ya se encuentran dentro
  de un contexto.
• pure: Toma un valor y lo introduce en un
  contexto mínimo (por defecto).
• <*> : Aplica una función en un contexto a
  un valor en un contexto del mismo tipo.
```

## Página 24

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=24)

```text
Functores aplicativos

type Applicative :: (* -> *) ->               -- Maybe
     Constraint
class Functor f = > Applicative f where       pure 5 :: Maybe Int -- Just 5
  pure :: a -> f a
  ( <* >) :: f ( a -> b ) -> f a -> f b       Just (+3) <* > Just 2 -- Just 5
  ...                                         Nothing   <* > Just 2 -- Nothing
                                              Just (+3) <* > Nothing -- Nothing
• Todo tipo Applicative debe ser un
  Functor.                                    -- Lists
• Es un functor que permite aplicar           pure 5 :: [ Int ] -- [5]

  funciones que ya se encuentran dentro       [] <* > [10 , 20] -- []
  de un contexto.                             [ id , (+1) , (*2) ] <* > [] -- - []
                                              [ id , (+1) , (*2) ] <* > [10 ,20] --
• pure: Toma un valor y lo introduce en un          [10 ,20 ,11 ,21 ,20 ,40]
  contexto mínimo (por defecto).
• <*> : Aplica una función en un contexto a
  un valor en un contexto del mismo tipo.
```

## Página 25

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=25)

```text
La Definición Formal: Clase Monad



La firma en el preludio:
  class Applicative m = > Monad m where
    return :: a -> m a
    ( > >=) :: m a -> ( a -> m b ) -> m b
    ...

  • Actúa como una “estrategia de
    combinación” para encadenar
    cómputos con un contexto.
```

## Página 26

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=26)

```text
La Definición Formal: Clase Monad



La firma en el preludio:                    • return (Inyección): Toma un
  class Applicative m = > Monad m where       valor puro y lo empaqueta en la
    return :: a -> m a                        mónada m.
    ( > >=) :: m a -> ( a -> m b ) -> m b
    ...                                     • »= (El operador Bind): Toma un
                                              valor empaquetado (m a), extrae
  • Actúa como una “estrategia de             el valor interno y se lo pasa a una
    combinación” para encadenar               función que genera el siguiente
    cómputos con un contexto.                 valor empaquetado (m b).
```

## Página 27

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=27)

```text
El Problema: Operar a Mano con Maybe

Evaluación manual con case:                   • Obliga al programador a
  -- Evaluar ’odd ’ sobre un ’ Maybe Int ’      escribir explícitamente el
  evaluarOdd :: Maybe Int -> Maybe Bool         manejo del caso de error
  evaluarOdd mi =
    case mi of                                  (Nothing) en cada paso.
       Nothing -> Nothing                     • Oscurece la lógica con código
       Just x -> Just ( odd x )
                                                de control repetitivo.
                                              • Rompe la fluidez de la
                                                composición de funciones
 No escala
                                                puras.
 Si tuviéramos un pipeline de 3 operaciones
 consecutivas que devuelven Maybe, ¡termi-
 naríamos con una pirámide de 3 niveles de
 case anidados!
```

## Página 28

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=28)

```text
Manejo de Opcionalidad: La Mónada Maybe


La instancia monádica:
  instance Monad Maybe where
      return x       = Just x
      Nothing > >= _ = Nothing
      Just x > >= f = f x


  • Encapsula cómputos que pueden
    fallar o no devolver un valor.
  • El operador >>= actúa como un
    fusible: si encuentra un Nothing,
    detiene la cadena inmediatamente.
```

## Página 29

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=29)

```text
Manejo de Opcionalidad: La Mónada Maybe


La instancia monádica:
                                        Usando el operador Bind (»=)
  instance Monad Maybe where
                                         evaluarOdd :: Maybe Int -> Maybe
      return x       = Just x
                                             Bool
      Nothing > >= _ = Nothing
                                         evaluarOdd mi = mi > >= (\ x ->
      Just x > >= f = f x
                                             return ( odd x ) )


  • Encapsula cómputos que pueden
    fallar o no devolver un valor.
  • El operador >>= actúa como un
    fusible: si encuentra un Nothing,
    detiene la cadena inmediatamente.
```

## Página 30

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=30)

```text
El Pipeline Monádico


Anidamiento con »=:                              ¿Cómo fluyen los datos?
  foo :: Maybe String                              • El operador >>= extrae el 3 y el
  foo = Just 3      > >= (\ x ->                     string "!" de sus respectivos
        Just " ! " > >= (\ y ->
        Just ( show x ++ y ) ) )                     contenedores Maybe.
                                                   • Diriante la ejecución, los
                                                     identificadores x e y actúan
 Scope                                               como variables puras
                                                     ordinarias.
 Al anidar los lambdas, la variable x definida     • El código se vuelve difícil de
 en el bloque más externo sigue estando en           leer debido a la acumulación de
 el scope de la expresión final.                     paréntesis y estructuras
                                                     tipadas.
```

## Página 31

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=31)

```text
La Notación do


                                                  • Las flechas <- se traducen en
  foo :: Maybe String
  foo = do                                          el operador »=
      x <- Just 3
      y <- Just " ! "
                                                  • Cada nueva línea abre una
      Just ( show x ++ y )                          lambda implícita hacia abajo.


 Conservación del Scope
 La notación do mantiene el scope de las
 variables: x e y están definidas en las lineas
 siguientes.
```

## Página 32

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=32)

```text
Mónada IO (Computación imperativa)



 Tipo monádico IO a                     putStrLn :: String -> IO ()
   • Representa una operación que,      putStrLn " hello " :: IO ()
     al ejecutarse, realiza una tarea
     con efectos secundarios
     (escribir en pantalla, leer del    IO()
     teclado, etc.).                    Una acción de I/O con resultado
   • Produce o “devuelve” un valor      unit (tipo ()).
     de tipo a al finalizar.
```

## Página 33

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=33)

```text
El Punto de Entrada: la función main



Función main                                -- Archivo : Main . hs
                                            main :: IO ()
 • Punto de entrada de todo                 main = putStrLn " hello , world "
   programa ejecutable en Haskell.
 • Tiene el tipo main :: IO a.
                                          Al compilar y ejecutar el programa, la
 • Le indica al compilador y al sistema
                                          acción imperativa se ejecuta, muestra la
   operativo dónde comienza la            cadena en la terminal y finaliza
   ejecución secuencial de los efectos    retornando ().
   secundarios.
```

## Página 34

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=34)

```text
Composición de Acciones: La Notación do

 Sintaxis do                             main :: IO ()
                                         main = do
   • Permite pegar/secuenciar                putStrLn " Hello , "
     múltiples acciones individuales         putStrLn " world ! "

     en una sola gran acción.
   • Todas las lineas del bloque
                                        El Tipo de la Composición
     deben ser del tipo IO.
   • No se pueden incluir expresiones   La acción resultante adopta el tipo de
     puras de forma directa.            la última acción de I/O del bloque.
   • Se lee de forma similar a un
     programa imperativo
     tradicional (pero no es lo
     mismo)
```

## Página 35

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=35)

```text
Expresión Pura vs. Acción con Efectos

Expresión Pura                          Acción con Efectos
            " Hola " :: String                   putStrLn " Hola " :: IO ()



 • Un dato en memoria.                   • Comando imperativo que realiza I/O.
 • Evaluación libre de efectos           • Al ejecutarse, interactúa con el
   secundarios.                            entorno.
 • No “hace” nada ni modifica el         • Retorna un valor de tipo unit ().
   contexto.

  Sistema de tipos de Haskell
  Garantiza que no se puede usar una acción IO dentro de una función pura.
  ¡Los mundos se mantienen separados!
```

## Página 36

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=36)

```text
Lectura y Desempaquetado: getLine

  getLine :: IO String

Empaquetado:                                 Operador bind <-:
 • Una acción IO a hace algo y                • name <- getLine : Ejecuta la
   produce un valor de tipo a.                  acción getLine, desempaqueta el
 • La única forma de acceder al                 resultado y lo asocia al
   valores desempaquetarlo.                    identificador name.
 • Restricción: Solo se puede                 • Como la acción name <- getLine
   extraer datos de una acción IO               es de tipo IO String, la variable
   dentro de otra acción IO.                    name tiene tipo String (puro).

  getLine es Impura
  Si se ejecuta dos veces puede producir valores distintos.
```

## Página 37

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=37)

```text
Ejemplos Prácticos de Interacción



  main :: IO ()                                  -- procesar :: String -> String
  main = do
    putStrLn " Hola , c ó mo te llam á s ? "     main :: IO ()
    nombre <- getLine                            main = do
    putStrLn ( " Hola " ++ nombre ++ " ! " )       putStrLn " Hola , c ó mo te llam á s ? "
                                                   nombre <- getLine
                                                   putStrLn ( " Hola : " ++ procesar
                                                     nombre )
Cada paso se ejecuta de forma secuencial. El
valor extraído con <- se concatena en el
último putStrLn.                               Podemos aplicar funciones puras (como
                                               procesar) al valor desempaquetado.
```

## Página 38

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=38)

```text
Errores Comunes: Mezclar Tipos Incompatibles


El contraejemplo incorrecto:
  -- CODIGO ERRONEO : No compila
  nameTag = " Hola " ++ getLine
```

## Página 39

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=39)

```text
Errores Comunes: Mezclar Tipos Incompatibles


El contraejemplo incorrecto:              ¿Por qué falla?
  -- CODIGO ERRONEO : No compila            • El operador ++ es un operador
  nameTag = " Hola " ++ getLine
                                              binario sobre String.
                                            • getLine no es una String, es
                                              una acción para obtener una
 Error de Tipos                               String.
 Haskell rechazará esto porque intenta-     • Para usar el valor debemos
 mos concatenar un String con una ac-         estar dentro de un bloque do y
 ción IO String.                              extraerlo usando <-.
```

## Página 40

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=40)

```text
Valores de Retorno y Enlaces Simbólicos



  main :: IO ()
  main = do                              Regla
    foo <- putStrLn " Hello , name ? "
    name <- getLine
    putStrLn ( " Hey " ++ name )
                                         La última acción de un bloque do no
                                         puede ligarse a un nombre usando <-
                                         .
  • Cada acción IO produce un
    resultado al ejecutarse.
                                          • El bloque do tiene como valor el
  • putStrLn retorna ().
                                            valor de la última acción.
  • Hacer un bind funciona, pero es
    irrelevante
```

## Página 41

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=41)

```text
Uso de let dentro de Bloques de I/O


import Data . Char
                                          Let vs bind:
                                            • <- ejecuta una acción IO y
main :: IO ()
main = do                                     desempaqueta el resultado.
  putStrLn " Nombre ? "
  nombre <- getLine                         • let asigna un alias a una
  putStrLn " Apellido ? "                     expresión pura. No requiere in
  apellido <- getLine
  let nombreM = map toUpper nombre            al final cuando está dentro de un
      apellidoM = map toUpper apellido        do.
  putStrLn ( " Hola " ++ nombreM ++ " "
               ++ apellidoM ++ " ! " )
                                          La Indentación Importa
                                          Expresiones dentro de let y do deben
                                          estar indentados.
```

## Página 42

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=42)

```text
Estudio de Caso: Invertir Palabras


   main :: IO ()                          • Condicional: En Haskell,
   main = do                                todo if requiere
     line <- getLine
     if null line                           obligatoriamente su else.
       then return ()
       else do                            • Bloque else: Agrupa dos
         putStrLn ( reverseWords line )     acciones (putStrLn y la
         main
                                            llamada recursiva a main)
   reverseWords :: String -> String         encapsulándolas en un
   reverseWords = unwords . map
       reverse . words                      sub-bloque do.
                                          • Bloque then: return
                                            empaqueta un valor puro en
                                            una acción IO:
                                            return :: a -> IO a
```

## Página 43

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=43)

```text
¿Qué hace return?

return es el dual de <-:                  Flujo continuo sin interrupciones:
  • return NO finaliza la ejecución        main :: IO ()
                                           main = do
    del bloque ni rompe el flujo del           return ()
    programa.                                  return " HAHAHA "
                                               line <- getLine
  • Su única función es tomar un               putStrLn line
    valor puro y envolverlo en una
    acción IO (introducirlo en una
    caja).                                Uso Técnico de return
  • return () crea una acción             Se emplea principalmente al final de un
    ficticia que no altera el mundo       bloque do cuando queremos forzar que
    real y solo entrega el valor vacío.   la macro-acción completa entregue un
                                          resultado específico en lugar del valor
                                          de su última instrucción.
```

## Página 44

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=44)

```text
Entrada y Salida: Primitivas de Datos


Primitivas esenciales:                   Ejemplo práctico:
  • putStr :: String -> IO ()             main :: IO ()
    Imprime texto sin salto de línea.     main = do
                                              putStr " Nombre : "
  • putChar :: Char -> IO ()                  line <- getLine
                                              putStr " Hola , "
    Imprime un único carácter.                putStrLn line
  • getLine :: IO String                      putStr " Inicial : "
                                              putChar ’X ’
    Lee una línea desde la terminal.
  • print :: Show a => a -> IO ()
    Aplica show e imprime con salto de
    línea.
```

## Página 45

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=45)

```text
Estructuras de Control y Combinadores


Combinadores lógicos:                      Ejemplo con iteradores:
  • when :: Bool -> IO () -> IO ()           import Control . Monad ( when ,
    Ejecuta la acción condicional                forever )
                                             import Data . Traversable ( forM )
    únicamente si el booleano es
    verdadero.                               main :: IO ()
                                             main = do
  • forever :: IO a -> IO b                      let valores = [1 , 2 , 3 , 4]
    Ejecuta una acción de forma                  forM valores (\ v -> do
                                                     when ( odd v ) ( print v ) )
    infinitamente cíclica.                       return ()
  • forM :: [a] -> (a -> IO b) -> IO [b]
    Itera sobre una lista mapeando         Nota: mapM es idéntico a forM pero toma
    acciones (estilo foreach).             la lista como su segundo argumento.
```

## Página 46

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=46)

```text
Estructuras de Control y Combinadores


Combinadores lógicos:                      Ejemplo con iteradores:
  • when :: Bool -> IO () -> IO ()          import Control . Monad ( when )
    Ejecuta la acción condicional           import Data . Traversable ( forM )
    únicamente si el booleano es            main :: IO ()
    verdadero.                              main = do
                                                let nombres = [ " Ana " , " Bo " , "
  • forever :: IO a -> IO b                     Carlos " ]
    Ejecuta una acción de forma                 forM nombres (\ n -> do
                                                    when ( length n > 2) (
    infinitamente cíclica.                      putStrLn n ) )
  • forM :: [a] -> (a -> IO b) -> IO [b]        return ()

    Itera sobre una lista mapeando
    acciones (estilo foreach).
```

## Página 47

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=47)

```text
Estado Mutable en un Mundo Puro: IORef

¿Qué es una IORef?                         • Como modificar la memoria es un
  • Es el mecanismo nativo más simple de     efecto, todas sus expresiones tiene
    Haskell para simular variables           tipo monádico IO.
    mutables tradicionales.                • Inicialización: No pueden estar
  • Funciona de forma análoga a un           vacías; se les da un valor inicial al
    puntero o una celda de memoria.          crearse.
  • Se importa desde el módulo
    Data.IORef.
  newIORef   :: a -> IO ( IORef a )
  readIORef :: IORef a -> IO a
  writeIORef :: IORef a -> a -> IO ()
```

## Página 48

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=48)

```text
Contador imperativo



  import Data . IORef

  incRef :: IORef Int -> IO ()
  incRef var = do
      val <- readIORef var
      writeIORef var ( val + 1)

  main :: IO ()
  main = do
      var <- newIORef 42
      incRef var
      val <- readIORef var
      print val -- Imprime 43
```

## Página 49

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=49)

```text
Contador imperativo



  import Data . IORef
                                  No son thread-safe:
                                   • Si múltiples hilos intentan
  incRef :: IORef Int -> IO ()
  incRef var = do                     ejecutar incRef en paralelo sobre
      val <- readIORef var
      writeIORef var ( val + 1)
                                      la misma referencia, ocurrirá
                                      una condición de carrera.
  main :: IO ()
  main = do                        • La lectura y la escritura se
      var <- newIORef 42              separan en dos pasos distintos,
      incRef var
      val <- readIORef var            rompiendo la atomicidad.
      print val -- Imprime 43
```

## Página 50

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=50)

```text
Threads en Haskell: forkIO

  • Permiten evaluar una expresión en    import Control . Concurrent
    hilo de ejecución concurrente.       main :: IO ()
  • Es manejado por el Runtime de        main = do
                                             forkIO ( putStr " Hello " )
    GHC.                                     putStr " world \ n "
  • Se importa el módulo
    Control.Concurrent.                 Asincronía y el Hilo Principal
  forkIO :: IO () -> IO ThreadId          • forkIO dispara el hilo y retorna
                                            inmediatamente sin esperar a
                                            que la acción termine.
                                          • Si el hilo principal (main)
                                            finaliza, todos los hijos mueren
                                            instantáneamente.
```

## Página 51

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=51)

```text
Demostración de Condición de Carrera con IORef

 import Control . Concurrent ( forkIO , threadDelay )
                                                        Resultado:
 import Control . Monad ( replicateM_ )                   • Esperado: 2000000.
 import Data . IORef
                                                          • Real: Un número menor
 worker :: IORef Int -> IO ()
 worker ref = replicateM_ 1000000 ( do                      y diferente en cada
    v <- readIORef ref                                      ejecución.
    writeIORef ref ( v + 1) )
                                                          • El incremento no es
 main :: IO ()
 main = do                                                  atómico.
    ref <- newIORef 0
    forkIO ( worker ref ) -- Hilo hijo 1
    forkIO ( worker ref ) -- Hilo hijo 2
    threadDelay 50000     -- Feo ( ver Async )
    final <- readIORef ref
    putStr " Resultado Final : "
    print final
```

## Página 52

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=52)

```text
Sincronización Controlada: La Librería async

Abstracción de alto nivel:              import Control . Concurrent . Async
 • Representa los hilos concurrentes
                                        main :: IO ()
    como promesas.                      main = do
                                          -- crea hilo y guarda promesa
 • Permite capturar resultados o          hilo <- async ( putStr " Hello " )
    sincronizar la finalización de        putStr " world \ n "
                                          -- Forzar la espera
    forma nativa.                         wait hilo
 • Se importa desde el módulo
    Control.Concurrent.Async.
                                       Control del Ciclo de Vida
  async :: IO a -> IO ( Async a )
  wait :: Async a -> IO a              A diferencia de forkIO, la función wait
                                       bloquea al hilo principal de forma limpia
                                       hasta que la acción concurrente termine.
```

## Página 53

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=53)

```text
Demostración de Condición de Carrera con IORef

 import Control . Concurrent . Async ( async , wait )
                                                        Resultado:
 import Control . Monad ( replicateM_ )                   • Esperado: 2000000.
 import Data . IORef
                                                          • Real: Un número menor
 worker :: IORef Int -> IO ()
 worker ref = do                                            y diferente en cada
    replicateM_ 1000000 ( do                                ejecución.
       v <- readIORef ref
       writeIORef ref ( v + 1) )                          • El incremento no es
    putStrLn " Termina worker "
                                                            atómico.
 main :: IO ()
 main = do
   ref <- newIORef 0
   t1 <- async ( worker ref ) -- Hilo hijo 1
   t2 <- async ( worker ref ) -- Hilo hijo 2
   wait t1
   wait t2
   final <- readIORef ref
   putStr " Resultado Final : "
   print final
```

## Página 54

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=54)

```text
STM y el Tipo IO

Quisiéramos poder definir una función:
  atomic :: IO a -> IO a



Para usarla de la siguiente forma:
  main = do
    forkIO ( atomic ( putStr " Hello " ) )
    atomic ( putStr " world \ n " )
```

## Página 55

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=55)

```text
STM y el Tipo IO

Quisiéramos poder definir una función:       Sin embargo, consideremos este
  atomic :: IO a -> IO a                     escenario:
                                               main = do
                                                   r <- newIORef 0
Para usarla de la siguiente forma:                 forkIO ( atomic ( incRef r ) )
                                                   ( incRef r )
  main = do
    forkIO ( atomic ( putStr " Hello " ) )
    atomic ( putStr " world \ n " )          El Sistema de Tipos
                                             El sistema de tipos no puede garanti-
                                             zar que las referencias se utilicen ex-
                                             clusivamente dentro de transacciones.
                                             Cualquier hilo podría modificar r por
                                             fuera usando writeIORef.
```

## Página 56

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=56)

```text
Tipos para Atomicidad (STM)

 data STM a -- abstracto
 data TVar a -- abstracto

 instance Monad STM

 newTVar   :: a -> STM ( TVar a )
 readTVar :: TVar a -> STM a
 writeTVar :: TVar a -> a -> STM ()

 atomically :: STM a -> IO a


 • Se introduce el tipo monádico STM y
   variables transaccionales
   independientes (TVar).
 • Las variables TVar solo pueden
   modificarse dentro de transacciones.
```

## Página 57

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=57)

```text
Tipos para Atomicidad (STM)

 data STM a -- abstracto
                                          Garantía de tipos:
 data TVar a -- abstracto                  • Como readTVar y writeTVar
 instance Monad STM                           devuelven acciones tipo STM, es
 newTVar   :: a -> STM ( TVar a )
                                              imposible usarlas por fuera de una
 readTVar :: TVar a -> STM a                  transacción.
 writeTVar :: TVar a -> a -> STM ()
                                           • atomically actúa como el único
 atomically :: STM a -> IO a                  puente al tipo monádico IO.
 • Se introduce el tipo monádico STM y
   variables transaccionales
   independientes (TVar).
 • Las variables TVar solo pueden
   modificarse dentro de transacciones.
                                                      Control.Concurrent.STM
```

## Página 58

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=58)

```text
Contador atómico STM


 import Control . Concurrent          • El bloque do dentro de incT tiene tipo
 import Control . Concurrent . STM      STM a.
 -- Incremento At ó mico              • La función atomically toma algo del
 incT :: TVar Int -> IO ()
 incT r = atomically ( do               tipo STM y lo ejecuta de forma
   v <- readTVar r                      atómica.
   writeTVar r ( v + 1) )

 main :: IO ()
 main = do
   r <- atomically ( newTVar 0)
   forkIO ( incT r )
   incT r
   val <- atomically ( readTVar r )
   print val
```

## Página 59

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=59)

```text
Contador atómico STM

                                              Resultado:
incT :: TVar Int -> IO ()                       • Esperado: 2.000.000.
incT r = atomically ( do
  v <- readTVar r                               • Real: Exactamente 2.000.000
  writeTVar r ( v + 1) )
                                                  de forma determinista.
worker :: TVar Int -> IO ()
worker ref = do                                 • Cada llamada a incT se ejecuta de
   replicateM_ 1000000 ( incT ref )
                                                  forma atómica.
main :: IO ()
main = do
                                                • Si hay colisión, el motor
  ref <- atomically ( newTVar 0)                  transaccional detecta la invalidez
  t1 <- async ( worker ref ) -- Hilo hijo 1
  t2 <- async ( worker ref ) -- Hilo hijo 2
                                                  del log local, descarta los cambios y
  wait t1                                         reintenta la operación.
  wait t2
  final <- atomically ( readTVar ref )
  putStr " Resultado Final : "
  print final
```

## Página 60

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=60)

```text
Composicionalidad



 -- ( reutilizable )                                    • Atomicidad: Las dos llamadas a
 incTransaccional :: TVar Int -> STM ()     incTransaccional en incT2 se
 incTransaccional r = do
     v <- readTVar r                                      ejecutan dentro del mismo
     writeTVar r ( v + 1)                                 atomically.
 -- Composicion de dos incrementos                      • Todo el bloque se valida y se impacta
 incT2 :: TVar Int -> IO ()                               en la memoria.
 incT2 r = atomically ( do
   incTransaccional r                     • Ningún hilo concurrentes puede ver un
   incTransaccional r )
                                                          estado intermedio donde la variable se
                                                          haya incrementado una vez.
```

## Página 61

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=61)

```text
Incremento transaccional


  import Control . Concurrent
                                       • La función incT define una
  import Control . Concurrent . STM      secuencia de operaciones dentro
  incT :: TVar Int -> STM ()             de la mónada STM.
  incT r = do                          • No es atómica por sí misma;
    v <- readTVar r
    writeTVar r ( v + 1)                 puede combinarse con otros
                                         cómputos transaccionales para
  main :: IO ()                          formar bloques más complejos.
  main = do
    r <- atomically ( newTVar 0)       • Es la función atomically la que
    forkIO ( atomically ( incT r ) )     toma la receta de tipo STM y la
    atomically ( incT r )
    val <- atomically ( readTVar r )     ejecuta de forma efectivamente
    print val
                                         atómica y aislada frente a los
                                         demás hilos concurrentes.
```

## Página 62

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=62)

```text
Cuenta bancaria como STM

  Diseño: Cuentas como Variables
    • Cada cuenta se modela como una variable (TVar Double).
    • Las funciones describen cómo mutar el estado, pero no aplican los cambios.

  extraer :: TVar Double -> Double -> STM ()
  extraer cta n = do
      saldo <- readTVar cta
      writeTVar cta ( saldo - n )

  depositar :: TVar Double -> Double -> STM ()
  depositar cta n = do
      saldo <- readTVar cta
      writeTVar cta ( saldo + n )

  transferir :: TVar Double -> TVar Double -> Double -> STM ()
  transferir de en n = do
      extraer de n
      depositar en n
```

## Página 63

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=63)

```text
Haciendo una transferencia



   main :: IO ()
   main = do
       a <- atomically ( newTVar 10) -- crea cuenta a
       b <- atomically ( newTVar 5)    -- crea cuenta b
       atomically ( transferir a b 4) -- transfiere at ó micamente
       sa <- atomically ( readTVar a )
       putStrLn ( show sa )


   • Imposibilidad de deadlock STM maneja un log local. Si hay conflicto, la
     transacción simplemente se aborta y se reintenta.
```

## Página 64

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=64)

```text
Control de Condiciones: El Operador retry


Operador retry :: STM a
 • Al ejecutar retry, la transacción
   se aborta inmediatamente y se
   reintenta más tarde.
 • Reintentar de forma inmediata
   sería ineficiente.
 • La implementación extrae del log
   de lectura las dependencias y lo
   despertierta cuando otra
   transacción concurrente modifique
   las dependencias.
```

## Página 65

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=65)

```text
Control de Condiciones: El Operador retry


Operador retry :: STM a                Control de saldo suficiente:
 • Al ejecutar retry, la transacción     extraerSi :: TVar Double -> Double ->
   se aborta inmediatamente y se             STM ()
                                         extraerSi cta n = do
   reintenta más tarde.                    saldo <- readTVar cta
                                           if saldo < n
 • Reintentar de forma inmediata             then retry
   sería ineficiente.                        else writeTVar cta ( saldo - n )

 • La implementación extrae del log
   de lectura las dependencias y lo
   despertierta cuando otra
   transacción concurrente modifique
   las dependencias.
```

## Página 66

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=66)

```text
La Función check



 Como el patrón de verificar una condición booleana es muy común, se define
 check:
   check :: Bool -> STM ()
   check True = return ()
   check False = retry

   extraerSi cta n = do
       saldo <- readTVar cta
       check ( n <= saldo )
       writeTVar cta ( saldo - n )
```

## Página 67

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=67)

```text
Ejemplo de Sincronización con Retardo


   depositoRetardo cta monto = do
         threadDelay 3000000
         putStrLn " Depositando ! "
         atomically ( do
               saldo <- readTVar cta
               writeTVar cta ( saldo + monto ) )

   mainRetardo = do
       cta <- atomically ( newTVar 100)
       putStrLn " Preparando deposito ... "
       forkIO ( depositoRetardo cta 10)
       putStrLn " Tratando de retirar ... "
       atomically ( extraerSi cta 101)
       putStrLn " Retire ! "
```

## Página 68

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=68)

```text
Composición Alternativa con orElse



  • orElse a1 a2 intenta ejecutar        Extracción alternativa:
    primero la acción a1.                  extraerAlt :: TVar Double -> TVar
  • Si a1 invoca un retry, sus efectos          Double -> Double -> STM ()
                                           extraerAlt cta1 cta2 monto =
    locales se descartan e intenta           orElse ( extraerSi cta1 monto )
    inmediatamente con a2.                          ( extraerSi cta2 monto )

  • Si a2 también reintenta, la
    composición duerme al hilo hasta
    que cambie alguna de las
    dependencias de alguna de las
    ramas.
```

## Página 69

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=69)

```text
Composición Alternativa y Orquestación con orElse


   extraerAlt :: TVar Double -> TVar Double -> Double -> STM ()
   extraerAlt cta1 cta2 monto =
     orElse ( extraerSi cta1 monto )
            ( extraerSi cta2 monto )

   main :: IO ()
   main = do
       cta1 <- newTVarIO 100
       cta2 <- newTVarIO 200
       atomically ( extraerAlt cta1 cta2 150)
       s1 <- readTVarIO cta1
       s2 <- readTVarIO cta2
       putStrLn ( " Saldos . cta1 : " ++ show s1 ++ " cta2 : " ++ show s2 )
```

## Página 70

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=70)

```text
Garantía de Tipos en STM
   -- Error de compilaci ó n por uso de conEfecto
   conEfecto :: IO ()
   conEfecto = putStr " Todo bien "

   main :: IO ()
   main = do
     x <- atomically   ( newTVar 2)
     y <- atomically   ( newTVar 1)
     atomically ( do
       a <- readTVar   x
       b <- readTVar   y
       if a > b then   conEfecto else return () )


  Tipado
    • El sistema de tipos detecta que conEfecto es de tipo IO ().
    • No es imposible inyectar una acción con efectos en un bloque monádico STM a.
    • La mónada STM no tiene efectos IO.
```

## Página 71

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=71)

```text
Caso de Estudio: Un Buffer Transaccional

  • Un buffer es una referencia a una
    lista.
  • put agrega un elemento al final.
  • get es bloqueante cuando el
    buffer es vacío. La función retry
    suspende al hilo consumidor hasta
    que un productor altere el estado
    con un put.
```

## Página 72

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=72)

```text
Caso de Estudio: Un Buffer Transaccional

  • Un buffer es una referencia a una   type Buf a = TVar [ a ]
    lista.                              newBuf   = newTVar []
  • put agrega un elemento al final.
                                        put :: Buf a -> a -> STM ()
  • get es bloqueante cuando el         put b x = do
                                          xs <- readTVar b
    buffer es vacío. La función retry     writeTVar b ( xs ++ [ x ])
    suspende al hilo consumidor hasta
                                        get :: Buf a -> STM ( a )
    que un productor altere el estado   get b = do
    con un put.                           xs <- readTVar b
                                          case xs of
                                            []         -> retry
                                            ( x : xs ) -> do
                                                        writeTVar b xs
                                                        return x
```

## Página 73

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=73)

```text
Productores y Consumidores sobre el Buffer

   writer :: Buf Int -> IO ()
   writer b = wloop 1000
     where
       wloop 0 = return ()
       wloop n = do atomically ( put b n )
                    wloop (n -1)

   reader :: Buf Int -> IO ()
   reader b = rloop
     where
       rloop = do v <- atomically ( get b )
                  putStrLn ( " Consumo " ++ show v )
                  rloop

   main :: IO ()
   main = do
     b <- atomically newBuf
     writer b
     t <- async ( reader b )
     wait t
```

## Página 74

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=74)

```text
Algunas clases de STM




      Estructura   Capacidad         esperan
      TVar         Ninguno (Celda)   No
      TQueue       Ilimitada         readTQueue (vacía)
      TBQueue      Acotada           read (vacía) / write (llena)
      TMVar        Exactamente 1     take (vacía) / put (llena)
      TChan        Ilimitada         readTChan (vacía)
      TArray       Fija Indexada     No
```

**Información gráfica:** [consultar el diagrama o tabla de la página 74](teorica-memoria-transaccional-por-software.pdf#page=74). Las flechas, posiciones, colores y marcas no se reconstruyen completamente con texto plano.

## Página 75

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=75)

```text
El Problema del Barbero Durmiente



   • El Barbero: Ejecuta un bucle infinito de atención. Si la sala está vacía, se
     sienta en su silla y se duerme profundamente.
   • La Sala de Espera: Cuenta con un número estrictamente limitado de N
     asientos para los clientes.
   • Los Clientes: Al llegar, inspeccionan la barbería. Si el barbero duerme, lo
     despiertan para el corte. Si está ocupado pero hay sillas, se sientan. Si no
     hay lugar, se retiran de inmediato.
```

## Página 76

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=76)

```text
El Barbero en STM


   import Control . Concurrent ( forkIO , threadDelay )
   import Control . Concurrent . Async ( async , wait )
   import Control . Concurrent . STM

  data Barberia = Barberia {
      sillasLibres :: TVar Int ,
      capacidadMax :: Int ,
      colaEspera   :: TQueue String
  }

   nuevaBarberia :: Int -> STM Barberia
   nuevaBarberia n = do
       libres <- newTVar n
       cola   <- newTQueue
       return ( Barberia libres n cola )
```

## Página 77

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=77)

```text
Lógica de Sincronización del Barbero y Clientes

   entrar :: Barberia -> String -> STM Bool
   entrar b cliente = do
       libres <- readTVar ( sillasLibres b )
       if libres == 0
           then return False
           else do
               writeTVar ( sillasLibres b ) ( libres - 1)
               writeTQueue ( colaEspera b ) cliente
               return True

   cliente :: Barberia -> String -> IO ()
   cliente b nombre = do
       sentado <- atomically ( entrar b nombre )
       if sentado
           then putStrLn ( " Cliente " ++ nombre ++ " : Sentado a esperar . " )
           else putStrLn ( " Cliente " ++ nombre ++ " : Barberia llena , me voy . "
       )
```

## Página 78

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=78)

```text
El Ciclo de Trabajo Concurrente
   atender :: Barberia -> STM String
   atender b = do
       cliente <- readTQueue ( colaEspera b )
       libres <- readTVar ( sillasLibres b )
       writeTVar ( sillasLibres b ) ( libres + 1)
       return cliente

   barbero :: Barberia -> IO ()
   barbero b = bloop
     where
       bloop = do
         cliente <- atomically ( atender b )
         putStrLn ( " Barbero : Cortando el pelo a " ++ cliente )
         threadDelay 1500000 -- Tiempo del corte de pelo
         bloop

   main :: IO ()
   main = do
       b <- atomically ( nuevaBarberia 3) -- Barberia con 3 sillas de espera
       t <- async ( barbero b ) -- Arranca el barbero ( puede empezar durmiendo )
       mapM ( async . cliente b ) [ " Juan " , " Ana " , " Pedro " , " Susana " , " Fran " ]
       wait t
```

## Página 79

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=79)

```text
Fumadores de Cigarrillos




  • Tres Fumadores: Cada uno posee un suministro infinito de un único
    ingrediente indispensable: Tabaco, Papel o Fósforos. Para fumar, un
    individuo necesita reunir los tres elementos de forma simultánea.
  • El Agente: Coloca al azar dos ingredientes diferentes sobre la mesa
    compartida. El fumador al que le falte exactamente esa combinación debe
    tomarlos de inmediato, armar su cigarrillo y fumar.
```

## Página 80

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=80)

```text
Problema de los Fumadores: Estructura de datos


   data Mesa =   Mesa {
       tabaco    :: TVar Bool ,
       papel     :: TVar Bool ,
       fosforo   :: TVar Bool
   }

   nuevaMesa :: STM    Mesa
   nuevaMesa = do
       t <- newTVar    False
       p <- newTVar    False
       f <- newTVar    False
       return ( Mesa   t p f)
```

## Página 81

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=81)

```text
Problema de los Fumadores: Fumadores

esperarIngredientes   :: Mesa -> TVar Bool -> TVar Bool -> STM ()
esperarIngredientes   mesa ing1 ing2 = do
    disponibles1 <-   readTVar ing1
    disponibles2 <-   readTVar ing2

    -- Dormir si no estan
    check ( disponibles1 && disponibles2 )

    -- Limpiar mesa
    writeTVar ing1 False
    writeTVar ing2 False

fumador :: String -> Mesa -> ( Mesa -> TVar Bool ) -> ( Mesa -> TVar Bool ) -> IO ()
fumador n m ing1 ing2 = floop
  where
    floop = do
      atomically ( esperarIngredientes m ( ing1 m ) ( ing2 m ) )
      putStrLn ( " Fumador " ++ n )
      threadDelay 1000000 -- Tiempo fumando
      floop
```

## Página 82

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=82)

```text
Problema de los Fumadores: Agente

   proveer :: Mesa -> ( Mesa -> TVar Bool ) -> ( Mesa -> TVar Bool ) -> STM ()
   proveer mesa ing1 ing2 = do
       t <- readTVar ( tabaco mesa )
       p <- readTVar ( papel mesa )
       f <- readTVar ( fosforo mesa )
       check ( not t && not p && not f )
       writeTVar ( ing1 mesa ) True
       writeTVar ( ing2 mesa ) True

   agente :: String -> Mesa -> ( Mesa -> TVar Bool ) -> ( Mesa -> TVar Bool ) ->
       IO ()
   agente n m ing1 ing2 = aloop
     where
       aloop = do
         do
            atomically ( proveer m ing1 ing2 )
            putStrLn $ " \ nAgente : " ++ n
            threadDelay 10000 -- Tiempo fumando
            aloop
```

## Página 83

[Ver página original](teorica-memoria-transaccional-por-software.pdf#page=83)

```text
Problema de los Fumadores: Principal



   main :: IO ()
   main = do
     m <- atomically nuevaMesa
     async ( fumador " tabaco y papel " m tabaco papel )
     async ( fumador " papel y fosforo " m papel fosforo )
     async ( fumador " tabaco y fosforo " m tabaco fosforo )
     async ( agente " tabaco y papel " m tabaco papel )
     async ( agente " papel y fosforo " m papel fosforo )
     t <- async ( agente " tabaco y fosforo " m tabaco fosforo )
     wait t
```
