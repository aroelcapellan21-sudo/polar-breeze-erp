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

## 6. Módulo 1 — Flujo de Efectivo

### MovimientoCapital

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `fondo` | Referencia (a Fondo) | Sí | Clasificación del movimiento (Artículo 18.1). |
| `cuentaOrigen` / `cuentaDestino` | Referencia (a Cuenta) | Al menos una | Cuenta afectada por el movimiento (Artículo 18.3). |
| `monto` | Monto | Sí | Valor del movimiento. |
| `tipo` | Enumeración (`ingreso` / `egreso`) | Sí | Naturaleza del movimiento. |
| `obligacionReferenciada` | Referencia (a Obligacion) | No, solo si el movimiento es un pago aplicado a una obligación | Vincula el pago con la obligación que salda, parcial o totalmente (Artículo 20.2). |
| `eventoOrigen` | Referencia (a Evento) | Sí | El evento que generó este registro. |

### Obligacion (Cuenta por Pagar)

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `proveedor` | Referencia (a Proveedor) | Sí | Contraparte de la obligación (Artículo 20.1 de la Constitución). |
| `montoOriginal` | Monto | Sí | Monto de la obligación; nunca se edita para reflejar pagos parciales (Artículo 20.2). |
| `fechaVencimiento` | Fecha/Hora | Sí | Fecha límite de pago pactada. |
| `cuenta` | Referencia (a Cuenta) | Sí | Vínculo a la Cuenta 4 — Cuentas por Pagar del plan de cuentas (`06-REGLAS-CONTABLES-Y-FINANCIERAS.md`, sección 3). |
| `saldoPendiente` | Monto | — (proyección) | `montoOriginal` menos la suma de pagos aplicados; derivado del historial de eventos, no editable directamente. |
| `eventoOrigen` | Referencia (a Evento) | Sí | El evento `ObligacionRegistrada` que originó esta obligación. |

## 7. Módulo 2 — Inventario y Almacén

### InventarioChofer / InventarioEncargado

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `responsable` | Referencia (a Usuario) | Sí | Chofer o encargado responsable. |
| `existencias` | Lista de (Referencia a Producto + cantidad + precio + total) | Sí | Existencias actuales (proyección derivada de eventos de mercancía). |

### NovedadInventario

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `tipo` | Enumeración (`cuarto_frío` / `dañado` / `roto` / `mal_estado` / `sobrante` / `faltante`) | Sí | Naturaleza de la novedad. |
| `producto` | Referencia (a Producto) | Sí | Producto afectado. |
| `cantidad` | Número entero | Sí | Cantidad afectada. |
| `procesoOrigen` | Enumeración (`InventarioChofer` / `InventarioEncargado`) | Sí | Proceso donde se detectó (Artículo 17.2). |
| `eventoOrigen` | Referencia (a Evento) | Sí | El evento que generó la novedad. |

## 8. Módulo 3 — Despacho y Consignaciones

### Consignacion

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `responsable` | Referencia (a Usuario) | Sí | Responsable de la consignación. |
| `contenido` | Lista de (Referencia a Producto + cantidad) | Sí | Mercancía consignada. |
| `estadoConsignacion` | Enumeración (`activa` / `cerrada`) | Sí | Al cerrarse, se vuelve inmutable (Artículo 14.1). |

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

## 9. Módulo 4 — Facturación

### Factura

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `código` (número de factura) | Código | Sí | Nunca reutilizable, incluso si la factura se anula (Artículo 9.3). |
| `vendedor` | Referencia (a Vendedor) | Sí | Vendedor asociado. |
| `líneas` | Lista de (Referencia a Producto + cantidad + precio) | Sí, al menos una línea | Detalle de la venta. |
| `total` | Monto | Sí | Suma de las líneas. |
| `estadoFactura` | Enumeración (`aprobada` / `anulada_por_nota_crédito`) | Sí | Al aprobarse, la factura es inmutable (Artículo 14.1). |

### NotaCredito

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `facturaOriginal` | Referencia (a Factura) | Sí | Obligatoria; nunca se edita la factura original (Artículo 14.2). |
| `motivo` | Texto largo | Sí | Razón de la nota de crédito. |
| `monto` | Monto | Sí | Monto afectado. |

## 10. Módulo 5 — Reportes

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

Observaciones:

Los campos marcados como Enumeración usan valores cerrados a modo ilustrativo, consistentes con lo ya documentado en `07-FLUJOS-DE-NEGOCIO.md` y `06-REGLAS-CONTABLES-Y-FINANCIERAS.md`. Cualquier valor adicional que el negocio requiera debe agregarse aquí antes de usarse en un módulo (Artículo 29.3 de la Constitución), no introducirse directamente en el código.
