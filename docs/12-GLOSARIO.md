# Glosario

Estado:

> Vigente — Aprobado por el Arquitecto/Product Owner del ERP el 2026-07-17 (baseline v0.41; ver Registro de Aprobaciones en `13-HISTORIAL-DE-VERSIONES.md`)

Objetivo:

Consolidar en un único lugar la terminología usada en toda la documentación del ERP Polar Breeze, y fijar el **catálogo formal de eventos del sistema** exigido por el Artículo 15 de `02-CONSTITUCION-ERP.md`. Los nombres de evento que aparecían como propuesta en `07-FLUJOS-DE-NEGOCIO.md` quedan formalizados aquí; cualquier evento nuevo debe agregarse primero a este documento antes de usarse en desarrollo (Artículo 15.3).

Contenido:

## A. Términos de Arquitectura

**Auditoría** — Registro independiente y de solo lectura de toda creación, modificación, aprobación, anulación o soft delete (Artículo 8 de la Constitución). Ver `RegistroAuditoria` en `11-DICCIONARIO-DE-DATOS.md`.

**Código** — Identificador de negocio único y estable de una entidad, usado como clave funcional en lugar del nombre libre (Principio 1 de `00-PRINCIPIOS-DEL-ERP.md`).

**Conflicto de sincronización** — Situación en que un evento capturado offline, válido en el momento de su captura, deja de serlo al sincronizarse por un cambio de estado ocurrido mientras tanto. Se registra como `ConflictoSincronizacion` y requiere resolución humana explícita mediante un evento nuevo, nunca automática ni editando el evento original (`04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`, sección 13; Artículo 26.4 de la Constitución).

**Constitución** — `02-CONSTITUCION-ERP.md`; el documento de reglas inquebrantables que ningún módulo puede violar.

**Empresa** — La unidad organizacional raíz del modelo multiempresa. Polar Breeze es la primera empresa del ecosistema, no el límite de su diseño.

**`empresaId`** — Campo obligatorio en toda entidad de negocio que la asocia a una `Empresa` y garantiza su aislamiento (Artículo 2.2 de la Constitución).

**Evento** — Hecho de negocio inmutable, con origen, momento, usuario y payload identificables, que el Motor de Flujos Patrimoniales aplica y persiste (Artículo 5 de la Constitución). Ver catálogo formal en la sección C.

**Evento compensatorio** — Un evento nuevo que corrige el efecto de un evento anterior sin editarlo ni eliminarlo, referenciándolo explícitamente (Artículo 5.4 y 14.2 de la Constitución).

**Flujo de Capital** — Uno de los tres flujos patrimoniales: el movimiento de efectivo y obligaciones financieras (`01-VISION-ERP.md`, sección 6).

**Flujo de Información** — Uno de los tres flujos patrimoniales: el movimiento de documentos, decisiones y aprobaciones que respaldan a los otros dos flujos.

**Flujo de Mercancía** — Uno de los tres flujos patrimoniales: el movimiento físico del inventario.

**Fondo** — Agrupación patrimonial de capital por propósito: Costo, Venta, Distribución o Mantenimiento (`01-VISION-ERP.md`, sección 7).

**Fuente única de verdad** — Principio por el cual cada dato tiene un único origen autoritativo; todo lo demás lo lee o lo proyecta, nunca lo copia editable (Artículo 3 de la Constitución).

**Inmutabilidad** — Propiedad de un documento aprobado o cerrado (factura, consignación cerrada, arqueo) de no poder editarse; toda corrección es un documento o evento nuevo (Artículo 14).

**Motor de Flujos Patrimoniales** — El componente central que recibe, valida, aplica y persiste todos los eventos del sistema (`04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`).

**Motor de Permisos** — El componente transversal que evalúa de forma centralizada toda acción sensible por combinación de `empresaId` + rol + acción + entidad (Artículo 13 de la Constitución).

**Multiempresa** — Principio arquitectónico por el cual el sistema opera múltiples empresas aisladas entre sí desde su diseño original, no como una adaptación posterior (Artículo 2 de la Constitución).

**Offline-first** — Principio por el cual toda operación crítica se captura primero en local y sincroniza automáticamente, sin depender de conectividad instantánea (Principio 2 de `00-PRINCIPIOS-DEL-ERP.md`).

**Proyección** — Vista de estado (saldo, existencia) calculada a partir de aplicar el historial de eventos; nunca un valor editado directamente (`04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`, sección 7).

**Soft delete** — Eliminación lógica: el registro se marca inactivo y se conserva íntegro, nunca se borra físicamente (Artículo 9 de la Constitución).

**`sucursalId`** — Campo que asocia una entidad a una sede, cuarto frío o punto de despacho específico dentro de una empresa (Artículo 2.3 de la Constitución).

**Versionado** — Práctica de mantener versión explícita de reglas y catálogos, de forma que eventos históricos se interpreten siempre con la versión vigente al momento de su captura (Artículo 11 de la Constitución).

## B. Términos de Negocio

**Arqueo manual** — Proceso de conciliación entre el saldo/existencia proyectado por el sistema y un conteo físico real, registrando cualquier diferencia como evento propio (Artículo 25.3 de la Constitución).

**Baja de inventario** — Salida definitiva de mercancía del inventario vendible por merma, pérdida, condonación, donación, bonificación o refrigerio, registrada como `BajaInventario` (Artículo 6.3 de la Constitución: ninguna mercancía desaparece sin un evento explícito que documente su destino).

**Bonificación** — Tipo de `BajaInventario` en que se entrega producto como incentivo promocional, sin que medie una obligación de pago o una deuda que perdonar.

**Chofer** — Actor operativo responsable del `InventarioChofer`, la mercancía en tránsito en su vehículo.

**Comprobante Fiscal (NCF)** — Número de Comprobante Fiscal que el Suplidor emite junto a su factura; campo `comprobanteFiscal` de `Obligacion` (Módulo 2 — CXP, Facturación y Reportes).

**Condición de Pago** — Catálogo reutilizable de plazos de crédito (ej. "Contado", "30 días"); toda `Obligacion` referencia una `CondicionPago` para calcular su fecha de vencimiento.

**Condonación** — Tipo de `BajaInventario` en que la empresa decide, por decisión administrativa, dar de baja mercancía sin que medie necesariamente una novedad detectada previamente.

**Consignación** — Documento y proceso por el cual mercancía se entrega a un responsable en un destino, sujeta a cierre y a las reglas de retiro/justificación (Artículo 21 de la Constitución).

**Cuarto frío** — Unidad operativa de almacenamiento refrigerado, tratada como una `Sucursal` propia dentro del flujo de mercancía (Artículo 22.1).

**Despacho** — Movimiento de mercancía de un origen a un destino identificables, dentro de la misma empresa (Artículo 23 de la Constitución).

**Donación** — Tipo de `BajaInventario` en que se regala producto activamente, sin deuda previa que perdonar — distinta de la `Condonación`.

**Encargado** — Actor operativo responsable del `InventarioEncargado`, la mercancía en almacén o punto fijo.

**ITBIS** — Impuesto (equivalente al IVA) aplicado sobre el costo de una factura del Suplidor; campo `montoITBIS` de `Obligacion` (Módulo 2), distinto del `montoCosto` y del `montoOriginal` (el total: costo + ITBIS).

**Justificación de retiro** — Documento que respalda una `Solicitud de retiro`; sin ella, el retiro no es válido (Artículo 21.3).

**Merma** — Tipo de `BajaInventario` originado en un deterioro operativo del producto (dañado, roto, en mal estado, rotura de cadena de frío) que impide reintegrarlo al inventario vendible.

**Novedad** — Registro de una condición anómala (dañado, roto, en mal estado, sobrante, faltante, rotura de cadena de frío) detectada en inventario o despacho, vinculada al proceso donde ocurrió (Artículo 17.2 de la Constitución). Distinta de una `Baja de inventario`: la novedad detecta la condición, la baja es la disposición patrimonial que puede seguirle.

**Nota de crédito** — Documento que corrige una factura aprobada sin editarla, referenciándola obligatoriamente (Artículo 14.2).

**Pérdida** — Tipo de `BajaInventario` originado en un extravío o siniestro confirmado, distinto del deterioro operativo de una merma.

**Refrigerio** — Tipo de `BajaInventario` en que se entrega producto para consumo del personal.

**Ruta** — Recorrido de distribución que agrupa consignaciones (ej. "Santiago"), con un presupuesto de inventario de referencia y puntos numerados individuales (Módulo 4 — Consignaciones).

**Solicitud de retiro** — Petición formal de retirar mercancía de una consignación o despacho, que requiere justificación asociada para considerarse válida.

## C. Catálogo Formal de Eventos del Sistema

Formaliza, según el Artículo 15 de la Constitución, los eventos propuestos en `07-FLUJOS-DE-NEGOCIO.md`. Todo evento incluye como mínimo `empresaId`, `sucursalId` (si aplica), entidad afectada, payload, usuario emisor y momento de captura (sección 3 de `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`).

| Evento | Flujo(s) | Entidad afectada | Flujo de negocio de origen |
|---|---|---|---|
| `CapitalIngresado` | Capital | `Fondo`, `Cuenta` | F1, F7 |
| `ObligacionRegistrada` | Capital | Cuenta por Pagar (Cuenta 4) | F2 |
| `MercanciaRecibida` | Mercancía | `InventarioEncargado` | F2 |
| `MercanciaTransferida` | Mercancía | `InventarioChofer`, `InventarioEncargado` | F3 |
| `NovedadInventarioRegistrada` | Mercancía | `NovedadInventario` | F3 |
| `ConciliacionInventarioRealizada` | Mercancía, Información | `InventarioChofer`, `InventarioEncargado` | F3 |
| `NovedadCuartoFrioRegistrada` | Mercancía | `NovedadInventario` | F4 |
| `ConsignacionCreada` | Mercancía, Información | `Consignacion` | F5 |
| `Despachado` | Mercancía | `Despacho` | F5 |
| `NovedadDespachoRegistrada` | Mercancía | `NovedadDespacho` | F5 |
| `RetiroSolicitado` | Información | `SolicitudRetiro` | F6 |
| `RetiroJustificado` | Información | `JustificacionRetiro` | F6 |
| `FacturaCreada` | Información | `Factura` | F7 |
| `MercanciaVendida` | Mercancía | Inventario de origen de la venta | F7 |
| `ProductoCreado` | Información | `Producto` | F8 |
| `NotaCreditoCreada` | Información | `NotaCredito` | F9 |
| `MercanciaDevuelta` | Mercancía | Inventario de destino de la devolución | F9 |
| `PagoRegistrado` | Capital | Cuenta por Pagar (Cuenta 4) | F10 |
| `ArqueoRealizado` | Capital / Mercancía, Información | `ArqueoManual` | F11 |
| `ReporteExportado` | Información | `ExportacionReporte` | F12 |
| `BajaInventarioRegistrada` | Mercancía, Información | `BajaInventario` | F13 |
| `ConflictoSincronizacionDetectado` | El mismo flujo del evento rechazado (Capital, Mercancía o Información) | `ConflictoSincronizacion` | No proviene de un flujo de negocio Fx: lo emite el propio Motor de Flujos Patrimoniales al sincronizar (`04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`, sección 13) |

## D. Relación con Otros Documentos

- `02-CONSTITUCION-ERP.md` (Artículo 15) — la regla que exige este catálogo formal.
- `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` — quien procesa estos eventos.
- `07-FLUJOS-DE-NEGOCIO.md` — el origen funcional de cada evento (columna "Flujo de negocio de origen").
- `11-DICCIONARIO-DE-DATOS.md` — el detalle de campos de las entidades que estos eventos afectan.
- `docs/diagramas/eventos.drawio` — representación visual pendiente de este mismo catálogo.

Observaciones:

Este catálogo cierra el estado "borrador de nomenclatura" que `07-FLUJOS-DE-NEGOCIO.md` dejaba pendiente en su sección de Observaciones. A partir de esta versión, estos son los nombres de evento oficiales; cualquier cambio de nombre debe registrarse como decisión en `DECISIONES-ARQUITECTURALES.md` (Artículo 14.3 de la Constitución) antes de aplicarse, no editarse silenciosamente aquí.
