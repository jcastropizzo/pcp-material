# Práctica 07 — desarrollo de estudio

[Apunte](practica-07-modelos-y-logica.pdf) · [Guía resuelta](../practicas-de-ejercicios/guia-07-logica-para-modelado-de-concurrencia_resuelto.md)

## Composición y reservas

Un lock tiene estados libre/tomado con acquire y release, sincronizados con el thread correspondiente. Para dos clientes, indexar las acciones del lock por cliente. El producto cambia simultáneamente los componentes que comparten una acción, y deja fijos los demás. Si una acción es compartida y alguna parte no la ofrece, no hay arista con esa etiqueta.

Para modelar una reserva, incluir en el estado compartido el dueño y en cada cliente su punto de control. Con consultar y escribir por separado, esta secuencia demuestra la carrera: consultar₁(libre), consultar₂(libre), escribir₁(1), escribir₂(2). Ambos pasan la comprobación y el último sobrescribe. Si la operación atómica tiene guardia dueño=libre y asigna dueño=i en el mismo paso, después del primer éxito esa guardia queda falsa hasta una liberación. Mantener como invariante que el éxito vigente tiene el mismo identificador que el dueño.

Para un canal con capacidad uno usado como lock, el estado del canal es vacío/lleno. En el protocolo que envía para entrar, el envío habilitado en vacío lo llena; la recepción de salida lo vacía. El invariante es que lleno equivale a una única sección crítica ocupada, si se cuentan como parte de la sección también los pasos entre el envío y la recepción. Con dos participantes, una ejecución puede elegir al segundo inmediatamente cada vez que queda vacío mientras el primero espera; el estado recurrente con canal vacío ofrece también la entrada del primero. Fair choice descarta repetir ese lazo ignorando siempre esa arista.

## Leyes temporales

- Gφ implica G(φ∨ψ); la disyunción de Gφ y Gψ también lo implica. La implicación inversa falla en ({φ}{ψ})ω.
- Fφ ≡ φ ∨ X Fφ. En un testigo de Fφ, separar la posición actual de las estrictamente posteriores.
- Gφ ≡ φ ∧ X Gφ. Separar la posición actual del resto del sufijo.
- φ W ψ ≡ (φ U ψ) ∨ Gφ. Si ψ nunca ocurre, la segunda alternativa exige φ para siempre.
- De p ∧ G(p→Xp) se deduce Gp por inducción sobre la posición. Con solo la implicación, la traza donde p nunca vale es un contraejemplo.

## Safety, liveness y turnos

Para refutar que una reserva nunca se sobrescribe, alcanza el prefijo de doble escritura. Para refutar progreso con infinito, describir un prefijo que llegue a un ciclo y repetirlo. Si la ejecución llega a un estado sin salida, completar la traza repitiendo ese estado. Por ejemplo, con turnos alternados, si el thread 1 sale y deja el turno al 2, el 2 termina, y el 1 vuelve a pedir, el 1 queda esperando sin que haya una arista que devuelva el turno. Fair choice no puede habilitar una transición que el modelo no tiene.

Si ambos participantes siguen activos y los tramos locales no permiten postergar indefinidamente la petición o la salida, una vez que el otro sale le deja el turno al que espera y queda bloqueado si vuelve a intentar. Si se permite una acción local de trabajo que se repite indefinidamente antes de pedir, se agrega un lazo que puede postergar el cambio de turno. Revisar si desde el estado recurrente hay una salida habilitada que fair choice obliga a tomar.

Para distinguir la existencia de una posibilidad de su garantía universal, EF acceso dice que desde el estado actual hay algún camino que alcanza el acceso. AF acceso lo exige en todos los caminos. AG EF acceso pide que se conserve la posibilidad de acceso en cada estado alcanzable, aunque alguna ejecución nunca la concrete.
