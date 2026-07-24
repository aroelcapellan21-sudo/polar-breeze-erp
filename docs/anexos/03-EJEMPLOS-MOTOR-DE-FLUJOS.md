# Anexo — Ejemplos Completos de Flujo del Motor de Flujos Patrimoniales

Estado:

> Listo para revisión final — usa exclusivamente eventos del catálogo formal vigente (`12-GLOSARIO.md`, sección C, 22 eventos)

Objetivo:

Complementar `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` con casos completos, de principio a fin, mostrando cómo se aplican en secuencia los eventos del catálogo formal. No introduce reglas nuevas — solo ilustra las ya vigentes.

Contenido:

## Ejemplo 1 — Compra y recepción de mercancía (F2, evento multi-flujo)

**Referencia:** `07-FLUJOS-DE-NEGOCIO.md` F2; `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`, sección 6 (Eventos Multi-Flujo y Atomicidad).

**Situación:** Se compra mercancía a un proveedor (a crédito o de contado — la `CondicionPago` referenciada define el plazo; "Contado" es simplemente una condición con plazo 0).

**Secuencia atómica:**

1. `ObligacionRegistrada` (Capital) — Cuenta 4, Cuentas por Pagar. Crea la obligación con el `Proveedor`, la `CondicionPago` y su fecha de vencimiento calculada.
2. `MercanciaRecibida` (Mercancía) — aumenta el `InventarioEncargado` de la sucursal correspondiente.

**Resultado:** ambos eventos se persisten juntos o ninguno se persiste. Si la mercancía se recibiera sin obligación registrada, o viceversa, el motor rechaza la operación completa (Artículo 6.3 de la Constitución).

**Nota:** este evento nunca representa una venta a crédito a un cliente — Polar Breeze no opera así. `ObligacionRegistrada` es exclusivamente para lo que la empresa debe a un tercero (proveedor, transportista, consignatario), nunca para lo que un cliente le debe a la empresa.

## Ejemplo 2 — Consignación y su liquidación (F5, F7, F9)

**Situación:** Se entrega mercancía en consignación a un vendedor; días después, liquida: parte vendida, parte devuelta, parte dañada.

**Paso 1 — Creación y despacho de la consignación:**

1. `ConsignacionCreada` (Mercancía, Información) — registra la `Consignacion`, con responsable y contenido.
2. `Despachado` (Mercancía) — mueve la mercancía hacia el punto de consignación.

**Paso 2 — Liquidación (mismo día, varios eventos):**

3. `MercanciaVendida` (Mercancía) — por la porción vendida; reduce el inventario de origen.
4. `FacturaCreada` (Información) — respalda la venta, con su `Vendedor` y líneas de producto.
5. `MercanciaDevuelta` (Mercancía) — por la porción que regresa sin venderse.
6. `NovedadInventarioRegistrada` (Mercancía) — registra la condición (dañado/faltante) de la porción detectada en mal estado durante la liquidación.
7. `BajaInventarioRegistrada` (Mercancía, Información) — da de baja definitivamente esa porción dañada, referenciando la `NovedadInventarioRegistrada` del paso anterior y el `MotivoSalidaSinCobro` correspondiente.

**Resultado:** la `Consignacion` queda explicada por completo (vendida + devuelta + dada de baja), sin que ningún evento haya editado los eventos anteriores — cada paso es nuevo y referencia lo que corresponde.

**Nota:** si Polar Breeze llega a confirmar la existencia de crédito interno a empleados (pendiente, ver `DECISIONES-ARQUITECTURALES.md`), ese caso usaría un evento nuevo (`CreditoEmpleadoRegistrado`, propuesto y no incorporado aún al catálogo formal) — no `ObligacionRegistrada`, que va en la dirección contraria.

## Ejemplo 3 — Corrección de un evento ya persistido

**Referencia:** `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`, secciones 3 y 8 (formalizado en v0.47).

**Situación:** Se registró `PagoRegistrado` por un monto incorrecto.

**Secuencia:**

1. `PagoRegistrado` original — ya persistido, inmutable, referencia la `Obligacion` que paga.
2. `PagoRegistrado` compensatorio — mismo tipo de evento del catálogo (no se crea un tipo nuevo), con:
   - `eventoCorregidoId` → referencia al evento del paso 1.
   - `motivoCorreccion` → "monto digitado incorrectamente, corregido a RD$ X".
   - `tipoCompensacion` (opcional) → "error de captura".

**Resultado:** el saldo pendiente de la `Obligacion` se recalcula como la suma de ambos `PagoRegistrado` aplicados en orden — nunca por editar el evento original.

## Relación con Otros Documentos

- `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` — las reglas que estos ejemplos ilustran.
- `12-GLOSARIO.md`, sección C — el catálogo formal de 22 eventos usado aquí, sin ninguno inventado.
- `07-FLUJOS-DE-NEGOCIO.md` — F2, F5, F7, F9 y F10, los flujos de origen de cada evento usado.

Observaciones:

Este anexo no modifica ninguna regla existente. El Ejemplo 2 marca explícitamente que el evento de crédito a empleados sigue sin confirmarse ni incorporarse al catálogo — no se usa en ningún ejemplo como si ya existiera.
