# Práctica 2: patrones con semáforos y APIs de Java — resumen

- Fuente: [practica-02-semaforos.pdf](practica-02-semaforos.pdf) (93 páginas).
- [Transcripción completa](practica-02-semaforos.transcripcion.md).
- Síntesis editorial basada en este PDF; no reemplaza los enunciados ni constituye una fe de erratas oficial.

## Mapa de consulta

| Páginas | Contenido |
| --- | --- |
| [3–11](practica-02-semaforos.transcripcion.md#página-3) | Permisos, débiles/fuertes, señalización, recursos y split |
| [12–25](practica-02-semaforos.transcripcion.md#página-12) | Productor-consumidor, lectores-escritores y Udding |
| [26–39](practica-02-semaforos.transcripcion.md#página-26) | Fumadores, barbero y bibliografía |
| [40–62](practica-02-semaforos.transcripcion.md#página-40) | Thread, Runnable, start/join e interrupciones |
| [63–82](practica-02-semaforos.transcripcion.md#página-63) | volatile, ReentrantLock y Semaphore |
| [83–93](practica-02-semaforos.transcripcion.md#página-83) | AQS, CAS, park/unpark y bloqueo en el SO |

## Patrones y garantías

- `acquire` consume un permiso o espera; `release` devuelve/transmite permiso. No usar `availablePermits()` para decidir una acción como si consulta y toma fueran atómicas (pp. 5, 80).
- Semáforo débil: cualquiera de los bloqueados puede ser elegido. Fuerte: cola FIFO según el modelo. La justicia del semáforo es distinta de la del scheduler (pp. 6–7).
- Señalización impone precedencia; split distribuye permisos entre fases. Los valores iniciales determinan quién puede empezar (pp. 8–11).
- Productor-consumidor separa permisos de datos/lugares de mutex para índices. Lectores-escritores combina contador protegido con la toma/liberación del recurso por primer/último lector (pp. 13–20).
- Para combatir starvation se estudian un molinete fuerte y el algoritmo de Udding con dos compuertas. Son protocolos distintos: no concluir que todo problema exige primitivas fuertes (pp. 17, 21–25).
- Fumadores ilustra el peligro de tomar ingredientes por separado y la coordinación mediante gestores. Barbero separa solicitud, disponibilidad, final de corte y retiro (pp. 27–37).
- Una cola de semáforos privados permite dirigir el permiso al cliente correspondiente, sin delegar el orden de selección a un semáforo compartido débil (p. 38).

## Java: detalles operativos

- `start()` crea ejecución concurrente; `run()` directo es una llamada ordinaria. Un objeto Thread no se inicia dos veces. Iniciar y hacer join inmediatamente dentro del mismo bucle serializa tareas (pp. 41–54).
- La interrupción es cooperativa. `isInterrupted()` consulta; `Thread.interrupted()` consulta y limpia el estado del thread actual. Si se captura InterruptedException, el ejemplo enseña a propagarla o restablecer el indicador (pp. 58–62).
- `volatile` no vuelve atómico un incremento. ReentrantLock tiene dueño y admite reentrada; un semáforo binario no ofrece esas dos propiedades (pp. 63–74, 82).
- `unlock()` va en finally. Los permisos adquiridos también deben devolverse según el protocolo y la ruta de excepciones; no liberar si la adquisición no se completó.
- `Semaphore(n, true)` pide fairness. Incluso en esa variante, `tryAcquire()` sin timeout puede adelantarse: detalle explícito de p. 81.
- La explicación de implementación conecta un contador atómico/CAS y cola de AQS con `LockSupport.park/unpark` y mecanismos del SO. Es un recorrido de la implementación presentada, no una identidad obligatoria de todas las JVM (pp. 83–93).

## Para resolver ejercicios

Registrar qué representa cada permiso, qué estado protege cada mutex y quién hace cada release. Revisar por separado deadlock, livelock e inanición; un protocolo con actividad global puede postergar a un participante.
