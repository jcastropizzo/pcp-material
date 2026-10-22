# TP 01 — Base de datos key-value replicada en Erlang

Fuente: [PDF](tp-01-kv-replicado-erlang.pdf) · [Transcripción completa](tp-01-kv-replicado-erlang.transcripcion.md).
Síntesis editorial; ante dudas prevalece el enunciado. No incluye una implementación resuelta.

## Entrega y alcance

- PCP, segundo cuatrimestre de 2026.
- **El PDF anuncia 11 de noviembre de 2026, 23:59 (p. 1).** No especifica zona horaria ni plataforma de entrega.
- Entregables (p. 6): archivos `.erl`, tests propios de todos los comportamientos y documento de diseño de **máximo 5 páginas**.
- Una única VM BEAM; procesos sin fallas; `stop/1` se invoca una sola vez al final con todos los PIDs iniciales.
- Cada proceso es una réplica completa e independiente, con operaciones sobre claves y valores que pueden ser cualquier término Erlang.

## API fija (p. 2)

```erlang
-module(simple_kv).
-export([start_link/1, stop/1, put/3, put/4, delete/2, get/2]).
-spec start_link(pos_integer()) -> {ok, [pid()]}.
-spec stop([pid()]) -> ok.
-spec put(pid(), term(), term()) -> ok.
-spec put(pid(), term(), term(), non_neg_integer() | infinity) -> ok.
-spec delete(pid(), term()) -> ok.
-spec get(pid(), term()) -> {ok, term()} | not_found.
```

`start_link(Count)` crea procesos enlazados al invocante; Count inválido falla con `function_clause`. `stop` retorna cuando terminaron todos. `put/3` equivale a TTL infinito. Borrar una clave inexistente es válido.

## Matriz de requisitos

| Área | Comportamiento exigido | Fuente |
| --- | --- | --- |
| Replicación | Todo cambio se propaga; put/delete responden sin esperar aplicación remota. | §4.2, p. 3 |
| Convergencia | Al cesar escrituras, las réplicas terminan con el mismo estado. | §4.3, p. 3 |
| Conflictos | Elegir consistentemente entre versiones independientemente del orden de llegada; justificar la política. | §4.4, pp. 3–4 |
| Borrado | Una escritura vieja demorada no puede resucitar un dato borrado; solo una nueva escritura permite reaparición. | §4.5, p. 4 |
| TTL | Milisegundos desde la escritura; nunca devolver vencidos. 0 vence inmediatamente, infinity no vence. Inválidos: function_clause en el invocante. | §4.6, p. 4 |
| Capacidad | Máximo 16 valores por réplica; las 12 claves escritas más recientemente siempre disponibles. | §4.7, p. 4 |
| Desalojo | No equivale a delete; antes de descartar un valor vigente, dar a otras réplicas oportunidad de conservarlo, sin intercambio infinito. | §4.7, p. 4 |
| Lecturas remotas | Si no hay información local sobre una clave, considerar la de las otras réplicas antes de responder. | §4.8, p. 5 |
| Pedido/respuesta | API sincrónica sobre mensajes asincrónicos; correlacionar respuestas y pedidos; descartar mensajes inesperados. | §5.1, p. 5 |
| Progreso | No producir deadlocks; mientras espera información remota una réplica debe seguir atendiendo pedidos. | §5.2, p. 5 |
| Orden de mensajes | Orden garantizado para un mismo emisor y receptor; no entre emisores distintos. | §5.3, p. 5 |

El ejemplo de lectura remota inmediata de p. 2 no debe interpretarse como garantía general de replicación sincrónica: §4.2 permite diferencias transitorias.

## Restricciones (p. 5)

No usar behaviours OTP (incluidos gen_server, gen_statem, gen_event y supervisor), ets/dets/mnesia/persistent_term, ni diccionario de proceso. Estado exclusivamente en argumentos del ciclo de recepción; resto de biblioteca estándar permitido. Compilar sin advertencias con `erlc +warn_all`.

## Documento de diseño (p. 6)

Debe explicar estructura del estado; tabla completa de mensajes (emisor, receptor, contenido y efecto); conflictos y borrados con justificación; detección de vencimiento; política de desalojo y finitud de intercambios; algoritmo de get remoto y ausencia de deadlock. Pasar tests no demuestra por sí solo corrección: se evalúan código y documento.

## Riesgos y preguntas para diseñar (análisis editorial)

- Diseñar conjuntamente versiones, borrados y vencimientos: resolverlos por separado puede reintroducir datos viejos.
- Distinguir ausencia de valor local de conocimiento de un borrado o vencimiento; consultar otras réplicas no debe ignorar información más reciente.
- Evitar que una espera de lectura remota bloquee la atención de otros mensajes.
- Explicar qué metadatos se conservan tras desalojo/borrado y cómo se contabilizan frente al límite de valores.
- Precisar con la cátedra cómo interpretar “12 claves más recientes” ante concurrencia, borrados y TTL, y cómo conciliar convergencia con desalojos locales.
- Comprobar finitud del protocolo de ofrecimiento de valores: reenviar sin control puede crear circulación permanente.
- Tests: API y argumentos inválidos; concurrencia y órdenes diferentes de llegada; borrado seguido de mensajes viejos; TTL 0/finito/infinito y sobrescrituras; más de 16 claves; consultas remotas simultáneas; cierre de procesos.
- No considerar el TP completo por tener un mapa local o replicación básica: faltan los requisitos de la matriz.

La planificación y el estado de implementación se mantienen en el [README del proyecto](https://github.com/jcastropizzo/pcp-tp#readme), separados del material de estudio.
