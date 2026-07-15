# Anexo — Estructura Oliver: Flujos Reales de los Módulos del ERP

Estado:

> Fuente de verdad — contenido provisto textualmente por Oliver, transcrito sin interpretación ni modificación

Objetivo:

Preservar, de forma literal y sin alteración, la estructura de los 6 módulos del ERP tal como la definió Oliver. Este documento es la **fuente de verdad** para el diseño de los módulos del sistema: cualquier documento técnico de este repositorio que describa módulos, campos o flujos de negocio (`05-MODELO-DE-DATOS-MAESTRO.md`, `07-FLUJOS-DE-NEGOCIO.md`, `08-CATALOGO-DE-MODULOS.md`, `11-DICCIONARIO-DE-DATOS.md`) debe, en su próxima revisión, contrastarse contra esta estructura.

El contenido de la sección siguiente se transcribe **exactamente como fue provisto**, sin resumir, reordenar, corregir ni reinterpretar ningún campo, viñeta o encabezado.

Contenido:

```
01. FLUJO DE EFECTIVO Y BANCOS
[FLUJO DE EFECTIVO]
• Venta / Costo / Distribución -> Agregar Registro

[CUENTAS]
• Cuenta 1, Cuenta 2, Cuenta 3, Cuenta 4, Cuenta 5, Cuenta 6 -> Mantenimiento / Crear Cuenta

[BANCOS]
• Banco / Número de Cuenta -> Participación de Capital

02. CXP, FACTURACIÓN Y REPORTES
[CUENTAS POR PAGAR]
• Suplidor
• Fecha de Factura
• Número de Factura
• Comprobante (NCF)
• Código de Producto / Descripción
• Costo / ITBIS / Total
• Cantidad de Productos
• Días de Crédito
• Subir Imagen del Pago

[NOTAS Y MEDIOS DE PAGO]
• Generar Nota de Crédito
• Seleccionar Factura y/o Nota de Crédito
• Medio de Pago (Depósito, Efectivo, Transferencia)
• Monto del Pago
• Pedir Numeración de Transacción
• Bandeja de Banco

[REPORTES GENERADOS]
• R1: Fecha Factura - Comprobante - Costo - ITBIS - Total
• R2: Fecha Factura - Comprobante - Total - Días Vencimiento
• R3: Fecha Factura - Comprobantes - Medio de Pago (Imagen)

03. INVENTARIO Y CUARTO FRÍO
[ALMACÉN PRINCIPAL]
• INVENTARIO BUY IN (Compra):
  - Código de Producto
  - Descripción
  - Cantidad
  - Costo
  - Total

• INVENTARIO FOR SALE (Venta):
  - Código de Producto
  - Descripción
  - Cantidad
  - Precio
  - Total

[NOVEDADES CUARTO FRÍO]
• Sobrantes / Faltantes Cuarto Frío
• Dañados / Consignado en Mal Estado
• Refrigerios / Bonificaciones / Donaciones
• BANDEJA DE NOVEDADES CUARTO FRÍO (Reportes)

04. CONSIGNACIONES
[ACCIONES]
• Consignar (Generar)
• Retirar Consignado
• Histórico de Consignaciones
• Visualizar Consignaciones

[RUTAS Y VÍAS]
• PPTO Inventario Santiago
• Consignaciones Individuales (1 al 23)
• Consignaciones Generadas / Retiradas / Todas

[FILTROS]
• Vendedor / Producto / Estado

05. DESPACHO, NOVEDADES Y CAJA
[PICKING / DESPACHO]
• Retirar Consignación / Justificar Retiro
• Faltante Despacho / Dañado Despacho

[BANDEJA NOVEDADES DESPACHO]
• Solicitud de Retiro
• Registro de Sobrantes / Dañados
• Reportes de Novedades en Despacho

[ARQUEO DE CAJA Y FACTURAR]
• Arqueo Manual / Sugerido
• Exportar Reportes de Arqueo / Histórico
• Facturación / Histórico Facturas

06. PARÁMETROS DE MANTENIMIENTO
[CREACIONES BASE]
☐ CREAR SUPLIDOR (Proveedores y condiciones)
☐ CREAR PRODUCTO (Código, descripción, costos)
☐ CONDICIÓN DE PAGO (Plazos de crédito)
☐ CREAR VENDEDOR (Personal y rutas)
☐ CREAR NOVEDADES (Inventario / Despacho)
☐ CREAR CONSIGNACIÓN (Puntos o lotes nuevos)
```

Observaciones:

Este documento se transcribe tal cual fue entregado, sin reconciliarlo todavía contra el resto de la documentación. Se deja constancia de que su estructura de 6 módulos ("01. Flujo de Efectivo y Bancos", "02. CXP, Facturación y Reportes", "03. Inventario y Cuarto Frío", "04. Consignaciones", "05. Despacho, Novedades y Caja", "06. Parámetros de Mantenimiento") no coincide, en número ni en agrupación, con los 5 módulos actualmente descritos en `08-CATALOGO-DE-MODULOS.md` ("Flujo de Efectivo", "Inventario y Almacén", "Despacho y Consignaciones", "Facturación", "Reportes"). Esta diferencia no se resuelve en este documento — queda señalada aquí para que una revisión futura decida cómo reconciliar ambas estructuras, consistente con la Convención de Cambios de `02-CONSTITUCION-ERP.md` y con el principio de humildad arquitectónica de `99-FILOSOFIA-DEL-SISTEMA.md` (declarar lo que aún no se ha resuelto, en vez de simular una reconciliación que no ha ocurrido).
