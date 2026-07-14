# Catálogo de Módulos

Estado:

> Vigente — pendiente de revisión y aprobación formal

Objetivo:

Documentar el catálogo completo de módulos del ERP Polar Breeze, organizados por área funcional, y declarar para cada uno lo exigido por el Artículo 29.2 de `02-CONSTITUCION-ERP.md`: su alcance por `empresaId`/`sucursalId` (Artículo 2.8), los eventos que emite o de los que depende (Artículo 15.3), y los catálogos maestros que crea o consume (Artículo 16.1). Ningún módulo se aprueba para desarrollo sin esta declaración completa (Artículo 29.1).

Contenido:

## Cómo leer este catálogo

Cada módulo se describe con la misma estructura, exigida por el Artículo 29.2 de la Constitución:

- **Funcionalidades** — qué hace el módulo, en el lenguaje original de este catálogo.
- **Alcance multiempresa** — cómo respeta `empresaId` y, cuando aplica, `sucursalId` (Artículo 2.8).
- **Eventos que emite** — los eventos del catálogo formal (`12-GLOSARIO.md`, sección C) que este módulo genera, y el flujo de negocio (`07-FLUJOS-DE-NEGOCIO.md`) del que provienen.
- **Eventos de otros módulos que le afectan** — eventos que este módulo no emite, pero cuyas proyecciones consume o de los que depende para operar.
- **Catálogos maestros** — cuáles crea y cuáles consume, de los siete definidos en `05-MODELO-DE-DATOS-MAESTRO.md`, sección 4 (Artículo 16.1): `Producto`, `Cuenta`, `CuentaBancaria`, `Fondo`, `Vendedor`, `Cliente`, `Proveedor`.

### Módulo 1 — Flujo de Efectivo

**Funcionalidades:**
- Agregar capital
- Clasificar en Costo / Venta / Distribución / Mantenimiento
- Gestionar Cuentas 1-6
- Crear cuenta bancaria con número y banco

**Alcance multiempresa:** Todas sus entidades (`MovimientoCapital`, `Obligacion`) y los catálogos que administra (`Cuenta`, `CuentaBancaria`, `Fondo`) llevan `empresaId` obligatorio (Artículo 2.2). Ninguna opera a nivel de `sucursalId` en el modelo actual — el efectivo se gestiona a nivel de empresa, no de sede (`05-MODELO-DE-DATOS-MAESTRO.md`, secciones 4 y 5).

**Eventos que emite:** `CapitalIngresado` (F1 — ingreso directo de capital: aporte, cobro, financiamiento), `ObligacionRegistrada` (F2 — obligación de pago a un proveedor, en unidad atómica con `MercanciaRecibida` del Módulo 2), `PagoRegistrado` (F10 — pago aplicado a una obligación existente).

**Eventos de otros módulos que le afectan:** `CapitalIngresado` también lo emite el Módulo 4 — Facturación (F7, ingreso de capital por venta), actualizando el mismo `Fondo` Venta que este módulo administra. `ArqueoRealizado` (Módulo 5) concilia el saldo de `Fondo`/`Cuenta` que este módulo proyecta, sin sobrescribirlo (Artículo 25.3).

**Catálogos maestros que crea:** `Cuenta`, `CuentaBancaria`, `Fondo`, `Proveedor`.

**Catálogos maestros que consume:** `Proveedor` (contraparte de `Obligacion`, el mismo que crea).

### Módulo 2 — Inventario y Almacén

**Funcionalidades:**
- Productos con cantidad / precio / total
- Novedades del cuarto frío
- Registro de dañados / rotos / en mal estado
- Sobrantes y faltantes

**Alcance multiempresa:** `InventarioChofer`, `InventarioEncargado` y `NovedadInventario` llevan `empresaId` y `sucursalId` obligatorios: cuarto frío, vehículo del chofer y punto de almacén son unidades `sucursalId` (Artículos 2.3 y 22.1). El Inventario del Chofer y el Inventario del Encargado son procesos independientes, nunca fusionados automáticamente (Artículo 17.1).

**Eventos que emite:** `MercanciaRecibida` (F2, en unidad atómica con `ObligacionRegistrada` del Módulo 1), `MercanciaTransferida` (F3), `NovedadInventarioRegistrada` (F3), `ConciliacionInventarioRealizada` (F3), `NovedadCuartoFrioRegistrada` (F4), `BajaInventarioRegistrada` (F13 — merma, pérdida o condonación; también aplica a mercancía de `Consignacion` del Módulo 3).

**Eventos de otros módulos que le afectan:** `MercanciaVendida` (Módulo 4, F7) y `MercanciaDevuelta` (Módulo 4, F9) modifican las existencias de `InventarioChofer`/`InventarioEncargado` que este módulo proyecta, sin que este módulo los emita. `Despachado` (Módulo 3, F5) retira mercancía del inventario de origen que este módulo mantiene.

**Catálogos maestros que crea:** ninguno.

**Catálogos maestros que consume:** `Producto` (creado por el Módulo 4 — Facturación).

### Módulo 3 — Despacho y Consignaciones

**Funcionalidades:**
- Crear consignación
- Novedades de despacho
- Dañado en despacho
- Solicitud de retiro
- Justificar retiro
- Sobrantes de despacho

**Alcance multiempresa:** `Consignacion`, `Despacho`, `NovedadDespacho`, `SolicitudRetiro` y `JustificacionRetiro` llevan `empresaId` obligatorio; `Consignacion` y `Despacho` llevan además `sucursalId` (origen y, en el caso del despacho, también destino, ambos dentro de la misma `empresaId` — Artículo 23.1).

**Eventos que emite:** `ConsignacionCreada`, `Despachado`, `NovedadDespachoRegistrada` (F5); `RetiroSolicitado`, `RetiroJustificado` (F6).

**Eventos de otros módulos que le afectan:** consume la existencia proyectada de `InventarioEncargado`/`InventarioChofer` (Módulo 2) como origen de la mercancía a consignar o despachar; no emite eventos de inventario él mismo — es el `Despachado` que este módulo emite quien retira esa mercancía del origen. `BajaInventarioRegistrada` (Módulo 2, F13) puede además reducir la existencia de una `Consignacion` cuando su mercancía sufre merma, pérdida o condonación.

**Catálogos maestros que crea:** ninguno.

**Catálogos maestros que consume:** `Producto`.

### Módulo 4 — Facturación

**Funcionalidades:**
- Crear factura
- Nota de crédito
- Código de producto
- Crear producto nuevo
- Crear vendedor

**Alcance multiempresa:** `Factura` lleva `empresaId` y `sucursalId` obligatorios (el punto de venta o despacho de origen); `NotaCredito` lleva `empresaId` obligatorio, heredando la sucursal de su `Factura` original. `Producto`, `Vendedor` y `Cliente` llevan `empresaId`, sin `sucursalId` — son catálogos maestros compartidos a nivel de empresa.

**Eventos que emite:** `FacturaCreada`, `MercanciaVendida`, `CapitalIngresado` (Venta) — los tres en unidad atómica (F7, Artículo 6.2 de la Constitución); `ProductoCreado` (F8); `NotaCreditoCreada`, `MercanciaDevuelta` (F9, cuando corresponde devolución física).

**Eventos de otros módulos que le afectan:** ninguno — es el módulo que más eventos propios emite de forma atómica hacia los otros dos flujos (capital e inventario), pero no depende de un evento emitido por el Módulo 1, 2 o 3 para poder operar.

**Catálogos maestros que crea:** `Producto`, `Vendedor`, `Cliente`.

**Catálogos maestros que consume:** `Producto`, `Vendedor`, `Cliente` (los que crea). El consumo de `Cuenta` (Cuenta 3 — Cuentas por Cobrar) para ventas a crédito queda pendiente de confirmación de negocio (`DECISIONES-ARQUITECTURALES.md`, decisión "Agregadas las entidades Cliente, Proveedor y Obligacion").

### Módulo 5 — Reportes

**Funcionalidades:**
- Exportar reportes
- Arqueo manual

**Alcance multiempresa:** `ArqueoManual` y `ExportacionReporte` llevan `empresaId` obligatorio y `sucursalId` cuando el alcance del reporte o arqueo se limita a una sede. Toda exportación declara explícitamente su alcance y nunca mezcla datos de más de una empresa salvo permiso explícito multiempresa (Artículo 24.1).

**Eventos que emite:** `ArqueoRealizado` (F11), `ReporteExportado` (F12).

**Eventos de otros módulos que le afectan:** es un **consumidor de solo lectura** (Artículo 24.2 de la Constitución; `03-ARQUITECTURA-GENERAL.md`, sección 12) del historial completo de eventos y de las proyecciones de los cuatro módulos anteriores. No emite eventos que otro módulo consuma, ni depende de un evento puntual para operar — lee el historial acumulado cada vez que se le invoca.

**Catálogos maestros que crea:** ninguno.

**Catálogos maestros que consume:** los siete catálogos compartidos (`Producto`, `Cuenta`, `CuentaBancaria`, `Fondo`, `Vendedor`, `Cliente`, `Proveedor`), en la medida en que un reporte o arqueo específico los requiera.

## Relación con Otros Documentos

- `02-CONSTITUCION-ERP.md` (Artículo 29.2) — la regla que exige esta declaración por módulo.
- `05-MODELO-DE-DATOS-MAESTRO.md` — las entidades y catálogos maestros de cada módulo.
- `07-FLUJOS-DE-NEGOCIO.md` — los flujos F1-F12 de donde provienen los eventos declarados aquí.
- `12-GLOSARIO.md` (sección C) — el catálogo formal de los eventos del sistema.
- `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` — el orden de implementación de estos módulos.

Observaciones:

Esta versión eleva el catálogo al estándar mínimo exigido por el Artículo 29.2 de la Constitución para que un módulo pueda aprobarse para desarrollo. Las funcionalidades originales (viñetas) se mantienen sin cambios; lo agregado es la declaración de alcance, eventos y catálogos de cada módulo, derivada de lo ya establecido en `05-MODELO-DE-DATOS-MAESTRO.md`, `07-FLUJOS-DE-NEGOCIO.md` y `12-GLOSARIO.md` — no introduce funcionalidad de negocio nueva.
