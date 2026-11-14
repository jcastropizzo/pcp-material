# Práctica 07a — desarrollo de estudio

[Diapositivas](practica-07a-servidor-seleccion.pdf) · [Simulacro, ejercicio 3](../practicas-de-ejercicios/modelo-segundo-parcial-2026-2c.pdf)

## Modelo y proposiciones

Cada cliente sigue P —pedidoᵢ→ E —respᵢ→ U —usarᵢ→ P. Su alfabeto contiene esas tres acciones. El servidor A tiene estado inicial 0 y, desde él, pedidoᵢ lo lleva a i y respᵢ lo devuelve a 0. El servidor B sigue el ciclo 0 —pedido₁→ 1 —resp₁→ 2 —pedido₂→ 3 —resp₂→ 0. Los dos pedidos y respuestas pertenecen al alfabeto del servidor.

El estado global inicial es (0,P,P). Para pedido o resp, avanzar tanto el servidor como el cliente correspondiente si ambos ofrecen el paso. Para usar, avanzar solo el cliente. pideᵢ, esperaᵢ y usaᵢ se identifican con P, E y U en su componente. atiende₁ vale cuando el servidor está en 1. atiende₂ vale en 2 para A y en 3 para B.

## Fórmulas

- Si pide, termina usando una respuesta: ∧ᵢ G(pideᵢ → F usaᵢ).
- Si está esperando, sigue esperando hasta usar: ∧ᵢ G(esperaᵢ → (esperaᵢ W usaᵢ)). W permite esperar indefinidamente.
- Sea eᵢ = ¬atiendeᵢ ∧ X atiendeᵢ el comienzo de una atención. Entre dos comienzos del cliente 1 debe ocurrir uno del cliente 2: G(e₁ → X(¬e₁ W e₂)). El flanco evita contar una misma atención varias veces mientras cambia el estado del otro cliente.

## Verificación

| Fórmula | A | B |
| --- | --- | --- |
| GF usa₂ | Falla; bajo fair choice vale | Vale |
| G(pide₁ → F espera₁) | Falla; bajo fair choice vale | Vale |
| G ¬(usa₁ ∧ usa₂) | Falla, también con fair choice | Falla, también con fair choice |
| G(espera₁ → X(espera₁ ∨ usa₁)) | Vale | Vale |

En A, repetir pedido₁, resp₁, usar₁ mantiene al cliente 2 en P. El lazo simétrico mantiene al 1 en P. La componente terminal incluye los estados con E₁ y U₂: desde cualquier estado se puede completar la respuesta en vuelo y ejecutar los pasos usar pendientes hasta volver a (0,P,P), y desde allí se alcanzan esos estados. Fair choice exige recorrer las salidas de esa componente en una ejecución infinita recurrente.

En B ninguna petición del mismo cliente puede repetirse sin haber dado turno al otro. Cuando el cliente esperado está en U, su paso usar queda disponible, y el otro cliente solo puede avanzar un número finito de pasos antes de necesitar al servidor. Ambos clientes vuelven a recibir y usar recurrentemente.

La secuencia pedido₁, resp₁, pedido₂, resp₂ llega a dos clientes en U en ambas versiones. Es un prefijo malo que puede prolongarse con una ejecución justa. Finalmente, desde E₁ el único paso que modifica al cliente 1 es resp₁, que lo lleva a U₁. Cualquier otro paso global conserva E₁.
