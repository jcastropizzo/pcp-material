# guia 06a mensajes plataforma streaming — resumen

Fuente: [guia-06a-mensajes-plataforma-streaming.pdf](guia-06a-mensajes-plataforma-streaming.pdf). [Transcripción completa](guia-06a-mensajes-plataforma-streaming.transcripcion.md).

Síntesis editorial para estudio; el original prevalece ante diferencias.

## Mapa de incisos
(a) Retransmitir paquetes a suscriptores mediante canales sincrónicos. (b) Iniciar la emisión tras registrar tres suscriptores. (c) Incorporar bajas de suscriptores. (d) Detener el streamer cuando ya no queden suscriptores.

## Núcleo del problema
Coordinar el ciclo de vida completo: alta confirmada, comienzo, difusión, baja y terminación. Cada paquete tiene un conjunto de destinatarios pendientes. El envío a uno de ellos no debe impedir procesar su baja si ya dejó de recibir.

## Qué verificar
Propiedad de los canales y responsable de cerrarlos; ausencia de doble cierre; no enviar a un canal cerrado; drenaje de una emisión que ya estaba en curso al pedir detener. La goroutine principal debe esperar el cierre del sistema. Un temporizador no garantiza periodicidad exacta frente a backpressure.

## Soluciones separadas

[Desarrollo de los ejercicios](guia-06a-mensajes-plataforma-streaming_resuelto.md) — elaboración de estudio, no oficial.
