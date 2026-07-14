# Decisiones Arquitecturales

Estado:

> En construcción

## Objetivo

Registrar todas las decisiones importantes de arquitectura del ERP Polar Breeze: por qué se eligió una tecnología, por qué cambió un modelo de datos, por qué se modificó un flujo, etc.

Este historial es tan valioso como el propio código: facilita la incorporación de nuevos desarrolladores o asistentes de IA al proyecto y evita que se repitan discusiones ya resueltas.

## Formato sugerido por decisión

```
### [Fecha] Título de la decisión

**Contexto:**
(¿Qué problema o disyuntiva motivó esta decisión?)

**Decisión:**
(¿Qué se decidió?)

**Alternativas consideradas:**
(¿Qué otras opciones se evaluaron y por qué se descartaron?)

**Consecuencias:**
(¿Qué implica esta decisión hacia adelante?)
```

## Historial de decisiones

### [2026-07-13] Arquitectura multiempresa desde el origen

**Contexto:**
El ERP necesitaba definirse desde el inicio como un sistema para una sola empresa o como un sistema multiempresa (multi-tenant). Postergar esta decisión habría obligado a reescribir el modelo de datos, las reglas de aislamiento y buena parte de los módulos una vez construidos sobre un supuesto de empresa única.

**Decisión:**
El ERP Polar Breeze se diseña multiempresa desde el día uno. Toda entidad de datos pertenece a una empresa (`empresa_id` o equivalente), el aislamiento entre empresas es obligatorio en datos, configuración y flujo, y ningún módulo puede asumir que existe una sola empresa en el sistema. Este principio quedó formalizado como Artículo 0 de `docs/02-CONSTITUCION-ERP.md`, de rango superior al resto de las reglas.

**Alternativas consideradas:**
Construir primero para una sola empresa y migrar a multiempresa más adelante. Se descartó porque el costo de retrofitting del aislamiento de datos (particionado, seguridad, catálogos "globales" que en realidad no lo son) es mucho mayor que diseñarlo desde el modelo de datos inicial.

**Consecuencias:**
Todo módulo nuevo debe declarar explícitamente cómo respeta el aislamiento multiempresa antes de ser aprobado para desarrollo (Artículo 0.6 de la Constitución). El modelo de datos maestro (`05-MODELO-DE-DATOS-MAESTRO.md`) deberá reflejar `empresa_id` como llave de partición desde su primera versión.

> **Nota de reemplazo (2026-07-13):** esta decisión queda superada en su nomenclatura y ubicación por la decisión "Reescritura extendida de la Constitución y estandarización de `empresaId`/`sucursalId`" registrada más abajo. El principio de fondo (multiempresa desde el origen) se mantiene sin cambios; lo que cambia es la convención de nombres y la numeración del artículo que lo formaliza.

### [2026-07-13] Reescritura extendida de la Constitución y estandarización de `empresaId`/`sucursalId`

**Contexto:**
La primera versión de `02-CONSTITUCION-ERP.md` cubría un conjunto inicial de reglas (identidad, datos, procesos operativos, plataforma, gobernanza) usando `empresa_id` en snake_case. El proyecto requería una Constitución de nivel "arquitectura empresarial" que cubriera explícitamente: fuente única de verdad, prohibición de duplicar información, arquitectura basada en eventos, arquitectura basada en flujos patrimoniales, trazabilidad, auditoría, soft delete, integridad referencial, versionado de datos, seguridad por roles, motor de permisos, inmutabilidad de documentos aprobados, catálogo de eventos, catálogos maestros, y reglas específicas de inventario, finanzas, contabilidad, cuentas por pagar, consignación, cuarto frío, despacho, exportación, reportes, IA, integraciones futuras, crecimiento del ERP y nuevos módulos.

**Decisión:**
Se reescribió `02-CONSTITUCION-ERP.md` completo, organizado en 29 artículos numerados, cubriendo todos los temas anteriores. Se estandarizó la nomenclatura multiempresa a **`empresaId`** y, cuando aplica a nivel de sede/unidad operativa, **`sucursalId`** (camelCase, consistente con el resto del diccionario de datos), reemplazando la referencia previa a `empresa_id`. El Estado del documento pasó de "En construcción" a "Vigente — sujeta a enmienda formal mediante `DECISIONES-ARQUITECTURALES.md`", dado que ahora cubre el alcance mínimo exigido para gobernar el desarrollo de módulos.

**Alternativas consideradas:**
Mantener la Constitución como un documento breve de principios generales y mover el detalle de cada regla (inventario, contabilidad, permisos, etc.) únicamente a sus documentos específicos (`06`, `08`, etc.). Se descartó porque el propósito explícito de la Constitución es fijar las reglas que **ningún módulo puede romper**; dejar esas reglas solo en documentos de detalle debilita su carácter inquebrantable y dificulta detectar violaciones al revisar un módulo nuevo.

**Consecuencias:**
- Todo módulo nuevo se evalúa contra los 29 artículos antes de aprobarse (Artículo 29.2).
- El diccionario de datos (`11-DICCIONARIO-DE-DATOS.md`) y el modelo de datos maestro (`05-MODELO-DE-DATOS-MAESTRO.md`) deben usar `empresaId`/`sucursalId` como convención de nombres, no `empresa_id`.
- Cualquier documento futuro que mencione el aislamiento multiempresa debe alinearse a esta nomenclatura.
- El catálogo de eventos (`docs/diagramas/eventos.drawio`) y `12-GLOSARIO.md` quedan como referencia obligatoria para el Artículo 15 (Eventos del Sistema).

### [2026-07-14] Redacción autorizada de la Visión (01-VISION-ERP.md) sin documento fuente externo

**Contexto:**
La instrucción original de creación del repositorio pedía copiar íntegramente un documento externo de Visión ya existente, sin resumir ni modificar. El usuario pegó únicamente el índice de 16 secciones del documento (títulos y sub-puntos), no el desarrollo completo de cada sección, y confirmó que ese texto completo no está disponible para copiarlo literalmente.

**Decisión:**
El usuario autorizó explícitamente redactar el contenido completo de `01-VISION-ERP.md` a partir del índice de 16 secciones y de todo el conocimiento ya documentado del proyecto (Constitución, catálogo de módulos, estándares de desarrollo, decisiones previas), en lugar de esperar por un documento fuente que no existe en esta conversación. El documento resultante queda marcado en su propio archivo (sección Observaciones) como una redacción de IA sujeta a revisión, no como transcripción literal de un original.

**Alternativas consideradas:**
Mantener `01-VISION-ERP.md` indefinidamente en estado "PENDIENTE" hasta que apareciera un documento fuente externo. Se descartó porque bloqueaba sin fecha cierta el propósito del repositorio (ser la fuente de verdad de la arquitectura) y el usuario prefirió avanzar con una primera versión revisable en lugar de un vacío documental.

**Consecuencias:**
- `01-VISION-ERP.md` pasa de Estado "PENDIENTE" a "Vigente — primera versión... pendiente de revisión y aprobación formal".
- Si en el futuro aparece el documento fuente original, deberá compararse contra esta versión y, de haber diferencias relevantes, registrarse como una nueva decisión de reemplazo (Artículo 14.3 de la Constitución), no como una edición silenciosa.
- El resto de la documentación (Constitución, catálogo de módulos) ya redactada previamente a esta Visión se mantiene consistente con ella; no se detectaron contradicciones al redactarla.

### [2026-07-14] Redacción completa de 00-PRINCIPIOS-DEL-ERP.md como capa explicativa entre Visión y Constitución

**Contexto:**
`01-VISION-ERP.md` (sección 4) enuncia los principios fundamentales de forma breve, y `02-CONSTITUCION-ERP.md` (Artículo 1 y siguientes) los fija como reglas formales inquebrantables. No existía un documento intermedio que explicara el fundamento y las implicaciones prácticas de cada principio.

**Decisión:**
Se redactó `00-PRINCIPIOS-DEL-ERP.md` con 12 principios (código como clave universal, offline-first, multiempresa desde el origen, compatibilidad multiplataforma, persistencia de sesión, una sola fuente de verdad, prohibición de duplicar información, arquitectura basada en eventos, arquitectura basada en flujos patrimoniales, trazabilidad absoluta, auditoría obligatoria, documentación antes que código), cada uno con Enunciado, Fundamento, Implica y Prohíbe. El documento se declara explícitamente como explicación razonada de la Constitución, no como fuente de reglas nuevas: ante cualquier diferencia, prevalece la Constitución.

**Alternativas consideradas:**
Dejar `00-PRINCIPIOS-DEL-ERP.md` como placeholder indefinidamente, remitiendo siempre a la Constitución. Se descartó porque el propio nombre del archivo (`00-`, antes incluso de la Visión) sugiere que su función es ser la puerta de entrada conceptual al resto de la documentación, y dejarlo vacío rompía esa expectativa de estructura.

**Consecuencias:**
- Si un principio nuevo se agrega a la Constitución, debe reflejarse también aquí con su fundamento; si se retira de la Constitución, debe retirarse o marcarse como obsoleto aquí también, para no dejar los tres documentos (00, 01, 02) desalineados.
- Este documento no introduce obligaciones nuevas para los módulos; sirve solo como referencia de consulta.

### [2026-07-14] Redacción completa de 03-ARQUITECTURA-GENERAL.md en siete capas

**Contexto:**
`01-VISION-ERP.md` (sección 9) esbozaba la arquitectura de alto nivel en un párrafo breve, remitiendo el detalle a `03-ARQUITECTURA-GENERAL.md`, que seguía siendo un placeholder. Sin este documento no había una descripción técnica de cómo se relacionan las capas del sistema (presentación, sincronización, API, módulos, motor de flujos, datos, configuración) ni de cómo el aislamiento multiempresa y la arquitectura de eventos atraviesan esas capas.

**Decisión:**
Se redactó `03-ARQUITECTURA-GENERAL.md` describiendo el sistema en siete capas (Presentación, Persistencia Local y Sincronización, API/Puerta de Entrada, Módulos de Negocio, Motor de Flujos Patrimoniales, Modelo de Datos Maestro, Configuración de Plataforma), más dos vistas transversales (Motor de Permisos, Multiempresa) y una vista del camino que sigue todo evento de extremo a extremo. El documento describe responsabilidades y fronteras entre capas, sin prescribir stack tecnológico ni proveedores específicos — eso queda para cuando se tome esa decisión, registrada aparte.

**Alternativas consideradas:**
Describir la arquitectura como un diagrama únicamente (en `docs/diagramas/arquitectura-general.drawio`), sin texto narrativo. Se descartó porque el diagrama sigue pendiente de elaborarse visualmente y, aun cuando exista, un diagrama sin las reglas de frontera entre capas (por ejemplo, "los módulos nunca escriben directo al modelo de datos maestro") deja ambigüedad que un desarrollador o una IA podría interpretar de forma distinta.

**Consecuencias:**
- `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` y `05-MODELO-DE-DATOS-MAESTRO.md`, cuando se redacten, deben ser consistentes con las responsabilidades ya fijadas aquí para esas capas (secciones 6 y 7).
- Cualquier decisión futura de stack tecnológico (lenguajes, frameworks, proveedor cloud) debe registrarse en este mismo archivo y ser consistente con las siete capas y el camino de eventos descrito en la sección 11.
- El diagrama `docs/diagramas/arquitectura-general.drawio` queda pendiente de elaborarse como representación visual de este documento.

### [2026-07-14] Redacción completa de 04-MOTOR-DE-FLUJOS-PATRIMONIALES.md

**Contexto:**
El motor de flujos patrimoniales se mencionaba como componente central desde `01-VISION-ERP.md` y `03-ARQUITECTURA-GENERAL.md`, pero no existía un documento que especificara su contrato de comportamiento: qué es un evento, cómo se valida, cómo se aplica de forma atómica sobre uno o más flujos, cómo se derivan las proyecciones de estado, y cómo se corrigen errores sin editar el historial.

**Decisión:**
Se redactó `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` cubriendo: responsabilidades centrales del motor, estructura mínima de un evento, ciclo de vida de un evento (emisión → recepción → validación → aplicación → persistencia → confirmación/rechazo), tratamiento de los tres flujos, atomicidad en eventos multi-flujo, proyecciones de estado como vistas derivadas del historial, eventos compensatorios para corrección de errores, separación entre validación de dominio (módulo) y validación de flujo (motor), multiempresa dentro del motor, versionado de reglas, manejo de rechazos, y su relación con la sincronización offline. El documento fija comportamiento y contrato, explícitamente sin prescribir tecnología de implementación (event store, base de datos, etc.), que queda para una decisión posterior registrada aparte.

**Alternativas consideradas:**
Describir el motor únicamente como parte de `03-ARQUITECTURA-GENERAL.md`, sin un documento dedicado. Se descartó porque el motor es, por diseño, el componente que ningún módulo puede rodear (Principio 9 de `00-PRINCIPIOS-DEL-ERP.md`), y su contrato de comportamiento (qué acepta, qué rechaza, cómo garantiza atomicidad) es lo bastante extenso y crítico como para merecer su propio documento de referencia al que cada módulo nuevo deba remitirse.

**Consecuencias:**
- Todo módulo nuevo, al documentarse (Artículo 29.2 de la Constitución), debe describir qué eventos emite hacia este motor y cómo cumple la separación de validación de dominio vs. validación de flujo de la sección 9.
- `05-MODELO-DE-DATOS-MAESTRO.md`, cuando se redacte, debe ser consistente con la noción de historial de eventos inmutable + proyecciones derivadas fijada aquí (sección 7).
- El catálogo de eventos (`docs/diagramas/eventos.drawio` y `12-GLOSARIO.md`) queda como la lista formal de tipos de evento que este motor reconoce; ambos siguen pendientes de completarse.
