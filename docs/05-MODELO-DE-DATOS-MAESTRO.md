# Modelo de Datos Maestro

Estado:

> Vigente — pendiente de revisión y aprobación formal

Objetivo:

Definir las entidades de datos del ERP Polar Breeze, sus campos conceptuales, sus relaciones y su particionado multiempresa. Este documento traduce a estructura de datos lo ya establecido en `02-CONSTITUCION-ERP.md` (Artículos 2, 10, 11, 16), `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` (historial de eventos y proyecciones) y `08-CATALOGO-DE-MODULOS.md` (los seis módulos funcionales).

Este documento describe **entidades y relaciones**, no esquemas de base de datos ni código. No define tipos de columna, índices ni tecnología de persistencia — esas decisiones viven en el repositorio de código y, si son arquitectónicamente relevantes, se registran en `DECISIONES-ARQUITECTURALES.md`.

Contenido:

## 1. Convenciones Generales

Toda entidad de este modelo respeta:

- **`empresaId`** — obligatorio en toda entidad, salvo el catálogo de Empresas y la Configuración de Plataforma (Artículo 2.2 de la Constitución).
- **`sucursalId`** — obligatorio en toda entidad que opera a nivel de sede física, cuarto frío, vehículo o punto de despacho (Artículo 2.3).
- **Código como clave de negocio** — nunca el nombre (Principio 1 de `00-PRINCIPIOS-DEL-ERP.md`). Un código, una vez asignado, no se reutiliza (Artículo 9.3 de la Constitución).
- **Soft delete** — ninguna entidad se elimina físicamente; se marca inactiva/anulada y se conserva (Artículo 9 de la Constitución).
- **Auditoría implícita** — toda entidad hereda los campos mínimos de trazabilidad: usuario creador, timestamp de creación, último evento aplicado (Artículo 7 y 8 de la Constitución).
- **Versión** — toda entidad configurable o normativa (catálogos de reglas, clasificaciones) lleva número de versión (Artículo 11 de la Constitución).
- **Moneda** — todo `Monto` se expresa en la moneda funcional de la `Empresa` a la que pertenece (campo `moneda` de `Empresa`, sección 2 de este documento). Ninguna entidad mezcla montos de dos monedas sin una conversión explícita y trazable.

## 2. Entidades de Plataforma (No Particionadas por Empresa)

### Empresa

La raíz del modelo multiempresa. Representa a cada empresa del ecosistema (Polar Breeze es la primera).

- Campos conceptuales: código de empresa, razón social, estado (activa/inactiva), fecha de alta, **moneda funcional** (la moneda base en la que se expresan todos los montos de la empresa).
- Relación: toda entidad del resto del modelo referencia a una `Empresa` a través de `empresaId`.
- Toda empresa opera en una única moneda funcional. Dos empresas del ecosistema pueden operar en monedas distintas entre sí (Artículo 28.1 de la Constitución — crecimiento a nuevas monedas), pero ningún `Monto` dentro de una misma `empresaId` se expresa en más de una moneda sin una conversión explícita y trazable; ese mecanismo de conversión, si el negocio llega a necesitarlo, es una decisión de arquitectura aparte, no asumida aquí.

### Usuario

Representa a una persona con acceso al sistema, potencialmente a más de una empresa.

- Campos conceptuales: código de usuario, nombre, credenciales de acceso, lista de membresías (empresa + rol por cada una).
- Relación: un `Usuario` tiene una o más membresías a `Empresa`, cada una con su propio `Rol` (Artículo 2.7 y 12.2 de la Constitución).

### Configuración de Plataforma (`config/*`)

Parámetros globales de la plataforma que no pertenecen a una empresa específica (por ejemplo, catálogo de países, versiones mínimas de app soportadas).

## 3. Entidades Particionadas por Empresa (Comunes a Todos los Módulos)

### Sucursal

Unidad operativa local dentro de una empresa: sede, punto de despacho o cuarto frío considerado como unidad propia (Artículo 22.1 de la Constitución).

- Campos conceptuales: código, nombre, tipo (sede / cuarto frío / punto de despacho), `empresaId`.

### Rol y Permiso

Definen el motor de permisos (Artículo 13 de la Constitución).

- **Rol**: código, nombre, `empresaId`, lista de permisos asociados.
- **Permiso**: combinación de acción + entidad, evaluado siempre junto a `empresaId` y, cuando aplica, `sucursalId` (Artículo 13.2).

### Evento (Historial Inmutable)

La entidad central del sistema: cada registro representa un evento aplicado por el Motor de Flujos Patrimoniales (`04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`, sección 3).

- Campos conceptuales: tipo de evento, `empresaId`, `sucursalId` (si aplica), entidad afectada (referencia por código), payload de negocio, usuario emisor, momento de captura, momento de persistencia.
- Regla: nunca se edita ni se elimina (Artículo 5.4 de la Constitución); toda corrección es un evento compensatorio nuevo que referencia al original.

### Registro de Auditoría

Independiente del propio registro de negocio que audita (Artículo 8 de la Constitución).

- Campos conceptuales: `empresaId`, `sucursalId` (si aplica), usuario, acción, entidad afectada, valores anteriores y nuevos, timestamp.
- Regla: de solo lectura para todos los roles, sin excepción.

### ConflictoSincronizacion

Registra un evento capturado offline que el Motor de Flujos Patrimoniales rechazó al sincronizarse, por haber dejado de ser válido debido a un cambio de estado ocurrido mientras estaba pendiente de sincronizar (`04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`, sección 13).

- Campos conceptuales: `empresaId`, `sucursalId` (si aplica), evento original rechazado (referencia), motivo del rechazo, estado (pendiente / resuelto — proyección: `resuelto` si y solo si existe un evento de resolución que la referencia, nunca un valor editado directamente, Artículo 5 de la Constitución), evento de resolución (referencia, presente solo cuando la proyección de estado es `resuelto`), usuario que resuelve, momento de detección.
- Toda resolución es un evento nuevo, nunca una edición del evento original rechazado ni de esta entidad (Artículo 5.4 y 14 de la Constitución); ninguna IA la resuelve de forma autónoma sin acción humana explícita (Artículo 26.4). Es, a diferencia del Registro de Auditoría (de solo lectura), un objeto de trabajo pendiente: su estado se recalcula de la existencia de un evento de resolución, no se marca manualmente.

## 4. Catálogos Maestros Compartidos

Consumidos por todos los módulos, nunca reimplementados por ninguno (Principio 6 y 7; Artículo 16.1 de la Constitución).

### Producto

- Campos conceptuales: **código** (clave de negocio), nombre, precio, `empresaId`.
- Se crea desde el Módulo 6 — Parámetros de Mantenimiento ("crear producto nuevo", "código de producto") y se consume desde el Módulo 3 — Inventario y Cuarto Frío, el Módulo 4 — Consignaciones, el Módulo 5 — Despacho, Novedades y Caja, y el Módulo 2 — CXP, Facturación y Reportes (líneas de la factura del Suplidor).

### Cuenta

Representa las Cuentas 1-6 del Módulo 1 — Flujo de Efectivo y Bancos.

- Campos conceptuales: código de cuenta (1 a 6), nombre/descripción, `empresaId`.
- Relación: toda `CuentaBancaria` y todo evento de capital referencia una `Cuenta`.

### CuentaBancaria

- Campos conceptuales: código, número de cuenta, banco, `Cuenta` asociada, `empresaId`.
- Se crea desde el Módulo 1 — Flujo de Efectivo y Bancos ("crear cuenta bancaria con número y banco").

### Fondo

Agrupación patrimonial de capital por propósito (`01-VISION-ERP.md`, sección 7): Costo, Venta, Distribución, Mantenimiento.

- Campos conceptuales: código, clasificación (Costo/Venta/Distribución/Mantenimiento), `empresaId`, saldo actual (proyección derivada del historial de eventos de capital).

### Vendedor

- Campos conceptuales: código, nombre, `empresaId`.
- Se crea desde el Módulo 6 — Parámetros de Mantenimiento ("crear vendedor").

### Cliente

- Campos conceptuales: código, nombre, `empresaId`.
- Catálogo maestro exigido explícitamente por el Artículo 16.1 de la Constitución ("productos, cuentas, vendedores, bancos, clientes, proveedores"). No aparece en ninguna parte de `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md` (fuente de verdad de módulos); se mantiene aquí por exigencia constitucional, sin un módulo de creación asignado todavía. Quedaría disponible para su consumo por el Módulo 5 — Despacho, Novedades y Caja (donde vive la facturación de venta) si el negocio confirma que existen ventas a crédito contra la Cuenta 3 — Cuentas por Cobrar (`06-REGLAS-CONTABLES-Y-FINANCIERAS.md`, sección 3).

### Proveedor

- Campos conceptuales: código, nombre, tipo (proveedor de mercancía / transportista / consignatario / otro), `empresaId`.
- Catálogo maestro exigido explícitamente por el Artículo 16.1 de la Constitución. Representa a todo tercero con quien la empresa puede contraer una obligación de pago (Artículo 20.1: "proveedor, transportista, consignatario"; Oliver lo llama "Suplidor"). Se crea desde el Módulo 6 — Parámetros de Mantenimiento ("Crear Suplidor"). Es la contraparte que referencia toda `Obligacion` (sección 6, Módulo 2).

### CondicionPago

Catálogo reutilizable de plazos de crédito (ej. "Contado", "30 días", "60 días"), consistente con la viñeta de Oliver "Condición de Pago (Plazos de crédito)" en el Módulo 6.

- Campos conceptuales: código, nombre, plazo en días, `empresaId`.
- Se crea desde el Módulo 6 — Parámetros de Mantenimiento. Se consume desde el Módulo 2 — CXP, Facturación y Reportes: toda `Obligacion` referencia una `CondicionPago` para calcular su fecha de vencimiento (sección 6).

## 5. Entidades del Módulo 1 — Flujo de Efectivo y Bancos

### MovimientoCapital

Proyección/registro de un evento de flujo de capital.

- Campos conceptuales: `empresaId`, `Fondo` (clasificación), `Cuenta` de origen/destino, monto, tipo (ingreso/egreso), obligación referenciada (opcional, cuando el movimiento es un pago aplicado a una `Obligacion` del Módulo 2), medio de pago (depósito / efectivo / transferencia — obligatorio solo si el movimiento es un pago), número de transacción (opcional), imagen del comprobante de pago (opcional), evento de origen.

## 6. Entidades del Módulo 2 — CXP, Facturación y Reportes

### Obligacion (Cuenta por Pagar)

Registra una obligación de pago con un tercero — la factura que el Suplidor emite a Polar Breeze (Artículo 20 de la Constitución), contabilizada contra la Cuenta 4 — Cuentas por Pagar (`06-REGLAS-CONTABLES-Y-FINANCIERAS.md`, sección 3).

- Campos conceptuales: `empresaId`, `Proveedor` referenciado (contraparte), `CondicionPago` referenciada (sección 4), fecha de la factura del Suplidor, fecha de vencimiento (calculada una vez, al registrar, como fecha de factura + plazo de la `CondicionPago` — no se recalcula si la condición cambia después), comprobante fiscal (NCF), monto de costo, monto de ITBIS, monto original (el total: costo + ITBIS), `Cuenta` asociada (Cuenta 4), saldo pendiente (proyección: monto original menos suma de pagos aplicados), evento de origen (`ObligacionRegistrada`).
- El monto original nunca se edita para reflejar pagos parciales (Artículo 20.2); un pago se registra como un `MovimientoCapital` (Módulo 1) que referencia esta `Obligacion`, y la obligación se considera saldada cuando la proyección de saldo pendiente llega a cero (`06-REGLAS-CONTABLES-Y-FINANCIERAS.md`, sección 5).
- Los reportes R1, R2 y R3 de Oliver ("Fecha Factura - Comprobante - Costo - ITBIS - Total", "Fecha Factura - Comprobante - Total - Días Vencimiento", "Fecha Factura - Comprobantes - Medio de Pago con Imagen") no son entidades nuevas: son proyecciones de solo lectura sobre los campos de `Obligacion` (R1, R2) y de `MovimientoCapital` (R3), exportables vía `ExportacionReporte` (Módulo 5) igual que cualquier otro reporte. "Días Vencimiento" de R2 se calcula al momento del reporte (hoy − fecha de vencimiento), no es un campo almacenado.
- La nota de crédito propia del proveedor (mencionada por Oliver junto a "Generar Nota de Crédito") sigue sin modelar — ver `08-CATALOGO-DE-MODULOS.md`, Módulo 2, "Pendiente de modelar".

## 7. Entidades del Módulo 3 — Inventario y Cuarto Frío

### InventarioChofer / InventarioEncargado

Dos entidades independientes, nunca fusionadas automáticamente (Artículo 17.1 de la Constitución).

- Campos conceptuales: `empresaId`, `sucursalId`, responsable, lista de existencias por `Producto` (cantidad, precio, total).

### NovedadInventario

Cubre novedades de inventario: dañados, rotos, en mal estado, sobrantes, faltantes, y roturas de cadena de frío.

- Campos conceptuales: `empresaId`, `sucursalId`, tipo de novedad — la **condición** detectada en el producto: dañado / roto / mal estado / sobrante / faltante / rotura de cadena de frío (Artículo 22.2) —, `Producto` referenciado, cantidad, proceso de origen (`InventarioChofer` o `InventarioEncargado` — Artículo 17.2), evento de origen.
- La **ubicación** de la novedad (si ocurrió en un cuarto frío, una sede u otro punto) no se codifica en el tipo de novedad: se determina por `sucursalId` (referenciando a una `Sucursal` de tipo cuarto frío, sede o punto de despacho — Artículo 22.1) y por el tipo de evento de origen (`NovedadCuartoFrioRegistrada` vs. `NovedadInventarioRegistrada` — `12-GLOSARIO.md`, sección C). Esto evita mezclar en un mismo valor una condición del producto (qué le pasó) con una ubicación (dónde pasó).

### BajaInventario

Registra la salida definitiva de mercancía del inventario vendible por merma, pérdida, condonación, donación, bonificación o refrigerio (Artículo 6.3 de la Constitución: todo movimiento patrimonial debe balancear, ninguna mercancía desaparece sin un evento explícito que documente su destino).

- Campos conceptuales: `empresaId`, `sucursalId`, `Producto` referenciado, cantidad, tipo (merma / pérdida / condonación / donación / bonificación / refrigerio), novedad de origen (referencia opcional a `NovedadInventario` o `NovedadDespacho` — no toda baja proviene de una novedad detectada; una condonación, donación, bonificación o refrigerio administrativo puede no tener novedad previa), inventario o consignación de origen (`InventarioChofer`, `InventarioEncargado` o `Consignacion`), motivo, usuario que autoriza, evento de origen.
- `condonación` (no cobrar/perdonar una deuda ya existente) es distinta de `donación` (regalar producto activamente, sin deuda previa) y de `bonificación` (producto entregado como incentivo promocional); `refrigerio` cubre la entrega de producto para consumo del personal. Las seis son formas de baja, no condiciones del producto (esas viven en `NovedadInventario.tipo`, sección 7).
- El tratamiento de capital de una baja (si genera un gasto/pérdida contable y contra qué `Fondo`/`Cuenta`) no está definido en este documento — queda pendiente de validación contable (`docs/anexos/01-PENDIENTE-VALIDACION-CONTABLE.md`, ítem 7, y `06-REGLAS-CONTABLES-Y-FINANCIERAS.md`). Mientras tanto, `BajaInventario` es únicamente un evento de flujo de mercancía e información; no genera un `MovimientoCapital`.

## 8. Entidades del Módulo 4 — Consignaciones

### Consignacion

- Campos conceptuales: código, `empresaId`, `sucursalId`, responsable, contenido (lista de `Producto` + cantidades), estado (activa / cerrada — inmutable al cerrarse, Artículo 14.1), `Ruta` referenciada (opcional), número de punto dentro de la ruta (opcional).
- Los filtros de Oliver por Vendedor, Producto y Estado no requieren campo nuevo: se resuelven combinando `Vendedor`, `Producto` y `estado` ya existentes en la interfaz de consulta.

### Ruta

Agrupa consignaciones a lo largo de un recorrido de distribución (Oliver: "Rutas y Vías" — ej. "PPTO Inventario Santiago", consignaciones individuales numeradas de 1 a 23).

- Campos conceptuales: código, nombre, presupuesto de inventario (monto o cantidad de referencia para la ruta), `empresaId`, `sucursalId` (opcional).
- Se crea desde el Módulo 4 — Consignaciones. Toda `Consignacion` puede referenciar una `Ruta` y su número de punto dentro de ella, opcionalmente.

## 9. Entidades del Módulo 5 — Despacho, Novedades y Caja

Este módulo agrupa, según la estructura de Oliver, tres funciones antes repartidas entre otros módulos: el despacho físico y sus novedades (antes junto a Consignaciones), la facturación de venta (antes su propio módulo), y el arqueo/exportación de reportes (antes un módulo "Reportes" independiente).

### Despacho

- Campos conceptuales: código, `empresaId`, `sucursalId` origen y destino, contenido, evento de origen.

### NovedadDespacho

Cubre novedades de despacho y dañado en despacho, sobrantes de despacho.

- Campos conceptuales: `empresaId`, `Despacho` referenciado, tipo (novedad / dañado / sobrante), `Producto`, cantidad.

### SolicitudRetiro / JustificacionRetiro

Dos entidades distintas y ambas obligatorias (Artículo 21.3 de la Constitución): no existe retiro sin justificación.

- **SolicitudRetiro**: código, `empresaId`, `Consignacion` o `Despacho` referenciado, motivo solicitado, usuario solicitante.
- **JustificacionRetiro**: referencia a la `SolicitudRetiro`, justificación, usuario que aprueba.

### Factura

Documento inmutable una vez aprobado (Artículo 14.1 de la Constitución). Es la factura de **venta** que Polar Breeze emite a sus clientes al despachar/cobrar — distinta de la `Obligacion` (Módulo 2), que es la factura que Polar Breeze **recibe** de un Suplidor.

- Campos conceptuales: código (número de factura, nunca reutilizable — Artículo 9.3), `empresaId`, `sucursalId`, `Vendedor`, líneas de `Producto` + cantidad + precio, total, estado (aprobada / anulada por nota de crédito).

### NotaCredito

Corrige una `Factura` de venta — distinta de la nota de crédito de proveedor mencionada en el Módulo 2 (`08-CATALOGO-DE-MODULOS.md`, "Pendiente de modelar"), que reduciría una `Obligacion` en lugar de una `Factura`.

- Campos conceptuales: código, `empresaId`, `Factura` original referenciada (obligatorio — Artículo 14.2), motivo, monto.

### ArqueoManual

Evento de conciliación entre el estado del sistema y un conteo físico (Artículo 25.3 de la Constitución).

- Campos conceptuales: código, `empresaId`, `sucursalId`, `Fondo` o `InventarioChofer`/`InventarioEncargado` conciliado, saldo/existencia del sistema al momento del arqueo, conteo físico, diferencia resultante, evento de origen.

### ExportacionReporte

Registro de una exportación (Artículo 24 de la Constitución), no una entidad de negocio en sí misma.

- Campos conceptuales: `empresaId` (y `sucursalId` si aplica), rango de fechas, versión de reglas usada, usuario, momento de exportación.

## 10. Integridad Referencial entre Entidades

Toda referencia entre entidades de este modelo respeta el Artículo 10 de la Constitución:

- Una `NotaCredito` siempre referencia una `Factura` existente de la misma `empresaId`.
- Una `NovedadDespacho` siempre referencia un `Despacho` existente.
- Una `JustificacionRetiro` siempre referencia una `SolicitudRetiro` existente.
- Un `MovimientoCapital` siempre referencia una `Cuenta` y un `Fondo` existentes.
- Una `Obligacion` siempre referencia un `Proveedor` y una `CondicionPago` existentes de la misma `empresaId`.
- Un `MovimientoCapital` que registra un pago siempre referencia una `Obligacion` existente de la misma `empresaId`.
- Una `Consignacion` que referencia una `Ruta` lo hace a una `Ruta` existente de la misma `empresaId`.
- Una `BajaInventario` siempre referencia un inventario o consignación de origen (`InventarioChofer`, `InventarioEncargado` o `Consignacion`) existente de la misma `empresaId`; cuando referencia una novedad de origen, esta debe existir (`NovedadInventario` o `NovedadDespacho`).
- Una `ConflictoSincronizacion` siempre referencia un `Evento` existente (el rechazado) de la misma `empresaId`; cuando tiene resolución, el evento de resolución también existe y es de la misma `empresaId`.
- Ninguna entidad referencia a otra de una `empresaId` distinta a la propia.

## 11. Relación con Otros Documentos

- `02-CONSTITUCION-ERP.md` — las reglas que este modelo está obligado a cumplir (particionado, integridad referencial, soft delete, versionado, auditoría).
- `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` — quien aplica los eventos que dan origen al historial y a las proyecciones descritas aquí.
- `08-CATALOGO-DE-MODULOS.md` — el origen funcional de cada entidad descrita por módulo.
- `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md` — la fuente de verdad de módulos con la que se reconcilió la agrupación de las secciones 5-9.
- `11-DICCIONARIO-DE-DATOS.md` — el detalle campo por campo de cada entidad aquí listada.
- `docs/diagramas/base-datos.drawio` — representación visual de este modelo (pendiente de diagramar).

Observaciones:

Este documento define entidades y relaciones a nivel conceptual. El detalle exhaustivo de cada campo (tipo de dato, obligatoriedad, valores permitidos) corresponde a `11-DICCIONARIO-DE-DATOS.md`, que debe mantenerse consistente con las entidades aquí listadas.

Las secciones 5-9 se reagruparon para reconciliarse con los 6 módulos de `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md`: `Obligacion` se movió del Módulo 1 al Módulo 2 (es la factura del Suplidor, no del Flujo de Efectivo general); `Despacho`, `NovedadDespacho`, `SolicitudRetiro`, `JustificacionRetiro`, `Factura` y `NotaCredito` se movieron al Módulo 5 (que combina despacho, facturación de venta y arqueo); `Consignacion` quedó sola en el Módulo 4. Ninguna entidad cambió sus campos por esta reconciliación — solo su agrupación por módulo.

El campo `moneda` de `Empresa` (sección 2) fija una única moneda funcional por empresa — el alcance mínimo necesario para que el ecosistema multiempresa no asuma una sola moneda global (Artículo 28.1 de la Constitución). Este modelo **no** cubre escenarios de multi-moneda dentro de una misma empresa (por ejemplo, una `CuentaBancaria` en una moneda distinta a la funcional, o conversión automática de tipo de cambio); si el negocio llega a necesitar eso, es una extensión futura que requiere su propia decisión en `DECISIONES-ARQUITECTURALES.md`, no una suposición de este documento.

Se modelaron 6 de los 7 conceptos que `08-CATALOGO-DE-MODULOS.md` marcaba como "Pendiente de modelar" tras la reconciliación con Oliver: NCF, ITBIS y fecha de factura (campos de `Obligacion`), Condición de Pago (entidad `CondicionPago`), Rutas y Vías (entidad `Ruta`), Reportes R1/R2/R3 (documentados como proyecciones, no entidades nuevas) y Refrigerios/Bonificaciones/Donaciones (ampliación del enum `BajaInventario.tipo`). **Participación de Capital** queda deliberadamente sin modelar — el usuario confirmó que la mención de Oliver ("Banco / Número de Cuenta -> Participación de Capital") es demasiado ambigua para diseñarla con confianza en esta pasada; sigue señalada en `08-CATALOGO-DE-MODULOS.md`, Módulo 1.
