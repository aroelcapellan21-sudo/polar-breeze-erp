# Catálogo de Módulos

Estado:

> Vigente — Aprobado por el Arquitecto/Product Owner del ERP y por Oliver (dueño de Polar Breeze) el 2026-07-17 (baseline v0.41; ver Registro de Aprobaciones en `13-HISTORIAL-DE-VERSIONES.md`)

Objetivo:

Documentar el catálogo completo de módulos del ERP Polar Breeze, reconciliado con `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md` (fuente de verdad para el diseño de módulos), y declarar para cada uno lo exigido por el Artículo 29.2 de `02-CONSTITUCION-ERP.md`: su alcance por `empresaId`/`sucursalId` (Artículo 2.8), los eventos que emite o de los que depende (Artículo 15.3), y los catálogos maestros que crea o consume (Artículo 16.1). Ningún módulo se aprueba para desarrollo sin esta declaración completa (Artículo 29.1).

Contenido:

## Cómo leer este catálogo

Cada módulo se describe con la misma estructura, exigida por el Artículo 29.2 de la Constitución:

- **Funcionalidades** — transcritas fielmente de `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md`, organizadas por las mismas subsecciones que Oliver definió.
- **Alcance multiempresa** — cómo respeta `empresaId` y, cuando aplica, `sucursalId` (Artículo 2.8).
- **Eventos que emite** — los eventos del catálogo formal (`12-GLOSARIO.md`, sección C) que este módulo genera, y el flujo de negocio (`07-FLUJOS-DE-NEGOCIO.md`) del que provienen.
- **Eventos de otros módulos que le afectan** — eventos que este módulo no emite, pero cuyas proyecciones consume o de los que depende para operar.
- **Catálogos maestros** — cuáles crea y cuáles consume, de los definidos en `05-MODELO-DE-DATOS-MAESTRO.md`, sección 4 (Artículo 16.1).
- **Pendiente de modelar** — funcionalidades que Oliver define y que este documento transcribe fielmente, pero para las que todavía no existe entidad, campo o evento formal en `05`/`07`/`11`/`12`. No se inventan aquí; quedan señaladas para una decisión aparte.

## Gobernanza de Catálogos de Configuración Dinámica (2026-07-17)

Todo catálogo maestro marcado como gestionado desde el **Hub Admin** en este documento (`Cuenta`, `CuentaBancaria`, `AporteCapital`, `MotivoSalidaSinCobro`, y por extensión el resto de los catálogos de la sección 4 de `05-MODELO-DE-DATOS-MAESTRO.md`) comparte el mismo patrón:

- **Patrón CRUD:** Agregar / Editar / Desactivar — nunca eliminación física (Artículo 9 de la Constitución), vía el campo común `estado` (`11-DICCIONARIO-DE-DATOS.md`, sección 2), sin campo adicional.
- **Acceso:** exclusivo del rol Administrador/Oliver, evaluado por el Motor de Permisos genérico ya existente (Artículo 13 de la Constitución) — no se introduce un mecanismo de permisos nuevo.
- **Ubicación:** Firestore `config/{empresaId}/<colección>/{código}` (`03-ARQUITECTURA-GENERAL.md`, sección 8).

### Módulo 1 — Flujo de Efectivo y Bancos

**Funcionalidades:**
- *Flujo de Efectivo*: Venta / Costo / Distribución / Mantenimiento -> Agregar Registro
- *Cuentas*: Cuenta 1, Cuenta 2, Cuenta 3, Cuenta 4, Cuenta 5, Cuenta 6 -> Mantenimiento / Crear Cuenta
- *Bancos*: Banco / Número de Cuenta -> Participación de Capital

**Alcance multiempresa:** `MovimientoCapital` y los catálogos que administra (`Cuenta`, `CuentaBancaria`, `Fondo`) llevan `empresaId` obligatorio (Artículo 2.2). Ninguno opera a nivel de `sucursalId` en el modelo actual — el efectivo se gestiona a nivel de empresa, no de sede (`05-MODELO-DE-DATOS-MAESTRO.md`, secciones 4 y 5).

**Eventos que emite:** `CapitalIngresado` (F1 — ingreso directo de capital: aporte, cobro, financiamiento).

**Eventos de otros módulos que le afectan:** `CapitalIngresado` también lo emite el Módulo 5 — Despacho, Novedades y Caja (F7, ingreso de capital por venta), actualizando el mismo `Fondo` Venta que este módulo administra. `ArqueoRealizado` (Módulo 5) concilia el saldo de `Fondo`/`Cuenta` que este módulo proyecta, sin sobrescribirlo (Artículo 25.3).

**Catálogos maestros que crea:** `Cuenta` (catálogo abierto desde el 2026-07-17, ya no restringido a 6 cuentas fijas), `CuentaBancaria`, `Fondo`, `AporteCapital`. Los cuatro se gestionan desde el Hub Admin (Agregar/Editar/Desactivar), acceso restringido al rol Administrador/Oliver.

**Catálogos maestros que consume:** ninguno adicional.

**Catálogo de Configuración Dinámica — Capital de la Empresa (2026-07-17):** se agrega `AporteCapital` (`05-MODELO-DE-DATOS-MAESTRO.md`, sección 5), catálogo con el capital inicial de la empresa (default 0, puramente informativo por ahora — no emite `CapitalIngresado` ni genera `MovimientoCapital`). Es un concepto **distinto** del pendiente "Participación de Capital" de abajo: ese pendiente es por `CuentaBancaria` individual, `AporteCapital` es el capital total de la empresa; ambos quedan documentados por separado.

**Pendiente de modelar:**
- **Participación de Capital** (Bancos) — sigue sin entidad ni campo formal en `05`/`11`. El usuario confirmó que la mención de Oliver ("Banco / Número de Cuenta -> Participación de Capital") es demasiado ambigua para modelarla con confianza sin más contexto; es el único de los 7 conceptos pendientes de la reconciliación con Oliver que sigue sin resolver (`DECISIONES-ARQUITECTURALES.md`, decisión "Modelados 6 de los 7 conceptos pendientes de Oliver"). No lo resuelve el `AporteCapital` agregado arriba (ver nota).

Las cuatro clasificaciones de `Fondo` (Costo, Venta, Distribución, Mantenimiento — Artículo 18.1 de la Constitución) están confirmadas contra la fuente de verdad de Oliver: la aparente discrepancia señalada en una versión anterior de este documento se debía a una transcripción incompleta de `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md`, ya corregida (`DECISIONES-ARQUITECTURALES.md`, decisión "Confirmadas las cuatro clasificaciones de Fondo contra la fuente de verdad de Oliver").

### Módulo 2 — CXP, Facturación y Reportes

**Funcionalidades:**
- *Cuentas por Pagar*: Suplidor / Fecha de Factura / Número de Factura / Comprobante (NCF) / Código de Producto / Descripción / Costo / ITBIS / Total / Cantidad de Productos / Días de Crédito / Subir Imagen del Pago
- *Notas y Medios de Pago*: Generar Nota de Crédito / Seleccionar Factura y/o Nota de Crédito / Medio de Pago (Depósito, Efectivo, Transferencia) / Monto del Pago / Pedir Numeración de Transacción / Bandeja de Banco
- *Reportes Generados*: R1 (Fecha Factura - Comprobante - Costo - ITBIS - Total) / R2 (Fecha Factura - Comprobante - Total - Días Vencimiento) / R3 (Fecha Factura - Comprobantes - Medio de Pago con Imagen)

**Alcance multiempresa:** `Obligacion` lleva `empresaId` obligatorio, sin `sucursalId` (Artículo 2.2; `05-MODELO-DE-DATOS-MAESTRO.md`, sección 6).

**Eventos que emite:** `ObligacionRegistrada` (F2 — factura del Suplidor recibida, en unidad atómica con `MercanciaRecibida` del Módulo 3), `PagoRegistrado` (F10 — pago aplicado a una obligación existente, vía depósito/efectivo/transferencia).

**Eventos de otros módulos que le afectan:** ninguno directo.

**Catálogos maestros que consume:** `Proveedor` (creado en el Módulo 6 — Parámetros de Mantenimiento; es la contraparte de `Obligacion`), `CondicionPago` (creada en el Módulo 6; fija el plazo de crédito y la fecha de vencimiento), `Producto` (para las líneas de la factura del Suplidor).

**Modelado:** Comprobante (NCF), Costo/ITBIS/Total y Fecha de Factura son ahora campos de `Obligacion` (`05-MODELO-DE-DATOS-MAESTRO.md`, sección 6); Días de Crédito se resuelve mediante la nueva entidad `CondicionPago` (sección 4). Los reportes R1, R2 y R3 quedan documentados como proyecciones de solo lectura sobre esos mismos campos, sin entidad propia.

**Pendiente de modelar:**
- **"Generar Nota de Crédito" de este módulo es la nota de crédito que el Suplidor emite hacia Polar Breeze** (reduce una `Obligacion`), un concepto **distinto** de la `NotaCredito` de venta ya modelada en `05` (que corrige una `Factura` de venta, Módulo 5). No existe todavía una entidad formal para esta nota de crédito de proveedor.

### Módulo 3 — Inventario y Cuarto Frío

**Funcionalidades:**
- *Almacén Principal*: Inventario Buy In (Compra) — Código de Producto / Descripción / Cantidad / Costo / Total; Inventario For Sale (Venta) — Código de Producto / Descripción / Cantidad / Precio / Total
- *Novedades Cuarto Frío*: Sobrantes / Faltantes Cuarto Frío / Dañados / Consignado en Mal Estado / Refrigerios / Bonificaciones / Donaciones / Bandeja de Novedades Cuarto Frío (Reportes)

**Alcance multiempresa:** `InventarioChofer`, `InventarioEncargado`, `NovedadInventario` y `BajaInventario` llevan `empresaId` y `sucursalId` obligatorios: cuarto frío, vehículo del chofer y punto de almacén son unidades `sucursalId` (Artículos 2.3 y 22.1). El Inventario del Chofer y el Inventario del Encargado son procesos independientes, nunca fusionados automáticamente (Artículo 17.1).

**Eventos que emite:** `MercanciaRecibida` (F2, en unidad atómica con `ObligacionRegistrada` del Módulo 2), `MercanciaTransferida` (F3), `NovedadInventarioRegistrada` (F3), `ConciliacionInventarioRealizada` (F3), `NovedadCuartoFrioRegistrada` (F4), `BajaInventarioRegistrada` (F13 — merma, pérdida, condonación, donación, bonificación o refrigerio).

**Eventos de otros módulos que le afectan:** `MercanciaVendida` y `MercanciaDevuelta` (Módulo 5, F7/F9) modifican las existencias que este módulo proyecta, sin que este módulo los emita. `Despachado` (Módulo 5, F5) retira mercancía del inventario de origen que este módulo mantiene.

**Catálogos maestros que crea:** ninguno.

**Catálogos maestros que consume:** `Producto` (creado en el Módulo 6 — Parámetros de Mantenimiento), `MotivoSalidaSinCobro` (creado en el Módulo 6, sección "Motivos de Salida sin Cobro" — `05-MODELO-DE-DATOS-MAESTRO.md`, sección 4).

**Modelado:** Refrigerios, Bonificaciones y Donaciones son valores de `BajaInventario.tipo` (`05-MODELO-DE-DATOS-MAESTRO.md`, sección 7), junto a merma, pérdida y condonación. **Cambio de modelo (2026-07-17):** `tipo` deja de ser un enum cerrado y pasa a referenciar el catálogo abierto `MotivoSalidaSinCobro`, administrable desde el Hub Admin.

**Pendiente de modelar:**
- La distinción explícita **Buy In (compra) / For Sale (venta)** del Almacén Principal no está modelada como dos vistas separadas de `InventarioEncargado`; hoy es una sola proyección de existencias.

### Módulo 4 — Consignaciones

**Funcionalidades:**
- *Acciones*: Consignar (Generar) / Retirar Consignado / Histórico de Consignaciones / Visualizar Consignaciones
- *Rutas y Vías*: PPTO Inventario Santiago / Consignaciones Individuales (1 al 23) / Consignaciones Generadas / Retiradas / Todas
- *Filtros*: Vendedor / Producto / Estado

**Alcance multiempresa:** `Consignacion` lleva `empresaId` y `sucursalId` obligatorios (Artículo 21.1).

**Eventos que emite:** `ConsignacionCreada` (F5).

**Eventos de otros módulos que le afectan:** consume la existencia proyectada de `InventarioEncargado`/`InventarioChofer` (Módulo 3) como origen de la mercancía a consignar. `BajaInventarioRegistrada` (Módulo 3, F13) puede reducir la existencia de una `Consignacion` cuando su mercancía sufre merma, pérdida, condonación, donación, bonificación o refrigerio. `Despachado` (Módulo 5, F5) retira mercancía de una consignación al ejecutarse el despacho asociado.

**Catálogos maestros que crea:** `Ruta`.

**Catálogos maestros que consume:** `Producto`, `Vendedor` (para los filtros).

**Modelado:** Rutas y Vías corresponde a la nueva entidad `Ruta` (`05-MODELO-DE-DATOS-MAESTRO.md`, sección 8) — código, nombre, presupuesto de inventario ("PPTO Inventario Santiago") — que `Consignacion` referencia opcionalmente junto a su número de punto (cubre "Consignaciones Individuales 1 al 23"). "Consignaciones Generadas/Retiradas/Todas" y los Filtros por Estado, Vendedor y Producto no requerían campo nuevo: se resuelven combinando `estadoConsignacion`, `Vendedor` y `Producto` ya existentes en la interfaz.

### Módulo 5 — Despacho, Novedades y Caja

**Funcionalidades:**
- *Picking / Despacho*: Retirar Consignación / Justificar Retiro / Faltante Despacho / Dañado Despacho
- *Bandeja Novedades Despacho*: Solicitud de Retiro / Registro de Sobrantes / Dañados / Reportes de Novedades en Despacho
- *Arqueo de Caja y Facturar*: Arqueo Manual / Sugerido / Exportar Reportes de Arqueo / Histórico / Facturación / Histórico Facturas

**Alcance multiempresa:** `Despacho` lleva `empresaId` y `sucursalId` origen y destino (Artículo 23.1); `NovedadDespacho`, `SolicitudRetiro` y `JustificacionRetiro` llevan `empresaId`. `Factura` lleva `empresaId` y `sucursalId`; `NotaCredito` lleva `empresaId`, heredando la sucursal de su `Factura` original. `ArqueoManual` y `ExportacionReporte` llevan `empresaId` y `sucursalId` cuando el alcance se limita a una sede.

**Eventos que emite:** `Despachado`, `NovedadDespachoRegistrada` (F5); `RetiroSolicitado`, `RetiroJustificado` (F6); `FacturaCreada`, `MercanciaVendida`, `CapitalIngresado` (Venta) — los tres en unidad atómica (F7, Artículo 6.2); `NotaCreditoCreada`, `MercanciaDevuelta` (F9); `ArqueoRealizado` (F11); `ReporteExportado` (F12).

**Eventos de otros módulos que le afectan:** consume la existencia proyectada de `Consignacion` (Módulo 4) y de `InventarioEncargado`/`InventarioChofer` (Módulo 3) como origen del despacho y de la venta.

**Catálogos maestros que crea:** ninguno directamente (`Cliente` queda sin vincular a `Factura` — ver Observaciones).

**Catálogos maestros que consume:** `Producto`, `Vendedor` (creados en el Módulo 6 — Parámetros de Mantenimiento).

**Pendiente de modelar:** ninguna funcionalidad nueva de Oliver en este módulo carece de respaldo — hereda directamente lo ya modelado para F5, F6, F7, F9, F11 y F12, solo reagrupado bajo el nombre y alcance que Oliver le dio.

### Módulo 6 — Parámetros de Mantenimiento

**Funcionalidades:**
- *Creaciones Base*: Crear Suplidor (Proveedores y condiciones) / Crear Producto (Código, descripción, costos) / Condición de Pago (Plazos de crédito) / Crear Vendedor (Personal y rutas) / Crear Novedades (Inventario / Despacho) / Crear Consignación (Puntos o lotes nuevos)

**Alcance multiempresa:** `Producto`, `Vendedor` y `Proveedor` llevan `empresaId`, sin `sucursalId` — son catálogos maestros compartidos a nivel de empresa (Artículo 2.2 y 16.1).

**Eventos que emite:** `ProductoCreado` (F8). La creación de `Vendedor` y `Proveedor` no tiene, hoy, un evento formal dedicado en el catálogo de `12-GLOSARIO.md` (mismo tratamiento que ya tenía `Vendedor` antes de esta reconciliación).

**Eventos de otros módulos que le afectan:** ninguno.

**Catálogos maestros que crea:** `Producto`, `Vendedor`, `Proveedor`, `CondicionPago`, `MotivoSalidaSinCobro` (2026-07-17).

**Catálogos maestros que consume:** ninguno.

**Modelado:** Condición de Pago corresponde a la nueva entidad `CondicionPago` (`05-MODELO-DE-DATOS-MAESTRO.md`, sección 4) — código, nombre, plazo en días — que `Obligacion` referencia (Módulo 2) para calcular su fecha de vencimiento.

**Catálogo de Configuración Dinámica — Motivos de Salida sin Cobro (2026-07-17):** contenido nuevo, no una transcripción de Oliver. Se crea aquí, desde el Hub Admin, el catálogo `MotivoSalidaSinCobro` que consume el Módulo 3 en `BajaInventario.tipo`, sembrado con merma/pérdida/condonación/donación/bonificación/refrigerio. Es un catálogo **distinto** del pendiente "Crear Novedades" de abajo: ese pendiente habla de los tipos de `NovedadInventario`/`NovedadDespacho` (condiciones detectadas en el producto), no de los motivos de `BajaInventario` (por qué sale la mercancía) — ambos siguen documentados por separado.

**Pendiente de modelar:**
- **"Crear Novedades"** — sugiere que los tipos de `NovedadInventario`/`NovedadDespacho` podrían ser un catálogo configurable por la empresa, en lugar de la enumeración cerrada que son hoy en `11-DICCIONARIO-DE-DATOS.md`. Sigue sin resolver; no lo resuelve el `MotivoSalidaSinCobro` agregado arriba (ver nota).
- **"Crear Consignación" (puntos o lotes nuevos)** — sugiere una plantilla o tipo de consignación configurable, distinta de la `Consignacion` transaccional que ya se crea en el Módulo 4.
- **`Cliente`** no aparece en ninguna parte de la estructura de Oliver, ni en este módulo de creaciones base ni en ningún otro. Su vínculo con `Factura` (ventas a crédito) sigue sin resolverse (`DECISIONES-ARQUITECTURALES.md`, decisión "Agregadas las entidades Cliente, Proveedor y Obligacion").

### Módulo 7 — Asistente de Consulta

**Funcionalidades:**
- *Consulta en lenguaje natural*: Oliver (u otro rol autorizado) pregunta sobre el estado patrimonial actual o histórico del negocio (ej. "¿qué tenemos en el banco?", "¿cómo está el saldo del cuarto frío?", "¿cuánto le debemos al proveedor X?") y recibe una respuesta basada exclusivamente en las proyecciones y el historial de eventos ya calculados por el Motor de Flujos Patrimoniales (`04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`).
- *Explicación de origen*: para cualquier saldo o cifra reportada, el asistente puede mostrar la cadena de eventos que llevó a ese estado (consistente con el Artículo 7.2 de la Constitución).
- *Sugerencias asistivas*: el asistente puede sugerir clasificaciones, señalar anomalías o proponer acciones, pero nunca las ejecuta por sí mismo.

**Naturaleza del módulo:**

Este módulo es de solo lectura sobre el estado patrimonial. No emite eventos que modifiquen capital, mercancía o información. Es una capa de interpretación y lenguaje natural sobre las proyecciones que ya existen — nunca una fuente de verdad paralela (Artículo 3.2 de la Constitución).

Su relación con el Motor de Flujos Patrimoniales es unidireccional: el motor calcula el estado; el asistente lo lee y lo explica. El asistente no valida eventos, no los aplica, y no tiene autoridad para alterar ninguna proyección ni el historial inmutable.

**Alcance multiempresa:** toda consulta se resuelve exclusivamente dentro de la `empresaId` (y `sucursalId`, cuando aplica) del usuario que pregunta, evaluado por el Motor de Permisos (Artículo 13 de la Constitución). El asistente nunca infiere ni expone datos de una empresa distinta a la del usuario, ni siquiera con fines de análisis (Artículo 26.3).

**Eventos que emite:** ninguno. Este módulo no participa en la escritura de estado patrimonial (Artículo 6, Artículo 26.4).

**Eventos de otros módulos que le afectan:** consume las proyecciones resultantes de todos los eventos de los Módulos 1 al 6, vía el Motor de Flujos Patrimoniales — no accede a ningún módulo directamente ni mantiene copia propia del estado (Artículo 4.1).

**Catálogos maestros que crea:** ninguno.

**Catálogos maestros que consume:** todos los necesarios para resolver una consulta (`Producto`, `Cuenta`, `CuentaBancaria`, `Fondo`, `Vendedor`, `Cliente`, `Proveedor`, etc.), siempre en modo lectura.

**Reglas de gobernanza aplicables (Artículo 26 de la Constitución):**
- **26.1** — Este módulo, como cualquier funcionalidad de IA del sistema, está sujeto a las mismas reglas de la Constitución que un desarrollador humano.
- **26.3** — No infiere ni expone datos entre empresas distintas.
- **26.4** — Toda sugerencia es asistiva: no puede aprobar, cerrar ni hacer inmutable ningún documento sin acción humana explícita.
- **30.5** — Toda respuesta o sugerencia que involucre una decisión automática deja huella de la regla o modelo que la originó.

**Pendiente de modelar:**
- Definir si las consultas del asistente quedan registradas en el historial de auditoría (Artículo 8) como evento de solo-lectura, o si se consideran fuera del alcance de auditoría por no modificar estado. Requiere decisión explícita antes de implementación (Artículo 29.1).
- Definir el canal de interacción (chat dentro del Hub, comando de voz, u otro) — no es una decisión de arquitectura patrimonial, pero debe quedar resuelta antes de aprobar el módulo para desarrollo.

## Relación con Otros Documentos

- `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md` — la fuente de verdad de la que se deriva esta reconciliación.
- `02-CONSTITUCION-ERP.md` (Artículo 29.2) — la regla que exige esta declaración por módulo.
- `05-MODELO-DE-DATOS-MAESTRO.md` — las entidades y catálogos maestros de cada módulo.
- `07-FLUJOS-DE-NEGOCIO.md` — los flujos F1-F13 de donde provienen los eventos declarados aquí.
- `12-GLOSARIO.md` (sección C) — el catálogo formal de eventos del sistema.
- `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` — el orden de implementación de estos módulos.

Observaciones:

Esta versión reconcilia el catálogo con `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md`: pasa de 5 a 6 módulos, con los nombres y agrupaciones exactos que Oliver definió. Las funcionalidades de cada módulo se transcriben fielmente de esa fuente; la interpretación de a qué entidad ya existente corresponde cada campo (declaraciones de Alcance/Eventos/Catálogos) es una lectura razonada de esta reconciliación, no un hecho literal del texto de Oliver — en particular, la distinción entre la "Factura" del Módulo 2 (documento del Suplidor, Cuentas por Pagar) y la "Facturación" del Módulo 5 (venta de Polar Breeze a sus clientes) fue confirmada explícitamente por el usuario durante la planificación de esta reconciliación, no inferida unilateralmente.

De los 7 conceptos de Oliver que quedaron sin respaldo formal tras esa reconciliación, 6 ya están modelados: NCF, ITBIS y Fecha de Factura (campos de `Obligacion`), Condición de Pago (`CondicionPago`), Rutas y Vías (`Ruta`), Reportes R1/R2/R3 (proyecciones documentadas, sin entidad propia) y Refrigerios/Bonificaciones/Donaciones (ampliación de `BajaInventario.tipo`) — ver `DECISIONES-ARQUITECTURALES.md`, decisión "Modelados 6 de los 7 conceptos pendientes de Oliver". Quedan sin modelar, deliberadamente: **Participación de Capital** (Módulo 1, demasiado ambigua sin más contexto de Oliver, por decisión del usuario), la nota de crédito de proveedor (Módulo 2), el catálogo configurable de tipos de Novedad y la plantilla de Consignación (Módulo 6), y el vínculo de `Cliente` con `Factura` para ventas a crédito.

**Catálogos de Configuración Dinámica — Hub Admin (2026-07-17):** se agregan cuatro catálogos administrables desde el Hub Admin: Cuentas Bancarias (`CuentaBancaria`, ya existía, gana `alias`), Capital de la Empresa (`AporteCapital`, nueva, informativa), Cuentas Contables (`Cuenta`, deja de estar restringida a 6 valores fijos) y Motivos de Salida sin Cobro (`MotivoSalidaSinCobro`, nueva, reemplaza el enum cerrado de `BajaInventario.tipo`). Todos comparten el patrón Agregar/Editar/Desactivar, acceso exclusivo del rol Administrador/Oliver, y viven en Firestore bajo `config/{empresaId}/<colección>/{código}` — ver "Gobernanza de Catálogos de Configuración Dinámica" arriba y `DECISIONES-ARQUITECTURALES.md`.
