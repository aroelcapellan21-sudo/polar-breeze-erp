# Flujos de Negocio

Estado:

> Vigente — Aprobado por Oliver (dueño de Polar Breeze) el 2026-07-17 (baseline v0.41; ver Registro de Aprobaciones en `13-HISTORIAL-DE-VERSIONES.md`)

Objetivo:

Describir, paso a paso, los procesos operativos concretos del ERP Polar Breeze — quién los ejecuta, qué precondiciones requieren, qué eventos generan y qué reglas aplican. Mientras `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` explica *cómo* el sistema procesa eventos y `05-MODELO-DE-DATOS-MAESTRO.md` define *qué entidades* existen, este documento explica *en qué orden y por quién* ocurren los procesos reales del negocio.

Cada flujo se describe con la misma estructura: Actores, Precondiciones, Pasos, Eventos generados y Reglas aplicables.

Contenido:

## F1 — Ingreso de Capital y Clasificación en Fondos

**Actores:** Administrador financiero.

**Precondiciones:** La `empresaId` y al menos un `Fondo` y una `Cuenta` existen.

**Pasos:**
1. Se registra el ingreso de capital (aporte, cobro, financiamiento).
2. Se clasifica obligatoriamente en uno de los cuatro fondos: Costo, Venta, Distribución o Mantenimiento.
3. Se asigna a una `Cuenta` (Caja General o Bancos) y, si aplica, a una `CuentaBancaria` específica.

**Eventos generados:** `CapitalIngresado`.

**Reglas aplicables:** Artículo 18.1 de la Constitución (clasificación obligatoria); `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` sección 2.

## F2 — Compra y Recepción de Mercancía

**Actores:** Administrador de compras, Encargado de almacén.

**Precondiciones:** El `Producto` a comprar existe en el catálogo (o se crea como parte del flujo — ver F8).

**Pasos:**
1. Se registra la obligación de pago al proveedor (evento de flujo de capital, clasificado como `Fondo` Costo).
2. Se registra el ingreso físico de mercancía al `InventarioEncargado` de la sucursal correspondiente.
3. Ambos eventos se aplican de forma atómica (`04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`, sección 6): la mercancía no ingresa sin su obligación de pago registrada, o viceversa.

**Eventos generados:** `ObligacionRegistrada` (Cuentas por Pagar), `MercanciaRecibida`.

**Reglas aplicables:** Artículo 20.1 de la Constitución; Artículo 6.3 (todo movimiento patrimonial debe balancear).

## F3 — Conciliación Inventario del Chofer / Inventario del Encargado

**Actores:** Chofer, Encargado.

**Precondiciones:** Existe un `InventarioEncargado` con existencias disponibles para cargar al vehículo.

**Pasos:**
1. El Encargado registra la salida de mercancía de su inventario hacia el vehículo del Chofer.
2. El Chofer registra la recepción en su propio `InventarioChofer` — un proceso independiente, no una copia automática del anterior (Artículo 17.1 de la Constitución).
3. Cualquier diferencia entre lo despachado por el Encargado y lo recibido por el Chofer se registra como `NovedadInventario` (sobrante o faltante), nunca se ajusta silenciosamente.
4. Al final de la jornada, se ejecuta una conciliación explícita entre ambos inventarios.

**Eventos generados:** `MercanciaTransferida`, `NovedadInventarioRegistrada` (si aplica), `ConciliacionInventarioRealizada`.

**Reglas aplicables:** Artículo 17 de la Constitución.

## F4 — Novedades de Cuarto Frío y Almacén

**Actores:** Encargado, responsable de cuarto frío.

**Precondiciones:** Existe inventario registrado en la `sucursalId` correspondiente al cuarto frío.

**Pasos:**
1. Se detecta una condición anómala (rotura de cadena de frío, producto dañado, roto, en mal estado, sobrante o faltante).
2. Se registra la novedad vinculada al `Producto`, la cantidad y el proceso de origen (`InventarioChofer` o `InventarioEncargado`).
3. La novedad queda disponible para revisión administrativa; no se reclasifica retroactivamente sin dejar evidencia del cambio (Artículo 17.2).

**Eventos generados:** `NovedadCuartoFrioRegistrada`.

**Reglas aplicables:** Artículo 17.2 y 22 de la Constitución.

## F5 — Creación y Despacho de Consignación

**Actores:** Encargado, Chofer, responsable de consignación.

**Precondiciones:** Existe mercancía disponible para consignar en el inventario de origen.

**Pasos:**
1. Se crea la `Consignacion`: código, responsable, contenido, `sucursalId`.
2. Se ejecuta el `Despacho` asociado, moviendo mercancía del origen al destino de consignación.
3. Cualquier novedad de despacho (dañado, sobrante) se registra vinculada al `Despacho`, nunca suelta.
4. La consignación permanece activa hasta su cierre formal, momento en el cual se vuelve inmutable (Artículo 14.1 de la Constitución).

**Eventos generados:** `ConsignacionCreada`, `Despachado`, `NovedadDespachoRegistrada` (si aplica).

**Reglas aplicables:** Artículo 21 y 23 de la Constitución.

## F6 — Solicitud y Justificación de Retiro

**Actores:** Responsable de consignación o despacho, Aprobador.

**Precondiciones:** Existe una `Consignacion` o `Despacho` activo del cual se solicita retirar mercancía.

**Pasos:**
1. Se crea la `SolicitudRetiro`, referenciando la consignación o despacho y el motivo solicitado.
2. Un aprobador registra la `JustificacionRetiro`, referenciando la solicitud original.
3. Sin justificación registrada, el retiro no se considera válido — no existe retiro sin justificación (Artículo 21.3 de la Constitución).

**Eventos generados:** `RetiroSolicitado`, `RetiroJustificado`.

**Reglas aplicables:** Artículo 21.3 de la Constitución.

## F7 — Facturación y Venta

**Actores:** Vendedor.

**Precondiciones:** El `Producto` y el `Vendedor` existen en el catálogo; hay existencia suficiente en el inventario de origen de la venta.

**Pasos:**
1. Se crea la `Factura`: código único (no reutilizable), `Vendedor`, líneas de producto, cantidades y precios.
2. La emisión de la factura genera de forma atómica: la salida de mercancía del inventario correspondiente y el ingreso de capital clasificado como `Fondo` Venta.
3. Al aprobarse, la `Factura` se vuelve inmutable (Artículo 14.1 de la Constitución).

**Eventos generados:** `FacturaCreada`, `MercanciaVendida`, `CapitalIngresado` (Venta).

**Reglas aplicables:** Artículo 6.2, 14.1 y 18 de la Constitución.

## F8 — Alta de Producto Nuevo

**Actores:** Administrador de catálogo, Vendedor.

**Precondiciones:** Ninguna, salvo pertenecer a una `empresaId` válida.

**Pasos:**
1. Se asigna un **código** único al producto dentro de la empresa (Principio 1; Artículo 1.2 de la Constitución) — nunca se identifica por nombre.
2. Se registra nombre, precio y datos asociados.
3. El producto queda disponible como catálogo maestro para todos los módulos (Inventario, Despacho, Facturación).

**Eventos generados:** `ProductoCreado`.

**Reglas aplicables:** Principio 1 y 6-7 de `00-PRINCIPIOS-DEL-ERP.md`; Artículo 16.1 de la Constitución.

## F9 — Nota de Crédito (Corrección de Factura)

**Actores:** Vendedor, Administrador.

**Precondiciones:** Existe una `Factura` aprobada que requiere corrección (devolución, error de facturación).

**Pasos:**
1. Se crea la `NotaCredito`, referenciando obligatoriamente a la `Factura` original — nunca se edita la factura original (Artículo 14.2 de la Constitución).
2. Se registra el motivo y el monto/cantidad afectados.
3. Si corresponde devolución física de mercancía, se genera el evento de ingreso a inventario correspondiente, vinculado a la nota de crédito.

**Eventos generados:** `NotaCreditoCreada`, `MercanciaDevuelta` (si aplica).

**Reglas aplicables:** Artículo 14.2 de la Constitución.

## F10 — Pago de Cuentas por Pagar

**Actores:** Administrador financiero.

**Precondiciones:** Existe una obligación (`ObligacionRegistrada`, F2) con saldo pendiente mayor a cero.

**Pasos:**
1. Se registra el pago, referenciando la obligación original.
2. El monto original de la obligación no se modifica; el saldo pendiente se recalcula como proyección (obligación menos suma de pagos).
3. La obligación se considera saldada cuando esa proyección llega a cero.

**Eventos generados:** `PagoRegistrado`.

**Reglas aplicables:** Artículo 20.2 de la Constitución; `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` sección 5.

## F11 — Arqueo Manual

**Actores:** Administrador financiero o de inventario.

**Precondiciones:** Existe un `Fondo`, `InventarioChofer` o `InventarioEncargado` con saldo/existencia proyectada por el sistema.

**Pasos:**
1. Se realiza un conteo físico real (efectivo o mercancía).
2. Se compara contra la proyección del sistema al momento del arqueo.
3. Toda diferencia se registra como evento propio — el saldo del sistema nunca se sobrescribe silenciosamente para cuadrar con el conteo físico.

**Eventos generados:** `ArqueoRealizado`.

**Reglas aplicables:** Artículo 25.3 de la Constitución; `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` sección 8.

## F12 — Exportación de Reportes

**Actores:** Cualquier rol con permiso de exportación.

**Precondiciones:** El usuario tiene permiso de exportación verificado por el Motor de Permisos (Artículo 13 de la Constitución).

**Pasos:**
1. Se define el alcance de la exportación: `empresaId`, `sucursalId` (si aplica), rango de fechas.
2. El sistema genera el reporte a partir de la fuente de verdad o una proyección declarada — nunca de una copia mantenida manualmente.
3. La exportación queda registrada (`ExportacionReporte`) con su alcance, versión de reglas usada y usuario.

**Eventos generados:** `ReporteExportado`.

**Reglas aplicables:** Artículo 24 y 25 de la Constitución.

## F13 — Baja de Mercancía por Merma, Pérdida o Condonación

**Actores:** Encargado, Chofer, Administrador de inventario (quien autoriza la baja).

**Precondiciones:** Existe una `NovedadInventario` o `NovedadDespacho` que motiva la baja (dañado, roto, mal estado, rotura de cadena de frío, faltante), o existe una decisión administrativa de condonar mercancía sin novedad previa.

**Pasos:**
1. Se determina que la mercancía afectada no puede reintegrarse al inventario vendible: deterioro irreversible, extravío confirmado, o decisión de condonarla.
2. Se registra la `BajaInventario`: producto, cantidad, tipo (merma / pérdida / condonación / donación / bonificación / refrigerio), novedad de origen (si aplica), inventario o consignación de origen, motivo y usuario que autoriza.
3. El motor reduce la existencia del `Producto` en el inventario o consignación de origen de forma atómica con el registro de la baja — la mercancía nunca "desaparece" sin este evento explícito (Artículo 6.3 de la Constitución).
4. El tratamiento de capital asociado a la baja (si genera o no un gasto/pérdida contable, y contra qué `Fondo`/`Cuenta`) queda pendiente de validación contable (`docs/anexos/01-PENDIENTE-VALIDACION-CONTABLE.md`, ítem 7). Mientras tanto, la baja se registra únicamente como evento de flujo de mercancía e información, sin generar un `MovimientoCapital`.

**Eventos generados:** `BajaInventarioRegistrada`.

**Reglas aplicables:** Artículo 6.3 de la Constitución (todo movimiento patrimonial debe balancear); Artículo 17.2 y 22.2 (novedades no se reclasifican retroactivamente sin dejar evidencia del cambio).

## Relación con Otros Documentos

- `02-CONSTITUCION-ERP.md` — las reglas inquebrantables que cada flujo respeta.
- `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` — cómo se procesan los eventos que cada flujo genera.
- `05-MODELO-DE-DATOS-MAESTRO.md` — las entidades que cada flujo crea o modifica.
- `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` — el detalle contable de los flujos F1, F2, F7, F9, F10 y F11.
- `08-CATALOGO-DE-MODULOS.md` — el módulo funcional al que pertenece cada flujo.
- `docs/diagramas/flujo-capital.drawio`, `flujo-mercancia.drawio`, `flujo-informacion.drawio` — representación visual de estos flujos (pendientes de diagramar).

Observaciones:

Los nombres de eventos usados en este documento (`CapitalIngresado`, `MercanciaRecibida`, etc.) son propuestos aquí por primera vez y deben consolidarse formalmente en el catálogo de `docs/diagramas/eventos.drawio` y `12-GLOSARIO.md` para evitar nombres divergentes entre documentos.
