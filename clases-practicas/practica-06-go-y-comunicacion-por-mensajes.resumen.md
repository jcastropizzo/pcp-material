# practica 06 go y comunicacion por mensajes — resumen

Fuente: [practica-06-go-y-comunicacion-por-mensajes.pdf](practica-06-go-y-comunicacion-por-mensajes.pdf). [Transcripción completa](practica-06-go-y-comunicacion-por-mensajes.transcripcion.md).

Síntesis editorial para estudio; el original prevalece ante diferencias.

## Mapa de lectura
- Páginas 3–13: fundamentos de Go, tipos, interfaces, slices, goroutines y canales.
- Páginas 14–15: Victoria canalizada.
- Páginas 16–22: generación de inversores y pirámide; canales y `WaitGroup`.
- Páginas 23–26: `select`, worker pool, buffer, backpressure y errores frecuentes.
- Páginas 27–28: recaudación y comisiones de la pirámide.

## Núcleo
Canales sin buffer sincronizan emisor y receptor; un buffer solo desacopla mientras haya espacio. `range` sobre un canal necesita cierre para finalizar. `WaitGroup` coordina terminación, no protege estado compartido. Registrar tareas antes de lanzarlas y evitar que `main` termine mientras queda trabajo.

## Ejercicios del laboratorio
Victoria requiere reunir las piezas correctas antes de construir. La pirámide avanza en cuatro etapas: nombres y asociados; generación completa; pagos hacia arriba; comisiones hacia abajo. Los archivos de apoyo están en el paquete `practica-06a`, cuya transcripción incluye las plantillas y la solución suministrada.

## Riesgos
Los slices pueden compartir almacenamiento; pasar un valor que contiene referencias no copia profundamente sus datos. Un `select` sin alternativa ejecutable bloquea. Diseñar quién cierra cada canal y cómo terminan las goroutines antes de agregar paralelismo.
