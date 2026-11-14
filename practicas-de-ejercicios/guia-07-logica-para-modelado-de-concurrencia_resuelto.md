# Guía 07 — resolución de estudio

[Enunciado](guia-07-logica-para-modelado-de-concurrencia.pdf) · [Transcripción](guia-07-logica-para-modelado-de-concurrencia.transcripcion.md)

Usamos G (siempre), F (eventualmente), X (siguiente), U (hasta fuerte) y W (hasta débil). En fórmulas de estado, un deadlock se prolonga repitiendo el último estado. Fair choice exige que una transición saliente de un estado recurrente se tome infinitas veces. Los productos se dan mediante reglas generadoras desde el estado inicial.

## 1. Incremento

Estado (pc₁, pc₂, x, loc₁, loc₂); inicial (1,1,0,⊥,⊥). Para cada i, desde pc=1 la acción leerᵢ pone locᵢ=x y pcᵢ=2; desde pc=2, escribirᵢ pone x=locᵢ+1 y pcᵢ=fin. El otro contador de programa y su variable local se conservan. Caminos que leen ambos antes de cualquier escritura terminan con x=1. Los dos caminos que completan un incremento antes de comenzar el otro terminan con x=2.

## 2. Locks invertidos

Estado (pcₚ,pc_q, dueño(a),dueño(b)); inicialmente ambos en su primera instrucción, con locks libres. Cada acción lock avanza solo con el recurso libre y lo marca propio. Cada unlock libera un recurso propio y avanza; usar avanza sin cambiar los locks. El camino p1,q1 (o su permutación) es el más corto al deadlock: ambos segundos locks están ocupados.

- **Retirada.** Cambiar p2 por tryLock(b); si falla, p libera a antes de reintentar. Ante espera de q por a, si p posee a, p tiene un tryLock disponible o un paso de liberación. Con b tomado por p, p puede completar su uso y liberar. Si los recursos están libres, algún primer lock está habilitado.
- **Orden común.** q también toma a antes que b y libera en orden inverso. Quien tiene a puede tomar b o ya lo posee; el otro no puede poseer b mientras espera a. Así no hay estado intermedio sin transición. Los dos threads terminados constituyen una terminación normal.

## 3. Capacidad del canal

PCs de cada participante enumeran sus dos operaciones más fin. Con capacidad cero, un envío y su recepción correspondiente avanzan juntos. Las primeras acciones son dos envíos, así que el estado inicial está bloqueado. Con capacidad uno, agregar un estado vacío o lleno por canal. Desde vacío, enviar marca lleno y avanza el emisor; desde lleno, recibir marca vacío y avanza el receptor. Cada canal tiene un único envío, siempre habilitado hasta realizarse. Después de ambos envíos están habilitadas las recepciones pendientes. El único estado sin pasos es el final.

## 4. Evaluación sobre trazas

| Traza | GF q | FG p | p U q | G(p → F q) | XX q | G(p U q) |
| --- | --- | --- | --- | --- | --- | --- |
| σ₁ | No | Sí | Sí | No | Sí | No |
| σ₂ | Sí | No | Sí | Sí | No | Sí |
| σ₃ | Sí | Sí | Sí | Sí | Sí | No |
| σ₄ | Sí | No | No | Sí | Sí | No |

En σ₃, la posición vacía después de q corta p U q desde allí. En σ₄, ese corte también ocurre antes de la primera aparición de q. En σ₁, el sufijo constante con p refuta toda obligación de llegar a q desde ese sufijo.

## 5. Equivalencias

- **a) Falsa.** Alternar {φ} y {ψ} satisface ambos F pero nunca su conjunción. La implicación de izquierda a derecha sí vale.
- **b) Verdadera.** Para cada posición futura deben valer ambas fórmulas, equivalente a exigir cada una en todas las posiciones futuras.
- **c) Verdadera.** Ambas expresan que hay una posición con φ de índice al menos uno a partir de la posición actual.
- **d) Falsa.** Si φ vale solo en la primera posición y ψ nunca, la implicación izquierda es vacua y la derecha falla. La derecha implica la izquierda: infinitas apariciones de φ con una ψ futura para cada una requieren infinitas ψ.
- **e) Verdadera.** La izquierda equivale a F(¬φ ∨ ψ). En la primera posición con ¬φ ∨ ψ, todas las anteriores satisfacen φ. En la otra dirección, el testigo de U satisface ¬φ ∨ ψ.

## 6. Satisfacibilidad y validez

| Inciso | Satisfacible | Válida | Justificación |
| --- | --- | --- | --- |
| a | Sí | No | a solo en posición 2 (desde 0) refuta. |
| b | Sí | Sí | El antecedente provee una a en alguna posición a partir de 1. |
| c | Sí | Sí | G a implica a en la posición siguiente. |
| d | Sí | No | Si b solo vale al inicio y a nunca, el antecedente tiene testigo inmediato y a U F b falla en el sufijo siguiente. |
| e | Sí | No | Una posición inicial sin a ni b seguida de b refuta. |

La traza donde a y b valen siempre satisface todos los incisos.

## 7. Propiedades del lock

Sea cᵢ = criticalᵢ, rᵢ = requestᵢ y eᵢ el evento de entrada, expresado desde la posición anterior mediante ¬cᵢ ∧ X cᵢ. Para (c) y (g), el pedido nuevo es el flanco ¬rᵢ ∧ X rᵢ, con una obligación inicial adicional si rᵢ vale al inicio antes de haber entrado. Tratar el flanco por separado evita reactivar el pedido durante la liberación del mismo turno.

- **a)** G ¬(c₁ ∧ c₂). Safety.
- **b)** ∧ᵢ G(cᵢ → F ¬cᵢ). Liveness.
- **c)** ∧ᵢ G((¬rᵢ ∧ X rᵢ) → X F cᵢ). Liveness.
- **d)** ∧ᵢ G(eᵢ → X(¬eᵢ W eⱼ)), j≠i. Safety: dos entradas del mismo thread sin una del otro dan un prefijo malo. Si se exige que ambos siempre sigan entrando, agregar GF e₁ ∧ GF e₂ como componente de liveness.
- **e)** GF(¬r₁ ∧ X r₁) → GF e₁. Liveness.
- **f)** FG r₁ → GF e₁. Liveness. Distingue pedido sostenido de infinitos flancos de pedido.
- **g)** Definir Q = (¬e₂ W c₁) y P = ¬e₂ W (c₁ ∨ (e₂ ∧ X Q)). Exigir G((¬r₁ ∧ X r₁) → X P). Hasta la entrada del thread 1 se permite como máximo un flanco e₂. Safety.

## 8. Operadores derivados

- **Próximo:** X(¬ψ W (ψ ∧ φ)), para la próxima aparición estrictamente posterior y con verdad vacua si nunca aparece. Con existencia obligatoria, usar U; para incluir la posición actual, quitar X.
- **Mientras:** φ W ¬ψ. Exige φ en el prefijo durante el cual ψ sigue siendo cierta.
- **Antes:** ¬ψ W (φ ∧ ¬ψ). Si hay una primera ψ, exige una φ en una posición estrictamente anterior.

## 9. Lock y pasos externos

G(held → F ¬held) ∧ G(¬held → F held), equivalente a GF held ∧ GF ¬held. Tolera pausas finitas entre cambios. Si el otro thread acapara para siempre el scheduler, la alternancia puede fallar: para exigirla en el producto hace falta descartar la inanición del participante del lock.

## 10. TryLock y backoff

Usar I=IDLE, T=TRYING, C=CRITICAL y B=BACKOFF. La única componente terminal incluye los cuatro estados.

| Fórmula | Sin fair choice | Con fair choice |
| --- | --- | --- |
| G(T → F C) | Falla en I(TB)ω | Vale: se visita C |
| G(T → F(C∨I)) | Falla en I(TB)ω | Vale: se visitan C e I |
| G(B → G ¬C) | Falla en ITBTC | Falla: se puede continuar el prefijo de forma justa |
| G(B → F T) | Vale: desde B se va a T o a I y luego a T | Vale |

## 11. Observador de recurso

Dos estados normales, Libre (inicial) y Tomado. Libre con acquire va a Tomado; con use va a Error. Tomado con use permanece; con release vuelve a Libre y con acquire va a Error. Error absorbe. Si se verifican solo las dos prohibiciones pedidas, Libre con release permanece en Libre. Si además se exige que release requiera posesión, va a Error.

Con las proposiciones a=acquire, u=use, r=release y una acción por paso, una fórmula para las dos prohibiciones es (¬u W a) ∧ G(a → X(¬a W r)) ∧ G(r → X(¬u W a)). Bloquea usos antes de la primera adquisición o después de liberar y bloquea dos adquisiciones consecutivas.

## 12. Bandera y desempate

**a)** Estado (pc₀,pc₁,y₀,y₁,s), inicial (N,N,0,0,1). N incluye el tramo no crítico. N→W activa yᵢ y pone s=i atómicamente. W→C requiere ¬yⱼ ∨ s=i. C→N libera yᵢ. Se supone que los tramos no crítico y crítico completan en un número finito de pasos locales.

**b)** G ¬(C₀ ∧ C₁) y ∧ᵢ G(Wᵢ → F Cᵢ).

**c)** Exclusión falla: 0 pone su bandera y entra con 1 aún en N, luego 1 pone su bandera y s=1 y entra mientras 0 sigue en C. El prefijo puede continuarse con una ejecución justa. Inanición también falla sin fair choice: dejar a 0 en W y repetir el ciclo completo de 1. Bajo fair choice, con i fijo en W el otro debe volver a N (o quedarse trabado en W con s=i, caso que habilita a i). Si el otro sigue ciclando, el estado con él en N y yⱼ=0 reaparece y obliga a tomar Wᵢ→Cᵢ. Así se recupera progreso, aunque sigue fallando exclusión.

## 13. Turnos con tickets

**a)** Inicial (N,N,0,0). Nᵢ→Wᵢ asigna yᵢ=yⱼ+1, Wᵢ→Cᵢ requiere yⱼ=0 o yᵢ<yⱼ, Cᵢ→Nᵢ pone yᵢ=0. Para el grafo restringido, suprimir aristas que creen un ticket mayor que 2. Desde el inicial, por simetría basta seguir el primer pedido de 1: (W,N,1,0) puede entrar a (C,N,1,0) o recibir el segundo pedido a (W,W,1,2). En este último entra 1. Desde (C,N,1,0), 1 puede volver al inicio o 2 puede pedir, alcanzando (C,W,1,2). Al salir 1 queda (N,W,0,2); solo 2 puede avanzar dentro del recorte y alcanza (N,C,0,2), que vuelve al inicio por salida de 2. Reflejar intercambiando índices.

**b)** Con ambos esperando y tickets consecutivos, entra el que tiene el menor. Sale y vuelve a pedir antes de que el otro salga, y obtiene un ticket uno mayor que el del otro. Repetir alternando obtiene tickets sin cota.

**c)** G ¬(C₁ ∧ C₂), G ¬deadlock y ∧ᵢ G(Wᵢ → F Cᵢ). Los tickets positivos son distintos y el menor habilita su entrada. Si j ya estaba en C cuando i pidió, i obtuvo yⱼ+1 y permanece esperando hasta su salida. Si j pidió después de que i entró, también obtiene un ticket mayor. Con alguien en C su salida queda disponible, con alguien esperando y sin ocupante puede entrar el menor, y con ambos en N se puede pedir. Para un i esperando, j puede como máximo terminar un turno anterior y tomar un ticket superior a yᵢ. A partir de ahí no puede volver a entrar antes de i. Con estos tramos finitos y este LTS, no hay un lazo que postergue a i indefinidamente.

## 14. Canal de respuestas compartido

**a)** Cliente i: Pᵢ —pedido(i)→ Eᵢ —recibirᵢ(v)→ Uᵢ(v) —usarᵢ→ Pᵢ. Inicial Pᵢ. Servidor libre toma pedido(i) en sincronía con el cliente, y pasa a tener pendiente i. Al entregar i vuelve a libre. En el modelo síncrono, la entrega sincroniza con cualquier cliente en E. Con buffer, agregar una celda vacía o llena(v); el servidor deposita solo si está vacía, el cliente consume el valor y la vacía. El servidor puede aceptar otra solicitud aunque el buffer conserve una respuesta.

**b)** Por cada i, G(Uᵢ(v) → v=i), para todos los valores posibles, y G(Eᵢ → F Uᵢ), donde Uᵢ incluye cualquier valor recibido.

**c)** Sin buffer, la única respuesta en vuelo corresponde al único cliente en E. Esa recepción está habilitada y solo puede demorarse por tramos finitos de uso del otro cliente. Ambas propiedades valen. Con capacidad uno, pedir(1), depositar(1), pedir(2), recibir₂(1) refuta correspondencia, incluso si después se continúa con fair choice. También puede inanicionarse 1: mantenerlo en E y hacer que 2 tome la primera respuesta y siga pidiendo, recibiendo y usando las siguientes. Con fair choice, el buffer lleno y 1 en E reaparecen infinitamente si 2 sigue ciclando. La arista de recepción de 1 desde un estado recurrente debe tomarse. Así se recupera el progreso de recepción, aunque puede recibir un valor ajeno.
