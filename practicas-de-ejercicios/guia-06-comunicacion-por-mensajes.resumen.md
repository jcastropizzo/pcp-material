# guia 06 comunicacion por mensajes — resumen

Fuente: [guia-06-comunicacion-por-mensajes.pdf](guia-06-comunicacion-por-mensajes.pdf). [Transcripción completa](guia-06-comunicacion-por-mensajes.transcripcion.md).

Síntesis editorial para estudio; el original prevalece ante diferencias.

## Mapa de ejercicios
|Ej.|Tema|
|---|---|
|1|Servidor contador y reinicio.|
|2|Pedidos y respuestas privadas.|
|3|Coordinación de entradas y salidas.|
|4|Juego de adivinación y sesiones.|
|5|Adaptador de comunicación asincrónica.|
|6|Extensión asincrónica del juego.|
|7|Variable compartida como servidor de mensajes.|
|8|Temporizador y suscriptores.|
|9|Pedidos de cifrado, claves y destinos.|
|10|Distribución de pedidos entre servicios y límites de concurrencia.|

## Núcleo para estudiar
Definir si cada canal es sincrónico, acotado o asincrónico ideal. Mantener estado en un único servidor evita carreras sobre ese estado, pero aún requiere un protocolo sin ciclos de espera. Enviar el canal de respuesta dentro del pedido separa clientes. Distinguir orden de aceptación, orden de terminación y orden de entrega.

## Riesgos recurrentes
Un buffer finito no implementa por sí solo asincronía ilimitada. Un servidor que se bloquea enviando puede dejar de aceptar la petición que permitiría avanzar. Incluir cierre, respuestas pendientes y correlación de sesiones. `select` no constituye una prueba de fairness; deshabilitar un caso con canal nulo no evita evaluar una expresión inválida usada para construir ese caso.

## Soluciones separadas

[Desarrollo de los ejercicios](guia-06-comunicacion-por-mensajes_resuelto.md) — elaboración de estudio, no oficial.
