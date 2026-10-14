# practica 06a laboratorio go victoria y piramides — resumen

Fuente: [practica-06a-laboratorio-go-victoria-y-piramides.zip](practica-06a-laboratorio-go-victoria-y-piramides.zip). [Transcripción completa](practica-06a-laboratorio-go-victoria-y-piramides.transcripcion.md).

Síntesis editorial para estudio; el original prevalece ante diferencias.

## Inventario y relación con la clase
Paquete de apoyo para la práctica 6. Contiene el ejercicio de Victoria y archivos comprimidos con las partes de la pirámide y una solución suministrada. La transcripción despliega los archivos de texto y código de los paquetes anidados, evitando descomprimirlos durante una sesión de estudio.

## Orden de trabajo
1. Victoria: sincronizar recepción de piezas y construcción.
2. Parte I: producir nombres, crear inversores y limitar la cantidad de invitados directos.
3. Parte II: completar la generación usando `WaitGroup` (se desarrolla junto con los materiales posteriores; no hay un archivo separado titulado Parte 2).
4. Parte III: pagos de hojas hacia sus superiores.
5. Parte IV: distribución de comisiones hacia abajo.
6. Comparar con la carpeta de solución suministrada después del intento propio.

## Núcleo
Cada asociado mantiene su estado; los canales conectan relaciones padre/hijo. Distinguir el fin de la generación del fin de los pagos y de las comisiones. Balance, pertenencia activa y canales de respuesta forman un protocolo, no variables independientes.

## Análisis editorial de la solución suministrada
La distribución de comisiones lanza goroutines emisoras. Si un destinatario deja de recibir, lanzar el envío en otra goroutine desplaza el bloqueo y puede dejar trabajo pendiente; no demuestra terminación global. Revisar también redondeo de divisiones enteras y momento de dar de baja a un asociado. Estas observaciones son puntos de revisión, no una corrección oficial. No se ejecutaron los programas del paquete en este procesamiento.
