# Semáforos: invariantes y patrones de sincronización — resumen

- Fuente: [teorica-02-semaforos.pdf](teorica-02-semaforos.pdf) (115 páginas).
- [Transcripción completa](teorica-02-semaforos.transcripcion.md).
- Síntesis editorial basada en este PDF; no reemplaza los enunciados ni constituye una fe de erratas oficial.

## Mapa de consulta

| Páginas | Contenido |
| --- | --- |
| [4–24](teorica-02-semaforos.transcripcion.md#página-4) | Procesos, definición, operaciones atómicas e invariantes |
| [25–54](teorica-02-semaforos.transcripcion.md#página-25) | Mutex, dos y N procesos, semáforos débiles y fuertes |
| [55–59](teorica-02-semaforos.transcripcion.md#página-55) | Orden de ejecución y mergesort concurrente |
| [60–85](teorica-02-semaforos.transcripcion.md#página-60) | Productor-consumidor, buffers y semáforos partidos |
| [86–108](teorica-02-semaforos.transcripcion.md#página-86) | Filósofos comensales y justificaciones |
| [109–115](teorica-02-semaforos.transcripcion.md#página-109) | Lectores-escritores y espera activa |

## Núcleo

- El semáforo tiene contador `V` y conjunto/cola `L` de bloqueados. `wait` consume permiso si hay; si no, bloquea. `signal` incrementa V si nadie espera o habilita a un bloqueado. Las operaciones son atómicas (pp. 12–17).
- Invariantes de la presentación: `V >= 0` y `V = k + #signal - #wait`. Se cuentan operaciones completadas; un wait bloqueado no cuenta como completado (p. 18).
- Un mutex parte de 1. La presentación razona con `#CS + V = 1` en su modelo abreviado para separar exclusión y progreso. Al expandir el código, prestar atención a permisos adquiridos antes de entrar materialmente en la sección crítica (pp. 28–45).
- La solución estándar con semáforo débil para N procesos no garantiza entrada individual. La versión fuerte organiza los bloqueados FIFO; las pruebas usan también que los procesos progresan y devuelven los permisos (pp. 46–54).
- Para imponer precedencia, un semáforo inicialmente cerrado transmite que la primera acción ya ocurrió. Es distinto del mutex: uno representa un evento/permiso y el otro exclusión (pp. 56–59).

## Patrones

- **Productor-consumidor:** distinguir disponibilidad de elementos, disponibilidad de lugares y protección de datos/índices. El código para un productor y un consumidor no se generaliza automáticamente a varios (pp. 60–85).
- `notEmpty` comienza en 0 y `notFull` en N. El material usa `notFull.V + notEmpty.V <= N`: los permisos en tránsito importan. Los accesos de productores y consumidores a sus respectivos índices se protegen con mutex adicionales (pp. 77–85).
- **Filósofos:** el intento simétrico de tomar un tenedor y esperar el otro permite espera circular. Se estudian restricción de comensales y asimetría de adquisición. Revisar separadamente exclusión de tenedores, ausencia de deadlock e inanición (pp. 86–108).
- **Lectores-escritores:** primer lector toma el recurso y último lo libera. La prioridad de lectores puede postergar escritores; la variante de prioridad de escritores impide nuevas lecturas cuando hay escritores esperando (pp. 109–114).

## Precauciones y detalles del original

Las igualdades de conteo de versiones abreviadas no deben aplicarse a cualquier estado intermedio del código expandido: pp. 68–74 explican esa diferencia. La p. 85 tiene una descripción verbal de `mutexP` que no coincide con los nombres del código (`mutexP` para productores y `mutexC` para consumidores); se conserva el original. La p. 115 ilustra conceptualmente espera activa: no convertir su chequeo y decremento en una implementación correcta si pueden entrelazarse.
