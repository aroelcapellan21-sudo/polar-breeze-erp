# Diccionario de Datos

Estado:

> Vigente — pendiente de revisión y aprobación formal

Objetivo:

Detallar campo por campo cada entidad ya identificada en `05-MODELO-DE-DATOS-MAESTRO.md`: tipo de dato conceptual, obligatoriedad y reglas de valor. Este documento no define tipos de columna de base de datos ni sintaxis de esquema — usa tipos **conceptuales**, consistentes con la prohibición de código en este repositorio. La correspondencia con tipos técnicos concretos se decide en el repositorio de código.

Este diccionario debe mantenerse consistente con las entidades de `05-MODELO-DE-DATOS-MAESTRO.md`: ninguna entidad se detalla aquí sin existir allí, y ninguna entidad nueva se agrega allí sin detallarse aquí.

Contenido:

## 1. Tipos de Dato Conceptuales

| Tipo | Descripción |
|---|---|
| Código | Texto corto, identificador de negocio único dentro de su alcance (empresa). Nunca se reutiliza una vez retirado (Artículo 9.3 de la Constitución). |
| Texto corto | Cadena breve (nombres, descripciones cortas). |
| Texto largo | Cadena extensa (motivos, justificaciones, observaciones). |
| Número entero | Cantidad sin decimales (unidades de producto, versión). |
| Monto | Número decimal con precisión monetaria, expresado siempre en la moneda funcional de la `Empresa` a la que pertenece la entidad que lo contiene (campo `moneda` de `Empresa`, sección 3). |
| Fecha/Hora | Instante o fecha calendario. |
| Booleano | Verdadero/falso. |
| Enumeración | Valor limitado a una lista cerrada predefinida. |
| Referencia | Apunta a otra entidad por su **código**, nunca por nombre (Principio 1). |
| Archivo/Imagen | Adjunto binario (por ejemplo, la foto de un comprobante de pago). Este documento no define formato ni almacenamiento técnico — solo que el campo existe conceptualmente y se referencia junto al registro que documenta. |

## 2. Campos Comunes Heredados por Toda Entidad

Salvo excepción indicada explícitamente, toda entidad de este diccionario incluye:

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `empresaId` | Referencia (a Empresa) | Sí, salvo `Empresa` y `ConfiguracionPlataforma` | Aísla la entidad a su empresa (Artículo 2.2 de la Constitución). |
| `sucursalId` | Referencia (a Sucursal) | Sí, cuando la entidad opera a nivel de sede/cuarto frío/despacho | Aísla la entidad a su sucursal (Artículo 2.3). |
| `código` | Código | Sí, para toda entidad de negocio | Clave de negocio única dentro del alcance de `empresaId` (Principio 1). |
| `estado` | Enumeración (`activo` / `inactivo`) | Sí | Soporta soft delete; nunca se elimina físicamente (Artículo 9 de la Constitución). |
| `creadoPor` | Referencia (a Usuario) | Sí | Usuario que originó la entidad o su evento fundacional. |
| `creadoEn` | Fecha/Hora | Sí | Momento de creación (Artículo 7 de la Constitución). |
| `version` | Número entero | Sí, para entidades configurables o normativas | Soporta versionado sin retroactividad (Artículo 11). |

Las secciones siguientes listan **solo los campos específicos adicionales** de cada entidad; los campos comunes se asumen presentes salvo indicación contraria.

## 3. Entidades de Plataforma

### Empresa

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `código` | Código | Sí | Identificador único de la empresa en todo el ecosistema (no particionado por sí mismo). |
| `razónSocial` | Texto corto | Sí | Nombre legal de la empresa. |
| `estado` | Enumeración (`activa` / `inactiva`) | Sí | Soft delete a nivel de empresa. |
| `moneda` | Código (ISO 4217, por ejemplo `USD`, `DOP`) | Sí | Moneda funcional de la empresa. Todo `Monto` de cualquier entidad con esta `empresaId` se expresa en esta moneda (Artículo 28.1 de la Constitución — crecimiento a nuevas monedas). |

### Usuario

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `código` | Código | Sí | Identificador único del usuario (no particionado por empresa: un usuario puede pertenecer a varias). |
| `nombre` | Texto corto | Sí | Nombre de presentación. |
| `credenciales` | — | Sí | Mecanismo de autenticación (detalle técnico fuera del alcance de este documento). |
| `membresías` | Lista de (Referencia a Empresa + Referencia a Rol) | Sí, al menos una | Empresas a las que pertenece y su rol en cada una (Artículo 2.7 de la Constitución). |
| `empresaActiva` | Referencia (a Empresa) | Sí, en tiempo de sesión | Contexto de empresa activa (Artículo 2.7); no se persiste como estado global del usuario, sino de la sesión. |

### ConfiguracionPlataforma (`config/*`)

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `clave` | Código | Sí | Identificador del parámetro de configuración. |
| `valor` | — | Sí | Valor del parámetro (tipo dependiente de la clave). |
| `empresaId` | Referencia (a Empresa) | No, solo si el parámetro es específico de una empresa | Distingue configuración global de plataforma vs. configuración por empresa (Artículo 16.3). |

## 4. Entidades Comunes Particionadas por Empresa

### Sucursal

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `nombre` | Texto corto | Sí | Nombre de la sede/cuarto frío/punto de despacho. |
| `tipo` | Enumeración (`sede` / `cuarto_frío` / `punto_despacho`) | Sí | Clasifica la naturaleza operativa de la sucursal (Artículo 22.1). |

### Rol

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `nombre` | Texto corto | Sí | Nombre descriptivo del rol. |
| `permisos` | Lista de Referencia (a Permiso) | Sí | Permisos asociados a este rol dentro de la empresa. |

### Permiso

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `acción` | Enumeración (`crear` / `leer` / `modificar` / `aprobar` / `anular` / `exportar`) | Sí | La operación que autoriza. |
| `entidad` | Texto corto | Sí | La entidad de negocio sobre la que aplica la acción. |
| `sucursalId` | Referencia (a Sucursal) | No, solo si el permiso se limita a una sucursal | Restringe el alcance del permiso (Artículo 13.2). |

### Evento

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `tipoEvento` | Código (del catálogo en `docs/diagramas/eventos.drawio`) | Sí | Naturaleza del hecho registrado. |
| `entidadAfectada` | Referencia | Sí | Entidad de negocio que el evento modifica. |
| `payload` | — | Sí | Datos específicos del evento (estructura dependiente del `tipoEvento`). |
| `usuarioEmisor` | Referencia (a Usuario) | Sí | Quién generó el evento. |
| `momentoCaptura` | Fecha/Hora | Sí | Instante real del hecho, no necesariamente el de sincronización (sección 13 de `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`). |
| `momentoPersistencia` | Fecha/Hora | Sí | Instante en que el motor lo persistió. |
| — | — | — | **No tiene `estado` de soft delete**: un evento nunca se inactiva ni se borra (Artículo 5.4). |

### RegistroAuditoria

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `usuario` | Referencia (a Usuario) | Sí | Quién ejecutó la acción auditada. |
| `acción` | Enumeración (`crear` / `modificar` / `aprobar` / `anular` / `soft_delete`) | Sí | La operación auditada. |
| `entidadAfectada` | Referencia | Sí | Entidad sobre la que se ejecutó la acción. |
| `valoresAnteriores` | — | No, según la acción | Estado previo (si aplica). |
| `valoresNuevos` | — | No, según la acción | Estado resultante (si aplica). |
| — | — | — | **De solo lectura para todos los roles** (Artículo 8.2), incluidos los administrativos. |

### ConflictoSincronizacion

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `eventoRechazado` | Referencia (a Evento) | Sí | El intento de evento que el motor no pudo aplicar al sincronizarse (`04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`, sección 13). |
| `motivoRechazo` | Texto largo | Sí | Explicación específica del rechazo. |
| `estadoConflicto` | Enumeración (`pendiente` / `resuelto`) | Sí (proyección) | `resuelto` si y solo si existe un `eventoResolucion` que la referencia; nunca un valor editado directamente (Artículo 5 de la Constitución). |
| `eventoResolucion` | Referencia (a Evento) | No, solo cuando `estadoConflicto` es `resuelto` | El evento nuevo que resuelve el conflicto (reintento corregido, ajuste, o registro de que la operación ya no procede). |
| `usuarioResuelve` | Referencia (a Usuario) | No, solo cuando está resuelto | Quién resolvió el conflicto; nunca una IA sin acción humana explícita (Artículo 26.4). |
| `momentoDeteccion` | Fecha/Hora | Sí | Cuándo el motor detectó el conflicto al sincronizar. |
| — | — | — | No es de solo lectura como `RegistroAuditoria`: es un objeto de trabajo pendiente cuyo `estadoConflicto` se recalcula, no se marca manualmente. |

## 5. Catálogos Maestros Compartidos

### Producto

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `nombre` | Texto corto | Sí | Etiqueta de presentación (nunca usada como clave — Principio 1). |
| `precio` | Monto | Sí | Precio de referencia del producto. |

### Cuenta

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `código` | Código (numérico, "1" a "6") | Sí | Identificador de la cuenta dentro del plan de cuentas (`06-REGLAS-CONTABLES-Y-FINANCIERAS.md`, sección 3 — Borrador); es el mismo número con el que el resto de la documentación ya se refiere a cada cuenta ("Cuenta 1", "Cuenta 4", etc.). Para esta entidad, el tipo `Código` genérico de la sección 1 queda restringido a los valores "1" a "6" — no es un código arbitrario como en el resto de los catálogos. |
| `nombre` | Texto corto | Sí | Nombre descriptivo de la cuenta. |
| `naturaleza` | Enumeración (`activo` / `pasivo` / `resultado`) | Sí | Clasificación contable de la cuenta. |

### CuentaBancaria

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `número` | Texto corto | Sí | Número de cuenta bancaria (Artículo 18.2 de la Constitución). |
| `banco` | Texto corto | Sí | Nombre del banco. |
| `cuentaAsociada` | Referencia (a Cuenta) | Sí | Vínculo a la Cuenta 2 — Bancos del plan de cuentas. |

### Fondo

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `clasificación` | Enumeración (`Costo` / `Venta` / `Distribución` / `Mantenimiento`) | Sí | Clasificación obligatoria (Artículo 18.1). |
| `saldoActual` | Monto | — (proyección) | Derivado del historial de eventos de capital; no se edita directamente. |

### Vendedor

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `nombre` | Texto corto | Sí | Nombre del vendedor. |

### Cliente

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `nombre` | Texto corto | Sí | Nombre o razón social del cliente (Artículo 16.1 de la Constitución). |

### Proveedor

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `nombre` | Texto corto | Sí | Nombre o razón social del proveedor o tercero. |
| `tipo` | Enumeración (`proveedor_mercancia` / `transportista` / `consignatario` / `otro`) | Sí | Clasifica el tipo de tercero con quien se puede contraer una obligación (Artículo 20.1 de la Constitución). |

### CondicionPago

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `nombre` | Texto corto | Sí | Nombre del plazo (ej. "Contado", "30 días"). |
| `plazoDias` | Número entero | Sí | Días desde la fecha de la factura hasta el vencimiento. |

## 6. Módulo 1 — Flujo de Efectivo y Bancos

### MovimientoCapital

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `fondo` | Referencia (a Fondo) | Sí | Clasificación del movimiento (Artículo 18.1). |
| `cuentaOrigen` / `cuentaDestino` | Referencia (a Cuenta) | Al menos una | Cuenta afectada por el movimiento (Artículo 18.3). |
| `monto` | Monto | Sí | Valor del movimiento. |
| `tipo` | Enumeración (`ingreso` / `egreso`) | Sí | Naturaleza del movimiento. |
| `obligacionReferenciada` | Referencia (a Obligacion) | No, solo si el movimiento es un pago aplicado a una obligación del Módulo 2 | Vincula el pago con la obligación que salda, parcial o totalmente (Artículo 20.2). |
| `medioDePago` | Enumeración (`depósito` / `efectivo` / `transferencia`) | No, solo si el movimiento es un pago | Cómo se realizó el pago. |
| `numeroTransaccion` | Código | No | Número de transacción bancaria, cuando el medio de pago lo genera. |
| `comprobanteImagen` | Archivo/Imagen | No | Foto del comprobante del pago. |
| `eventoOrigen` | Referencia (a Evento) | Sí | El evento que generó este registro. |

## 7. Módulo 2 — CXP, Facturación y Reportes

### Obligacion (Cuenta por Pagar)

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `proveedor` | Referencia (a Proveedor) | Sí | Contraparte de la obligación — el Suplidor (Artículo 20.1 de la Constitución). |
| `condicionPago` | Referencia (a CondicionPago) | Sí | Plazo de crédito aplicado. |
| `fechaFactura` | Fecha/Hora | Sí | Fecha de la factura que el Suplidor emitió. |
| `fechaVencimiento` | Fecha/Hora | Sí | `fechaFactura` + `condicionPago.plazoDias`, calculada una vez al registrar la obligación; no se recalcula si la condición de pago cambia después. |
| `comprobanteFiscal` | Código | Sí | Número de Comprobante Fiscal (NCF) que el Suplidor emite. |
| `montoCosto` | Monto | Sí | Monto antes de impuesto. |
| `montoITBIS` | Monto | Sí | Impuesto (ITBIS) aplicado. |
| `montoOriginal` | Monto | Sí | El total: `montoCosto` + `montoITBIS`. Nunca se edita para reflejar pagos parciales (Artículo 20.2). |
| `cuenta` | Referencia (a Cuenta) | Sí | Vínculo a la Cuenta 4 — Cuentas por Pagar del plan de cuentas (`06-REGLAS-CONTABLES-Y-FINANCIERAS.md`, sección 3). |
| `saldoPendiente` | Monto | — (proyección) | `montoOriginal` menos la suma de pagos aplicados; derivado del historial de eventos, no editable directamente. |
| `eventoOrigen` | Referencia (a Evento) | Sí | El evento `ObligacionRegistrada` que originó esta obligación. |
| — | — | — | Los reportes R1 ("Fecha Factura-Comprobante-Costo-ITBIS-Total"), R2 ("Fecha Factura-Comprobante-Total-Días Vencimiento") y R3 ("Fecha Factura-Comprobantes-Medio de Pago con Imagen") de Oliver son proyecciones de solo lectura sobre estos campos y los de `MovimientoCapital` — no entidades nuevas. "Días Vencimiento" de R2 se calcula al generar el reporte, no es un campo almacenado. Sigue sin modelar: la nota de crédito propia del Suplidor — ver `08-CATALOGO-DE-MODULOS.md`, Módulo 2, "Pendiente de modelar". |

## 8. Módulo 3 — Inventario y Cuarto Frío

### InventarioChofer / InventarioEncargado

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `responsable` | Referencia (a Usuario) | Sí | Chofer o encargado responsable. |
| `existencias` | Lista de (Referencia a Producto + cantidad + precio + total) | Sí | Existencias actuales (proyección derivada de eventos de mercancía). |

### NovedadInventario

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `tipo` | Enumeración (`dañado` / `roto` / `mal_estado` / `sobrante` / `faltante` / `rotura_cadena_frio`) | Sí | La **condición** detectada en el producto (Artículo 22.2 de la Constitución, para `rotura_cadena_frio`). No codifica ubicación — ver la nota sobre `sucursalId` más abajo. |
| `producto` | Referencia (a Producto) | Sí | Producto afectado. |
| `cantidad` | Número entero | Sí | Cantidad afectada. |
| `procesoOrigen` | Enumeración (`InventarioChofer` / `InventarioEncargado`) | Sí | Proceso donde se detectó (Artículo 17.2). |
| `eventoOrigen` | Referencia (a Evento) | Sí | El evento que generó la novedad; su `tipoEvento` (`NovedadCuartoFrioRegistrada` o `NovedadInventarioRegistrada` — `12-GLOSARIO.md`, sección C) distingue si la novedad se originó en un cuarto frío. |
| — | — | — | La **ubicación** de la novedad (cuarto frío, sede u otro punto) se determina por el `sucursalId` heredado (sección 2), referenciando a una `Sucursal` cuyo `tipo` sea `cuarto_frío`, `sede` o `punto_despacho` (Artículo 22.1) — nunca por el campo `tipo` de esta entidad. |

### BajaInventario

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `producto` | Referencia (a Producto) | Sí | Producto dado de baja. |
| `cantidad` | Número entero | Sí | Cantidad dada de baja. |
| `tipo` | Enumeración (`merma` / `pérdida` / `condonación` / `donación` / `bonificación` / `refrigerio`) | Sí | Naturaleza de la baja: deterioro operativo, extravío/siniestro, no cobrar una deuda existente, regalar producto, incentivo promocional, o consumo del personal. |
| `novedadOrigen` | Referencia (a NovedadInventario o NovedadDespacho) | No, solo si la baja proviene de una novedad detectada previamente | Vincula la baja con la novedad que la motivó (Artículo 17.2/23.2: nunca suelta cuando existe una novedad previa). |
| `inventarioOrigen` | Referencia (a InventarioChofer, InventarioEncargado o Consignacion) | Sí | De dónde se retira la mercancía dada de baja. |
| `motivo` | Texto largo | Sí | Justificación de la baja. |
| `usuarioAutoriza` | Referencia (a Usuario) | Sí | Quién autoriza la baja. |
| `eventoOrigen` | Referencia (a Evento) | Sí | El evento `BajaInventarioRegistrada` que la originó. |
| — | — | — | **No genera `MovimientoCapital`**: el tratamiento de capital de una baja (si genera un gasto/pérdida contable y contra qué `Fondo`/`Cuenta`) está pendiente de validación contable (`docs/anexos/01-PENDIENTE-VALIDACION-CONTABLE.md`, ítem 7). |

## 9. Módulo 4 — Consignaciones

### Consignacion

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `responsable` | Referencia (a Usuario) | Sí | Responsable de la consignación. |
| `contenido` | Lista de (Referencia a Producto + cantidad) | Sí | Mercancía consignada. |
| `estadoConsignacion` | Enumeración (`activa` / `cerrada`) | Sí | Al cerrarse, se vuelve inmutable (Artículo 14.1). |
| `ruta` | Referencia (a Ruta) | No | Ruta de distribución a la que pertenece, cuando aplica. |
| `numeroPunto` | Número entero | No, solo si tiene `ruta` | Posición dentro de la ruta (ej. 1 a 23). |

### Ruta

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `nombre` | Texto corto | Sí | Nombre de la ruta (ej. "Santiago"). |
| `presupuestoInventario` | Monto | No | Presupuesto de inventario de referencia para la ruta ("PPTO Inventario Santiago"). |

## 10. Módulo 5 — Despacho, Novedades y Caja

### Despacho

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `sucursalOrigen` / `sucursalDestino` | Referencia (a Sucursal) | Sí | Ambas dentro de la misma `empresaId` (Artículo 23.1). |
| `contenido` | Lista de (Referencia a Producto + cantidad) | Sí | Mercancía despachada. |
| `eventoOrigen` | Referencia (a Evento) | Sí | El evento que generó el despacho. |

### NovedadDespacho

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `despacho` | Referencia (a Despacho) | Sí | El despacho al que queda vinculada (Artículo 23.2: nunca suelta). |
| `tipo` | Enumeración (`novedad` / `dañado` / `sobrante`) | Sí | Naturaleza de la novedad. |
| `producto` | Referencia (a Producto) | Sí | Producto afectado. |
| `cantidad` | Número entero | Sí | Cantidad afectada. |

### SolicitudRetiro

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `referencia` | Referencia (a Consignacion o Despacho) | Sí | El origen de la mercancía a retirar. |
| `motivoSolicitado` | Texto largo | Sí | Motivo declarado por el solicitante. |
| `usuarioSolicitante` | Referencia (a Usuario) | Sí | Quién solicita el retiro. |

### JustificacionRetiro

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `solicitudRetiro` | Referencia (a SolicitudRetiro) | Sí | La solicitud que esta justificación resuelve (Artículo 21.3: no existe retiro sin justificación). |
| `justificación` | Texto largo | Sí | Justificación registrada. |
| `usuarioAprobador` | Referencia (a Usuario) | Sí | Quién aprueba. |

### Factura

Factura de **venta** que Polar Breeze emite a sus clientes — distinta de la `Obligacion` del Módulo 2 (factura que Polar Breeze recibe de un Suplidor).

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `código` (número de factura) | Código | Sí | Nunca reutilizable, incluso si la factura se anula (Artículo 9.3). |
| `vendedor` | Referencia (a Vendedor) | Sí | Vendedor asociado. |
| `líneas` | Lista de (Referencia a Producto + cantidad + precio) | Sí, al menos una línea | Detalle de la venta. |
| `total` | Monto | Sí | Suma de las líneas. |
| `estadoFactura` | Enumeración (`aprobada` / `anulada_por_nota_crédito`) | Sí | Al aprobarse, la factura es inmutable (Artículo 14.1). |

### NotaCredito

Corrige una `Factura` de venta — distinta de la nota de crédito de proveedor mencionada en el Módulo 2.

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `facturaOriginal` | Referencia (a Factura) | Sí | Obligatoria; nunca se edita la factura original (Artículo 14.2). |
| `motivo` | Texto largo | Sí | Razón de la nota de crédito. |
| `monto` | Monto | Sí | Monto afectado. |

### ArqueoManual

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `objetoConciliado` | Referencia (a Fondo, InventarioChofer o InventarioEncargado) | Sí | Qué se está conciliando. |
| `saldoSistema` | Monto o Número entero | Sí | Proyección del sistema al momento del arqueo. |
| `conteoFísico` | Monto o Número entero | Sí | Resultado del conteo real. |
| `diferencia` | Monto o Número entero | Sí (calculado) | `conteoFísico - saldoSistema`; nunca sobrescribe el saldo del sistema (Artículo 25.3). |

### ExportacionReporte

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `rangoFechas` | Fecha/Hora (inicio y fin) | Sí | Alcance temporal de la exportación. |
| `versiónReglasUsada` | Número entero | Sí | Versión de las reglas de negocio aplicadas (Artículo 25.2). |
| `usuario` | Referencia (a Usuario) | Sí | Quién ejecutó la exportación. |

## 11. Relación con Otros Documentos

- `05-MODELO-DE-DATOS-MAESTRO.md` — el origen de cada entidad detallada aquí; ninguna entidad se agrega a uno sin agregarse al otro.
- `02-CONSTITUCION-ERP.md` — las reglas de integridad, soft delete, versionado y auditoría que estos campos implementan.
- `12-GLOSARIO.md` — la definición de términos usados en los nombres de campo y enumeraciones.
- `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` — el detalle contable de `Cuenta`, cuyo plan de cuentas sigue en estado Borrador.
- `08-CATALOGO-DE-MODULOS.md` y `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md` — la agrupación de entidades por módulo (secciones 6-10) se reconcilió con estos dos documentos.

Observaciones:

Los campos marcados como Enumeración usan valores cerrados a modo ilustrativo, consistentes con lo ya documentado en `07-FLUJOS-DE-NEGOCIO.md` y `06-REGLAS-CONTABLES-Y-FINANCIERAS.md`. Cualquier valor adicional que el negocio requiera debe agregarse aquí antes de usarse en un módulo (Artículo 29.3 de la Constitución), no introducirse directamente en el código.

Las secciones 6-10 se reagruparon para reconciliarse con los 6 módulos de `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md`, en el mismo orden en que se reagruparon las secciones equivalentes de `05-MODELO-DE-DATOS-MAESTRO.md`: `Obligacion` pasó del Módulo 1 al Módulo 2; `Despacho`, `NovedadDespacho`, `SolicitudRetiro`, `JustificacionRetiro`, `Factura` y `NotaCredito` pasaron al Módulo 5; `Consignacion` quedó sola en el Módulo 4. Ningún campo cambió — solo su agrupación por módulo.

Se modelaron 6 de los 7 conceptos que `08-CATALOGO-DE-MODULOS.md` marcaba como "Pendiente de modelar": NCF, ITBIS y fecha de factura (`Obligacion`), Condición de Pago (`CondicionPago`), Rutas y Vías (`Ruta`), Reportes R1/R2/R3 (proyecciones, no entidades) y Refrigerios/Bonificaciones/Donaciones (ampliación de `BajaInventario.tipo`). Se agregó el tipo conceptual Archivo/Imagen (sección 1) para dar soporte al comprobante de pago de `MovimientoCapital`. **Participación de Capital** queda deliberadamente sin modelar, por decisión del usuario — sigue señalada en `08-CATALOGO-DE-MODULOS.md`, Módulo 1.
