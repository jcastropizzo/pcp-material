# Monitores: condiciones, disciplinas y Java — resumen

- Fuente: [teorica-03-monitores.pdf](teorica-03-monitores.pdf) (86 páginas).
- [Transcripción completa](teorica-03-monitores.transcripcion.md).
- Síntesis editorial basada en este PDF; no reemplaza los enunciados ni constituye una fe de erratas oficial.

## Mapa de consulta

| Páginas | Contenido |
| --- | --- |
| [2–31](teorica-03-monitores.transcripcion.md#página-2) | Motivación, exclusión implícita, variables de condición y colas |
| [32–44](teorica-03-monitores.transcripcion.md#página-32) | Simulación de semáforo y buffer de un lugar |
| [45–64](teorica-03-monitores.transcripcion.md#página-45) | Signal, Hoare/IRR, Mesa y transferencia de permisos |
| [65–70](teorica-03-monitores.transcripcion.md#página-65) | Buffer circular y señalización incorrecta |
| [71–78](teorica-03-monitores.transcripcion.md#página-71) | Filósofos y lectores-escritores |
| [79–86](teorica-03-monitores.transcripcion.md#página-79) | Comparación con semáforos y alternativas Java |

## Núcleo

- Un monitor encapsula estado, operaciones y exclusión mutua. Sus condiciones permiten esperar por predicados del estado sin retener el lock mientras se está bloqueado (pp. 3–22).
- `wait(c)` bloquea y libera el lock; al retomar debe contarse con el lock correspondiente. `signal(c)` sin bloqueados no almacena un aviso para una futura espera (pp. 22–28).
- El material introduce condiciones FIFO en su modelo inicial. **Eso no autoriza a asumir FIFO en cualquier enunciado:** el ejercicio 3 del simulacro declara colas no fair.
- Primero se presenta signal al final con transferencia inmediata. Después se distinguen otras disciplinas (p. 45).

| Disciplina | Quién continúa | Consecuencia |
| --- | --- | --- |
| Hoare / espera urgente | El despertado recibe el mutex; el señalizador espera con prioridad sobre entradas nuevas | Puede aprovecharse la condición garantizada en el momento del signal bajo las hipótesis del protocolo |
| Mesa / signal y continúa | Sigue el señalizador; el despertado compite luego por el mutex | El predicado puede haber dejado de valer antes de reingresar |

La p. 52 expresa las prioridades como `E < S < W` para Hoare y `E = W < S` para Mesa (E: entradas, S: señalizador, W: despertados).

## Decisiones de diseño

- Bajo Mesa, el patrón ordinario es `while (!predicado) wait(condicion)`, seguido de la actualización protegida. Despertar no equivale a reservar un recurso (pp. 55–63).
- La p. 64 muestra una **excepción construida explícitamente**: transferencia lógica de un permiso a un bloqueado, sin incrementarlo como disponibilidad pública. No copiar su `if` sin las hipótesis de colas y ausencia de despertares espurios que lo hacen válido.
- El buffer circular tiene condiciones separadas para espacio y datos, contador e índices. Una optimización incorrecta de signals puede dejar procesos dormidos (pp. 65–70).
- Para varios motivos de espera, separar condiciones permite señalización dirigida; con una condición común, puede hacer falta despertar a todos y revalidar (pp. 73–78).
- Java intrínseco: `synchronized`, `wait`, `notify`, `notifyAll`, una condición por objeto. Con `ReentrantLock` se crean varias `Condition`; liberar el lock en `finally` (pp. 80–86).

## Precauciones del original

La tabla de p. 79 mezcla la convención FIFO de condiciones con una comparación operacional que depende de la disciplina: leerla junto con pp. 52–54 y 80. En p. 70 la traza verbal afirma que el buffer está lleno y luego un productor inserta: no usar esa redacción como contraejemplo ejecutable sin revisarla. El código deliberadamente problemático de p. 69 se conserva como tal; no es la solución de referencia.
