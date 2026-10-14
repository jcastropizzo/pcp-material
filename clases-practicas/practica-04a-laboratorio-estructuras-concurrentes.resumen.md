# practica 04a laboratorio estructuras concurrentes — resumen

Fuente: [practica-04a-laboratorio-estructuras-concurrentes.pdf](practica-04a-laboratorio-estructuras-concurrentes.pdf). [Transcripción completa](practica-04a-laboratorio-estructuras-concurrentes.transcripcion.md).

Síntesis editorial para estudio; el original prevalece ante diferencias.

## Material incluido
Enunciado de cuatro páginas extraído del paquete original y proyecto Java conservado en `.tar.gz`. La transcripción reúne el enunciado y los archivos textuales del proyecto, incluidas plantillas, pruebas y configuración.

## Recorrido
1. Interfaz de conjunto ordenado y proyecto de trabajo.
2. Bloqueo de toda la lista.
3. Bloqueo fino por nodo.
4. Recorrido optimista y validación.
5. Eliminación lazy y marca lógica.
6. Conjunto lock-free con referencias marcables y CAS.
7. Experimentación y comparación.

## Núcleo para resolver
Mantener orden y unicidad de claves; distinguir eliminación lógica de desvinculación física. En granularidad fina, ordenar adquisición y liberación de locks. En optimista/lazy, validar adyacencia y estado tras adquirirlos. En lock-free, estudiar cómo `find` ayuda a remover nodos marcados y cómo un CAS fallido obliga a revalidar.

## Qué medir y justificar
Corrección funcional y concurrente antes de medir rendimiento. Comparar carga de lecturas/escrituras, cantidad de threads y tamaño del conjunto bajo el mismo entorno. No atribuir resultados de benchmarks a estas plantillas sin ejecutar implementaciones completas. El paquete conserva TODOs: la transcripción no los presenta como código resuelto.
