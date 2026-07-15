# Catálogo de Módulos

Estado:

> Vigente — pendiente de revisión y aprobación formal

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

### Módulo 1 — Flujo de Efectivo y Bancos

**Funcionalidades:**
- *Flujo de Efectivo*: Venta / Costo / Distribución -> Agregar Registro
- *Cuentas*: Cuenta 1, Cuenta 2, Cuenta 3, Cuenta 4, Cuenta 5, Cuenta 6 -> Mantenimiento / Crear Cuenta
- *Bancos*: Banco / Número de Cuenta -> Participación de Capital

**Alcance multiempresa:** `MovimientoCapital` y los catálogos que administra (`Cuenta`, `CuentaBancaria`, `Fondo`) llevan `empresaId` obligatorio (Artículo 2.2). Ninguno opera a nivel de `sucursalId` en el modelo actual — el efectivo se gestiona a nivel de empresa, no de sede (`05-MODELO-DE-DATOS-MAESTRO.md`, secciones 4 y 5).

**Eventos que emite:** `CapitalIngresado` (F1 — ingreso directo de capital: aporte, cobro, financiamiento).

**Eventos de otros módulos que le afectan:** `CapitalIngresado` también lo emite el Módulo 5 — Despacho, Novedades y Caja (F7, ingreso de capital por venta), actualizando el mismo `Fondo` Venta que este módulo administra. `ArqueoRealizado` (Módulo 5) concilia el saldo de `Fondo`/`Cuenta` que este módulo proyecta, sin sobrescribirlo (Artículo 25.3).

**Catálogos maestros que crea:** `Cuenta`, `CuentaBancaria`, `Fondo`.

**Catálogos maestros que consume:** ninguno adicional.

**Pendiente de modelar:**
- **Participación de Capital** (Bancos) — no tiene entidad ni campo formal en `05`/`11` todavía.
- **Discrepancia con el Artículo 18.1 de la Constitución:** Oliver lista solo tres clasificaciones para el Flujo de Efectivo (Venta / Costo / Distribución), mientras el Artículo 18.1 y la entidad `Fondo` exigen cuatro (agregan Mantenimiento). Esta discrepancia **no se resuelve en este documento** — se transcribe la estructura de Oliver tal cual, y la Constitución conserva sus cuatro clasificaciones hasta que se confirme con el negocio si la cuarta (Mantenimiento) sigue vigente o Oliver la omitió deliberadamente.

### Módulo 2 — CXP, Facturación y Reportes

**Funcionalidades:**
- *Cuentas por Pagar*: Suplidor / Fecha de Factura / Número de Factura / Comprobante (NCF) / Código de Producto / Descripción / Costo / ITBIS / Total / Cantidad de Productos / Días de Crédito / Subir Imagen del Pago
- *Notas y Medios de Pago*: Generar Nota de Crédito / Seleccionar Factura y/o Nota de Crédito / Medio de Pago (Depósito, Efectivo, Transferencia) / Monto del Pago / Pedir Numeración de Transacción / Bandeja de Banco
- *Reportes Generados*: R1 (Fecha Factura - Comprobante - Costo - ITBIS - Total) / R2 (Fecha Factura - Comprobante - Total - Días Vencimiento) / R3 (Fecha Factura - Comprobantes - Medio de Pago con Imagen)

**Alcance multiempresa:** `Obligacion` lleva `empresaId` obligatorio, sin `sucursalId` (Artículo 2.2; `05-MODELO-DE-DATOS-MAESTRO.md`, sección 5).

**Eventos que emite:** `ObligacionRegistrada` (F2 — factura del Suplidor recibida, en unidad atómica con `MercanciaRecibida` del Módulo 3), `PagoRegistrado` (F10 — pago aplicado a una obligación existente, vía depósito/efectivo/transferencia).

**Eventos de otros módulos que le afectan:** ninguno directo.

**Catálogos maestros que consume:** `Proveedor` (creado en el Módulo 6 — Parámetros de Mantenimiento; es la contraparte de `Obligacion`), `Producto` (para las líneas de la factura del Suplidor).

**Pendiente de modelar:**
- **Comprobante (NCF)** y **`ITBIS`** — campos fiscales sin tipo conceptual ni campo formal en `Obligacion` todavía.
- **Días de Crédito / Condición de Pago** — Oliver la trata como un catálogo reutilizable creado aparte (ver Módulo 6); hoy `Obligacion` solo tiene `fechaVencimiento` como fecha puntual, no una `Condición de Pago` referenciable.
- **"Generar Nota de Crédito" de este módulo es la nota de crédito que el Suplidor emite hacia Polar Breeze** (reduce una `Obligacion`), un concepto **distinto** de la `NotaCredito` de venta ya modelada en `05` (que corrige una `Factura` de venta, Módulo 5). No existe todavía una entidad formal para esta nota de crédito de proveedor.
- **Reportes R1/R2/R3** — no existen como entidades o plantillas formales; `ExportacionReporte` (genérica) podría ser su base, pero sus columnas específicas no están modeladas.

### Módulo 3 — Inventario y Cuarto Frío

**Funcionalidades:**
- *Almacén Principal*: Inventario Buy In (Compra) — Código de Producto / Descripción / Cantidad / Costo / Total; Inventario For Sale (Venta) — Código de Producto / Descripción / Cantidad / Precio / Total
- *Novedades Cuarto Frío*: Sobrantes / Faltantes Cuarto Frío / Dañados / Consignado en Mal Estado / Refrigerios / Bonificaciones / Donaciones / Bandeja de Novedades Cuarto Frío (Reportes)

**Alcance multiempresa:** `InventarioChofer`, `InventarioEncargado`, `NovedadInventario` y `BajaInventario` llevan `empresaId` y `sucursalId` obligatorios: cuarto frío, vehículo del chofer y punto de almacén son unidades `sucursalId` (Artículos 2.3 y 22.1). El Inventario del Chofer y el Inventario del Encargado son procesos independientes, nunca fusionados automáticamente (Artículo 17.1).

**Eventos que emite:** `MercanciaRecibida` (F2, en unidad atómica con `ObligacionRegistrada` del Módulo 2), `MercanciaTransferida` (F3), `NovedadInventarioRegistrada` (F3), `ConciliacionInventarioRealizada` (F3), `NovedadCuartoFrioRegistrada` (F4), `BajaInventarioRegistrada` (F13 — merma, pérdida o condonación).

**Eventos de otros módulos que le afectan:** `MercanciaVendida` y `MercanciaDevuelta` (Módulo 5, F7/F9) modifican las existencias que este módulo proyecta, sin que este módulo los emita. `Despachado` (Módulo 5, F5) retira mercancía del inventario de origen que este módulo mantiene.

**Catálogos maestros que crea:** ninguno.

**Catálogos maestros que consume:** `Producto` (creado en el Módulo 6 — Parámetros de Mantenimiento).

**Pendiente de modelar:**
- **Refrigerios / Bonificaciones / Donaciones** — tipos de salida de mercancía mencionados por Oliver; se relacionan con `BajaInventario.tipo` (hoy solo `merma` / `pérdida` / `condonación` — "Donaciones" podría mapear a `condonación`, pero "Refrigerios" y "Bonificaciones" no tienen equivalente todavía).
- La distinción explícita **Buy In (compra) / For Sale (venta)** del Almacén Principal no está modelada como dos vistas separadas de `InventarioEncargado`; hoy es una sola proyección de existencias.

### Módulo 4 — Consignaciones

**Funcionalidades:**
- *Acciones*: Consignar (Generar) / Retirar Consignado / Histórico de Consignaciones / Visualizar Consignaciones
- *Rutas y Vías*: PPTO Inventario Santiago / Consignaciones Individuales (1 al 23) / Consignaciones Generadas / Retiradas / Todas
- *Filtros*: Vendedor / Producto / Estado

**Alcance multiempresa:** `Consignacion` lleva `empresaId` y `sucursalId` obligatorios (Artículo 21.1).

**Eventos que emite:** `ConsignacionCreada` (F5).

**Eventos de otros módulos que le afectan:** consume la existencia proyectada de `InventarioEncargado`/`InventarioChofer` (Módulo 3) como origen de la mercancía a consignar. `BajaInventarioRegistrada` (Módulo 3, F13) puede reducir la existencia de una `Consignacion` cuando su mercancía sufre merma, pérdida o condonación. `Despachado` (Módulo 5, F5) retira mercancía de una consignación al ejecutarse el despacho asociado.

**Catálogos maestros que crea:** ninguno.

**Catálogos maestros que consume:** `Producto`, `Vendedor` (para los filtros).

**Pendiente de modelar:**
- **Rutas y Vías** (PPTO Inventario Santiago, Consignaciones Individuales 1 al 23, agrupación Generadas/Retiradas/Todas) — sugiere una estructura de rutas de venta con hasta 23 puntos individuales; no existe como entidad ni como estructura de `sucursalId` en `05`/`11`.
- **Filtros por Estado** — `Consignacion.estadoConsignacion` ya existe (`activa`/`cerrada`); los filtros por Vendedor y Producto son de interfaz, no requieren campo nuevo.

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

**Catálogos maestros que crea:** `Producto`, `Vendedor`, `Proveedor`.

**Catálogos maestros que consume:** ninguno.

**Pendiente de modelar:**
- **Condición de Pago** (plazos de crédito) — Oliver la trata como una creación base reutilizable, distinta de `fechaVencimiento` puntual de `Obligacion`; no existe como catálogo en `05`/`11`.
- **"Crear Novedades"** — sugiere que los tipos de `NovedadInventario`/`NovedadDespacho` podrían ser un catálogo configurable por la empresa, en lugar de la enumeración cerrada que son hoy en `11-DICCIONARIO-DE-DATOS.md`.
- **"Crear Consignación" (puntos o lotes nuevos)** — sugiere una plantilla o tipo de consignación configurable, distinta de la `Consignacion` transaccional que ya se crea en el Módulo 4.
- **`Cliente`** no aparece en ninguna parte de la estructura de Oliver, ni en este módulo de creaciones base ni en ningún otro. Su vínculo con `Factura` (ventas a crédito) sigue sin resolverse (`DECISIONES-ARQUITECTURALES.md`, decisión "Agregadas las entidades Cliente, Proveedor y Obligacion").

## Relación con Otros Documentos

- `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md` — la fuente de verdad de la que se deriva esta reconciliación.
- `02-CONSTITUCION-ERP.md` (Artículo 29.2) — la regla que exige esta declaración por módulo.
- `05-MODELO-DE-DATOS-MAESTRO.md` — las entidades y catálogos maestros de cada módulo.
- `07-FLUJOS-DE-NEGOCIO.md` — los flujos F1-F13 de donde provienen los eventos declarados aquí.
- `12-GLOSARIO.md` (sección C) — el catálogo formal de eventos del sistema.
- `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` — el orden de implementación de estos módulos.

Observaciones:

Esta versión reconcilia el catálogo con `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md`: pasa de 5 a 6 módulos, con los nombres y agrupaciones exactos que Oliver definió. Las funcionalidades de cada módulo se transcriben fielmente de esa fuente; la interpretación de a qué entidad ya existente corresponde cada campo (declaraciones de Alcance/Eventos/Catálogos) es una lectura razonada de esta reconciliación, no un hecho literal del texto de Oliver — en particular, la distinción entre la "Factura" del Módulo 2 (documento del Suplidor, Cuentas por Pagar) y la "Facturación" del Módulo 5 (venta de Polar Breeze a sus clientes) fue confirmada explícitamente por el usuario durante la planificación de esta reconciliación, no inferida unilateralmente. Los conceptos de Oliver sin respaldo formal en el modelo (NCF, ITBIS, Condición de Pago, Participación de Capital, Rutas y Vías, Reportes R1/R2/R3, Refrigerios/Bonificaciones, nota de crédito de proveedor) quedan señalados en cada módulo como "Pendiente de modelar" — no se inventaron entidades ni eventos nuevos para ellos en esta reconciliación.
