# Estado del Trabajo y Pendientes para Retomar

Fecha: 2026-07-17
Último commit en `main`: `b9e2c4a` (rama al día con `origin/main`, árbol de trabajo limpio).

## Contexto de esta sesión

Continuación directa de `resumenes/2026-07-17-estado-y-pendientes-para-retomar.md` (guardado unas horas antes, commit `b2efe34`). Se retomó el pendiente #11 de esa lista:

| Commit | Qué resolvió |
|---|---|
| `b9e2c4a` | Pendiente #11 — Se agrega el **Artículo 31 — Proceso de Aprobación Formal de la Documentación** (31.1 a 31.6) a `02-CONSTITUCION-ERP.md`, elevando a regla constitucional el proceso ya decidido en v0.42 (doble autoridad Arquitecto/Product Owner vs. Oliver, aprobación por baseline, re-aprobación solo ante cambios MAYOR). La Constitución pasa de 30 a 31 artículos. Por ser un cambio MAYOR a la propia Constitución, se aplicó de inmediato el Artículo 31.4: `02-CONSTITUCION-ERP.md` se re-aprobó por ambas autoridades, registrado en el "Registro de Aprobaciones" de `13-HISTORIAL-DE-VERSIONES.md` (v0.43). |

Versión actual de la documentación: **v0.43**.

## Pendientes que NO se han abordado todavía

Idéntica a la lista de `resumenes/2026-07-17-estado-y-pendientes-para-retomar.md`, menos el ítem #11 (ya cerrado arriba):

1. **Cliente no vinculado a Factura** (ventas a crédito) — decisión de negocio pendiente de confirmar con el usuario/Oliver.
2. **Plan de cuentas de `06-REGLAS-CONTABLES-Y-FINANCIERAS.md`** sigue en Borrador — bloqueado por los ítems 2, 3, 4 y 5 del anexo `01-PENDIENTE-VALIDACION-CONTABLE.md` (clasificación de Fondos, reglas de CxP, periodo contable, tolerancia de arqueo), todos Pendiente, requieren un contador real. `06` tampoco tiene todavía la aprobación formal de Oliver (Artículo 31 — excluida hasta que el anexo se cierre).
3. **Tratamiento contable de `BajaInventario`** (ítem 7 del anexo, no bloquea ninguna fase) — sigue sin resolver.
4. **6 diagramas `.drawio`** sin contenido visual — excluido explícitamente por instrucción previa del usuario.
5. **Multi-moneda dentro de una misma empresa** — excluido deliberadamente, documentado como extensión futura.
6. **Participación de Capital** (Módulo 1, Bancos) — sigue sin modelar; demasiado ambigua sin más contexto de Oliver. Distinto de `AporteCapital` (v0.40), que no lo resuelve.
7. **Nota de crédito de proveedor** (Módulo 2) — sin modelar.
8. **Catálogo configurable de tipos de Novedad** (Módulo 6, "Crear Novedades" — `NovedadInventario`/`NovedadDespacho.tipo`) — sin resolver; distinto de `MotivoSalidaSinCobro` (v0.40), que no lo resuelve.
9. **Plantilla de Consignación** (Módulo 6, "Crear Consignación", puntos o lotes nuevos) — sin resolver.
10. **Checklist de `14-REQUISITOS-NO-FUNCIONALES.md`, sección 7** (5 ítems, ninguno bloqueante): retención legal, régimen de protección de datos, volumetría real de un cliente en operación, RPO/RTO de infraestructura, SLA de disponibilidad del backend/API.

## Cómo retomar

Los ítems 1-3 (Cliente/Factura, validación contable, tratamiento de bajas) requieren un contador o al propio Oliver, no son tareas de modelado resolubles solo con el usuario actual. Los ítems 6-9 (Participación de Capital, nota de crédito de proveedor, catálogo de Novedades, plantilla de Consignación) son modelado de datos pendiente, mismo patrón ya usado repetidamente (identificar el gap, diseñar la solución mínima, registrar en `DECISIONES-ARQUITECTURALES.md`, actualizar `13-HISTORIAL-DE-VERSIONES.md`, comitear/publicar). No queda ninguna tarea de gobernanza documental abierta por ahora (los pendientes #7, #8 y #11 de la ronda anterior ya están cerrados).
