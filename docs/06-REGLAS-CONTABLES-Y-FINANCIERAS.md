# Reglas Contables y Financieras

Estado:

> Borrador — contiene una propuesta de plan de cuentas (sección 3) que requiere validación de un contador/responsable financiero antes de aprobarse

Objetivo:

Detallar el tratamiento contable y financiero del ERP Polar Breeze: la clasificación de movimientos de capital, el plan de cuentas base, las reglas de cuentas por pagar, cierre de períodos y su relación con el Motor de Flujos Patrimoniales. Este documento desarrolla en detalle los Artículos 18 (Reglas Financieras), 19 (Reglas Contables) y 20 (Reglas de Cuentas por Pagar) de `02-CONSTITUCION-ERP.md`, y las entidades `Fondo`, `Cuenta`, `CuentaBancaria` y `MovimientoCapital` ya definidas en `05-MODELO-DE-DATOS-MAESTRO.md`.

Contenido:

## 1. Principio Rector

Todo movimiento financiero del sistema es un **evento del flujo de capital** (Artículo 6 y 18 de la Constitución), nunca un ajuste manual de saldo. El saldo de cualquier `Fondo` o `Cuenta` es siempre una proyección derivada del historial de eventos (`04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`, sección 7), reconstruible en cualquier momento.

## 2. Clasificación de Movimientos de Capital (Fondos)

Todo movimiento de capital se clasifica obligatoriamente en uno de cuatro `Fondo` (Artículo 18.1 de la Constitución) antes de considerarse registrado:

- **Costo** — erogaciones directamente asociadas a la adquisición o producción de la mercancía que se distribuye (por ejemplo, compra de producto a proveedor).
- **Venta** — ingresos originados por la venta de mercancía (facturación) y sus movimientos asociados.
- **Distribución** — costos asociados a mover la mercancía desde el origen hasta el punto de despacho o venta (combustible, transporte, logística).
- **Mantenimiento** — costos de conservación de los activos operativos del negocio (vehículos, cuartos fríos, infraestructura).

Ningún movimiento de capital se registra sin esta clasificación. El motor rechaza cualquier evento de capital que no pueda vincularse a un `Fondo` válido (`04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`, sección 5).

## 3. Plan de Cuentas Base (Cuentas 1-6) — Propuesta Sujeta a Validación

`08-CATALOGO-DE-MODULOS.md` establece que el Módulo 1 — Flujo de Efectivo debe "gestionar Cuentas 1-6", sin especificar su significado contable. Esta sección propone una interpretación inicial, **que debe ser revisada y validada por un contador o responsable financiero antes de aprobarse para implementación** (Artículo 29.3 de la Constitución: ninguna decisión se implementa sin documentación aprobada).

| Cuenta | Nombre propuesto | Naturaleza | Uso previsto |
|---|---|---|---|
| 1 | Caja General | Activo | Efectivo físico en manos de encargados/choferes antes de depositarse |
| 2 | Bancos | Activo | Saldo en cuentas bancarias registradas (`CuentaBancaria`) |
| 3 | Cuentas por Cobrar | Activo | Obligaciones pendientes de clientes (ventas a crédito) |
| 4 | Cuentas por Pagar | Pasivo | Obligaciones pendientes con proveedores, transportistas, consignatarios (ver sección 5) |
| 5 | Costos Operativos | Resultado | Acumulación de movimientos clasificados como `Fondo` Costo y Distribución |
| 6 | Gastos de Mantenimiento | Resultado | Acumulación de movimientos clasificados como `Fondo` Mantenimiento |

Cada `Cuenta` pertenece a una `empresaId` (Artículo 2.2 de la Constitución); dos empresas del ecosistema nunca comparten el mismo saldo de cuenta, aunque usen la misma numeración base.

## 4. Cuentas Bancarias

Toda `CuentaBancaria` (Artículo 18.2 de la Constitución) registra, como mínimo, número de cuenta y banco, y se asocia a la Cuenta 2 — Bancos del plan de cuentas. Ninguna `CuentaBancaria` se comparte entre empresas.

## 5. Reglas de Cuentas por Pagar

Desarrollan el Artículo 20 de la Constitución:

5.1. Toda obligación con un tercero (proveedor, transportista, consignatario) se registra como un evento de flujo de capital pendiente, con `empresaId`, monto, contraparte y fecha de vencimiento, contabilizado contra la Cuenta 4 — Cuentas por Pagar.

5.2. Un pago aplicado a una obligación es un evento independiente que **referencia** la obligación original. El monto original de la obligación nunca se edita para reflejar pagos parciales; el saldo pendiente es siempre una proyección: obligación original menos suma de pagos aplicados.

5.3. Una obligación puede tener múltiples pagos parciales asociados; la obligación se considera saldada cuando la proyección de saldo pendiente llega a cero, nunca por marcarla manualmente como pagada.

## 6. Reglas de Registro de Movimientos

6.1. Ningún movimiento financiero se registra sin cuenta de origen y cuenta o clasificación de destino identificables (Artículo 18.3 de la Constitución).

6.2. Todo movimiento de capital que se origine en otro flujo (por ejemplo, una obligación de pago generada por un despacho) se registra como parte de la misma unidad atómica que el evento que lo originó (`04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`, sección 6), nunca como un registro contable posterior y desconectado.

6.3. Todo movimiento de capital respeta el Artículo 4 de la Constitución (prohibición de duplicar información): un mismo movimiento no se registra dos veces en dos cuentas distintas salvo que represente, de forma legítima, contrapartidas de un mismo evento (por ejemplo, salida de Caja General e ingreso a Cuentas por Cobrar).

## 7. Cierre y Periodos Contables

7.1. Un cambio a una regla contable o a una clasificación de `Fondo` no se aplica retroactivamente a periodos ya cerrados (Artículo 19.3 y 11.2 de la Constitución).

7.2. El cierre de un periodo es un evento de flujo de información que congela las proyecciones de saldo de ese periodo; los eventos del periodo cerrado permanecen en el historial, pero cualquier operación posterior que los afecte se modela como un evento nuevo en el periodo vigente, nunca como una edición del periodo cerrado.

## 8. Arqueo y Conciliación

El arqueo manual (Módulo 5 — Reportes; `05-MODELO-DE-DATOS-MAESTRO.md`, sección 9) es la herramienta formal de conciliación entre el saldo proyectado por el sistema y el conteo físico real de efectivo. Toda diferencia detectada se registra como evento propio (Artículo 25.3 de la Constitución); el saldo del sistema nunca se sobrescribe silenciosamente para "cuadrar" con el conteo físico — la diferencia queda documentada y visible.

## 9. Auditoría Financiera

Todo movimiento de capital, toda clasificación de `Fondo`, toda cuenta por pagar y todo pago genera su registro de auditoría independiente (Artículo 8 de la Constitución), de solo lectura para todos los roles, incluidos los administrativos y contables.

## 10. Relación con Otros Documentos

- `02-CONSTITUCION-ERP.md` (Artículos 18, 19, 20) — las reglas formales que este documento desarrolla.
- `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` — quien aplica los eventos de capital descritos aquí.
- `05-MODELO-DE-DATOS-MAESTRO.md` — las entidades `Fondo`, `Cuenta`, `CuentaBancaria` y `MovimientoCapital`.
- `08-CATALOGO-DE-MODULOS.md` (Módulo 1) — el origen funcional de estas reglas.

Observaciones:

El plan de cuentas de la sección 3 es una **propuesta inicial de arquitectura**, no una definición contable validada. Antes de que cualquier módulo implemente lógica basada en la Cuenta 1-6 descrita aquí, un contador o responsable financiero de Polar Breeze debe revisar, ajustar y aprobar formalmente esta sección, dejando constancia en `DECISIONES-ARQUITECTURALES.md`.
