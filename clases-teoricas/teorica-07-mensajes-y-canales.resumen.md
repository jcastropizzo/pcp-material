# Mensajes y canales — resumen

Fuente: [PDF](teorica-07-mensajes-y-canales.pdf). [Transcripción](teorica-07-mensajes-y-canales.transcripcion.md).

## Mapa de lectura

|Páginas|Contenido|
|---|---|
|2–10|Comunicación por mensajes, trazas, sincronía e identificación de procesos.|
|11–18|Go: goroutines, canales, buffers, `select`, canales enviados como valores.|
|19–23|Protocolos de ida/vuelta y separación de canales por dirección.|
|24–28|Servidores de números y canal privado de respuesta por pedido.|
|29–32|Semáforo con canales y servidor con estado.|
|33–35|Filósofos y límites de los ejemplos.|
|36–40|Álgebras de procesos, CSP y π-cálculo.|

## Núcleo

- Sincronía determina cuándo se completa un envío; un canal acotado no equivale a una cola asincrónica ilimitada.
- Elegir canales por dirección evita que un proceso consuma un mensaje pensado para su interlocutor.
- Una respuesta privada transportada con el pedido permite correlación entre múltiples clientes.
- Un actor puede mantener estado secuencialmente, pero sus envíos y esperas aún pueden formar deadlocks.
- En `select`, canales nulos deshabilitan casos; no asumir fairness suficiente para demostrar ausencia de starvation.
- En π-cálculo se comunican nombres de canales: la movilidad cambia las conexiones posibles entre procesos.

## Notación que requiere el PDF

En las páginas 38–39, la extracción textual pierde la barra del prefijo de salida. La salida se escribe `x̄⟨y⟩.P`; la entrada, `x(y).P`. La sintaxis distingue composición paralela `P | Q`, restricción `(νx)P`, replicación `!P` e inacción `0`. No interpretar las barras de la gramática como si todas fueran composición paralela.

## Uso

Leer junto con la práctica 6 y la guía 6. Los ejemplos deliberadamente problemáticos requieren analizar las trazas; su presencia en diapositivas no los convierte en soluciones correctas. Esta síntesis es editorial.
