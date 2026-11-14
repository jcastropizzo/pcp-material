# Práctica 08 — desarrollo de estudio

[Diapositivas](practica-08-analisis-de-algoritmos-paralelos.pdf) · [Guía resuelta](../practicas-de-ejercicios/guia-08-computo-paralelo_resuelto.md)

Sea n el lado de la matriz o la longitud de entrada. Para partir rangos en fork-join se supone que la división y combinación estructural tienen coste constante y que los subproblemas se ejecutan sobre rangos disjuntos de salida.

## Producto matriz por vector

Para cada fila i se calcula y[i]=Σⱼ A[i][j] x[j]. Una bifurcación recursiva del rango de filas produce n tareas, cada una con una suma secuencial de n términos. El work es Θ(n²) y el span Θ(log n+n)=Θ(n). El paralelismo es Θ(n).

En cada tarea de fila, reemplazar el acumulador secuencial por una reducción binaria sobre columnas. El caso base calcula A[i][j]x[j]; cada nodo espera las dos mitades y suma. El work de fila sigue siendo Θ(n), el span baja a Θ(log n), y con la bifurcación de filas el span total conserva Θ(log n). El paralelismo resulta Θ(n²/log n).

## Sistema triangular inferior

Resolver en orden creciente i. Calcular en paralelo el producto escalar de L[i,0..i−1] con x[0..i−1], después escribir x[i]=(b[i]−suma)/L[i][i]. Suponer diagonal no nula, y tomar suma cero en la primera fila. Cada x[i] está disponible antes de que se compute el producto escalar de la fila siguiente. La suma del work de filas da Θ(n²). La cadena de n filas con reducciones de span Θ(log(i+1)) da Θ(n log n) de span y Θ(n/log n) de paralelismo para este algoritmo.

## Factorización LU sin pivoteo

Para cada k, calcular en paralelo los multiplicadores mᵢ=A[i][k]/A[k][k] para i>k y actualizar A[i][j]←A[i][j]−mᵢA[k][j] para j>k. Reservar A[i][k] para el multiplicador. Suponer que cada pivote es distinto de cero. Las filas leen la fila pivote y escriben sus propias celdas; debe haber join antes de pasar al siguiente pivote. La diagonal unitaria de L es implícita y la parte superior guarda U.

Con m=n−k−1 filas de longitud m, cada etapa cuesta Θ(m²) de work. Si solo se bifurcan las filas, el camino de cada etapa cuesta Θ(m+log(m+1)), por lo que el total es W=Θ(n³), S=Θ(n²) y W/S=Θ(n). Si también se bifurca el rango de columnas una vez que su multiplicador está disponible, cada etapa cuesta Θ(log(m+1)) de span. Se conserva W=Θ(n³) y el span queda Θ(n log n), con paralelismo Θ(n²/log n).

## Quicksort con partición secuencial

Particionar el rango con el pivote, después ordenar en paralelo ambos rangos resultantes sin volver a incluir el pivote. Con particiones balanceadas, W(n)=2W(n/2)+Θ(n), S(n)=S(n/2)+Θ(n); los órdenes son Θ(n log n) y Θ(n), respectivamente. La partición precede a ambas llamadas y aporta al camino crítico. Con particiones extremas repetidas, solo queda una rama significativa y tanto work como span suben a Θ(n²). Revisar la hipótesis sobre el resultado de la partición: elegir por posición el elemento del medio no garantiza balance de valores.
