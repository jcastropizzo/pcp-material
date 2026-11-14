# Guía 08 — resolución de estudio

[Enunciado](guia-08-computo-paralelo.pdf) · [Transcripción](guia-08-computo-paralelo.transcripcion.md)

W es work, S es span y W/S es paralelismo. Se supone coste constante de cada fork/join y de las operaciones elementales.

## 1. Cruce de rendimiento

Sea ΔS = S′−S. La estimación da T′ₚ−Tₚ ≈ ΔS−1024/P. Con 32 procesadores queremos ΔS < 32 y con 512 queremos ΔS > 2. El intervalo es **2 < ΔS < 32**. Los extremos representan empates en la estimación.

## 2. Cota greedy

Sean c los pasos completos y k los incompletos. Cada completo ejecuta P operaciones y cada incompleto al menos una, por lo que Pc+k ≤ W. Un paso incompleto ejecuta todos los nodos listos y reduce al menos una unidad el camino crítico restante; así k ≤ S. Entonces T=c+k ≤ W/P +(1−1/P)k ≤ (W−S)/P+S.

## 3. Diez procesadores

T₆₄=10 implica S≤10. La cota refinada aplicada a T₄=80 exige W+3S≥320 y la ley de trabajo da W≤320. Para diez procesadores, T₁₀≤W/10+0,9S≤32+9=**41 pasos**.

## 4. Suma de vector

```text
suma(A, lo, hi):                    # [lo, hi)
    si hi - lo == 1: devolver A[lo]
    m = (lo + hi) // 2
    izq = fork suma(A, lo, m)
    der = suma(A, m, hi)
    devolver join(izq) + der
```

W(n)=2W(n/2)+Θ(1)=Θ(n); S(n)=S(n/2)+Θ(1)=Θ(log n). Paralelismo Θ(n/log n) para n≥2.

## 5. Potenciación por cuadrados

```text
power(x, c):
    si c == 0: devolver 1
    mitad = power(x, c // 2)
    cuadrado = mitad * mitad
    si c es impar: devolver x * cuadrado
    devolver cuadrado
```

Hay una sola llamada por nivel. Su resultado alimenta el cuadrado y, si corresponde, la multiplicación final. El DAG de operaciones dependientes es una cadena con Θ(log(c+1)) operaciones para c≥1; W y S tienen esa misma cota y el paralelismo es Θ(1). Un fork seguido por un join sin otra tarea disponible no crea trabajo independiente.

## 6. Transposición

Cada llamada recibe coordenadas (i,j) y lado m del bloque fuente. Para m=1 escribe B[j][i]=A[i][j]. Para m>1, h=m/2, ejecuta en fork-join llamadas sobre (i,j), (i+h,j), (i,j+h) e (i+h,j+h), todas de lado h. El mapeo (i,j)→(j,i) es biyectivo, por lo que las ramas escriben en cuadrantes de destino disjuntos y solo leen A. W(n)=4W(n/2)+Θ(1)=Θ(n²); S(n)=S(n/2)+Θ(1)=Θ(log n); paralelismo Θ(n²/log n).

## 7. Mergesort con fusión secuencial

W(n)=2W(n/2)+Θ(n)=Θ(n log n). S(n)=S(n/2)+Θ(n)=Θ(n), porque las mitades se ordenan concurrentemente pero ambas preceden la fusión. Paralelismo Θ(log n).

## 8. Fusión por mediana

**a)** Si la mediana vale v, la búsqueda en el otro segmento cuenta cuántos elementos de ese segmento son menores a v. La distancia de la mediana al comienzo de su segmento cuenta los restantes menores. La suma da su rango exacto en la unión. Las ramas leen subconjuntos de los segmentos de entrada, colocan los menores antes de esa posición y los mayores después, y escriben rangos de salida disjuntos. Usar un destino diferente de los rangos de lectura. Inducción sobre el número de elementos, con base vacía, prueba que la salida queda ordenada y conserva sus elementos.

**b)** Sea n el total y m el número de menores de la mediana. Entonces m y n−1−m son aproximadamente n/2. W(n)=W(m)+W(n−1−m)+Θ(log n)=Θ(n); el coste por niveles suma una progresión ponderada y hay Θ(n) pivotes. S(n)=max(S(m),S(n−1−m))+Θ(log n)=Θ(log² n). Paralelismo Θ(n/log² n).

**c)** Con esa fusión, mergesort tiene W(n)=2W(n/2)+Θ(n)=Θ(n log n) y S(n)=S(n/2)+Θ(log² n)=Θ(log³ n). Paralelismo Θ(n/log² n). En cada nivel del camino crítico, la fusión comienza después de las dos ordenaciones hijas.
