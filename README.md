# Material de PCP

PDF organizados por el tipo de material. Los nombres son descriptivos; el contenido de los PDF se conserva sin cambios.

## Prácticas de ejercicios

| Archivo | Contenido |
| --- | --- |
| [guia-01-modelo-de-computo-y-exclusion-mutua.pdf](practicas-de-ejercicios/guia-01-modelo-de-computo-y-exclusion-mutua.pdf) | Guía 1: modelo de cómputo y exclusión mutua |
| [guia-02-semaforos.pdf](practicas-de-ejercicios/guia-02-semaforos.pdf) | Guía 2: semáforos |
| [guia-03-monitores.pdf](practicas-de-ejercicios/guia-03-monitores.pdf) | Guía 3: monitores |
| [guia-04-estructuras-de-datos-y-lock-free.pdf](practicas-de-ejercicios/guia-04-estructuras-de-datos-y-lock-free.pdf) | Guía 4: estructuras de datos y sincronización lock-free |
| [modelo-de-parcial-2026-2c.pdf](practicas-de-ejercicios/modelo-de-parcial-2026-2c.pdf) | Modelo de parcial (simulacro; no es una guía) |

## Clases prácticas

| Archivo | Contenido |
| --- | --- |
| [practica-01-introduccion-semantica-y-java.pdf](clases-practicas/practica-01-introduccion-semantica-y-java.pdf) | Práctica 1: introducción, semántica, diagramas de estados y Java |
| [practica-02-semaforos.pdf](clases-practicas/practica-02-semaforos.pdf) | Práctica 2: semáforos |
| [practica-03-de-semaforos-a-monitores.pdf](clases-practicas/practica-03-de-semaforos-a-monitores.pdf) | Práctica 3: de semáforos a monitores (apunte de clase) |
| [practica-04-listas-y-skip-lists-concurrentes.pdf](clases-practicas/practica-04-listas-y-skip-lists-concurrentes.pdf) | Práctica 4: listas y skip lists concurrentes |

## Clases teóricas

| Archivo | Contenido |
| --- | --- |
| [teorica-01-introduccion.pdf](clases-teoricas/teorica-01-introduccion.pdf) | Introducción |
| [teorica-02-semaforos.pdf](clases-teoricas/teorica-02-semaforos.pdf) | Semáforos |
| [teorica-03-monitores.pdf](clases-teoricas/teorica-03-monitores.pdf) | Monitores |
| [teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf](clases-teoricas/teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf) | Conjuntos concurrentes: con y sin locks |
| [teorica-05-pilas-colas-y-problema-aba.pdf](clases-teoricas/teorica-05-pilas-colas-y-problema-aba.pdf) | Pools (pilas y colas): problema ABA |
| [teorica-memoria-transaccional-por-software.pdf](clases-teoricas/teorica-memoria-transaccional-por-software.pdf) | Memoria transaccional por software (STM) |

## Notas de clasificación

- El simulacro se agrupa con los ejercicios y se identifica explícitamente como modelo de evaluación.
- `03-Monitores (1).pdf` es la guía de ejercicios; `teorica-03-monitores.pdf` contiene diapositivas teóricas. No son duplicados.
- `practica-03-de-semaforos-a-monitores.pdf` es el material de la clase práctica 3, aunque su formato no sea una presentación.
- `teorica-memoria-transaccional-por-software.pdf` corresponde a la teórica de memoria transaccional por software.
- Algunos PDF conservan el encabezado «Primer cuatrimestre 2026» del original. La clasificación no modifica ni corrige esas fechas.

## Nombres originales

| Nombre original | Nombre actual |
| --- | --- |
| `01-Mutex.pdf` | `guia-01-modelo-de-computo-y-exclusion-mutua.pdf` |
| `02-Semaforos.pdf` | `guia-02-semaforos.pdf` |
| `03-Monitores (1).pdf` | `guia-03-monitores.pdf` |
| `04-LockFree.pdf` | `guia-04-estructuras-de-datos-y-lock-free.pdf` |
| `examen_simulacro.pdf` | `modelo-de-parcial-2026-2c.pdf` |
| `clase-01.pdf` | `practica-01-introduccion-semantica-y-java.pdf` |
| `practica-clase-02.pdf` | `practica-02-semaforos.pdf` |
| `apunte.pdf` | `practica-03-de-semaforos-a-monitores.pdf` |
| `clase-04.pdf` | `practica-04-listas-y-skip-lists-concurrentes.pdf` |
| `pcp-teorica-01.pdf` | `teorica-01-introduccion.pdf` |
| `pcp-teorica-02.pdf` | `teorica-02-semaforos.pdf` |
| `03-Monitores.pdf` | `teorica-03-monitores.pdf` |
| `04-Listas.pdf` | `teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf` |
| `05-colas.pdf` | `teorica-05-pilas-colas-y-problema-aba.pdf` |
| `main.pdf` | `teorica-memoria-transaccional-por-software.pdf` |

## Material procesado para estudio y agentes

Cada PDF tiene dos Markdown al lado:

- **`*.resumen.md`**: síntesis, mapa de páginas y, para guías, catálogo completo de ejercicios sin soluciones agregadas. Incluye precauciones sobre convenciones y discrepancias observadas.
- **`*.transcripcion.md`**: contenido textual por página, con columnas y sangría conservadas en bloques de texto. Mantiene las diapositivas progresivas y ejemplos del original. Incluye enlaces a páginas del PDF con información gráfica y descripciones editoriales de esquemas seleccionados.

### Orden de lectura recomendado

1. Consultar el resumen para localizar un tema o ejercicio.
2. Leer el enunciado o desarrollo completo en la sección de página de la transcripción.
3. Consultar la página del PDF cuando importen las flechas, las posiciones, los colores, los símbolos o el formato del código. Los enlaces al PDF usan `#page=N` (su soporte depende del visor).
4. El PDF prevalece ante discrepancias. No confundir notas editoriales con afirmaciones del original ni corregir silenciosamente sus ejemplos deliberadamente incorrectos.
5. No asumir las mismas convenciones en todos los documentos: en particular, comprobar semáforos débiles/fuertes, disciplina del monitor, fairness y despertares espurios en cada consigna.

### Índice de derivados

| Material | Páginas | Transcripción | Resumen |
| --- | --- | --- | --- |
| [practica-01-introduccion-semantica-y-java](clases-practicas/practica-01-introduccion-semantica-y-java.pdf) | 104 | [Texto completo](clases-practicas/practica-01-introduccion-semantica-y-java.transcripcion.md) | [Síntesis](clases-practicas/practica-01-introduccion-semantica-y-java.resumen.md) |
| [practica-02-semaforos](clases-practicas/practica-02-semaforos.pdf) | 93 | [Texto completo](clases-practicas/practica-02-semaforos.transcripcion.md) | [Síntesis](clases-practicas/practica-02-semaforos.resumen.md) |
| [practica-03-de-semaforos-a-monitores](clases-practicas/practica-03-de-semaforos-a-monitores.pdf) | 30 | [Texto completo](clases-practicas/practica-03-de-semaforos-a-monitores.transcripcion.md) | [Síntesis](clases-practicas/practica-03-de-semaforos-a-monitores.resumen.md) |
| [practica-04-listas-y-skip-lists-concurrentes](clases-practicas/practica-04-listas-y-skip-lists-concurrentes.pdf) | 45 | [Texto completo](clases-practicas/practica-04-listas-y-skip-lists-concurrentes.transcripcion.md) | [Síntesis](clases-practicas/practica-04-listas-y-skip-lists-concurrentes.resumen.md) |
| [teorica-01-introduccion](clases-teoricas/teorica-01-introduccion.pdf) | 90 | [Texto completo](clases-teoricas/teorica-01-introduccion.transcripcion.md) | [Síntesis](clases-teoricas/teorica-01-introduccion.resumen.md) |
| [teorica-02-semaforos](clases-teoricas/teorica-02-semaforos.pdf) | 115 | [Texto completo](clases-teoricas/teorica-02-semaforos.transcripcion.md) | [Síntesis](clases-teoricas/teorica-02-semaforos.resumen.md) |
| [teorica-03-monitores](clases-teoricas/teorica-03-monitores.pdf) | 86 | [Texto completo](clases-teoricas/teorica-03-monitores.transcripcion.md) | [Síntesis](clases-teoricas/teorica-03-monitores.resumen.md) |
| [teorica-04-conjuntos-concurrentes-con-y-sin-locks](clases-teoricas/teorica-04-conjuntos-concurrentes-con-y-sin-locks.pdf) | 131 | [Texto completo](clases-teoricas/teorica-04-conjuntos-concurrentes-con-y-sin-locks.transcripcion.md) | [Síntesis](clases-teoricas/teorica-04-conjuntos-concurrentes-con-y-sin-locks.resumen.md) |
| [teorica-05-pilas-colas-y-problema-aba](clases-teoricas/teorica-05-pilas-colas-y-problema-aba.pdf) | 118 | [Texto completo](clases-teoricas/teorica-05-pilas-colas-y-problema-aba.transcripcion.md) | [Síntesis](clases-teoricas/teorica-05-pilas-colas-y-problema-aba.resumen.md) |
| [teorica-memoria-transaccional-por-software](clases-teoricas/teorica-memoria-transaccional-por-software.pdf) | 83 | [Texto completo](clases-teoricas/teorica-memoria-transaccional-por-software.transcripcion.md) | [Síntesis](clases-teoricas/teorica-memoria-transaccional-por-software.resumen.md) |
| [guia-01-modelo-de-computo-y-exclusion-mutua](practicas-de-ejercicios/guia-01-modelo-de-computo-y-exclusion-mutua.pdf) | 7 | [Texto completo](practicas-de-ejercicios/guia-01-modelo-de-computo-y-exclusion-mutua.transcripcion.md) | [Síntesis](practicas-de-ejercicios/guia-01-modelo-de-computo-y-exclusion-mutua.resumen.md) |
| [guia-02-semaforos](practicas-de-ejercicios/guia-02-semaforos.pdf) | 4 | [Texto completo](practicas-de-ejercicios/guia-02-semaforos.transcripcion.md) | [Síntesis](practicas-de-ejercicios/guia-02-semaforos.resumen.md) |
| [guia-03-monitores](practicas-de-ejercicios/guia-03-monitores.pdf) | 4 | [Texto completo](practicas-de-ejercicios/guia-03-monitores.transcripcion.md) | [Síntesis](practicas-de-ejercicios/guia-03-monitores.resumen.md) |
| [guia-04-estructuras-de-datos-y-lock-free](practicas-de-ejercicios/guia-04-estructuras-de-datos-y-lock-free.pdf) | 4 | [Texto completo](practicas-de-ejercicios/guia-04-estructuras-de-datos-y-lock-free.transcripcion.md) | [Síntesis](practicas-de-ejercicios/guia-04-estructuras-de-datos-y-lock-free.resumen.md) |
| [modelo-de-parcial-2026-2c](practicas-de-ejercicios/modelo-de-parcial-2026-2c.pdf) | 4 | [Texto completo](practicas-de-ejercicios/modelo-de-parcial-2026-2c.transcripcion.md) | [Síntesis](practicas-de-ejercicios/modelo-de-parcial-2026-2c.resumen.md) |

### Cobertura y límites de la conversión

- 15 PDF, 918 páginas: 15 transcripciones y 15 resúmenes.
- Se conservan los PDF originales sin cambios. Los enlaces a páginas gráficas apuntan al PDF correspondiente.
- Las transcripciones se basan en la capa de texto del PDF con disposición espacial; se normalizaron acentos y se reparó espaciado de identificadores contrastando con la capa textual de la misma página. No son archivos de código ejecutable.
- La información gráfica completa se conserva en el PDF. Se añadieron descripciones editoriales para esquemas seleccionados; no hay una reconstrucción textual exhaustiva de todas las flechas de todos los diagramas. Para resolver un ejercicio dependiente de esos detalles, consultar la página enlazada.
- La extracción matemática puede representar corchetes semánticos como `J...K`, o la flecha de actualización como `7→`. En esos casos consultar el original; no interpretarlos como identificadores o números literales.
- Los resúmenes son derivados de estudio, no una fe de erratas oficial ni garantía sobre el alcance del próximo examen. El resumen del modelo registra la ambigüedad de su regla de aprobación.
