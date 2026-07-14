# Modelo de Datos Maestro

Estado:

> Vigente — pendiente de revisión y aprobación formal

Objetivo:

Definir las entidades de datos del ERP Polar Breeze, sus campos conceptuales, sus relaciones y su particionado multiempresa. Este documento traduce a estructura de datos lo ya establecido en `02-CONSTITUCION-ERP.md` (Artículos 2, 10, 11, 16), `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` (historial de eventos y proyecciones) y `08-CATALOGO-DE-MODULOS.md` (los cinco módulos funcionales).

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

## 4. Catálogos Maestros Compartidos

Consumidos por todos los módulos, nunca reimplementados por ninguno (Principio 6 y 7; Artículo 16.1 de la Constitución).

### Producto

- Campos conceptuales: **código** (clave de negocio), nombre, precio, `empresaId`.
- Se crea desde el Módulo 4 — Facturación ("crear producto nuevo", "código de producto") y se consume desde el Módulo 2 — Inventario y Almacén.

### Cuenta

Representa las Cuentas 1-6 del Módulo 1 — Flujo de Efectivo.

- Campos conceptuales: código de cuenta (1 a 6), nombre/descripción, `empresaId`.
- Relación: toda `CuentaBancaria` y todo evento de capital referencia una `Cuenta`.

### CuentaBancaria

- Campos conceptuales: código, número de cuenta, banco, `Cuenta` asociada, `empresaId`.
- Se crea desde el Módulo 1 — Flujo de Efectivo ("crear cuenta bancaria con número y banco").

### Fondo

Agrupación patrimonial de capital por propósito (`01-VISION-ERP.md`, sección 7): Costo, Venta, Distribución, Mantenimiento.

- Campos conceptuales: código, clasificación (Costo/Venta/Distribución/Mantenimiento), `empresaId`, saldo actual (proyección derivada del historial de eventos de capital).

### Vendedor

- Campos conceptuales: código, nombre, `empresaId`.
- Se crea desde el Módulo 4 — Facturación ("crear vendedor").

### Cliente

- Campos conceptuales: código, nombre, `empresaId`.
- Catálogo maestro exigido explícitamente por el Artículo 16.1 de la Constitución ("productos, cuentas, vendedores, bancos, clientes, proveedores"). Se crea desde el Módulo 4 — Facturación, igual que `Producto` y `Vendedor`. Queda disponible para su consumo por ese mismo módulo y por el Módulo 1 — Flujo de Efectivo cuando el negocio registre ventas a crédito contra la Cuenta 3 — Cuentas por Cobrar (`06-REGLAS-CONTABLES-Y-FINANCIERAS.md`, sección 3).

### Proveedor

- Campos conceptuales: código, nombre, tipo (proveedor de mercancía / transportista / consignatario / otro), `empresaId`.
- Catálogo maestro exigido explícitamente por el Artículo 16.1 de la Constitución. Representa a todo tercero con quien la empresa puede contraer una obligación de pago (Artículo 20.1: "proveedor, transportista, consignatario"). Se crea desde el Módulo 1 — Flujo de Efectivo, como contraparte de las obligaciones que ese módulo administra. Es la contraparte que referencia toda `Obligacion` (sección 5).

## 5. Entidades del Módulo 1 — Flujo de Efectivo

### MovimientoCapital

Proyección/registro de un evento de flujo de capital.

- Campos conceptuales: `empresaId`, `Fondo` (clasificación), `Cuenta` de origen/destino, monto, tipo (ingreso/egreso), obligación referenciada (opcional, cuando el movimiento es un pago aplicado a una `Obligacion`), evento de origen.

### Obligacion (Cuenta por Pagar)

Registra una obligación de pago con un tercero (Artículo 20 de la Constitución), contabilizada contra la Cuenta 4 — Cuentas por Pagar (`06-REGLAS-CONTABLES-Y-FINANCIERAS.md`, sección 3).

- Campos conceptuales: `empresaId`, `Proveedor` referenciado (contraparte), monto original, fecha de vencimiento, `Cuenta` asociada (Cuenta 4), saldo pendiente (proyección: monto original menos suma de pagos aplicados), evento de origen (`ObligacionRegistrada`).
- El monto original nunca se edita para reflejar pagos parciales (Artículo 20.2); un pago se registra como un `MovimientoCapital` que referencia esta `Obligacion`, y la obligación se considera saldada cuando la proyección de saldo pendiente llega a cero (`06-REGLAS-CONTABLES-Y-FINANCIERAS.md`, sección 5).

## 6. Entidades del Módulo 2 — Inventario y Almacén

### InventarioChofer / InventarioEncargado

Dos entidades independientes, nunca fusionadas automáticamente (Artículo 17.1 de la Constitución).

- Campos conceptuales: `empresaId`, `sucursalId`, responsable, lista de existencias por `Producto` (cantidad, precio, total).

### NovedadInventario

Cubre novedades del cuarto frío, dañados, rotos, en mal estado, sobrantes y faltantes.

- Campos conceptuales: `empresaId`, `sucursalId`, tipo de novedad (cuarto frío / dañado / roto / mal estado / sobrante / faltante), `Producto` referenciado, cantidad, proceso de origen (`InventarioChofer` o `InventarioEncargado` — Artículo 17.2), evento de origen.

## 7. Entidades del Módulo 3 — Despacho y Consignaciones

### Consignacion

- Campos conceptuales: código, `empresaId`, `sucursalId`, responsable, contenido (lista de `Producto` + cantidades), estado (activa / cerrada — inmutable al cerrarse, Artículo 14.1).

### Despacho

- Campos conceptuales: código, `empresaId`, `sucursalId` origen y destino, contenido, evento de origen.

### NovedadDespacho

Cubre novedades de despacho y dañado en despacho, sobrantes de despacho.

- Campos conceptuales: `empresaId`, `Despacho` referenciado, tipo (novedad / dañado / sobrante), `Producto`, cantidad.

### SolicitudRetiro / JustificacionRetiro

Dos entidades distintas y ambas obligatorias (Artículo 21.3 de la Constitución): no existe retiro sin justificación.

- **SolicitudRetiro**: código, `empresaId`, `Consignacion` o `Despacho` referenciado, motivo solicitado, usuario solicitante.
- **JustificacionRetiro**: referencia a la `SolicitudRetiro`, justificación, usuario que aprueba.

## 8. Entidades del Módulo 4 — Facturación

### Factura

Documento inmutable una vez aprobado (Artículo 14.1 de la Constitución).

- Campos conceptuales: código (número de factura, nunca reutilizable — Artículo 9.3), `empresaId`, `sucursalId`, `Vendedor`, líneas de `Producto` + cantidad + precio, total, estado (aprobada / anulada por nota de crédito).

### NotaCredito

- Campos conceptuales: código, `empresaId`, `Factura` original referenciada (obligatorio — Artículo 14.2), motivo, monto.

## 9. Entidades del Módulo 5 — Reportes

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
- Una `Obligacion` siempre referencia un `Proveedor` existente de la misma `empresaId`.
- Un `MovimientoCapital` que registra un pago siempre referencia una `Obligacion` existente de la misma `empresaId`.
- Ninguna entidad referencia a otra de una `empresaId` distinta a la propia.

## 11. Relación con Otros Documentos

- `02-CONSTITUCION-ERP.md` — las reglas que este modelo está obligado a cumplir (particionado, integridad referencial, soft delete, versionado, auditoría).
- `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` — quien aplica los eventos que dan origen al historial y a las proyecciones descritas aquí.
- `08-CATALOGO-DE-MODULOS.md` — el origen funcional de cada entidad descrita por módulo.
- `11-DICCIONARIO-DE-DATOS.md` — el detalle campo por campo de cada entidad aquí listada.
- `docs/diagramas/base-datos.drawio` — representación visual de este modelo (pendiente de diagramar).

Observaciones:

Este documento define entidades y relaciones a nivel conceptual. El detalle exhaustivo de cada campo (tipo de dato, obligatoriedad, valores permitidos) corresponde a `11-DICCIONARIO-DE-DATOS.md`, que debe mantenerse consistente con las entidades aquí listadas.

El campo `moneda` de `Empresa` (sección 2) fija una única moneda funcional por empresa — el alcance mínimo necesario para que el ecosistema multiempresa no asuma una sola moneda global (Artículo 28.1 de la Constitución). Este modelo **no** cubre escenarios de multi-moneda dentro de una misma empresa (por ejemplo, una `CuentaBancaria` en una moneda distinta a la funcional, o conversión automática de tipo de cambio); si el negocio llega a necesitar eso, es una extensión futura que requiere su propia decisión en `DECISIONES-ARQUITECTURALES.md`, no una suposición de este documento.
