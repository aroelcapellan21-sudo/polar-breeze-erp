# Constitución ERP

Estado:

> Vigente — sujeta a enmienda formal mediante `DECISIONES-ARQUITECTURALES.md`

Objetivo:

Este documento define las reglas que **ningún módulo, flujo, integración, dato o decisión técnica del ERP Polar Breeze puede violar**. Es la norma de más alto rango de todo el repositorio, superada únicamente por `01-VISION-ERP.md`, del cual se deriva.

Ante un conflicto entre esta Constitución y cualquier otro documento (`00`, `03` a `13`, `99`), prevalece la Constitución. Ningún módulo del `08-CATALOGO-DE-MODULOS.md` puede aprobarse para desarrollo si su diseño contradice alguno de los artículos siguientes.

Este documento se escribe y mantiene con estándar de arquitectura empresarial: cada regla debe ser verificable, no aspiracional.

Contenido:

## Artículo 1 — Principios del ERP

1.1. El ERP existe para modelar y gobernar **flujos patrimoniales** (efectivo, inventario, mercancía, deuda) de una o más empresas, no solo para registrar transacciones aisladas.

1.2. El **código** es la clave universal de todo producto o entidad de negocio. Nunca se usa el nombre libre como identificador funcional, clave de búsqueda ni clave foránea; el nombre es solo una etiqueta de presentación.

1.3. El sistema es **offline-first** en todos los módulos operativos de campo (inventario, despacho, facturación en punto de venta). Toda operación crítica se guarda primero en local y sincroniza automáticamente cuando hay conectividad, sin pérdida silenciosa de información.

1.4. Compatibilidad obligatoria con **Android e iOS** en toda funcionalidad de operación de campo. Ninguna funcionalidad crítica puede quedar disponible en una plataforma y no en la otra.

1.5. La **persistencia de sesión** es resistente a interrupciones: cierres inesperados de la app, pérdida de conectividad o reinicio del dispositivo no pueden causar pérdida de contexto de trabajo ni de datos capturados.

1.6. El desarrollo del ERP y su documentación evolucionan juntos: ningún principio de este artículo se implementa de forma parcial "por ahora" sin quedar registrado como deuda técnica explícita en `DECISIONES-ARQUITECTURALES.md`.

## Artículo 2 — Arquitectura Multiempresa desde el Primer Día

2.1. El ERP **nace multiempresa**. No existe, ni existirá, una versión "de una sola empresa" que luego se adapte a varias. **Polar Breeze es únicamente la primera empresa del ecosistema**, no el modelo de referencia de "empresa única".

2.2. Toda entidad de negocio del sistema incluye el campo **`empresaId`** como parte obligatoria de su identidad, sin excepción salvo el propio catálogo de empresas y la configuración de plataforma.

2.3. Cuando una entidad opera a nivel de sede física, punto de despacho, cuarto frío o unidad local, incluye adicionalmente el campo **`sucursalId`**. Toda entidad con `sucursalId` hereda y respeta el aislamiento por `empresaId` de su sucursal.

2.4. Ningún módulo puede asumir que existe una sola empresa o una sola sucursal en el sistema. Todo query, cálculo, regla de negocio y reporte debe estar acotado explícitamente por `empresaId` (y por `sucursalId` cuando aplique).

2.5. El aislamiento entre empresas es de datos, configuración y flujo: una empresa nunca puede leer, escribir ni inferir información de otra empresa por ningún camino (UI, API, reportes, exportaciones, notificaciones, IA).

2.6. Los catálogos que parezcan "universales" (productos, cuentas, vendedores, bancos, etc.) están, salvo declaración explícita en `05-MODELO-DE-DATOS-MAESTRO.md`, scoped a `empresaId`. Todo catálogo verdaderamente compartido entre empresas debe documentarse como excepción explícita, con su justificación.

2.7. Un mismo usuario puede pertenecer a más de una empresa y a más de una sucursal, pero cada sesión opera dentro de una **empresa activa** y, cuando aplique, una **sucursal activa** únicas a la vez. El cambio de contexto activo es una acción explícita y auditable, nunca implícita.

2.8. Todo módulo nuevo debe declarar, en su documentación, cómo respeta `empresaId` y `sucursalId` antes de ser aprobado para desarrollo.

## Artículo 3 — Una Sola Fuente de Verdad

3.1. Cada dato del sistema tiene **un único origen autoritativo**. Ningún otro lugar del sistema puede considerarse fuente de verdad para ese mismo dato, incluidas copias, cachés, exportaciones o reportes.

3.2. Toda pantalla, reporte o integración que muestre un dato lo **lee** de su fuente de verdad (directamente o vía una proyección declarada), nunca mantiene su propia copia editable en paralelo.

3.3. Cuando exista una proyección o vista derivada (por ejemplo, para performance offline), debe estar documentada como tal en `05-MODELO-DE-DATOS-MAESTRO.md`, indicando de qué fuente de verdad se deriva y cómo se resincroniza.

## Artículo 4 — Prohibición de Duplicar Información

4.1. Ningún dato de negocio se almacena dos veces con la intención de que ambas copias se mantengan manualmente en sincronía. Si un dato se necesita en más de un lugar, se referencia por clave (`empresaId` + código), no se copia.

4.2. Está prohibido crear catálogos paralelos de la misma entidad (por ejemplo, dos listas de productos, dos listas de vendedores) para distintos módulos. Todos los módulos consumen el mismo catálogo maestro.

4.3. Excepción única: los datos capturados offline en el dispositivo antes de sincronizar. Esa duplicación temporal es funcional (Artículo 1.3) y se resuelve automáticamente al sincronizar, nunca queda como estado permanente.

## Artículo 5 — Arquitectura Basada en Eventos

5.1. Todo cambio relevante de estado patrimonial (movimiento de efectivo, movimiento de inventario, despacho, facturación, novedad) se modela como un **evento inmutable**, no como una actualización silenciosa de un valor.

5.2. El estado actual de cualquier entidad es siempre el resultado de aplicar su historial de eventos, no un valor editado directamente sin dejar rastro del evento que lo originó.

5.3. Los eventos se nombran en el catálogo de `docs/diagramas/eventos.drawio` y en `12-GLOSARIO.md`, siguiendo una nomenclatura consistente (verbo en pasado + entidad, por ejemplo `ProductoDespachado`, `PagoRegistrado`).

5.4. Ningún evento se elimina una vez emitido. Una corrección se modela como un nuevo evento compensatorio, nunca como edición o borrado del evento original.

## Artículo 6 — Arquitectura Basada en Flujos Patrimoniales

6.1. El sistema modela tres flujos patrimoniales centrales: **flujo de capital** (efectivo), **flujo de mercancía** (inventario físico) y **flujo de información** (documentos, aprobaciones, decisiones). Todo módulo de negocio se ubica explícitamente dentro de uno o más de estos flujos.

6.2. Ningún flujo patrimonial puede alterarse desde fuera de su propio motor de reglas (Artículo `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`). Por ejemplo, el inventario no se ajusta manualmente sin pasar por un evento de flujo de mercancía.

6.3. Todo movimiento patrimonial debe balancear: no puede "desaparecer" capital o mercancía sin un evento explícito que documente su destino (venta, merma, traslado, pérdida, devolución).

## Artículo 7 — Trazabilidad Absoluta

7.1. Todo dato patrimonial es trazable de extremo a extremo: cada cambio de estado tiene un origen, un momento (timestamp), un responsable (usuario) y una empresa/sucursal identificables.

7.2. Debe ser posible reconstruir, para cualquier entidad, la cadena completa de eventos que la llevó a su estado actual, sin depender de memoria humana ni de documentación externa al sistema.

7.3. Ninguna funcionalidad puede exponer una acción que modifique estado patrimonial sin registrar quién la ejecutó y cuándo.

## Artículo 8 — Auditoría Obligatoria

8.1. Toda operación que cree, modifique, apruebe, anule o elimine (soft delete) un registro de negocio genera un registro de auditoría independiente del propio registro.

8.2. El registro de auditoría es de solo lectura para todos los roles, incluidos los administrativos. Ningún rol puede editar o eliminar el historial de auditoría.

8.3. La auditoría incluye, como mínimo: `empresaId`, `sucursalId` (si aplica), usuario, acción, entidad afectada, valores anteriores y nuevos, y timestamp.

## Artículo 9 — Soft Delete

9.1. Ningún registro de negocio se elimina físicamente de la base de datos. Toda "eliminación" es un **soft delete**: el registro se marca como inactivo/anulado y se conserva íntegro.

9.2. Los registros con soft delete no aparecen en las operaciones normales del sistema, pero permanecen disponibles para auditoría, reportes históricos y reconstrucción de trazabilidad.

9.3. Un registro con soft delete nunca se reutiliza como si fuera un nuevo registro; su clave de negocio queda retirada permanentemente (ver Artículo 1.2 y `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` sobre unicidad de claves).

## Artículo 10 — Integridad Referencial

10.1. Ninguna entidad puede referenciar una clave (`empresaId`, `sucursalId`, código de producto, cuenta, etc.) que no exista en su catálogo maestro correspondiente.

10.2. Toda referencia cruzada respeta el límite de `empresaId`: una entidad de la Empresa A nunca referencia una entidad de la Empresa B.

10.3. Un catálogo maestro no puede eliminarse físicamente (Artículo 9) mientras existan entidades activas que lo referencien; su desactivación se documenta y se propaga como advertencia a los módulos dependientes.

## Artículo 11 — Versionado de Datos

11.1. Toda entidad configurable o todo documento que pueda cambiar en el tiempo (reglas contables, catálogos, parámetros de flujo, documentos aprobados) mantiene versión explícita.

11.2. Los cálculos y reportes históricos siempre usan la versión del dato/regla vigente **al momento del evento**, no la versión actual, salvo que se solicite explícitamente un recálculo con reglas nuevas (y ese recálculo también queda versionado).

11.3. Un cambio de versión de una regla o catálogo no modifica retroactivamente eventos ya registrados.

## Artículo 12 — Seguridad por Roles

12.1. Todo acceso al sistema se gobierna por roles, nunca por identidad de usuario individual codificada en la lógica de negocio.

12.2. Un rol se define por empresa; el mismo usuario puede tener roles distintos en empresas distintas (consistente con el Artículo 2.7).

12.3. Ningún módulo implementa su propio esquema de permisos paralelo; todos consumen el Motor de Permisos (Artículo 13).

## Artículo 13 — Motor de Permisos

13.1. Los permisos se evalúan de forma centralizada por un único motor de permisos, nunca mediante verificaciones ad hoc dispersas en cada módulo.

13.2. Los permisos se definen por combinación de `empresaId` + rol + acción + entidad (y `sucursalId` cuando aplique). No existen permisos globales implícitos.

13.3. Toda acción sensible (aprobar, anular, exportar, modificar configuración) requiere una verificación explícita del motor de permisos antes de ejecutarse, incluso si la interfaz ya oculta la opción a roles no autorizados.

## Artículo 14 — Inmutabilidad de Documentos Aprobados

14.1. Un documento de negocio (factura, nota de crédito, consignación aprobada, arqueo cerrado) que alcanza estado "aprobado" o "cerrado" es **inmutable**.

14.2. Ninguna corrección se aplica editando un documento aprobado. Toda corrección se modela como un nuevo documento o evento compensatorio que referencia al original (por ejemplo, una nota de crédito referencia su factura).

14.3. Esta regla aplica también a la propia documentación arquitectónica: una decisión ya registrada en `DECISIONES-ARQUITECTURALES.md` no se edita retroactivamente para cambiar su contenido; una decisión nueva puede reemplazarla, dejando explícito el reemplazo.

## Artículo 15 — Eventos del Sistema

15.1. El catálogo de eventos del sistema es único y compartido entre módulos; vive documentado en `docs/diagramas/eventos.drawio` y `12-GLOSARIO.md`.

15.2. Todo evento incluye como mínimo: tipo de evento, `empresaId`, `sucursalId` (si aplica), entidad afectada, payload de negocio, usuario emisor y timestamp.

15.3. Ningún módulo nuevo introduce un evento fuera del catálogo sin antes documentarlo (Artículo 5.3 y regla de gobernanza documental del Artículo 29).

## Artículo 16 — Catálogos Maestros

16.1. Los catálogos maestros (productos, cuentas, vendedores, bancos, clientes, proveedores) se definen una sola vez en `05-MODELO-DE-DATOS-MAESTRO.md` y son consumidos, no reimplementados, por todos los módulos.

16.2. Todo catálogo maestro está scoped por `empresaId` salvo excepción documentada (Artículo 2.6).

16.3. La configuración variable de negocio (tasas, clasificaciones, parámetros de flujo) vive en Firestore bajo `config/*`, particionada por `empresaId`. Ningún valor de configuración de negocio se hardcodea en el código de la aplicación.

## Artículo 17 — Reglas de Inventario

17.1. El **Inventario del Chofer** y el **Inventario del Encargado** son procesos independientes, con sus propios registros, novedades y responsables. Uno nunca sustituye ni se fusiona automáticamente con el otro; su conciliación es un proceso explícito y auditable.

17.2. Las novedades (dañados, rotos, en mal estado, sobrantes, faltantes) se registran en el proceso donde ocurren y no se reclasifican retroactivamente sin dejar evidencia del cambio (Artículo 14).

17.3. Todo movimiento de inventario es un evento de flujo de mercancía (Artículo 6) y respeta `empresaId`/`sucursalId`.

## Artículo 18 — Reglas Financieras

18.1. Todo movimiento de efectivo (Módulo 1 — Flujo de Efectivo) se clasifica obligatoriamente en Costo, Venta, Distribución o Mantenimiento antes de considerarse registrado.

18.2. Toda cuenta bancaria pertenece a una única `empresaId` y registra número de cuenta y banco como datos obligatorios.

18.3. Ningún movimiento financiero se registra sin cuenta de origen y cuenta o clasificación de destino identificables.

## Artículo 19 — Reglas Contables

19.1. Toda operación con impacto contable genera su contrapartida de forma consistente con el motor de flujos patrimoniales (Artículo 6); no se permiten asientos "sueltos" fuera de un evento de negocio.

19.2. Las reglas contables detalladas (cuentas, clasificaciones, tratamiento de casos especiales) se documentan en `06-REGLAS-CONTABLES-Y-FINANCIERAS.md`; esta Constitución fija el principio, ese documento fija el detalle.

19.3. Ningún cambio a una regla contable se aplica retroactivamente a periodos ya cerrados (Artículo 11.2).

## Artículo 20 — Reglas de Cuentas por Pagar

20.1. Toda obligación con un tercero (proveedor, transportista, consignatario) se registra como evento de flujo de capital pendiente, con `empresaId`, monto, contraparte y fecha de vencimiento.

20.2. Un pago aplicado a una cuenta por pagar es un evento independiente que referencia la obligación original; nunca se edita el monto original de la obligación para reflejar pagos parciales.

## Artículo 21 — Reglas de Consignación

21.1. Toda consignación (Módulo 3 — Despacho y Consignaciones) se crea como un documento de flujo de mercancía con responsable, contenido y `empresaId`/`sucursalId` explícitos.

21.2. Las novedades, daños y sobrantes de consignación siguen el mismo principio de trazabilidad y no reclasificación retroactiva del Artículo 17.2.

21.3. Una solicitud de retiro y su justificación son eventos distintos y ambos obligatorios; no existe retiro sin justificación documentada.

## Artículo 22 — Reglas de Cuarto Frío

22.1. El cuarto frío es una `sucursalId` o unidad operativa propia dentro del flujo de mercancía; sus novedades (Módulo 2) se registran con el mismo rigor de trazabilidad que cualquier otro punto de inventario.

22.2. Toda condición anómala del cuarto frío (rotura de cadena de frío, producto dañado, mal estado) genera un evento propio, distinguible de las novedades de despacho o de almacén general.

## Artículo 23 — Reglas de Despacho

23.1. Todo despacho es un evento de flujo de mercancía que mueve inventario de un origen a un destino identificables, ambos dentro de la misma `empresaId`.

23.2. Las novedades de despacho (dañado en despacho, sobrantes de despacho) se registran en el momento del despacho y quedan vinculadas al documento de despacho correspondiente, nunca sueltas.

## Artículo 24 — Reglas de Exportación

24.1. Toda exportación de datos (reportes, respaldos, integraciones) respeta el aislamiento por `empresaId`: una exportación nunca mezcla datos de más de una empresa salvo que el usuario tenga permiso explícito multiempresa y así se declare en el archivo exportado.

24.2. Una exportación es una **lectura**, nunca una vía de escritura o modificación de datos de origen.

## Artículo 25 — Reglas de Reportes

25.1. Todo reporte se genera a partir de la fuente de verdad (Artículo 3) o de una proyección declarada, nunca de una copia mantenida manualmente.

25.2. Todo reporte incluye de forma explícita su alcance: `empresaId` (y `sucursalId` si aplica), rango de fechas y versión de reglas usada (Artículo 11.2).

25.3. El arqueo manual (Módulo 5) es un evento de conciliación que compara el estado del sistema contra un conteo físico y registra la diferencia como evento propio, sin sobrescribir el saldo del sistema silenciosamente.

## Artículo 26 — Reglas para IA

26.1. Cualquier asistente de IA (incluido este) que participe en el desarrollo o la operación del ERP está sujeto a las mismas reglas de esta Constitución que un desarrollador humano.

26.2. Ninguna IA puede tocar código sin mostrar el plan primero, ni commitear sin prueba previa, ni asumir ante ambigüedad — debe preguntar (heredado de `09-ESTANDARES-DE-DESARROLLO.md`).

26.3. Ninguna IA puede inferir ni exponer datos de una empresa a otra (Artículo 2.5), ni siquiera con fines de análisis o soporte, salvo permiso explícito multiempresa (Artículo 24.1).

26.4. Toda funcionalidad de IA integrada al producto (sugerencias, clasificación automática, detección de anomalías) es asistiva: no puede aprobar, cerrar ni hacer inmutable un documento (Artículo 14) sin una acción humana explícita.

## Artículo 27 — Reglas para Futuras Integraciones

27.1. Toda integración externa (pasarelas de pago, facturación electrónica, bancos, logística) se conecta al sistema a través de eventos (Artículo 5) o de una capa de sincronización explícita, nunca escribiendo directamente sobre catálogos maestros.

27.2. Ninguna integración externa puede ser la fuente de verdad de un dato que ya tiene fuente de verdad interna (Artículo 3); actúa como consumidor o como origen de eventos, no como reemplazo del modelo de datos maestro.

27.3. Toda integración nueva se documenta antes de conectarse en producción, incluyendo su alcance por `empresaId`.

## Artículo 28 — Reglas para Crecimiento del ERP

28.1. El crecimiento del ERP (nuevas empresas, nuevas sucursales, nuevos países, nuevas monedas) se diseña como parametrización de lo existente, nunca como una reescritura del modelo multiempresa (Artículo 2).

28.2. Ninguna decisión de corto plazo puede comprometer la capacidad del sistema de incorporar una nueva empresa sin cambios estructurales al modelo de datos.

## Artículo 29 — Reglas para Nuevos Módulos

29.1. Ningún módulo del `08-CATALOGO-DE-MODULOS.md` se desarrolla sin documentación aprobada previamente (heredado de la Regla importante del `README.md`).

29.2. Todo módulo nuevo debe declarar explícitamente, en su documentación: su alcance por `empresaId`/`sucursalId` (Artículo 2.8), los eventos que emite o consume (Artículo 15.3), y qué catálogos maestros consume (Artículo 16.1).

29.3. Toda decisión arquitectónica relevante para un módulo nuevo se registra en `DECISIONES-ARQUITECTURALES.md` antes de implementarse.

29.4. Ningún cambio de código se realiza sin mostrar el plan primero, sin prueba previa, y sin confirmar que no rompe funcionalidad existente. Ante ambigüedad, se pregunta — nunca se asume (detalle operativo en `09-ESTANDARES-DE-DESARROLLO.md`).

## Convención de Cambios a esta Constitución

Ninguna de las reglas anteriores se modifica por conveniencia de una implementación puntual. Un cambio a este documento requiere:

1. Registrar la propuesta y su justificación en `DECISIONES-ARQUITECTURALES.md`.
2. Evaluar el impacto sobre los módulos ya documentados en `08-CATALOGO-DE-MODULOS.md`.
3. Actualizar este documento solo después de lo anterior, nunca antes ni en paralelo a una implementación que ya la esté violando.

Observaciones:

(Espacio reservado)
