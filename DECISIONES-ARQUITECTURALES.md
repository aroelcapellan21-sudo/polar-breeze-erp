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

### [2026-07-14] Redacción completa de 05-MODELO-DE-DATOS-MAESTRO.md a partir de los cinco módulos ya documentados

**Contexto:**
`08-CATALOGO-DE-MODULOS.md` ya listaba las funcionalidades de los cinco módulos (Flujo de Efectivo, Inventario y Almacén, Despacho y Consignaciones, Facturación, Reportes), y `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` ya definía cómo se procesan los eventos, pero no existía un documento que tradujera esas funcionalidades a entidades de datos concretas con sus relaciones y su particionado multiempresa.

**Decisión:**
Se redactó `05-MODELO-DE-DATOS-MAESTRO.md` derivando las entidades directamente de los cinco módulos ya documentados: Empresa, Usuario, Sucursal, Rol/Permiso, Evento y Auditoría como entidades de plataforma/comunes; Producto, Cuenta, CuentaBancaria, Fondo y Vendedor como catálogos maestros compartidos; y por módulo — MovimientoCapital (Módulo 1); InventarioChofer, InventarioEncargado y NovedadInventario (Módulo 2); Consignacion, Despacho, NovedadDespacho, SolicitudRetiro y JustificacionRetiro (Módulo 3); Factura y NotaCredito (Módulo 4); ArqueoManual y ExportacionReporte (Módulo 5). El documento define entidades y relaciones a nivel conceptual, explícitamente sin tipos de dato ni esquema técnico, remitiendo ese detalle a `11-DICCIONARIO-DE-DATOS.md`.

**Alternativas consideradas:**
Esperar a que existiera el diccionario de datos (`11`) para derivar de ahí el modelo maestro. Se descartó por ser el orden inverso al natural: el modelo maestro (entidades y relaciones) debe preceder al diccionario (detalle campo por campo), no al revés — es más fácil detallar campos de entidades ya identificadas que inferir entidades a partir de una lista de campos sin agrupar.

**Consecuencias:**
- `11-DICCIONARIO-DE-DATOS.md`, cuando se redacte, debe detallar campo por campo cada una de las entidades listadas aquí, sin introducir entidades nuevas no derivadas de un módulo documentado.
- Cualquier módulo nuevo que se agregue a `08-CATALOGO-DE-MODULOS.md` debe, al aprobarse, declarar qué entidades nuevas necesita y cómo se relacionan con las ya existentes en este documento (evitando duplicación, Artículo 4 de la Constitución).
- El diagrama `docs/diagramas/base-datos.drawio` queda pendiente de elaborarse como representación visual de este modelo.

### [2026-07-14] Redacción completa de 06-REGLAS-CONTABLES-Y-FINANCIERAS.md, con plan de cuentas propuesto pendiente de validación contable

**Contexto:**
`08-CATALOGO-DE-MODULOS.md` (Módulo 1) exige "gestionar Cuentas 1-6" sin especificar su significado contable, y `02-CONSTITUCION-ERP.md` fija los principios de los Artículos 18-20 sin desarrollar su aplicación práctica. No existía un documento que tradujera esos principios en reglas de clasificación de movimientos, plan de cuentas y tratamiento de cuentas por pagar.

**Decisión:**
Se redactó `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` desarrollando: la clasificación obligatoria de todo movimiento de capital en los cuatro `Fondo` (Costo, Venta, Distribución, Mantenimiento), una **propuesta** de plan de cuentas para las Cuentas 1-6 (Caja General, Bancos, Cuentas por Cobrar, Cuentas por Pagar, Costos Operativos, Gastos de Mantenimiento), las reglas de cuentas por pagar y pagos parciales como eventos independientes, reglas de cierre de periodos sin retroactividad, y la relación del arqueo manual con la conciliación de saldos. Dado que el significado de las Cuentas 1-6 no estaba definido en ningún documento previo, se marcó explícitamente esa sección (3) como propuesta de arquitectura pendiente de validación por un contador o responsable financiero, y el Estado del documento se dejó en "Borrador" en lugar de "Vigente".

**Alternativas consideradas:**
Dejar la sección de plan de cuentas vacía ("pendiente de definición contable") en lugar de proponer una interpretación. Se descartó porque el propósito del documento es servir de referencia completa para el desarrollo, y una sección vacía no permite evaluar si el resto de las reglas (cuentas por pagar, cierre de periodos) son coherentes con un plan de cuentas concreto; se prefirió una propuesta explícita y claramente marcada como no validada, sobre un vacío sin punto de partida.

**Consecuencias:**
- Ningún módulo puede implementar lógica dependiente del significado de las Cuentas 1-6 hasta que esta sección sea revisada y aprobada formalmente por un responsable financiero, y esa aprobación quede registrada aquí.
- Si el plan de cuentas real difiere del propuesto, la actualización de la sección 3 debe registrarse como una nueva decisión de reemplazo (Artículo 14.3 de la Constitución), no como edición silenciosa.
- El Estado de `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` debe pasar de "Borrador" a "Vigente" solo después de esa validación.

### [2026-07-14] Redacción completa de 07-FLUJOS-DE-NEGOCIO.md como capa procedimental sobre el motor y el modelo de datos

**Contexto:**
`04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` explica cómo el sistema procesa eventos y `05-MODELO-DE-DATOS-MAESTRO.md` define qué entidades existen, pero ningún documento describía, paso a paso, los procesos operativos reales (quién hace qué, en qué orden, con qué precondiciones) que un usuario de Polar Breeze ejecuta día a día.

**Decisión:**
Se redactó `07-FLUJOS-DE-NEGOCIO.md` con 12 flujos (F1 a F12) cubriendo el ciclo completo: ingreso de capital, compra y recepción de mercancía, conciliación chofer/encargado, novedades de cuarto frío, consignación y despacho, solicitud/justificación de retiro, facturación, alta de producto, nota de crédito, pago de cuentas por pagar, arqueo manual y exportación de reportes. Cada flujo sigue la misma estructura (Actores, Precondiciones, Pasos, Eventos generados, Reglas aplicables) y propone nombres de evento consistentes con el Artículo 15 de la Constitución.

**Alternativas consideradas:**
Integrar estos flujos como ejemplos dentro de `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` en lugar de un documento aparte. Se descartó porque el motor describe comportamiento general del sistema (aplicable a cualquier evento), mientras que los flujos de negocio son específicos del dominio operativo de Polar Breeze (chofer, encargado, cuarto frío, consignación); mezclar ambos niveles de abstracción en un mismo documento dificultaría mantener cada uno actualizado por separado.

**Consecuencias:**
- Los nombres de evento propuestos en este documento (`CapitalIngresado`, `MercanciaRecibida`, `ConsignacionCreada`, etc.) deben consolidarse formalmente en `docs/diagramas/eventos.drawio` y `12-GLOSARIO.md`; hasta entonces son un borrador de nomenclatura, no el catálogo oficial.
- Cualquier flujo de negocio nuevo que se agregue en el futuro debe seguir la misma estructura (Actores, Precondiciones, Pasos, Eventos generados, Reglas aplicables) para mantener consistencia.
- Los diagramas `flujo-capital.drawio`, `flujo-mercancia.drawio` y `flujo-informacion.drawio` quedan pendientes de elaborarse como representación visual de estos flujos.

### [2026-07-14] Redacción completa de 10-PLAN-MAESTRO-DE-IMPLEMENTACION.md en 9 fases

**Contexto:**
Con `00` a `09` ya redactados (principios, visión, constitución, arquitectura, motor, modelo de datos, reglas contables, flujos de negocio, catálogo de módulos, estándares), no existía un documento que definiera el **orden** de implementación: qué se construye primero, qué depende de qué, y cuándo se considera cada parte completa.

**Decisión:**
Se redactó `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` con 9 fases: Fase 0 (fundamentos arquitectónicos: multiempresa, motor de eventos, motor de permisos, offline-first probado de extremo a extremo, configuración de plataforma), Fase 1 (catálogos maestros compartidos), Fases 2-6 (un módulo del catálogo por fase, en el orden Flujo de Efectivo → Inventario → Despacho/Consignaciones → Facturación → Reportes, reflejando sus dependencias reales), Fase 7 (incorporación de una segunda empresa real como prueba de que el aislamiento multiempresa funciona en la práctica), y Fase 8 (crecimiento continuo, sin fin definido). Se documentó explícitamente que la Fase 2 (Flujo de Efectivo) está bloqueada hasta que el plan de cuentas Borrador de `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` sea validado formalmente.

**Alternativas consideradas:**
Ordenar las fases siguiendo la numeración de los módulos en `08-CATALOGO-DE-MODULOS.md` (Flujo de Efectivo primero por ser "Módulo 1") sin analizar dependencias reales. Se descartó porque el orden de un catálogo es solo de presentación; el orden real de construcción depende de qué catálogos y fundamentos necesita cada módulo (por ejemplo, Facturación depende de que Capital e Inventario ya existan), y ese análisis de dependencias es precisamente el valor que este documento debe aportar.

**Consecuencias:**
- Ningún equipo de desarrollo debe iniciar la implementación del Módulo 1 (Fase 2) hasta que el plan de cuentas de `06` deje de estar en estado Borrador.
- La Fase 7 (segunda empresa real) es el hito formal que valida en producción la decisión de "Arquitectura multiempresa desde el origen" ya registrada anteriormente en este archivo; su resultado debe registrarse aquí como una nueva entrada cuando ocurra.
- Cambios futuros al orden de fases o a sus criterios de salida deben registrarse como nueva decisión antes de aplicarse (regla que este mismo documento se autoimpone en su sección 11).

### [2026-07-14] Redacción completa de 11-DICCIONARIO-DE-DATOS.md campo por campo

**Contexto:**
`05-MODELO-DE-DATOS-MAESTRO.md` dejaba explícitamente el detalle campo por campo de cada entidad para este documento (ver su sección Observaciones), y hasta ahora `11-DICCIONARIO-DE-DATOS.md` era un placeholder vacío.

**Decisión:**
Se redactó `11-DICCIONARIO-DE-DATOS.md` detallando las ~24 entidades ya identificadas en `05-MODELO-DE-DATOS-MAESTRO.md`, con tipos de dato **conceptuales** (Código, Texto corto/largo, Número entero, Monto, Fecha/Hora, Booleano, Enumeración, Referencia — sin tipos de columna técnicos), obligatoriedad y reglas de valor por campo. Se definió un bloque de "campos comunes heredados" (`empresaId`, `sucursalId`, `código`, `estado`, `creadoPor`, `creadoEn`, `version`) para no repetirlos en cada entidad, y cada sección de entidad lista solo sus campos específicos adicionales.

**Alternativas consideradas:**
Detallar el diccionario con tipos de dato técnicos concretos (string, integer, timestamp de una base de datos específica). Se descartó porque el repositorio es exclusivamente documental (regla del `README.md`: "no escribir código") y porque atarse a tipos técnicos antes de elegir la tecnología de persistencia (aún no decidida, ver `03-ARQUITECTURA-GENERAL.md` sección 6-7) generaría documentación que contradice al código real en cuanto se tome esa decisión.

**Consecuencias:**
- Ninguna entidad puede agregarse a `05-MODELO-DE-DATOS-MAESTRO.md` sin detallarse aquí, y viceversa (regla que este documento se autoimpone en su sección 11).
- Cuando se elija la tecnología de persistencia, el mapeo de estos tipos conceptuales a tipos técnicos concretos se documenta en el repositorio de código, no aquí.
- Los valores de Enumeración documentados aquí (por ejemplo, tipos de `NovedadInventario` o `NovedadDespacho`) son la lista cerrada autorizada; agregar un valor nuevo requiere actualizar este documento antes de usarse en desarrollo (Artículo 29.3 de la Constitución).

### [2026-07-14] Redacción completa de 12-GLOSARIO.md y formalización del catálogo de eventos (Artículo 15)

**Contexto:**
Varios documentos ya redactados (`04`, `05`, `07`, `11`) referenciaban `12-GLOSARIO.md` como el lugar donde se consolidaría la terminología del sistema y, junto con `docs/diagramas/eventos.drawio`, el catálogo formal de eventos exigido por el Artículo 15 de la Constitución. Los 20 nombres de evento propuestos en `07-FLUJOS-DE-NEGOCIO.md` seguían marcados explícitamente como "borrador de nomenclatura, no el catálogo oficial".

**Decisión:**
Se redactó `12-GLOSARIO.md` con tres secciones: (A) términos de arquitectura (empresaId, sucursalId, evento, proyección, fuente de verdad, soft delete, motor de flujos patrimoniales, etc.), (B) términos de negocio (arqueo, consignación, cuarto frío, despacho, novedad, nota de crédito, etc.), y (C) el catálogo formal de los 20 eventos del sistema (tabla con evento, flujo(s) afectado(s), entidad afectada y flujo de negocio de origen), cerrando así el estado de "borrador" que tenían esos nombres desde `07`.

**Alternativas consideradas:**
Mantener el catálogo de eventos únicamente en `docs/diagramas/eventos.drawio` (diagrama), sin tabla textual en el glosario. Se descartó porque el diagrama sigue vacío/pendiente de elaborarse visualmente, y siete documentos ya redactados dependen de nombres de evento consistentes; postergar la formalización textual hasta que existiera el diagrama habría dejado la nomenclatura sin fuente de verdad por tiempo indefinido.

**Consecuencias:**
- Los 20 eventos de la sección C son ahora los nombres oficiales; `07-FLUJOS-DE-NEGOCIO.md` queda consistente con ellos sin requerir cambios (los nombres no se alteraron, solo se formalizaron).
- Cualquier evento nuevo que un módulo futuro necesite emitir debe agregarse primero a esta tabla (Artículo 15.3 de la Constitución) antes de usarse en desarrollo.
- El diagrama `docs/diagramas/eventos.drawio` queda pendiente de elaborarse como representación visual de esta misma tabla, y debe mantenerse consistente con ella si se actualiza.

### [2026-07-14] Redacción completa de 13-HISTORIAL-DE-VERSIONES.md y adopción de un esquema de versionado de documentación

**Contexto:**
`10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` (sección 11) ya preveía que el avance se reflejaría en `13-HISTORIAL-DE-VERSIONES.md`, pero el documento seguía siendo un placeholder. Con 15 commits acumulados desde la creación del repositorio, ya no era trivial saber, sin revisar `git log`, qué contenido existía en cada momento.

**Decisión:**
Se redactó `13-HISTORIAL-DE-VERSIONES.md` adoptando un esquema `vMAYOR.MENOR` para la documentación (distinto del futuro versionado semántico del software, que se definirá cuando exista código): MAYOR para cambios que alteran una regla constitucional, reestructuran el modelo de datos o reemplazan una decisión previa; MENOR para contenido nuevo que no rompe lo existente. Se reconstruyó el historial completo desde `ab1f0f9` (v0.1) hasta el commit de esta misma entrada (v0.16) a partir de `git log`, y se agregó una tabla de estado de completitud de toda la documentación a la fecha.

**Alternativas consideradas:**
No adoptar un esquema de versión numerado y limitarse a un changelog de fechas y commits. Se descartó porque distinguir cambios MAYOR (como la reescritura completa de la Constitución en `1108020`) de cambios MENOR (como redactar un documento antes vacío) aporta señal real sobre el impacto de cada cambio, que una lista plana de commits no transmite por sí sola.

**Consecuencias:**
- Toda futura redacción o modificación sustantiva de un documento en `docs/` debe agregar una entrada nueva a este historial como parte del mismo commit.
- El avance de las fases de `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md`, cuando inicie la implementación de código, se registrará aquí como nuevas entradas de versión.
- La tabla de estado de completitud debe actualizarse cada vez que un documento pase de "Pendiente"/"Borrador" a "Vigente", o viceversa.

### [2026-07-14] Redacción completa de 99-FILOSOFIA-DEL-SISTEMA.md como cierre reflexivo de la biblioteca

**Contexto:**
Con `00` a `13` ya redactados, la biblioteca cubría principios operativos (`00`), reglas formales (`02`) y todo el detalle técnico intermedio, pero no existía un documento que explicara el espíritu común del que se derivan esas reglas, ni un mensaje explícito dirigido a quien continúe el proyecto en el futuro (persona nueva o IA en otra conversación).

**Decisión:**
Se redactó `99-FILOSOFIA-DEL-SISTEMA.md` con ocho secciones: la filosofía resumida en una frase, el patrimonio como verdad reconstruible (no opinión editable), la documentación como memoria colectiva del proyecto, crecer sin reescribir, humildad arquitectónica (declarar explícitamente lo que sigue en Borrador o sin validar, en vez de fingir certeza), el rol de la IA en el proyecto, qué significa "terminar" el ERP (nunca, por diseño), y un mensaje directo a quien retome el proyecto. Se decidió explícitamente que este documento no introduce reglas ni entidades nuevas — solo da contexto a las que ya existen.

**Alternativas consideradas:**
Fusionar este contenido dentro de `00-PRINCIPIOS-DEL-ERP.md`, evitando un documento adicional. Se descartó porque `00` está escrito en un registro operativo (Enunciado/Fundamento/Implica/Prohíbe, consultable rápidamente) mientras que este contenido es deliberadamente reflexivo y narrativo; mezclar ambos registros habría diluido la utilidad de consulta rápida de `00` sin aportar claridad adicional.

**Consecuencias:**
- Con esta entrada, todos los documentos numerados (`00` a `13`, `99`) del catálogo original tienen contenido completo; solo quedan pendientes los 6 diagramas `.drawio` (sin contenido visual) mencionados en `13-HISTORIAL-DE-VERSIONES.md`.
- El "Mensaje a Quien Continúe Este Proyecto" (sección 8) queda como el punto de partida recomendado para cualquier persona o IA nueva en el proyecto, antes de profundizar en el detalle técnico.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.17) reflejando este commit y marcando `99` como Vigente en su tabla de completitud.

### [2026-07-14] Agregado el Artículo 30 — Principio de Huella Permanente a la Constitución

**Contexto:**
El usuario solicitó agregar un artículo nuevo a `02-CONSTITUCION-ERP.md` exigiendo que todo dato creado, modificado, aprobado, anulado o utilizado por el ERP deje una huella permanente e irreversible, incluyendo las decisiones automáticas tomadas por el propio sistema o por una IA.

**Decisión:**
Se agregó el **Artículo 30 — Principio de Huella Permanente** al final del cuerpo de artículos de la Constitución (después del Artículo 29, antes de la Convención de Cambios), con el texto exacto provisto por el usuario: 30.1 (todo dato deja huella permanente), 30.2 (ningún movimiento sin evidencia suficiente para reconstruir qué/quién/cuándo/dónde/por qué), 30.3 (contenido mínimo de la huella: id de evento, `empresaId`, `sucursalId`, usuario, fecha/hora, dispositivo/cliente de origen, entidad afectada, estado anterior y posterior, motivo, relación con otros eventos), 30.4 (la huella no se elimina ni modifica, solo se complementa con eventos de corrección) y 30.5 (las decisiones automáticas de sistema o IA también dejan huella, indicando la regla/algoritmo/modelo que las originó).

**Alternativas consideradas:**
No se consideraron alternativas de contenido: el usuario proveyó el texto completo del artículo a incorporar. La única decisión de arquitectura documental fue de ubicación (al final del cuerpo de artículos, manteniendo la Convención de Cambios como cierre del documento) y de no editar retroactivamente las referencias a "29 artículos" en decisiones e historial ya registrados (Artículo 14.3: las decisiones ya registradas no se editan, se complementan con una nueva).

**Consecuencias:**
- El Artículo 30 formaliza y refuerza, a nivel constitucional, lo que los Artículos 5, 7 y 8 ya establecían por separado (eventos inmutables, trazabilidad absoluta, auditoría obligatoria) — 30.3 en particular fija el contenido mínimo concreto de esa evidencia, más detallado que lo que tenían el Artículo 7.1 y 8.3 hasta ahora.
- 30.5 extiende explícitamente la obligación de trazabilidad a decisiones automáticas/IA, complementando al Artículo 26 (Reglas para IA) con un requisito de huella específico para algoritmos y modelos de decisión.
- Las referencias a "29 artículos" en decisiones anteriores de este archivo y en `13-HISTORIAL-DE-VERSIONES.md` quedan como registro histórico válido de su momento y no se editan; a partir de esta entrada, la Constitución tiene 30 artículos.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.18) reflejando este cambio como MAYOR (modifica la Constitución).

### [2026-07-14] Contenido de docs/diagramas/README.md completado; anexos/README.md se deja sin cambios

**Contexto:**
El usuario pidió continuar redactando el contenido de todos los archivos que faltan. A esa fecha, todos los documentos numerados (`00`-`13`, `99`) ya tenían contenido completo; lo único con la sección "Contenido" todavía vacía eran los dos README de carpeta (`docs/diagramas/README.md` y `docs/anexos/README.md`) y los 6 archivos `.drawio`, estos últimos excluidos explícitamente por instrucción previa del usuario (deben quedar en blanco).

**Decisión:**
Se completó la sección "Contenido" de `docs/diagramas/README.md` con una tabla que lista los 6 diagramas ya creados, qué representa cada uno y qué documento(s) complementa. **No** se agregó contenido a `docs/anexos/README.md`: la carpeta `anexos/` no contiene ningún archivo todavía, así que su sección "Contenido" describiría documentos inexistentes si se llenara — se deja como está hasta que exista al menos un anexo real que listar.

**Alternativas consideradas:**
Inventar una lista de anexos previstos ("plantilla de próximos anexos") para no dejar la sección vacía. Se descartó por ser inconsistente con el principio de humildad arquitectónica de `99-FILOSOFIA-DEL-SISTEMA.md` (sección 5): declarar abiertamente que algo no existe todavía es preferible a simular contenido que no está respaldado por nada real.

**Consecuencias:**
- Con esta entrada, todo el contenido "redactable" con la información disponible queda completo. Lo único pendiente en el repositorio es: contenido visual de los 6 `.drawio` (fuera de alcance por instrucción del usuario) y el llenado de `anexos/README.md` cuando exista al menos un anexo real.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.19) reflejando este cambio.

### [2026-07-14] Regla permanente de entrega de resúmenes largos vía CLAUDE.md

**Contexto:**
El usuario pidió que, en este repositorio, todo resumen, conclusión o documento de más de 20 líneas se guarde como archivo en lugar de mostrarse completo en terminal, mostrando en pantalla solo la ruta y un resumen de máximo 5 líneas.

**Decisión:**
Se creó `CLAUDE.md` en la raíz del repositorio con esta regla permanente: contenido de más de 20 líneas se guarda en `~/polar-breeze-erp/resumenes/` con nombre descriptivo y fecha (`AAAA-MM-DD-descripcion-corta.md`); en terminal solo se muestra la ruta y un resumen de hasta 5 líneas. Se aclaró explícitamente que la regla aplica a resúmenes/informes de trabajo, no al contenido normal de `docs/` (que se sigue editando en su propio archivo) ni a respuestas breves. Se creó la carpeta `resumenes/` con su primer archivo: un inventario de todos los archivos creados/modificados en el repositorio con su estado actual.

**Alternativas consideradas:**
Mantener el comportamiento previo de publicar resúmenes largos como Artifact (enlace web). Se descartó como regla por defecto para este repositorio porque el usuario reportó que ese enlace no le abría, y porque pidió explícitamente un mecanismo basado en archivo local versionado en el propio repo — más consistente además con la filosofía de este proyecto de que el conocimiento vive en el repositorio (`99-FILOSOFIA-DEL-SISTEMA.md`, sección 3), no en un servicio externo.

**Consecuencias:**
- `resumenes/` es una carpeta de trabajo/bitácora, explícitamente fuera de la biblioteca oficial de arquitectura (`docs/`); no sustituye a este archivo ni a `13-HISTORIAL-DE-VERSIONES.md`.
- Cualquier resumen largo futuro generado en este repositorio debe seguir este mismo patrón: archivo en `resumenes/` + resumen de 5 líneas en terminal.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.20) reflejando la creación de `CLAUDE.md` y la carpeta `resumenes/`.

### [2026-07-14] Primer anexo real: VALIDACIONES-PENDIENTES-CONTADOR.md

**Contexto:**
`06-REGLAS-CONTABLES-Y-FINANCIERAS.md` quedó en estado Borrador porque su plan de cuentas (sección 3) y varias reglas asociadas (fondos, cuentas por pagar, cierre de periodos, arqueo) son propuestas de arquitectura sin validar por un contador. Esas menciones estaban dispersas en varios documentos (`06`, `10`, `11`, `99`, `13`) sin un único lugar accionable que las consolidara como tareas concretas. `docs/anexos/README.md` seguía sin contenido porque no existía ningún anexo real todavía.

**Decisión:**
Se creó el primer anexo real, `docs/anexos/VALIDACIONES-PENDIENTES-CONTADOR.md`, con un checklist de 6 ítems (plan de cuentas, clasificación de fondos, cuentas por pagar y pagos parciales, periodo contable y cierre, tolerancia de diferencias en arqueo, y correspondencia con el diccionario de datos), cada uno con su referencia exacta, qué debe confirmar el contador, y su estado (Pendiente/Validado). Se definió el procedimiento de cierre: cada ítem validado se registra como decisión aparte en este archivo antes de marcarse como Validado en el checklist. Se actualizó `docs/anexos/README.md` para listar este anexo, ahora que existe contenido real que describir.

**Alternativas consideradas:**
Dejar las menciones a validación contable dispersas en sus documentos originales, sin consolidarlas. Se descartó porque el propósito explícito de un anexo (`docs/anexos/README.md`) es reunir justamente este tipo de material de soporte, y un checklist único con estado por ítem es más accionable para un contador externo al proyecto que tener que leer cinco documentos distintos para encontrar qué falta validar.

**Consecuencias:**
- Cuando los 6 ítems del checklist pasen a Validado, `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` puede pasar de Borrador a Vigente, y la Fase 2 de `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` queda desbloqueada.
- Cada validación de un ítem debe registrarse aquí como una nueva decisión antes de marcarse como Validado en el anexo — nunca editarse el checklist en silencio.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.21) reflejando este anexo y la actualización de `docs/anexos/README.md`.

### [2026-07-14] Renombrado el primer anexo a 01-PENDIENTE-VALIDACION-CONTABLE.md

**Contexto:**
El usuario pidió renombrar `docs/anexos/VALIDACIONES-PENDIENTES-CONTADOR.md` a `docs/anexos/01-PENDIENTE-VALIDACION-CONTABLE.md`, adoptando un prefijo numérico para los anexos, consistente con la convención ya usada en `docs/` (`00` a `13`, `99`).

**Decisión:**
Se renombró el archivo con `git mv` (sin recrearlo, preservando su historial en `git`) y se actualizó la única referencia vigente a su nombre, en `docs/anexos/README.md`. Las referencias al nombre anterior en decisiones ya registradas de este archivo y en `13-HISTORIAL-DE-VERSIONES.md` **no se editaron**: son registro histórico válido de cómo se llamaba el archivo en el momento en que se creó (Artículo 14.3 de la Constitución).

**Alternativas consideradas:**
Ninguna: fue una instrucción directa del usuario sobre convención de nombres, sin ambigüedad de contenido.

**Consecuencias:**
- Cualquier anexo futuro en `docs/anexos/` debe seguir la misma convención de prefijo numérico (`02-...`, `03-...`, etc.) para mantener consistencia con este primer anexo.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.22) reflejando el renombre.

### [2026-07-14] Creado 00-MANIFIESTO-DEL-ERP.md — coexiste con 00-PRINCIPIOS-DEL-ERP.md bajo el mismo prefijo

**Contexto:**
El usuario pidió crear `docs/00-MANIFIESTO-DEL-ERP.md` con un párrafo de apertura provisto textualmente, y desarrollar un manifiesto completo a partir de toda la documentación existente. El repositorio ya tenía `docs/00-PRINCIPIOS-DEL-ERP.md` ocupando ese mismo prefijo numérico desde `721be71`.

**Decisión:**
Se creó `docs/00-MANIFIESTO-DEL-ERP.md` exactamente en la ruta solicitada, con el párrafo de apertura provisto por el usuario sin modificar, seguido de un manifiesto en tono declarativo (no operativo como `00-PRINCIPIOS`, ni articulado como `02-CONSTITUCION`, ni reflexivo como `99-FILOSOFIA`) que sintetiza: memoria/huella permanente, patrimonio demostrable vs. opinable, los tres flujos como una sola unidad, multiempresa desde el origen, offline-first, corrección hacia adelante sin borrar, documentación antes que código, el rol de la IA, y la naturaleza continua (nunca "terminada") del sistema. El documento se declara explícitamente como síntesis de la Constitución, no como fuente de reglas nuevas. Se actualizó `README.md` para reflejar ambos archivos `00-*` en la estructura. **No se resolvió la colisión de prefijo** — quedan dos documentos numerados `00` (`00-MANIFIESTO-DEL-ERP.md` y `00-PRINCIPIOS-DEL-ERP.md`), ordenados alfabéticamente entre sí (Manifiesto antes que Principios) en cualquier listado de archivos.

**Alternativas consideradas:**
Renumerar automáticamente uno de los dos documentos (por ejemplo, mover el manifiesto a un prefijo libre como `14`) para evitar la colisión. Se descartó sin consultar al usuario porque la ruta exacta fue parte explícita de su instrucción; la colisión se señaló en la respuesta al usuario en lugar de resolverse unilateralmente, dejando la decisión de renumerar (o mantener ambos bajo `00` como "nivel fundacional") en sus manos.

**Consecuencias:**
- Mientras no se decida lo contrario, `docs/` tiene dos archivos con prefijo `00`; cualquier herramienta o persona que asuma "un archivo por número" debe tratarlo como una excepción conocida y documentada aquí.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.23) y su tabla de completitud debe incluir la nueva fila para `00-MANIFIESTO-DEL-ERP.md`.
- Si el usuario decide renumerar cualquiera de los dos archivos, ese cambio se registra como una nueva decisión, no como edición silenciosa de esta.

### [2026-07-14] Fortalecido 00-MANIFIESTO-DEL-ERP.md — marco de "Sistema Operativo Empresarial" y tres bloques nuevos

**Contexto:**
El usuario pidió ampliar `docs/00-MANIFIESTO-DEL-ERP.md` sin tocar lo ya redactado: (1) explicitar en el Objetivo que el manifiesto describe al ERP como el núcleo patrimonial de un **Sistema Operativo Empresarial basado en Flujos Patrimoniales**, donde el ERP gobierna el patrimonio y las aplicaciones operativas son puntos de entrada de eventos; (2) agregar un bloque sobre que nada existe aislado y el flujo continúa mientras exista la empresa; (3) agregar una sección nueva sobre que el patrimonio nunca aparece ni desaparece, solo cambia de forma, y que pérdidas/mermas/daños/condonaciones se registran y explican, nunca se ocultan; (4) cerrar el documento con una declaración final ("No administramos datos. Administramos patrimonio...").

**Decisión:**
Se agregó un párrafo al Objetivo introduciendo explícitamente el marco de "Sistema Operativo Empresarial basado en Flujos Patrimoniales" (ERP = núcleo patrimonial; aplicaciones operativas = puntos de entrada de eventos). Se agregaron dos secciones nuevas ("Nada existe aislado." y "El patrimonio cambia de forma. Nunca aparece ni desaparece.") antes de la sección de cierre ya existente ("Esto no termina. Se sostiene."), con el texto exacto provisto por el usuario como cuerpo de cada una. Se agregó la declaración de cierre final como párrafo adicional después de esa sección, sin reemplazarla. Ningún contenido previo se modificó ni se eliminó.

**Alternativas consideradas:**
Reescribir también `03-ARQUITECTURA-GENERAL.md` para introducir formalmente el término "Sistema Operativo Empresarial" en la descripción técnica de capas. Se descartó porque el usuario acotó explícitamente el pedido a `00-MANIFIESTO-DEL-ERP.md`; introducir ese marco en documentos técnicos sin que se pida es exactamente el tipo de extensión no solicitada que este proyecto busca evitar (ver `09-ESTANDARES-DE-DESARROLLO.md`, regla "una sola cosa a la vez").

**Consecuencias:**
- El término "Sistema Operativo Empresarial basado en Flujos Patrimoniales" queda introducido únicamente en el manifiesto por ahora; si se decide adoptarlo formalmente como marco arquitectónico en `01-VISION-ERP.md` o `03-ARQUITECTURA-GENERAL.md`, eso requiere su propia decisión y documentación, no se asume aquí.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.24) reflejando este cambio.

### [2026-07-14] Agregadas las entidades Cliente, Proveedor y Obligacion (Cuenta por Pagar) al modelo de datos maestro

**Contexto:**
Una evaluación completa de la biblioteca de arquitectura (`resumenes/2026-07-14-evaluacion-completa-documentacion.md`) detectó que el Artículo 16.1 de `02-CONSTITUCION-ERP.md` exige explícitamente "clientes" y "proveedores" como catálogos maestros, pero ninguno de los dos existía en `05-MODELO-DE-DATOS-MAESTRO.md` ni en `11-DICCIONARIO-DE-DATOS.md`. Además, los flujos F2 y F10 de `07-FLUJOS-DE-NEGOCIO.md` y la sección 5 de `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` dependen de una "obligación" con contraparte, monto original inmutable, fecha de vencimiento y saldo pendiente proyectado (Artículo 20 de la Constitución), pero esa obligación tampoco existía como entidad — solo como evento (`ObligacionRegistrada`, `PagoRegistrado`) sin registro estructural que integridad referencial (Artículo 10) pudiera exigir.

**Decisión:**
Se agregaron tres entidades a `05-MODELO-DE-DATOS-MAESTRO.md` (secciones 4 y 5) y a `11-DICCIONARIO-DE-DATOS.md` (secciones 5 y 6): **Cliente** (código, nombre, `empresaId`) como catálogo maestro compartido, consumible por el Módulo 4 — Facturación y por el Módulo 1 cuando exista venta a crédito contra la Cuenta 3 — Cuentas por Cobrar; **Proveedor** (código, nombre, tipo — proveedor de mercancía / transportista / consignatario / otro — `empresaId`) como catálogo maestro compartido, representando a todo tercero con quien la empresa puede contraer una obligación (Artículo 20.1); y **Obligacion** (Cuenta por Pagar) como entidad del Módulo 1 — Flujo de Efectivo, con `Proveedor` referenciado, monto original, fecha de vencimiento, `Cuenta` asociada (Cuenta 4), saldo pendiente como proyección y evento de origen. Se agregó también el campo opcional `obligacionReferenciada` a `MovimientoCapital`, para que un pago pueda vincularse a la obligación que salda (Artículo 20.2), y se actualizó la sección 10 de `05` (Integridad Referencial) con las dos reglas de referencia correspondientes.

**Alternativas consideradas:**
Modelar `Obligacion.contraparte` con un cuarto tipo de entidad distinto por cada tercero (Proveedor, Transportista, Consignatario). Se descartó por ser una duplicación de catálogo para el mismo concepto (tercero acreedor de la empresa) que el propio Artículo 4 de la Constitución prohíbe; en su lugar, `Proveedor` incluye un campo `tipo` que distingue el rol del tercero sin crear catálogos paralelos.
También se consideró vincular `Cliente` a `Factura` en esta misma decisión para resolver de una vez la ausencia de ventas a crédito. Se descartó porque esa pregunta (¿Polar Breeze factura a crédito o solo de contado?) es una decisión de negocio pendiente de confirmar, no una corrección de modelo de datos; se deja fuera de alcance de esta decisión y queda señalada como pendiente.

**Consecuencias:**
- El Artículo 16.1 de la Constitución queda satisfecho en su totalidad por el modelo de datos maestro: los seis catálogos que menciona (productos, cuentas, vendedores, bancos, clientes, proveedores) existen ahora en `05` y `11`.
- `Factura` **no** se modificó para referenciar `Cliente` en esta decisión; la Cuenta 3 — Cuentas por Cobrar del plan de cuentas sigue sin flujo de negocio que la alimente. Esa brecha queda pendiente y debe resolverse como una decisión aparte cuando se confirme si existen ventas a crédito.
- Cualquier módulo futuro que dependa de `Obligacion`, `Cliente` o `Proveedor` debe consumir estas entidades ya existentes, nunca reimplementarlas (Artículo 4 y 16.1 de la Constitución).
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.25) reflejando este cambio como MENOR (agrega entidades sin reestructurar el modelo existente).

### [2026-07-14] Agregado el campo `moneda` a Empresa — una moneda funcional por empresa en el modelo multiempresa

**Contexto:**
La misma evaluación completa (`resumenes/2026-07-14-evaluacion-completa-documentacion.md`, sección 3, punto 3) señaló que el Artículo 28.1 de `02-CONSTITUCION-ERP.md` anticipa el crecimiento del ERP hacia "nuevas monedas", pero ni `Empresa` ni el tipo conceptual `Monto` (`11-DICCIONARIO-DE-DATOS.md`, sección 1) declaraban moneda alguna. Sin ese campo, el modelo asumía implícitamente una sola moneda global para todo el ecosistema — justo el tipo de supuesto de "empresa única" que el Artículo 2 de la Constitución prohíbe extender a cualquier dimensión del sistema.

**Decisión:**
Se agregó el campo `moneda` (Código, formato ISO 4217, por ejemplo `USD` o `DOP`) a la entidad `Empresa` en `05-MODELO-DE-DATOS-MAESTRO.md` (sección 2) y `11-DICCIONARIO-DE-DATOS.md` (sección 3), como su **moneda funcional**: la moneda base en la que se expresan todos los montos de esa empresa. Se actualizó la definición del tipo conceptual `Monto` (`11`, sección 1) para dejar explícito que todo monto se interpreta siempre en la moneda funcional de la `empresaId` a la que pertenece. Se agregó una convención general en `05` (sección 1): ninguna entidad mezcla montos de dos monedas sin conversión explícita y trazable.

Deliberadamente **no** se agregó un campo `moneda` a `MovimientoCapital`, `Obligacion`, `CuentaBancaria`, `Fondo`, `Factura` ni a ninguna otra entidad monetaria: la moneda de cualquier `Monto` ya es deducible de forma no ambigua a través de su `empresaId` → `Empresa.moneda`, y duplicar el campo en cada entidad monetaria violaría la prohibición de duplicar información (Artículo 4 de la Constitución) sin necesidad real, dado que una empresa opera en una única moneda funcional.

**Alternativas consideradas:**
Agregar `moneda` como campo propio de cada entidad monetaria (`MovimientoCapital`, `Obligacion`, `Factura`, etc.), a modo de "snapshot" redundante. Se descartó por ser una duplicación no justificada del mismo dato mientras la moneda funcional de una empresa se mantenga estable en el tiempo; si en el futuro una empresa cambiara su moneda funcional (redenominación), el tratamiento de los montos históricos ya registrados en la moneda anterior es una decisión de arquitectura aparte (análoga al versionado de reglas del Artículo 11), no resuelta ni asumida por esta decisión.
También se consideró modelar una entidad `Moneda` (catálogo con símbolo, decimales, etc.) en lugar de un código ISO 4217 simple. Se descartó por ser sobre-construcción para el alcance actual (una moneda funcional fija por empresa, sin conversión ni tasas de cambio); un código ISO 4217 es suficiente y evita introducir un catálogo sin consumidores reales todavía.

**Consecuencias:**
- El modelo de datos maestro ya no asume una sola moneda global: cada `empresaId` tiene su propia moneda funcional declarada explícitamente, consistente con el Artículo 28.1 de la Constitución.
- Este cambio **no** cubre multi-moneda dentro de una misma empresa (por ejemplo, una `CuentaBancaria` en moneda extranjera, o conversión automática de tipo de cambio); `05-MODELO-DE-DATOS-MAESTRO.md` deja esa limitación explícita en sus Observaciones, consistente con la humildad arquitectónica de `99-FILOSOFIA-DEL-SISTEMA.md` (sección 5).
- Si en el futuro una empresa necesita cambiar su moneda funcional, ese cambio y el tratamiento de los montos históricos ya registrados debe registrarse como una nueva decisión aparte antes de implementarse, nunca como una edición silenciosa del campo `moneda`.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.26) reflejando este cambio como MENOR (agrega un campo sin reestructurar el modelo existente).

### [2026-07-14] Elevado 08-CATALOGO-DE-MODULOS.md al estándar del Artículo 29.2 de la Constitución

**Contexto:**
La misma evaluación completa (`resumenes/2026-07-14-evaluacion-completa-documentacion.md`, sección 4, punto 1) señaló que `08-CATALOGO-DE-MODULOS.md` seguía siendo, desde su redacción original, una lista de viñetas de funcionalidades por módulo, sin cumplir el Artículo 29.2 de `02-CONSTITUCION-ERP.md`: ningún módulo declaraba su alcance por `empresaId`/`sucursalId` (Artículo 2.8), los eventos que emite o consume (Artículo 15.3), ni los catálogos maestros que crea o consume (Artículo 16.1). El Artículo 29.1 exige exactamente esta declaración para que un módulo pueda aprobarse para desarrollo — ninguno de los cinco módulos existentes cumpliría hoy su propio criterio de aprobación. `13-HISTORIAL-DE-VERSIONES.md` marcaba además este documento como "Vigente" en su tabla de completitud, mientras el propio archivo seguía declarando Estado "En construcción" — una inconsistencia adicional detectada en la misma evaluación (sección 2.3).

**Decisión:**
Se reescribió `08-CATALOGO-DE-MODULOS.md` conservando íntegras las funcionalidades ya listadas para los cinco módulos, y se agregó a cada uno cuatro bloques nuevos derivados de documentación ya existente (sin inventar comportamiento nuevo): **Alcance multiempresa** (a partir de las entidades de `05-MODELO-DE-DATOS-MAESTRO.md`), **Eventos que emite** y **Eventos de otros módulos que le afectan** (a partir de `07-FLUJOS-DE-NEGOCIO.md` y el catálogo formal de `12-GLOSARIO.md`, sección C), y **Catálogos maestros que crea/consume** (a partir de los siete catálogos de `05`, sección 4: `Producto`, `Cuenta`, `CuentaBancaria`, `Fondo`, `Vendedor`, `Cliente`, `Proveedor`). Se ajustaron además las entradas de `Cliente` y `Proveedor` en `05` para declarar explícitamente su módulo de creación (Módulo 4 y Módulo 1 respectivamente), simétrico con cómo ya se documentaban `Producto`, `Vendedor` y `CuentaBancaria`. El Estado del documento pasó de "En construcción" a "Vigente — pendiente de revisión y aprobación formal", resolviendo también la inconsistencia con la tabla de completitud de `13-HISTORIAL-DE-VERSIONES.md`.

**Alternativas consideradas:**
Agregar solo un párrafo genérico de alcance común a los cinco módulos, en lugar de una declaración específica por módulo. Se descartó porque el Artículo 29.2 exige la declaración **por módulo**, no una nota general; un párrafo compartido no permitiría detectar, por ejemplo, que el Módulo 1 no opera a nivel de `sucursalId` mientras los Módulos 2 y 3 sí, o que el Módulo 5 es de solo lectura (Artículo 24.2) y no emite eventos que otros módulos consuman.
También se consideró resolver en esta misma decisión la ambigüedad de a qué módulo pertenece el evento `ObligacionRegistrada`/`MercanciaRecibida` de F2 (que cruza Módulo 1 y Módulo 2). Se descartó inventar una asignación exclusiva: se documentó explícitamente como un evento cruzado, atómico entre ambos módulos, consistente con cómo `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` (sección 6) ya describe los eventos multi-flujo.

**Consecuencias:**
- Los cinco módulos de `08-CATALOGO-DE-MODULOS.md` cumplen ahora el estándar mínimo de aprobación del Artículo 29.2; cualquier módulo nuevo que se agregue en el futuro debe seguir la misma estructura de cinco bloques (Funcionalidades, Alcance multiempresa, Eventos que emite, Eventos que le afectan, Catálogos maestros).
- La inconsistencia detectada entre el Estado de `08` ("En construcción") y su fila en la tabla de completitud de `13-HISTORIAL-DE-VERSIONES.md` ("Vigente") queda resuelta: ambos documentos dicen ahora lo mismo.
- Esta decisión no resuelve la inconsistencia equivalente de `09-ESTANDARES-DE-DESARROLLO.md` (mismo problema, señalado en la misma evaluación); queda fuera de alcance de esta decisión.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.27) reflejando este cambio como MENOR (completa contenido exigido sin reestructurar los módulos ya definidos).

### [2026-07-14] Fijada la jerarquía documental: la Constitución prevalece sobre la Visión

**Contexto:**
La evaluación completa (`resumenes/2026-07-14-evaluacion-completa-documentacion.md`, sección 2.1) detectó una contradicción textual entre tres documentos: el Objetivo de `02-CONSTITUCION-ERP.md` declaraba ser "la norma de más alto rango... superada únicamente por `01-VISION-ERP.md`" (la Visión ganaría en un conflicto), mientras que `99-FILOSOFIA-DEL-SISTEMA.md` (sección 8) afirmaba sin excepción que "la Constitución (02) gana", y la propia `01-VISION-ERP.md` (sección 13) ya establecía que ante tensión "se resuelve actualizando la Constitución... nunca ignorándola en la implementación" — un texto compatible con que la Constitución sea la autoridad operativa, no con que la Visión pueda anularla directamente. No existía una regla única de resolución de conflictos entre estos dos documentos.

**Decisión:**
Se preguntó al usuario cuál de tres criterios adoptar: la Constitución prevalece, la Visión prevalece, o ninguna prevalece automáticamente (cada conflicto se registra como decisión pendiente caso por caso). El usuario eligió que **la Constitución prevalece**. Se reescribió el Objetivo de `02-CONSTITUCION-ERP.md` para eliminar la frase "superada únicamente por `01-VISION-ERP.md`" y declarar explícitamente que la Constitución prevalece ante cualquier conflicto, incluido con la Visión — que queda descrita como origen y fundamento de las reglas, no como instancia superior de apelación. No fue necesario modificar `01-VISION-ERP.md` ni `99-FILOSOFIA-DEL-SISTEMA.md`: ambos ya eran consistentes con esta jerarquía.

**Alternativas consideradas:**
Las otras dos opciones presentadas al usuario (Visión prevalece; sin regla de desempate por defecto). No se evaluaron variantes adicionales de redacción: la elección de fondo fue del usuario, no una decisión técnica de arquitectura tomada unilateralmente por la IA (consistente con el Artículo 26.2 de la Constitución y la regla 10 de `09-ESTANDARES-DE-DESARROLLO.md`: ante ambigüedad de gobernanza, se pregunta, no se asume).

**Consecuencias:**
- Cualquier tensión futura detectada entre `01-VISION-ERP.md` y `02-CONSTITUCION-ERP.md` se resuelve enmendando explícitamente la Constitución (Convención de Cambios, al final de ese documento), nunca invocando la Visión para dejar sin efecto un artículo vigente en la práctica.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.28) reflejando este cambio como MAYOR (modifica una regla de gobernanza documental de la propia Constitución).

### [2026-07-14] Formalizada la coexistencia de 00-MANIFIESTO-DEL-ERP.md y 00-PRINCIPIOS-DEL-ERP.md bajo el prefijo 00

**Contexto:**
Desde la decisión "Creado 00-MANIFIESTO-DEL-ERP.md — coexiste con 00-PRINCIPIOS-DEL-ERP.md bajo el mismo prefijo" (registrada anteriormente en este archivo), el repositorio tenía dos documentos con prefijo `00`, señalada explícitamente como "colisión conocida... a la espera de decisión del usuario" y así reflejada en `13-HISTORIAL-DE-VERSIONES.md`. La evaluación completa (`resumenes/2026-07-14-evaluacion-completa-documentacion.md`, sección 2.12) volvió a señalarla como pendiente de resolver.

**Decisión:**
Se preguntó al usuario si mantener ambos documentos bajo `00`, renumerar `00-PRINCIPIOS-DEL-ERP.md`, o renumerar `00-MANIFIESTO-DEL-ERP.md`. El usuario eligió **mantener ambos bajo `00`**, formalizando el prefijo `00` como el nivel fundacional/declarativo compartido del repositorio por diseño — no un error pendiente de corregir. No se renombra ningún archivo ni se actualiza ninguna referencia cruzada existente.

**Alternativas consideradas:**
Renumerar uno de los dos documentos (Principios o Manifiesto) a un prefijo libre. No se evaluaron variantes adicionales de numeración: la elección de fondo fue del usuario.

**Consecuencias:**
- La colisión de prefijo `00` deja de estar "pendiente de decisión" y pasa a ser una característica intencional y permanente del repositorio.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse: la fila de `00-MANIFIESTO-DEL-ERP.md` en la tabla de completitud ya no dice "sin resolver", sino que refleja esta decisión como definitiva.
- Cualquier herramienta o persona que asuma "un archivo por número" en `docs/` debe seguir tratando `00` como la única excepción documentada del repositorio, ahora con carácter permanente en lugar de temporal.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.29) reflejando este cambio como MENOR (cierra una decisión pendiente sin modificar reglas ni contenido de ningún artículo).

### [2026-07-14] Limpieza de referencias cruzadas rotas y estado de 09-ESTANDARES-DE-DESARROLLO.md

**Contexto:**
La evaluación completa (`resumenes/2026-07-14-evaluacion-completa-documentacion.md`, secciones 2.2, 2.3 y 2.9) detectó varias referencias cruzadas incorrectas de bajo costo y alto retorno en confiabilidad, acumuladas a lo largo de las redacciones previas: (1) cuatro citas a "Artículo 1.3" (offline-first) donde el artículo correcto era el 9.3 (no reutilización de claves) — en `05-MODELO-DE-DATOS-MAESTRO.md` (Convenciones Generales y entidad `Factura`) y `11-DICCIONARIO-DE-DATOS.md` (tipo Código y entidad `Factura`); (2) el Artículo 9.3 de la Constitución remitía a `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` "sobre unicidad de claves", tema que ese documento no desarrolla; (3) el Artículo 6.2 llamaba "Artículo" a un documento completo (`04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`); (4) `99-FILOSOFIA-DEL-SISTEMA.md` (sección 4) citaba "la regla de no añadir abstracción prematura en `09-ESTANDARES-DE-DESARROLLO.md`", regla que no existe en ese documento; (5) `09-ESTANDARES-DE-DESARROLLO.md` seguía declarando Estado "En construcción" mientras la tabla de completitud de `13-HISTORIAL-DE-VERSIONES.md` ya lo marcaba como "Vigente" desde su creación — la misma inconsistencia ya resuelta para `08-CATALOGO-DE-MODULOS.md` en una decisión anterior, pero explícitamente dejada fuera de esa decisión; (6) el árbol de estructura de `README.md` no incluía `CLAUDE.md` ni `resumenes/`, ya existentes en el repositorio.

**Decisión:**
Se corrigieron las cuatro citas de "Artículo 1.3" a "Artículo 9.3" en `05` y `11`. Se corrigió la referencia cruzada del Artículo 9.3 de la Constitución de `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` a `05-MODELO-DE-DATOS-MAESTRO.md`, sección 1 (donde realmente se documenta la no reutilización de claves). Se corrigió el Artículo 6.2 para decir "ver `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`" en lugar de "Artículo `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`". Se eliminó de `99-FILOSOFIA-DEL-SISTEMA.md` la cita a una regla inexistente de `09`, dejando la afirmación como principio general sin atribución falsa. Se cambió el Estado de `09-ESTANDARES-DE-DESARROLLO.md` de "En construcción" a "Vigente — pendiente de revisión y aprobación formal" (su contenido de 13 reglas ya estaba completo para su propósito) y se completó su sección de Observaciones, antes vacía. Se actualizó el árbol de `README.md` para incluir `CLAUDE.md` y `resumenes/`.

**Alternativas consideradas:**
Para la inconsistencia de estado de `09`, se consideró la alternativa inversa: corregir la tabla de `13-HISTORIAL-DE-VERSIONES.md` de "Vigente" a "En construcción" en lugar de elevar `09`. Se descartó porque el contenido de `09` (13 reglas concretas de arquitectura y construcción) ya está completo para el propósito acotado de ese documento — una lista de referencia rápida, no un desarrollo extenso como `00-PRINCIPIOS-DEL-ERP.md` — consistente con el patrón ya usado en este repositorio de marcar "Vigente" en cuanto un documento tiene contenido sustantivo completo, no solo cuando alcanza cierta extensión.
Para la cita de "abstracción prematura" en `99`, se consideró agregar esa regla como una nueva regla 14 en `09-ESTANDARES-DE-DESARROLLO.md` en lugar de quitar la cita. Se descartó por quedar fuera del alcance de una limpieza de referencias: agregar una regla nueva es una decisión de contenido, no una corrección de una cita rota.

**Consecuencias:**
- Las cuatro citas corregidas de "Artículo 1.3" ya no podrán inducir a un futuro desarrollador o IA a implementar una regla de no-reutilización de claves buscándola erróneamente en las reglas de offline-first.
- `09-ESTANDARES-DE-DESARROLLO.md` y `13-HISTORIAL-DE-VERSIONES.md` quedan consistentes entre sí; ya no existe ningún documento numerado (`00` a `13`, `99`) con Estado "En construcción".
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.30) reflejando este cambio como MENOR (corrige referencias y alinea un estado, sin modificar el contenido normativo de ningún artículo).

### [2026-07-14] Revisión final: sincronizado 10-PLAN-MAESTRO-DE-IMPLEMENTACION.md con los catálogos agregados en decisiones previas

**Contexto:**
Durante una revisión final de todo el repositorio, tras las cinco rondas de correcciones anteriores, se detectó que `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` (Fase 1 y Fase 2) seguía sin actualizarse desde que se agregaron las entidades `Cliente`, `Proveedor` y `Obligacion` a `05-MODELO-DE-DATOS-MAESTRO.md`: la Fase 1 listaba solo cinco catálogos ("`Producto`, `Cuenta`, `Fondo`, `CuentaBancaria`, `Vendedor`... los cinco catálogos existen"), sin `Cliente` ni `Proveedor`; y la Fase 2 no mencionaba que el Módulo 1 también depende de `Proveedor` (como contraparte de `Obligacion`) ni que su criterio de salida debía incluir poder registrar y pagar una `Obligacion`. También se corrigió una referencia obsoleta en `05-MODELO-DE-DATOS-MAESTRO.md` (sección 11), que seguía calificando a `11-DICCIONARIO-DE-DATOS.md` como "pendiente de redactar" pese a estar completo desde la versión v0.14.

**Decisión:**
Se actualizó la Fase 1 de `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` para incluir los siete catálogos actuales (`Producto`, `Cuenta`, `Fondo`, `CuentaBancaria`, `Vendedor`, `Cliente`, `Proveedor`) y su criterio de salida ("los siete catálogos existen..."). Se actualizó la Fase 2 para declarar la dependencia de `Proveedor` y para que su criterio de salida incluya explícitamente registrar y pagar una `Obligacion`. Se eliminó la calificación "(pendiente de redactar)" de la referencia a `11-DICCIONARIO-DE-DATOS.md` en `05`.

**Alternativas consideradas:**
Ninguna: son correcciones de sincronización directa entre documentos que ya se derivan unos de otros: si `05` cambia el número y la lista de catálogos, `10` (que depende explícitamente de esa lista para su Fase 1) debe reflejarlo, sin margen de interpretación distinta.

**Consecuencias:**
- No queda ningún documento del repositorio que enumere un número de catálogos maestros compartidos distinto a los siete definidos en `05-MODELO-DE-DATOS-MAESTRO.md`, sección 4.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.31) reflejando este cambio como MENOR.

### [2026-07-14] Eliminado el campo duplicado `número` de Cuenta en el diccionario de datos

**Contexto:**
La evaluación completa (`resumenes/2026-07-14-evaluacion-completa-documentacion.md`, sección 2.10) y la revisión final posterior (`resumenes/2026-07-14-revision-final-post-correcciones.md`, pendiente #3) señalaron que `11-DICCIONARIO-DE-DATOS.md` declaraba dos campos para identificar una `Cuenta`: el `código` heredado de los campos comunes (sección 2, tipo Código) y un `número` (Número entero, 1 a 6) agregado como campo específico adicional — sin ninguna regla que explicara la relación entre ambos. `05-MODELO-DE-DATOS-MAESTRO.md` nunca tuvo este problema: siempre describió a `Cuenta` con un único campo ("código de cuenta, 1 a 6"); la duplicación se introdujo solo en `11` al detallar campo por campo.

**Decisión:**
Se eliminó el campo `número` de la tabla de `Cuenta` en `11-DICCIONARIO-DE-DATOS.md`. El campo `código` heredado (ya presente por convención general) queda como la única clave de negocio de `Cuenta`, con una nota explícita de que su valor está restringido a "1" a "6" (a diferencia del resto de los catálogos, donde el código es arbitrario), y de que coincide con el número con el que el resto de la documentación ya se refiere a cada cuenta ("Cuenta 1", "Cuenta 4", etc.).

**Alternativas consideradas:**
Declarar `Cuenta` como una excepción a la convención general de campos comunes (sin `código` propio, usando únicamente `número` como su clave, de forma análoga a cómo `Evento` ya se documenta como excepción sin campo `estado`). Se descartó por requerir más aparato documental (una excepción a la regla general, más un ajuste a la definición del tipo `Referencia` en la sección 1, que dice "apunta a otra entidad por su código") para llegar al mismo resultado práctico. Mantener `código` como el campo único y simplemente acotar su rango de valores es la corrección más simple que resuelve la duplicación sin tocar ninguna otra regla del documento.

**Consecuencias:**
- `Cuenta` ya no tiene dos campos candidatos a clave de negocio; `código` es el único, consistente con el Principio 1 (`00-PRINCIPIOS-DEL-ERP.md`) y con cómo `05-MODELO-DE-DATOS-MAESTRO.md` ya la describía desde su redacción original.
- Ninguna otra entidad del diccionario tenía este problema (se verificó mediante búsqueda dirigida): el campo `número` de `CuentaBancaria` es un campo distinto y legítimo (el número de cuenta bancaria real, Artículo 18.2), no relacionado con esta duplicación.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.32) reflejando este cambio como MENOR (corrige un campo duplicado sin alterar ninguna regla ni relación entre entidades).

### [2026-07-14] Separadas ubicación y condición en el campo `tipo` de NovedadInventario

**Contexto:**
La evaluación completa (`resumenes/2026-07-14-evaluacion-completa-documentacion.md`, sección 2.11) y la revisión final (`resumenes/2026-07-14-revision-final-post-correcciones.md`, pendiente #4) señalaron que el campo `tipo` de `NovedadInventario`, tanto en `05-MODELO-DE-DATOS-MAESTRO.md` como en `11-DICCIONARIO-DE-DATOS.md`, mezclaba en una sola enumeración un valor de **ubicación** (`cuarto_frío`) con valores de **condición** del producto (`dañado`, `roto`, `mal_estado`, `sobrante`, `faltante`). Un producto dañado *dentro* de un cuarto frío solo podía registrar uno de los dos hechos, no ambos. Se verificó contra `02-CONSTITUCION-ERP.md` (Artículos 22.1 y 22.2), `07-FLUJOS-DE-NEGOCIO.md` (F4) y `12-GLOSARIO.md` (catálogo de eventos): los tres ya trataban correctamente "rotura de cadena de frío" como una **condición** (Artículo 22.2 la lista junto a "producto dañado, mal estado") y "cuarto frío" como una **ubicación** (`sucursalId`, Artículo 22.1) — el error de conflación existía solo en la enumeración de `05` y `11`. Además, la distinción "esta novedad ocurrió en un cuarto frío" ya estaba disponible por dos vías independientes que el modelo no aprovechaba: el `sucursalId` de la novedad (referenciando una `Sucursal` de tipo `cuarto_frío`) y el tipo de evento de origen (`NovedadCuartoFrioRegistrada` vs. `NovedadInventarioRegistrada`, ya distintos en `12-GLOSARIO.md` desde su redacción original).

**Decisión:**
Se corrigió la enumeración `tipo` de `NovedadInventario` en `05` y `11`, reemplazando el valor `cuarto_frío` por `rotura_cadena_frio` (una condición, con la redacción exacta del Artículo 22.2), junto con `dañado` / `roto` / `mal_estado` / `sobrante` / `faltante`. Se documentó explícitamente en ambos archivos que la ubicación de la novedad (cuarto frío, sede u otro punto) se determina por `sucursalId` + `Sucursal.tipo`, y adicionalmente por el `tipoEvento` de su evento de origen — nunca por el campo `tipo` de la propia entidad.

**Alternativas consideradas:**
Agregar un campo nuevo de ubicación explícito a `NovedadInventario` (por ejemplo, `ubicacionOrigen`), en lugar de solo corregir la enumeración. Se descartó por ser una duplicación innecesaria: `sucursalId` ya cumple ese rol (es un campo común heredado, obligatorio para toda entidad que opera a nivel de sede — Artículo 2.3), y `Sucursal.tipo` ya distingue `cuarto_frío` de `sede` y `punto_despacho` (`11-DICCIONARIO-DE-DATOS.md`, sección 4). Agregar un campo nuevo habría reintroducido el mismo tipo de redundancia que se acaba de eliminar de `Cuenta` en la decisión anterior.

**Consecuencias:**
- `NovedadInventario.tipo` ahora describe exclusivamente una condición del producto, nunca una ubicación; un producto puede estar dañado y esa novedad puede además haber ocurrido en un cuarto frío, sin que ambos hechos compitan por el mismo campo.
- La trazabilidad de "esta novedad es de cuarto frío" queda respaldada por dos señales independientes y ya existentes (`sucursalId` → `Sucursal.tipo`, y `eventoOrigen` → `tipoEvento`), consistente con el Artículo 30 (huella permanente: la evidencia debe alcanzar para reconstruir dónde ocurrió cada hecho).
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.33) reflejando este cambio como MENOR (corrige una enumeración sin alterar entidades ni relaciones).

### [2026-07-14] Agregado el flujo F13 y la entidad BajaInventario — mercancía dada de baja por merma, pérdida o condonación

**Contexto:**
La evaluación completa (`resumenes/2026-07-14-evaluacion-completa-documentacion.md`, sección 3, punto 2) y la revisión final (pendiente #5) señalaron que el Artículo 6.3 de `02-CONSTITUCION-ERP.md` ("todo movimiento patrimonial debe balancear: no puede 'desaparecer' capital o mercancía sin un evento explícito que documente su destino... merma, pérdida") y el propio `00-MANIFIESTO-DEL-ERP.md` ("cuando existe una pérdida, una merma, un daño o una condonación, el sistema no la oculta: la registra, la explica...") no tenían respaldo estructural: `NovedadInventario` registra la **detección** de una condición anómala, pero ningún flujo, entidad o evento formalizaba la **disposición patrimonial** que sigue — la salida real de esa mercancía del inventario vendible. Sin ese evento, una `NovedadInventario` de tipo `dañado` o `faltante` no tenía un mecanismo definido para reducir la existencia proyectada de `InventarioChofer`/`InventarioEncargado`, dejando en los hechos una mercancía que "desaparece" sin el evento explícito que el Artículo 6.3 exige.

**Decisión:**
Se agregó el flujo **F13 — Baja de Mercancía por Merma, Pérdida o Condonación** a `07-FLUJOS-DE-NEGOCIO.md`, y la entidad **`BajaInventario`** a `05-MODELO-DE-DATOS-MAESTRO.md` (Módulo 2) y `11-DICCIONARIO-DE-DATOS.md`, con el evento formal `BajaInventarioRegistrada` en el catálogo de `12-GLOSARIO.md` (sección C) y sus términos (`Baja de inventario`, `Merma`, `Pérdida`, `Condonación`) en la sección B. `BajaInventario` referencia opcionalmente la `NovedadInventario` o `NovedadDespacho` que la motiva (opcional, porque una condonación administrativa puede no tener novedad previa), el inventario o `Consignacion` de origen, y reduce la existencia del `Producto` afectado de forma atómica con su registro. Se declaró **deliberadamente sin definir** el tratamiento de capital de una baja (si genera un `MovimientoCapital` y contra qué `Fondo`/`Cuenta`): se agregó como ítem 7 al anexo `docs/anexos/01-PENDIENTE-VALIDACION-CONTABLE.md`, aclarando explícitamente que ese ítem, a diferencia de los 6 anteriores, **no** bloquea la Fase 2 (Módulo 1) ni ninguna otra fase — `BajaInventario` puede operar como evento de mercancía/información puro hasta que se resuelva. Se actualizó `08-CATALOGO-DE-MODULOS.md` (Módulo 2 emite el evento; Módulo 3 puede verse afectado cuando la baja es de una `Consignacion`) y `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` (Fase 3 incluye F13 en su objetivo y criterio de salida). Se agregó una referencia cruzada en `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` aclarando que el vacío de tratamiento contable es una pregunta abierta, no una omisión de ese documento.

**Alternativas consideradas:**
Definir también, en esta misma decisión, una clasificación de `Fondo` o una `Cuenta` del plan de cuentas para absorber el gasto de una merma/pérdida. Se descartó porque el plan de cuentas (`06`, sección 3) ya está marcado como Borrador pendiente de validación contable, y los cuatro `Fondo` existentes (Costo, Venta, Distribución, Mantenimiento — Artículo 18.1) no tienen una clasificación natural para pérdidas; inventar una quinta clasificación o una nueva Cuenta sin criterio contable real habría repetido exactamente el tipo de decisión no validada que el anexo `01` ya existe para evitar. Se prefirió declarar la entidad completa por el lado de mercancía (que la Constitución sí exige, sin ambigüedad) y dejar el lado de capital como pregunta explícita, siguiendo el mismo patrón ya usado para el plan de cuentas original.
También se consideró no vincular `BajaInventario` a `Consignacion` (limitarlo solo a `InventarioChofer`/`InventarioEncargado`, Módulo 2). Se descartó porque el Artículo 6.3 habla de mercancía en general, no de un módulo específico, y una consignación puede sufrir la misma merma/pérdida/condonación que cualquier otro inventario; se prefirió una referencia flexible (mismo patrón ya usado en `ArqueoManual.objetoConciliado`) en lugar de duplicar la entidad por módulo.

**Consecuencias:**
- El Artículo 6.3 de la Constitución queda respaldado estructuralmente para el flujo de mercancía: toda merma, pérdida o condonación tiene ahora un evento y una entidad que documentan su destino, consistente con la promesa ya escrita en `00-MANIFIESTO-DEL-ERP.md`.
- El catálogo formal de eventos pasa de 20 a 21 eventos (`docs/diagramas/eventos.drawio` sigue pendiente de diagramarse, y debe incluir `BajaInventarioRegistrada` cuando se elabore).
- El anexo de validaciones contables pasa de 6 a 7 ítems; el ítem 7 es explícitamente independiente del cierre de los ítems 1-6 y no bloquea ninguna fase del `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md`.
- Si en el futuro se valida que una baja sí debe generar un movimiento de capital, esa validación se registra como una nueva decisión que puede requerir agregar un campo de referencia a `MovimientoCapital` (similar al ya agregado para `Obligacion`) — no se asume aquí.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.34) reflejando este cambio como MENOR (agrega flujo, entidad y evento nuevos sin alterar ni romper lo existente).

### [2026-07-14] Definido el mecanismo de detección y resolución de conflictos de sincronización offline

**Contexto:**
La evaluación completa (`resumenes/2026-07-14-evaluacion-completa-documentacion.md`, sección 3, punto 4) señaló este como "el vacío técnico más relevante": `03-ARQUITECTURA-GENERAL.md` (sección 3) decía que los conflictos de sincronización "se detectan, se registran y quedan disponibles para resolución explícita", pero ningún documento definía qué es exactamente un conflicto en este sistema, quién lo resuelve, con qué mecanismo, ni qué ocurre con las proyecciones de estado mientras permanece sin resolver. Para un sistema declarado offline-first desde su primer principio (`00-PRINCIPIOS-DEL-ERP.md`, Principio 2), este era el hueco de mayor impacto práctico entre los pendientes identificados.

**Decisión:**
Se expandió `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`, sección 13 (renombrada "Relación con la Sincronización Offline y Resolución de Conflictos"), con tres subsecciones nuevas: (13.1) por qué la arquitectura basada en eventos evita la mayoría de los conflictos de sincronización que sí requieren fusión en sistemas de registros mutables; (13.2) la definición precisa de qué constituye un conflicto en este sistema — un evento offline que era válido al capturarse pero deja de serlo al sincronizarse, por un cambio de estado ocurrido mientras tanto (ejemplos: sobregiro de inventario, doble pago de una misma `Obligacion`, colisión de código); (13.3) el tratamiento: el motor registra el rechazo como una nueva entidad `ConflictoSincronizacion`, visible solo para roles con permiso de resolución (Motor de Permisos, Artículo 13), nunca resuelto de forma automática por el sistema ni por una IA (Artículo 26.4), y cuya resolución es siempre un evento nuevo (nunca una edición del evento original, Artículo 5.4 y 14). Se agregó la entidad `ConflictoSincronizacion` a `05-MODELO-DE-DATOS-MAESTRO.md` (nivel de plataforma, junto a `Evento` y `RegistroAuditoria`) y `11-DICCIONARIO-DE-DATOS.md`, con su campo `estado` (pendiente/resuelto) definido explícitamente como una proyección — nunca un valor editado directamente — derivada de la existencia de un evento de resolución que la referencia, consistente con el Artículo 5 de la Constitución. Se agregó el evento `ConflictoSincronizacionDetectado` al catálogo formal de `12-GLOSARIO.md`, señalado explícitamente como una excepción al patrón habitual: no proviene de un flujo de negocio Fx, sino que lo emite el propio motor. Se actualizaron `03-ARQUITECTURA-GENERAL.md` (referencia al contrato completo) y `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` (Fase 0 incluye ahora este mecanismo en su alcance y criterio de salida).

**Alternativas consideradas:**
Modelar la resolución con un segundo evento formal dedicado (por ejemplo, `ConflictoSincronizacionResuelto`), en lugar de reutilizar el patrón genérico de "evento compensatorio" ya establecido en el Artículo 5.4 y la sección 8 de `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`. Se descartó porque el proyecto ya tiene precedente de no formalizar un evento distinto para cada transición de estado (por ejemplo, el cierre de una `Consignacion` no tiene un evento propio en el catálogo, solo una descripción en prosa del flujo F5); forzar un segundo evento nuevo aquí habría sido inconsistente con ese patrón ya existente sin aportar información adicional que el `eventoResolucion` (una referencia genérica a cualquier evento correctivo) no proporcione ya.
También se consideró que la resolución del conflicto pudiera automatizarse cuando la corrección es obvia (por ejemplo, reordenar automáticamente dos eventos según su `momentoCaptura`). Se descartó porque el Artículo 26.4 exige que ninguna decisión de este tipo se tome sin acción humana explícita, y porque automatizar "correcciones obvias" es exactamente el tipo de atajo que erosiona la trazabilidad que el Artículo 30 exige — se prefirió que toda resolución, sin excepción, quede como una decisión humana registrada.

**Consecuencias:**
- El sistema ya no tiene un vacío conceptual entre "detectar un conflicto" y "resolverlo": ambos extremos del ciclo quedan definidos, con una entidad concreta (`ConflictoSincronizacion`) que los conecta.
- El catálogo formal de eventos pasa de 21 a 22 eventos; es el primero cuyo origen no es un flujo de negocio Fx sino el propio motor — una excepción documentada explícitamente en `12-GLOSARIO.md`, sección C.
- Cualquier decisión futura sobre la interfaz concreta de resolución (qué rol la ve, qué acciones ofrece) es una decisión de implementación que debe registrarse aparte, respetando el contrato de comportamiento fijado aquí.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.35) reflejando este cambio como MENOR (agrega entidad, evento y contrato de comportamiento nuevos sin alterar lo existente).

### [2026-07-14] Creado el anexo 02 — Estructura Oliver: transcripción literal de los flujos reales de los módulos

**Contexto:**
El usuario proveyó la estructura completa de 6 módulos del ERP, definida por Oliver, con la instrucción explícita de crear `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md` con su contenido exacto — sin interpretar ni modificar la estructura — y declarándola fuente de verdad para el diseño de los módulos.

**Decisión:**
Se creó `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md`, transcribiendo el contenido provisto **exactamente como fue entregado**, dentro de un bloque de código para preservarlo literal (sin que el renderizado Markdown alterara viñetas, numeración o casillas de verificación). No se resumió, reordenó, corrigió ni completó ningún campo. Se actualizó `docs/anexos/README.md` para listar este nuevo anexo, siguiendo la misma convención de prefijo numérico ya establecida con el anexo 01. Se agregó una única observación, sin modificar el contenido transcrito: la estructura de 6 módulos de Oliver ("01. Flujo de Efectivo y Bancos" ... "06. Parámetros de Mantenimiento") no coincide en número ni en agrupación con los 5 módulos actualmente descritos en `08-CATALOGO-DE-MODULOS.md`. Esa diferencia se señala, no se resuelve.

**Alternativas consideradas:**
Reconciliar de inmediato `08-CATALOGO-DE-MODULOS.md` (y por extensión `05-MODELO-DE-DATOS-MAESTRO.md`, `07-FLUJOS-DE-NEGOCIO.md`, `11-DICCIONARIO-DE-DATOS.md`) contra esta nueva estructura de 6 módulos. Se descartó explícitamente: la instrucción del usuario fue transcribir la estructura tal cual, no reinterpretarla ni propagarla a otros documentos en esta misma acción; reconciliar los módulos existentes es un trabajo sustancial que debe abordarse como su propia decisión, deliberada y explícita, no como un efecto secundario de crear este anexo.

**Consecuencias:**
- `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md` es ahora la fuente de verdad declarada para el diseño de módulos, por encima de lo que hoy describen `05`, `07`, `08` y `11` en cuanto a módulos y campos — pero esos documentos **no** se han actualizado todavía para reflejarla.
- Cualquier reconciliación futura entre esta estructura y `08-CATALOGO-DE-MODULOS.md` (y los documentos que de él dependen) debe registrarse como una nueva decisión antes de implementarse, evaluando explícitamente qué módulos se fusionan, se dividen o se renombran.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.36) reflejando esta creación como MENOR (agrega un anexo nuevo sin modificar la documentación técnica existente).

### [2026-07-14] Reconciliación completa de la documentación técnica con los 6 módulos de la Estructura Oliver

**Contexto:**
El usuario pidió reconciliar `08-CATALOGO-DE-MODULOS.md` con `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md` (6 módulos), "exactamente". Antes de tocar nada se presentó un plan (modo plan) que reveló dos cosas no anticipadas: (1) el esquema de 5 módulos no vivía solo en `08` — estaba citado por nombre/número en `01-VISION-ERP.md`, `02-CONSTITUCION-ERP.md` (Artículos 18.1, 21.1, 22.1, 25.3), `03-ARQUITECTURA-GENERAL.md`, `05-MODELO-DE-DATOS-MAESTRO.md`, `06-REGLAS-CONTABLES-Y-FINANCIERAS.md`, `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` y `11-DICCIONARIO-DE-DATOS.md`; (2) la estructura de Oliver introduce conceptos (NCF, ITBIS, Condición de Pago, Participación de Capital, Rutas y Vías, reportes R1/R2/R3, Refrigerios/Bonificaciones) sin respaldo formal en el modelo. El usuario confirmó, tras consulta explícita, propagar la reconciliación a los 8 documentos y tratar los conceptos nuevos como "pendiente de modelar" en lugar de inventarlos.

Durante la ejecución surgió una tercera ambigüedad no prevista en el plan: el Módulo 02 de Oliver ("CXP, Facturación y Reportes") describe una "Factura" con campos de Suplidor (Fecha de Factura, NCF, Días de Crédito), mientras el Módulo 05 ("Despacho, Novedades y Caja") tiene por separado "Facturación / Histórico Facturas" dentro de "Arqueo de Caja y Facturar". Se consultó al usuario, que confirmó: el Módulo 2 es la factura que Polar Breeze **recibe** de un Suplidor (mapea a `Obligacion`/`Proveedor`, ya existentes), y el Módulo 5 es la factura de **venta** que Polar Breeze **emite** a sus clientes (mapea a `Factura`/`NotaCredito`, ya existentes) — dos documentos distintos que ambos se llaman "factura" en el habla de Oliver.

**Decisión:**
Se reescribió `08-CATALOGO-DE-MODULOS.md` completo con los 6 módulos de Oliver (Flujo de Efectivo y Bancos; CXP, Facturación y Reportes; Inventario y Cuarto Frío; Consignaciones; Despacho, Novedades y Caja; Parámetros de Mantenimiento), transcribiendo sus funcionalidades fielmente y marcando "Pendiente de modelar" cada concepto sin respaldo formal, sin inventar entidades ni eventos nuevos para ellos. Se propagó la reconciliación a los 8 documentos identificados:

- `01-VISION-ERP.md` (sección 10 y sección 9) — lista de módulos actualizada a los 6 nuevos.
- `02-CONSTITUCION-ERP.md` — Artículos 18.1 ("Módulo 1 — Flujo de Efectivo y Bancos"), 21.1 ("Módulo 4 — Consignaciones"), 22.1 ("Módulo 3 — Inventario y Cuarto Frío") y 25.3 ("Módulo 5 — Despacho, Novedades y Caja") enmendados para citar el número/nombre correcto.
- `03-ARQUITECTURA-GENERAL.md` (sección 5, capa 4, y sección 12) — lista de módulos y referencia al módulo de Reportes actualizadas.
- `05-MODELO-DE-DATOS-MAESTRO.md` (secciones 4-9) — `Obligacion` se movió del Módulo 1 al Módulo 2; `Despacho`, `NovedadDespacho`, `SolicitudRetiro`, `JustificacionRetiro`, `Factura` y `NotaCredito` se movieron al Módulo 5; `Consignacion` quedó sola en el Módulo 4; se actualizaron los módulos de creación de `Producto`, `Vendedor` y `Proveedor` a Módulo 6.
- `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` — referencias a "Módulo 1" actualizadas, agregando el Módulo 2 como origen funcional de las reglas de cuentas por pagar (sección 5).
- `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` — Fases 2-6 redistribuidas de 5 a 6 módulos (Módulo 6 se cubre en la Fase 1, sin fase propia); se agregó a la Fase 3 (Módulo 2) el mismo bloqueante del plan de cuentas que ya tenía la Fase 2.
- `11-DICCIONARIO-DE-DATOS.md` (secciones 6-10) — misma redistribución de entidades que `05`.

No se modificaron `07-FLUJOS-DE-NEGOCIO.md`, `09-ESTANDARES-DE-DESARROLLO.md` ni `12-GLOSARIO.md`: ninguno cita el esquema de 5 módulos por nombre/número (verificado por barrido de grep antes de empezar).

**Alternativas consideradas:**
Reconciliar solo `08-CATALOGO-DE-MODULOS.md`, dejando los otros 8 documentos citando el esquema viejo como inconsistencia conocida. Se descartó explícitamente por el usuario (opción presentada y rechazada durante la planificación): habría dejado la propia Constitución citando un esquema de módulos que ya no existe en el catálogo, una inconsistencia de mayor severidad que la que existía antes de esta sesión.
Modelar de inmediato los conceptos nuevos de Oliver (NCF, ITBIS, Condición de Pago, etc.) como entidades formales en `05`/`11`. Se descartó explícitamente por el usuario: se listan fielmente como funcionalidades de cada módulo, marcadas "Pendiente de modelar", sin inventar su estructura de datos en esta pasada.
Asumir sin preguntar que "Factura" significaba lo mismo en el Módulo 2 y el Módulo 5 de Oliver. Se descartó porque los campos de cada uno (Suplidor vs. ninguna mención de Suplidor, contexto de Cuentas por Pagar vs. contexto de Arqueo de Caja) apuntaban a interpretaciones opuestas con consecuencias muy distintas sobre qué entidad existente correspondía a cada uno; se prefirió confirmar con el usuario antes de propagar una interpretación a 7 documentos.

**Consecuencias:**
- El repositorio queda internamente consistente: ningún documento cita ya el esquema de 5 módulos: Flujo de Efectivo, Inventario y Almacén, Despacho y Consignaciones, Facturación, Reportes.
- `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md` deja de ser una fuente de verdad no reflejada en la documentación técnica — ahora lo está, con las interpretaciones necesarias explícitamente marcadas como tales (no como hechos literales del texto de Oliver).
- Quedan señalados, sin resolver, como "Pendiente de modelar" en `08-CATALOGO-DE-MODULOS.md`: NCF, ITBIS, Condición de Pago (catálogo reutilizable), Participación de Capital, Rutas y Vías (con Consignaciones Individuales 1 al 23), Reportes R1/R2/R3, Refrigerios/Bonificaciones/Donaciones, nota de crédito de proveedor, y un catálogo configurable de tipos de Novedad — ninguno de estos requiere una decisión inmediata, pero ninguno debe implementarse sin que primero se modele formalmente (Artículo 29.3 de la Constitución).
- Se detectó y se dejó explícitamente señalada (sin resolverla) una discrepancia entre Oliver (3 clasificaciones de Flujo de Efectivo: Venta/Costo/Distribución) y el Artículo 18.1 de la Constitución (4 clasificaciones, incluye Mantenimiento) — pendiente de confirmar con el negocio.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.37, MAYOR) reflejando esta reconciliación como reestructuración del modelo de módulos y enmienda de 4 artículos de la Constitución (Artículo 14.3).

### [2026-07-14] Confirmadas las cuatro clasificaciones de Fondo contra la fuente de verdad de Oliver

**Contexto:**
La decisión anterior ("Reconciliación completa de la documentación técnica con los 6 módulos de la Estructura Oliver") señaló, sin resolverla, una aparente discrepancia: `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md` transcribía la sección [FLUJO DE EFECTIVO] del Módulo 01 como "Venta / Costo / Distribución -> Agregar Registro" (tres clasificaciones), mientras el Artículo 18.1 de la Constitución y la entidad `Fondo` exigen cuatro (agregan Mantenimiento). El usuario confirmó que no existe tal discrepancia: la fuente de verdad de Oliver sí incluye Mantenimiento como cuarta clasificación — la transcripción original del anexo la omitía por un error de transcripción, no porque Oliver la excluyera de su estructura real.

**Decisión:**
Se corrigió `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md` para que la sección [FLUJO DE EFECTIVO] del Módulo 01 diga "Venta / Costo / Distribución / Mantenimiento -> Agregar Registro", con una nota en sus Observaciones dejando constancia de que se trata de una corrección de transcripción (2026-07-14), no de una reinterpretación del contenido de Oliver. Se actualizó `08-CATALOGO-DE-MODULOS.md` (Módulo 1, Funcionalidades y "Pendiente de modelar") para reflejar la misma corrección y reemplazar la nota de discrepancia por la confirmación de que las cuatro clasificaciones de `Fondo` (Costo, Venta, Distribución, Mantenimiento) están alineadas con la fuente de verdad de Oliver. El Artículo 18.1 de la Constitución **no requiere ningún cambio**: ya declaraba correctamente las cuatro clasificaciones: esta decisión confirma que el modelo existente estaba en lo correcto, y que el error estaba en la transcripción del anexo.

**Alternativas consideradas:**
Ninguna: el usuario proveyó la corrección de forma directa y sin ambigüedad sobre el contenido real de la fuente de verdad de Oliver.

**Consecuencias:**
- Ya no queda ninguna discrepancia abierta entre `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md` y `02-CONSTITUCION-ERP.md` respecto a las clasificaciones de Fondo.
- Este caso queda como referencia de que una "discrepancia" detectada durante una reconciliación puede deberse a un error de transcripción de la fuente, no a una diferencia real de diseño — y de que corregirla en el anexo (la fuente de verdad transcrita) es preferible a dejarla marcada como abierta indefinidamente, una vez que el usuario la resuelve con información directa.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.38) reflejando esta corrección como MENOR (corrige una transcripción, no reestructura ninguna regla ni entidad).

### [2026-07-14] Modelados 6 de los 7 conceptos pendientes de Oliver

**Contexto:**
La reconciliación con la Estructura Oliver dejó 7 conceptos marcados "Pendiente de modelar" en `08-CATALOGO-DE-MODULOS.md`: NCF, ITBIS, Condición de Pago, Participación de Capital, Rutas y Vías, Reportes R1/R2/R3, y Refrigerios/Bonificaciones/Donaciones. El usuario pidió modelarlos en `05-MODELO-DE-DATOS-MAESTRO.md` y `11-DICCIONARIO-DE-DATOS.md`, con un plan previo a la implementación. Al diseñar contra las entidades existentes aparecieron dos dependencias no anticipadas: NCF/ITBIS exigían agregar `fechaFactura` a `Obligacion` (no existía, solo `fechaVencimiento`); los reportes R3 exigían agregar `medioDePago`/`comprobanteImagen` a `MovimientoCapital` y un tipo conceptual nuevo ("Archivo/Imagen") que el diccionario no tenía. Se preguntó al usuario cómo interpretar "Participación de Capital" (única mención ambigua, sin contexto adicional de Oliver); el usuario confirmó dejarla explícitamente pendiente.

**Decisión:**
Se modelaron 6 conceptos:
- **NCF y ITBIS**: `Obligacion` gana `comprobanteFiscal`, `montoCosto`, `montoITBIS` y `fechaFactura`; `montoOriginal` se reinterpreta explícitamente como el total (`montoCosto + montoITBIS`), sin romper su rol de monto inmutable.
- **Condición de Pago**: entidad nueva `CondicionPago` (catálogo compartido, creada desde el Módulo 6), con `plazoDias`; `Obligacion` la referencia y calcula `fechaVencimiento` una sola vez al registrarse (`fechaFactura` + `plazoDias`), sin recalcularla después.
- **Rutas y Vías**: entidad nueva `Ruta` (Módulo 4), con `presupuestoInventario` (cubre "PPTO Inventario Santiago"); `Consignacion` gana `ruta` y `numeroPunto` (cubre "Consignaciones Individuales 1 al 23"), ambos opcionales.
- **Reportes R1/R2/R3**: documentados como proyecciones de solo lectura sobre `Obligacion` y `MovimientoCapital`, sin entidad propia — mismo tratamiento que ya tenía `ExportacionReporte`. "Días Vencimiento" de R2 se calcula al generar el reporte, no es un campo almacenado.
- **Refrigerios / Bonificaciones / Donaciones**: se amplió `BajaInventario.tipo` de tres a seis valores (se agregan `donación`, `bonificación`, `refrigerio`), distinguiéndolos de `condonación` (perdonar una deuda existente, no regalar producto activamente).
- Para dar soporte a R3, `MovimientoCapital` ganó `medioDePago`, `numeroTransaccion` y `comprobanteImagen`; se agregó el tipo conceptual **Archivo/Imagen** a `11-DICCIONARIO-DE-DATOS.md`, sección 1.

Se actualizó `08-CATALOGO-DE-MODULOS.md` reemplazando cada "Pendiente de modelar" resuelto por una referencia a la entidad/campo real, y actualizando los catálogos que crean el Módulo 4 (`Ruta`) y el Módulo 6 (`CondicionPago`). **Participación de Capital** queda explícitamente pendiente en el Módulo 1, con nota de que el usuario la dejó así por decisión propia, no por descuido.

**Alternativas consideradas:**
Modelar tres entidades separadas para `donación`/`bonificación`/`refrigerio` en lugar de ampliar el enum de `BajaInventario.tipo`. Se descartó por ser exactamente la clase de sobre-construcción que este proyecto evita: las tres son formas de baja de mercancía, no conceptos con campos o comportamiento distintos entre sí.
Modelar R1/R2/R3 como tres entidades de "plantilla de reporte". Se descartó porque `ExportacionReporte` ya existe como registro genérico de exportación y los reportes son, en esencia, proyecciones de campos ya modelados — inventar plantillas formales habría sido redundante.
Intentar adivinar el significado de "Participación de Capital" para no dejar nada pendiente. Se descartó: se preguntó al usuario, que confirmó dejarla pendiente — consistente con el principio de este proyecto de preguntar ante ambigüedad genuina en lugar de asumir (Artículo 26.2 de la Constitución).

**Consecuencias:**
- De los 7 conceptos pendientes de la reconciliación con Oliver, solo **Participación de Capital** sigue sin modelar — con nota explícita de que requiere más contexto de Oliver, no una decisión arquitectónica pospuesta sin razón.
- Cualquier módulo futuro que implemente CXP, Rutas o los reportes R1/R2/R3 debe consumir estas entidades ya existentes (`CondicionPago`, `Ruta`), nunca reimplementarlas (Artículo 4 y 16.1 de la Constitución).
- Si en el futuro se decide modelar Participación de Capital, esa decisión debe registrarse aparte, con el contexto adicional de Oliver que hoy falta.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.39) reflejando este cambio como MENOR (agrega entidades y campos sin alterar ni romper lo existente).

### [2026-07-17] Catálogos de Configuración Dinámica (Hub Admin) y unificación de catálogos maestros bajo `config/*`

**Contexto:**
El usuario pidió agregar cuatro catálogos administrables por el rol Administrador/Oliver desde una interfaz de administración ("Hub Admin"), todos con patrón Agregar/Editar/Desactivar (soft delete, Artículo 9): Cuentas Bancarias, Capital de la Empresa, Cuentas Contables (plan de cuentas) y Motivos de Salida sin Cobro. Antes de tocar código se acordó un plan (`resumenes/2026-07-17-plan-catalogos-configuracion-dinamica.md`), aprobado por el usuario con dos precisiones y cuatro decisiones de diseño confirmadas previamente:

1. **Cuentas Contables amplía la entidad `Cuenta` existente**, hoy fija a códigos "1" a "6" (`06-REGLAS-CONTABLES-Y-FINANCIERAS.md`, sección 3 — Borrador, bloqueada por dos ítems del anexo `01-PENDIENTE-VALIDACION-CONTABLE.md` pendientes de un contador). Al investigar se encontró que Oliver ya pedía "Crear Cuenta" en su propia estructura (`docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md`, Módulo 1) — la restricción "1"-"6" era una limitación que esta documentación había impuesto, no un límite real del negocio.
2. **Todos los catálogos maestros — existentes y nuevos — viven bajo `config/{empresaId}/<colección>/{código}`.** Se detectó una inconsistencia real en `03-ARQUITECTURA-GENERAL.md`: su sección 7 (Modelo de Datos Maestro) ubicaba ahí los catálogos maestros, mientras su sección 8 y la lista de capas de la sección 1 decían que vivían en `config/*` (Capa de Configuración de Plataforma).
3. **"Capital de la Empresa" es distinto del pendiente "Participación de Capital"** (Módulo 1 — Bancos, dejado sin modelar por ambigüedad en la decisión "Modelados 6 de los 7 conceptos pendientes de Oliver"). Ambos quedan documentados por separado; el pendiente sigue abierto.
4. **Se formaliza "Hub Admin"** como la interfaz web de administración del rol Administrador/Oliver, dentro de la Capa de Presentación.

Sobre los dos catálogos con preguntas abiertas en el plan, el usuario confirmó: (a) `MotivoSalidaSinCobro` se siembra con los seis valores — Refrigerio, Donación, Bonificación, Merma, Pérdida y Condonación — no solo los cuatro mencionados inicialmente; (b) `AporteCapital` es puramente informativo por ahora, sin conexión a `MovimientoCapital` ni a ningún `Fondo`, hasta que exista un aporte real que lo requiera.

**Decisión:**
Se implementaron los cuatro catálogos:

- **Cuentas Bancarias**: entidad `CuentaBancaria` ya existente gana el campo `alias` (opcional). Sin cambios de módulo (sigue creándose desde el Módulo 1).
- **Capital de la Empresa**: nueva entidad `AporteCapital` (Módulo 1), diseñada como catálogo/subcolección desde el inicio — nunca un campo suelto en `Empresa` — para admitir múltiples aportes sin remodelar después. Campos: monto (default 0), fecha, descripción opcional. No genera `MovimientoCapital`; puramente informativa.
- **Cuentas Contables**: se elimina la restricción de `Cuenta.código` a "1"-"6" (`05`, `11`); pasa a ser un código de negocio libre, como cualquier otro catálogo. Los 6 nombres que proponía `06-REGLAS-CONTABLES-Y-FINANCIERAS.md`, sección 3, quedan como datos semilla de ejemplo, no como restricción. Esto resuelve directamente los ítems 1 y 6 del anexo `01-PENDIENTE-VALIDACION-CONTABLE.md` (pasan a "No aplica"); los ítems 2, 3, 4 y 5 siguen pendientes de un contador real, y `06` sigue en **Borrador** hasta que se cierren.
- **Motivos de Salida sin Cobro**: nueva entidad `MotivoSalidaSinCobro` (catálogo maestro, creado en el Módulo 6, consumido en el Módulo 3). `BajaInventario.tipo` cambia de Enumeración cerrada a Referencia a este catálogo, sembrado con los seis valores ya existentes (merma, pérdida, condonación, donación, bonificación, refrigerio). Distinto del pendiente "Crear Novedades" del Módulo 6 (que trata sobre `NovedadInventario`/`NovedadDespacho`, no sobre `BajaInventario`).

Se corrige `03-ARQUITECTURA-GENERAL.md`: la sección 7 deja de mencionar catálogos maestros (quedan solo las entidades transaccionales/patrimoniales); la sección 8 los declara explícitamente a todos, con su ubicación concreta en Firestore; la sección 2 formaliza el Hub Admin. Se agrega una sección de gobernanza en `08-CATALOGO-DE-MODULOS.md` (patrón CRUD, acceso por rol, ubicación) para no repetirla en cada módulo.

**Alternativas consideradas:**
Modelar "Cuentas Contables" como una entidad nueva y separada (`CuentaContable`), dejando `Cuenta` intacta. Se descartó (opción presentada y rechazada por el usuario): habría dejado dos catálogos de cuentas contables en paralelo, uno bloqueado por validación contable y otro libre, sin resolver la razón original del bloqueo.
Dejar los catálogos maestros ya existentes donde la sección 7 de `03-ARQUITECTURA-GENERAL.md` los ubicaba, usando `config/*` solo para los 4 catálogos nuevos. Se descartó (opción presentada y rechazada por el usuario): habría dejado el sistema con dos patrones de ubicación distintos para el mismo tipo de dato, sin necesidad.
Tratar el capital de la empresa como un campo `capitalInicial` suelto en `Empresa`. Se descartó explícitamente por el usuario en su pedido original: un campo escalar habría requerido remodelar la entidad por completo el día en que existan múltiples aportes.
Sembrar `MotivoSalidaSinCobro` solo con los cuatro valores mencionados en el pedido original (Refrigerio, Donación, Bonificación, Merma). Se descartó: el usuario confirmó incluir también Pérdida y Condonación, preservando la cobertura completa que ya tenía `BajaInventario.tipo` antes del cambio.

**Consecuencias:**
- `Cuenta`, `CuentaBancaria`, `Fondo`, `Vendedor`, `Cliente`, `Proveedor`, `CondicionPago`, `Ruta`, `AporteCapital` y `MotivoSalidaSinCobro` viven, todos, en Firestore bajo `config/{empresaId}/<colección>/{código}` — convención fijada por primera vez de forma concreta para catálogos maestros (hasta ahora solo `config/*` en abstracto, Artículo 16.3).
- El anexo `01-PENDIENTE-VALIDACION-CONTABLE.md` pasa de 6 ítems bloqueantes de la Fase 2 a 4 (ítems 2, 3, 4 y 5); los ítems 1 y 6 quedan en "No aplica", resueltos por este cambio de arquitectura, no por validación contable.
- `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` sigue en **Borrador** — este cambio no lo promueve a Vigente, porque los ítems 2-5 del anexo siguen sin un contador real.
- Cualquier módulo futuro que necesite un catálogo administrable por el negocio debe seguir este mismo patrón (Hub Admin, Agregar/Editar/Desactivar, `config/{empresaId}/...`), documentado en `08-CATALOGO-DE-MODULOS.md` ("Gobernanza de Catálogos de Configuración Dinámica"), nunca reinventarlo módulo por módulo (Artículo 4 y 16.1 de la Constitución).
- El pendiente "Participación de Capital" (Módulo 1) y el pendiente "Crear Novedades" (Módulo 6) siguen abiertos, sin relación de resolución con `AporteCapital` ni `MotivoSalidaSinCobro` respectivamente — quedó documentado explícitamente para que no se confundan en el futuro.
- `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` (no listado originalmente en el plan de esta sesión, pero directamente afectado) se actualiza: la Fase 1 incluye ahora `AporteCapital` y `MotivoSalidaSinCobro`; el bloqueante de las Fases 2 y 3 se reformula (ya no es "todo el plan de cuentas", sino específicamente la clasificación de Fondos — ítem 2 — y las reglas de Cuentas por Pagar — ítem 3 — del anexo).

### [2026-07-17] Requisitos no funcionales como criterios de diseño escalable, no cifras de un cliente

**Contexto:**
El pendiente #7 de `resumenes/2026-07-14-estado-y-pendientes-para-retomar.md` señalaba que ningún documento cubría respaldos/recuperación, retención de datos, privacidad de datos personales, volumetría ni disponibilidad. Antes de redactar contenido, se preguntó al usuario dónde debía vivir (documento nuevo vs. sección de `03-ARQUITECTURA-GENERAL.md`) y cómo tratar cuatro áreas que dependen de información que este repositorio no tiene: jurisdicción legal, cifras de volumetría, y targets de infraestructura (RPO/RTO/SLA). El usuario aclaró un punto de fondo: el ERP Polar Breeze es un **producto multiempresa pensado para extrapolarse a distintos clientes futuros**, no un sistema a medida de la operación actual de Polar Breeze — por lo que la volumetría, en particular, no debía documentarse como cifras fijas de un negocio, sino como un criterio de diseño escalable (rango de operación pequeña a grande), con un checklist de qué medir cuando un cliente real entre en operación.

**Decisión:**
Se creó `docs/14-REQUISITOS-NO-FUNCIONALES.md`, documento nuevo (no una sección de `03-ARQUITECTURA-GENERAL.md`), siguiendo el patrón granular ya establecido de un documento por área de preocupación. Cubre, a nivel de principio arquitectónico:

- **Respaldos y recuperación:** redundancia obligatoria y restauración verificada como principios; sin RPO/RTO numéricos ni frecuencia de respaldo concretos — decisión de infraestructura futura.
- **Retención de datos:** reformulada como "cuánto se mantiene en caliente vs. archivado", no "cuándo se borra" (ya resuelto por el Artículo 9 — soft delete); sin período ni jurisdicción específica asumida.
- **Privacidad de datos personales:** principio de minimización de datos agregado; sin nombrar un régimen legal específico (se descartó asumir Ley 172-13 de República Dominicana pese a que el ecosistema usa DOP/NCF, precisamente porque el producto se extiende a otras jurisdicciones).
- **Volumetría y escalabilidad:** documentada como rango de diseño (una sucursal hasta múltiples empresas/sucursales), nunca como cifra fija; con checklist de qué medir al entrar en operación un cliente real.
- **Disponibilidad:** el offline-first ya cubre la continuidad de los roles de campo; la disponibilidad crítica es la del backend/API para Hub Admin y reportes; sin SLA numérico fijado.

Se agregó una sección 7 con un checklist de 5 validaciones pendientes (retención legal, régimen de privacidad, volumetría real, RPO/RTO, SLA), ninguna bloqueante, siguiendo el mismo patrón que `docs/anexos/01-PENDIENTE-VALIDACION-CONTABLE.md`. Se corrigió `README.md`: el árbol de documentos no incluía `14` ni el anexo `02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md` (creado en v0.36, nunca agregado al árbol).

**Alternativas consideradas:**
Documentar la volumetría con las cifras reales o estimadas de Polar Breeze. Se descartó explícitamente por el usuario: confundiría la operación de un cliente con la capacidad que el producto debe ofrecer a cualquier cliente futuro.
Asumir la jurisdicción de República Dominicana (Ley 172-13, reglas de la DGII) para retención y privacidad, dado que el ecosistema ya usa DOP y NCF. Se descartó por el usuario: se prefirió dejarlo a nivel de principio y marcarlo como pendiente de validación legal, mismo tratamiento que recibió el plan de cuentas antes de que un contador lo validara.
Fijar RPO/RTO/SLA numéricos ahora, aunque fueran estimaciones razonables. Se descartó: no existe todavía infraestructura real elegida, y un número inventado sería una suposición, no una definición de arquitectura.
Agregar la sección como parte de `03-ARQUITECTURA-GENERAL.md` en lugar de un documento nuevo. Se descartó (opción presentada, el usuario prefirió documento nuevo): mezclaría la descripción de capas/componentes con targets operativos de naturaleza distinta.

**Consecuencias:**
- `docs/14-REQUISITOS-NO-FUNCIONALES.md` queda Vigente, con 5 ítems pendientes de validación (legal, infraestructura, volumetría real) que no bloquean ninguna fase del `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md`.
- Cualquier decisión futura sobre retención, privacidad, backups o disponibilidad debe partir de este documento y cerrarse aquí (checklist sección 7) antes de fijarse en el repositorio de código, nunca asumirse directamente en la implementación.
- Si en el futuro se onboarda un cliente en una jurisdicción distinta a República Dominicana, este documento ya está preparado para eso — no requiere reescritura, solo cerrar el checklist con el régimen legal correspondiente a cada empresa.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.41, MENOR: agrega contenido nuevo sin alterar ni contradecir lo existente).

### [2026-07-17] Proceso de aprobación formal de la documentación

**Contexto:**
El pendiente #8 de `resumenes/2026-07-14-estado-y-pendientes-para-retomar.md` señalaba que casi todos los documentos decían "Vigente — pendiente de revisión y aprobación formal" (o, en el caso de `01-VISION-ERP.md`, "por el equipo"), sin que ningún documento definiera quién aprueba, cómo, ni dónde se registra esa aprobación — a pesar de que el Artículo 29.1 de la Constitución ya exige "documentación aprobada previamente" antes de desarrollar cualquier módulo. Se preguntó al usuario quién tiene autoridad de aprobación, con qué granularidad, dónde se registra, y qué dispara una re-aprobación.

**Decisión:**
Se define una autoridad de aprobación dual:

- **Oliver** (dueño de Polar Breeze) aprueba documentos de negocio: `07-FLUJOS-DE-NEGOCIO.md`.
- **El Arquitecto/Product Owner del ERP** (el usuario que dirige esta biblioteca) aprueba documentos técnicos/estructurales: `00-MANIFIESTO-DEL-ERP.md`, `00-PRINCIPIOS-DEL-ERP.md`, `03-ARQUITECTURA-GENERAL.md`, `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`, `05-MODELO-DE-DATOS-MAESTRO.md`, `09-ESTANDARES-DE-DESARROLLO.md`, `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md`, `11-DICCIONARIO-DE-DATOS.md`, `12-GLOSARIO.md`, `13-HISTORIAL-DE-VERSIONES.md`, `14-REQUISITOS-NO-FUNCIONALES.md`.
- **Ambos** aprueban los documentos que mezclan negocio y arquitectura: `01-VISION-ERP.md`, `02-CONSTITUCION-ERP.md`, `08-CATALOGO-DE-MODULOS.md`, `99-FILOSOFIA-DEL-SISTEMA.md`.

Se aprueba **todo el baseline vigente como un solo evento** (2026-07-17, sobre `v0.41`), no documento por documento desde cero — consistente con que ningún módulo ha comenzado su implementación todavía (Fase 0 del `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` no ha iniciado). Una **re-aprobación individual** solo se dispara cuando un documento reciba, después de esta fecha, un cambio de versión **MAYOR** (reestructura modelo/regla constitucional, según la convención ya existente de `13-HISTORIAL-DE-VERSIONES.md`) — nunca por un cambio MENOR.

Se registra en dos lugares: el campo Estado de cada documento (quién aprobó y cuándo) y una nueva sección "Registro de Aprobaciones" en `13-HISTORIAL-DE-VERSIONES.md` que centraliza los 16 documentos aprobados en una tabla, sin anexo dedicado aparte.

Quedan **excluidos** de este evento:
- `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` — sigue en Borrador, bloqueado por los ítems 2-5 de `docs/anexos/01-PENDIENTE-VALIDACION-CONTABLE.md`; recibirá la aprobación de Oliver aparte, cuando esos ítems se validen.
- `DECISIONES-ARQUITECTURALES.md` — registro vivo por diseño ("En construcción"), nunca alcanza un estado final "aprobado".

**Alternativas consideradas:**
Una sola autoridad de aprobación (solo Oliver, o solo el arquitecto). Se descartó explícitamente por el usuario: la documentación mezcla contenido de negocio real (reglas contables, flujos operativos de Oliver) con contenido puramente arquitectónico (modelo de datos, estructura documental), y cada uno requiere la validación de quien realmente tiene el criterio para juzgarlo.
Aprobación independiente de los 17 documentos desde el inicio. Se descartó por el usuario (opción presentada): más pesado sin un consumidor real esperando, dado que ningún módulo ha iniciado implementación todavía.
Un anexo dedicado para el registro de aprobaciones, en vez de una sección en `13-HISTORIAL-DE-VERSIONES.md`. Se descartó por el usuario (opción presentada): innecesario, dado que `13` ya es el documento que centraliza el estado de toda la biblioteca.
Exigir re-aprobación ante cualquier cambio, incluso MENOR. Se descartó por el usuario: habría convertido cada adición aditiva (como las de esta misma sesión) en un evento de re-aprobación, sin beneficio real dado que un cambio MENOR por definición no rompe ni contradice lo ya aprobado.

**Consecuencias:**
- 16 de los 17 documentos numerados quedan formalmente aprobados a partir de esta decisión; sus campos Estado y la nueva sección "Registro de Aprobaciones" de `13-HISTORIAL-DE-VERSIONES.md` son la fuente de verdad de quién aprobó qué y cuándo.
- Cualquier cambio MAYOR futuro a un documento ya aprobado debe agregar su propia fila de re-aprobación en el Registro, con la autoridad correspondiente según esta misma clasificación (negocio/técnica/ambas) — no se inventa una autoridad nueva por cambio.
- `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` sigue sin aprobación formal de Oliver hasta que los ítems 2-5 del anexo contable se validen; en ese momento, su aprobación se registra como una decisión aparte, no retroactiva a esta.
- El Artículo 29.1 de la Constitución ("ningún módulo se desarrolla sin documentación aprobada previamente") queda, por primera vez, con un mecanismo concreto de qué significa "aprobada": este registro.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.42, MENOR: agrega un proceso de gobernanza nuevo sin alterar ni contradecir ninguna regla, entidad o decisión existente).

### [2026-07-17] Formalización del Artículo 31 — Proceso de Aprobación Formal de la Documentación

**Contexto:**
La decisión anterior ("Proceso de aprobación formal de la documentación", v0.42) definió el mecanismo de aprobación dual (Arquitecto/Product Owner vs. Oliver, según negocio o técnica) a nivel de proceso, registrado en el campo Estado de cada documento y en el "Registro de Aprobaciones" de `13-HISTORIAL-DE-VERSIONES.md`, pero explícitamente **sin** elevarlo a regla constitucional — el usuario pidió dejarlo como tarea futura, a su propia decisión, en sesión aparte (pendiente #11 de `resumenes/2026-07-17-estado-y-pendientes-para-retomar.md`). El usuario retomó esta tarea explícitamente en la sesión siguiente.

Siguiendo la "Convención de Cambios a esta Constitución" (`02-CONSTITUCION-ERP.md`, al final del documento): (1) esta decisión se registra antes de tocar la Constitución; (2) se evaluó el impacto sobre `08-CATALOGO-DE-MODULOS.md` — ninguno, esta regla gobierna la documentación arquitectónica en sí, no las declaraciones de alcance/eventos/catálogos que exige el Artículo 29.2 para cada módulo; (3) la Constitución se actualiza recién ahora, después de los dos pasos anteriores.

**Decisión:**
Se agrega el **Artículo 31 — Proceso de Aprobación Formal de la Documentación** (31.1 a 31.6) a `02-CONSTITUCION-ERP.md`, elevando a regla constitucional exactamente lo ya decidido en v0.42, sin ampliarlo ni reinterpretarlo:

- 31.1 distingue "documentación aprobada" (Artículo 29.1) de simplemente "Vigente".
- 31.2 fija la doble autoridad y exige que quede registrada en el Registro de Aprobaciones, nunca de forma implícita.
- 31.3 permite aprobar varios documentos como un solo evento ("baseline").
- 31.4 fija que solo un cambio de versión MAYOR dispara re-aprobación individual, nunca uno MENOR.
- 31.5 impide aprobar un documento bloqueado por una validación externa pendiente (ej. el anexo contable).
- 31.6 distingue explícitamente esta aprobación documental de la inmutabilidad de documentos de negocio del Artículo 14, para que no se confundan dos conceptos que comparten la palabra "aprobado".

Por ser un cambio MAYOR a `02-CONSTITUCION-ERP.md` (agrega una regla constitucional), el propio Artículo 31.4 exige que este documento reciba su propia re-aprobación individual — aplicada de inmediato como caso de prueba del mecanismo recién formalizado: `02-CONSTITUCION-ERP.md` se re-aprueba por ambas autoridades (Arquitecto/Product Owner y Oliver, dado que mezcla artículos de negocio y de arquitectura) el 2026-07-17, con una fila nueva en el Registro de Aprobaciones.

**Alternativas consideradas:**
Extender el Artículo 29 ("Reglas para Nuevos Módulos") con sub-artículos nuevos (29.5, 29.6) en lugar de crear un Artículo 31 separado. Se descartó: el Artículo 29 está explícitamente acotado a módulos nuevos, mientras que esta regla gobierna la aprobación de **toda** la biblioteca documental, incluidos documentos que no son módulos (Constitución, Visión, Historial, etc.) — mezclarlo en el Artículo 29 habría ensanchado su alcance más allá de su título.
No formalizarlo nunca en la Constitución, dejándolo solo como proceso descrito en `DECISIONES-ARQUITECTURALES.md` y `13-HISTORIAL-DE-VERSIONES.md`. Fue la decisión explícita del usuario en la sesión anterior (v0.42); se revirtió ahora porque el propio usuario pidió retomar esta tarea en la sesión siguiente, tal como quedó anotado como pendiente.

**Consecuencias:**
- La Constitución pasa de 30 a 31 artículos.
- El Artículo 29.1 ("ningún módulo se desarrolla sin documentación aprobada previamente") queda respaldado, por primera vez, por una regla constitucional propia que define qué significa "aprobada" — antes solo estaba desarrollado en `DECISIONES-ARQUITECTURALES.md` y `13-HISTORIAL-DE-VERSIONES.md`, sin rango de ley del proyecto.
- Cualquier cambio MAYOR futuro a un documento ya aprobado debe, a partir de ahora, citar el Artículo 31.4 como la regla que exige su re-aprobación — no solo la convención de `13-HISTORIAL-DE-VERSIONES.md`.
- `02-CONSTITUCION-ERP.md` mismo queda como el primer caso real de re-aprobación individual por cambio MAYOR bajo este mecanismo.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.43, MAYOR: agrega una regla constitucional nueva) y una fila nueva en el Registro de Aprobaciones para `02-CONSTITUCION-ERP.md`.
- `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse con una nueva entrada (v0.40, MAYOR: reestructura `Cuenta` — quita su restricción de cardinalidad — y cambia el tipo de campo de `BajaInventario.tipo` de Enumeración a Referencia).

### [2026-07-23] Agregado el Módulo 7 — Asistente de Consulta al catálogo de módulos

**Contexto:**
El catálogo de módulos no contemplaba una capa de consulta conversacional de solo lectura sobre el estado patrimonial calculado por el Motor de Flujos Patrimoniales.

**Decisión:**
Se agrega el Módulo 7 — Asistente de Consulta, estrictamente de solo lectura, sin capacidad de emitir eventos ni alterar el estado patrimonial. Permite consultas en lenguaje natural sobre el estado patrimonial actual o histórico (basadas exclusivamente en las proyecciones y el historial de eventos del Motor de Flujos Patrimoniales), explicación de origen de cualquier saldo o cifra, y sugerencias asistivas que nunca se ejecutan por sí solas. Su alcance multiempresa, gobernanza (Artículo 26) y relación unidireccional con el motor quedan declaradas siguiendo la misma estructura de cinco bloques exigida por el Artículo 29.2 para los seis módulos anteriores. Quedan señalados, sin resolver, dos pendientes: si sus consultas se registran en el historial de auditoría (Artículo 8), y el canal de interacción concreto.

**Alternativas consideradas:**
- No agregar ningún módulo de consulta conversacional.
- Agregar un agente que también pueda ejecutar movimientos por su cuenta.
- Agregar un módulo estrictamente de solo lectura, gobernado por el Artículo 26 ya existente en la Constitución.

Se eligió la tercera opción.

**Consecuencias:**
El Artículo 26 de la Constitución ya exige que cualquier funcionalidad de IA sea asistiva y nunca decisoria sin acción humana explícita. Un módulo de solo lectura es la única forma de ofrecer una interfaz conversacional a Oliver sin comprometer la integridad ni la trazabilidad del patrimonio (Artículo 3, Artículo 6). El catálogo de módulos pasa de 6 a 7 módulos; `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse reflejando este cambio como MENOR (agrega un módulo nuevo, de solo lectura, sin alterar ni contradecir lo existente).

### [2026-07-23] Resueltos los dos pendientes del Módulo 7 — Asistente de Consulta

**Contexto:**
El Módulo 7 — Asistente de Consulta quedó aprobado con dos pendientes abiertos: si sus consultas se auditan, y cuál sería su canal de interacción.

**Decisión:**
(a) Las consultas del asistente no entran al historial de auditoría patrimonial del Artículo 8; llevan una bitácora de actividad propia, separada, sin naturaleza contable.
(b) El asistente es un servicio central consumible desde múltiples canales: chat de texto en el Hub, comando de voz, y otros canales futuros no descartados.

**Alternativas consideradas:**
No se presentaron alternativas de fondo distintas a las resoluciones adoptadas: el contenido fue provisto directamente por el usuario para cerrar ambos pendientes.

**Razón:**
(a) Mezclar el uso de una herramienta de consulta con el historial que reconstruye el patrimonio real generaría ruido en un mecanismo que la Constitución reserva para eventos que modifican estado (Artículo 8). Una bitácora separada da la visibilidad de uso deseada sin comprometer la naturaleza del historial de auditoría.
(b) El usuario (Oliver) opera tanto en oficina como en campo; limitar el asistente a un solo canal reduciría su utilidad real. Un diseño de servicio central con múltiples interfaces es consistente con el patrón ya usado por el Motor de Flujos Patrimoniales (módulos como puntos de entrada hacia un núcleo común).

**Consecuencias:**
El Módulo 7 en `08-CATALOGO-DE-MODULOS.md` queda con dos pendientes nuevos y más acotados (formato/ubicación de la bitácora de actividad, y prioridad de implementación entre canales), en lugar de los dos pendientes de fondo originales. `13-HISTORIAL-DE-VERSIONES.md` debe actualizarse reflejando este cambio como MENOR (resuelve pendientes sin alterar ni contradecir lo existente).
