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
