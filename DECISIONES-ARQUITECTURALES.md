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
