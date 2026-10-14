# guia 04a haskell io y monadas — resumen

Fuente: [guia-04a-haskell-io-y-monadas.pdf](guia-04a-haskell-io-y-monadas.pdf). [Transcripción completa](guia-04a-haskell-io-y-monadas.transcripcion.md).

Síntesis editorial para estudio; el original prevalece ante diferencias.

## Qué aporta
Preparación de Haskell para STM: programación pura, tipos y composición de acciones. Es un complemento previo a la guía 5, no una guía de concurrencia independiente.

## Recorrido de ejercicios
1: listas recursivas; 2: alto orden (`map`, `filter`, `foldr`); 3: árboles de búsqueda; 4: funciones parciales con `Maybe`; 5: composición con `Maybe` y `Either`; 6: listas como mónada; 7: combinadores monádicos; 8: programas interactivos; 9: acciones IO y orden de ejecución; 10: juego de adivinación. Consultar los incisos exactos en la transcripción; no confundir una acción construida con una acción ejecutada.

## Núcleo para estudiar
- Separar valores puros de acciones `IO a`; `do` secuencia acciones y `<-` liga su resultado.
- `return` introduce un valor en el contexto: no corta la ejecución como en Java.
- Conocer `mapM`, `sequence`, `>>=` y el comportamiento de falla del contexto elegido.
- Una acción infinita o que no produce resultados no se caracteriza solo por su tipo.

## Uso recomendado
Resolver lo que impida leer la práctica de STM. Las soluciones están separadas para poder intentar primero los ejercicios.

## Soluciones separadas

[Desarrollo de los ejercicios](guia-04a-haskell-io-y-monadas_resuelto.md) — elaboración de estudio, no oficial.
