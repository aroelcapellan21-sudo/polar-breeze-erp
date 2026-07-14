# Anexo — Validaciones Pendientes del Contador / Responsable Financiero

Estado:

> Pendiente de revisión — checklist abierto hasta que cada ítem sea validado y registrado en `DECISIONES-ARQUITECTURALES.md`

Objetivo:

Consolidar en un solo lugar todo lo que, en la biblioteca de arquitectura del ERP Polar Breeze, depende de criterio contable o financiero real y que hasta ahora ha sido propuesto por arquitectura sin validación de un contador. Mientras estos ítems sigan pendientes, ningún módulo puede implementar lógica que dependa de ellos (Artículo 29.3 de `02-CONSTITUCION-ERP.md`), y la Fase 2 del `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` permanece bloqueada.

Contenido:

## Checklist de Validación

| # | Ítem | Dónde está propuesto | Qué debe confirmar el contador | Estado |
|---|---|---|---|---|
| 1 | Plan de cuentas (Cuentas 1-6) | `06-REGLAS-CONTABLES-Y-FINANCIERAS.md`, sección 3 | Si los 6 nombres, naturaleza (activo/pasivo/resultado) y uso previsto propuestos (Caja General, Bancos, Cuentas por Cobrar, Cuentas por Pagar, Costos Operativos, Gastos de Mantenimiento) corresponden al plan de cuentas real del negocio, o deben renombrarse/reordenarse/ampliarse. | Pendiente |
| 2 | Clasificación de movimientos en Fondos | `06-REGLAS-CONTABLES-Y-FINANCIERAS.md`, sección 2 | Si las cuatro clasificaciones (Costo, Venta, Distribución, Mantenimiento) son exhaustivas y mutuamente excluyentes para todo movimiento de capital real de Polar Breeze, o falta alguna categoría. | Pendiente |
| 3 | Tratamiento de Cuentas por Pagar y pagos parciales | `06-REGLAS-CONTABLES-Y-FINANCIERAS.md`, sección 5 | Si el criterio propuesto (obligación original inmutable, saldo pendiente como proyección de obligación menos pagos) coincide con la política real de pagos a proveedores/transportistas/consignatarios. | Pendiente |
| 4 | Definición de periodo contable y reglas de cierre | `06-REGLAS-CONTABLES-Y-FINANCIERAS.md`, sección 7 | Cuál es la periodicidad real de cierre (mensual, quincenal, otra) — el documento fija el principio de no retroactividad, pero no define la periodicidad, que depende de la operación real del negocio. | Pendiente |
| 5 | Tolerancia de diferencias en arqueo manual | `06-REGLAS-CONTABLES-Y-FINANCIERAS.md`, sección 8; `07-FLUJOS-DE-NEGOCIO.md`, F11 | Si existe un umbral de diferencia aceptable antes de requerir investigación formal, o toda diferencia (por mínima que sea) debe investigarse. | Pendiente |
| 6 | Correspondencia de `Cuenta` con el catálogo técnico | `11-DICCIONARIO-DE-DATOS.md`, sección 5 (`Cuenta`) | Que el campo `número` (entero 1 a 6) y `naturaleza` sigan siendo suficientes una vez validados los ítems 1-5, o si se requieren campos contables adicionales (por ejemplo, cuenta contable externa/SAT, centro de costo). | Pendiente |

## Cómo cerrar cada ítem

1. El contador o responsable financiero revisa el ítem contra la operación real de Polar Breeze.
2. Si confirma la propuesta tal cual, o la ajusta, el ajuste se registra como una nueva decisión en `DECISIONES-ARQUITECTURALES.md` (nunca editando en silencio `06-REGLAS-CONTABLES-Y-FINANCIERAS.md`).
3. Una vez validado, la fila correspondiente de este checklist pasa de **Pendiente** a **Validado**, con la fecha y referencia a la decisión.
4. Cuando los 6 ítems estén en **Validado**, el Estado de `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` pasa de "Borrador" a "Vigente", y la Fase 2 del `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` queda desbloqueada.

## Relación con Otros Documentos

- `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` — el documento cuyo estado Borrador depende de este checklist.
- `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` (Fase 2) — bloqueada mientras este checklist no se cierre.
- `DECISIONES-ARQUITECTURALES.md` — donde se registra cada validación conforme ocurre.

Observaciones:

Este anexo no reemplaza al Borrador de `06`; lo complementa como lista de tareas concreta y verificable. Ningún ítem de este checklist se marca como validado sin la decisión correspondiente registrada.
