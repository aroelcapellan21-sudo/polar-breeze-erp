# Estado del Trabajo y Pendientes para Retomar

Fecha: 2026-07-17
Último commit en `main`: `3d6dc0e` (rama al día con `origin/main`, árbol de trabajo limpio).

## Contexto de esta sesión

Se retomó desde `resumenes/2026-07-14-estado-y-pendientes-para-retomar.md`, que dejaba dos pendientes originales sin abordar (#7 y #8) más una lista de exclusiones deliberadas. Antes de tocar los pendientes, se ejecutó una tarea adicional pedida por el usuario (no listada en el estado anterior) y luego los dos pendientes, cada uno como su propio commit ya publicado en `main`:

| Commit | Qué resolvió |
|---|---|
| `8a0c3ce` | 4 catálogos de configuración dinámica (Hub Admin): Cuentas Bancarias, Capital de la Empresa (`AporteCapital`), Cuentas Contables (`Cuenta` abierta, ya no fija a "1"-"6") y Motivos de Salida sin Cobro (`MotivoSalidaSinCobro`, reemplaza el enum de `BajaInventario.tipo`). Unificó todos los catálogos maestros bajo Firestore `config/{empresaId}/<colección>/{código}` y formalizó el Hub Admin en `03-ARQUITECTURA-GENERAL.md`. |
| `08bb640` | Pendiente #7 — `docs/14-REQUISITOS-NO-FUNCIONALES.md` (documento nuevo): respaldos, retención, privacidad, volumetría/escalabilidad y disponibilidad, a nivel de principio, sin cifras ni ley específica. Volumetría documentada como rango de diseño escalable (el ERP es producto multiempresa para clientes futuros, no solo Polar Breeze), con checklist de qué medir al onboardear un cliente real. |
| `3d6dc0e` | Pendiente #8 — Proceso de aprobación formal: doble autoridad (Arquitecto/Product Owner vs. Oliver, según si el documento es técnico o de negocio, o ambos si mezcla). Aprobación de todo el baseline como un solo evento sobre `v0.41`; re-aprobación individual solo ante cambios MAYOR. Registro centralizado en la nueva sección "Registro de Aprobaciones" de `13-HISTORIAL-DE-VERSIONES.md`. |

Versión actual de la documentación: **v0.42**.

## Pendientes que NO se han abordado todavía

De la lista original de `resumenes/2026-07-14-estado-y-pendientes-para-retomar.md`, ya se cerraron los ítems #7 y #8. Siguen abiertos, documentados como exclusiones deliberadas (no descuidos):

1. **Cliente no vinculado a Factura** (ventas a crédito) — decisión de negocio pendiente de confirmar con el usuario/Oliver.
2. **Plan de cuentas de `06-REGLAS-CONTABLES-Y-FINANCIERAS.md`** sigue en Borrador — bloqueado por los ítems 2, 3, 4 y 5 del anexo `01-PENDIENTE-VALIDACION-CONTABLE.md` (clasificación de Fondos, reglas de CxP, periodo contable, tolerancia de arqueo), todos Pendiente, requieren un contador real. Los ítems 1 y 6 ya se resolvieron en v0.40 (catálogo `Cuenta` abierto). `06` tampoco tiene todavía la aprobación formal de Oliver (excluida explícitamente del pendiente #8 hasta que el anexo se cierre).
3. **Tratamiento contable de `BajaInventario`** (ítem 7 del anexo, no bloquea ninguna fase) — sigue sin resolver.
4. **6 diagramas `.drawio`** sin contenido visual — excluido explícitamente por instrucción previa del usuario.
5. **Multi-moneda dentro de una misma empresa** — excluido deliberadamente, documentado como extensión futura.
6. **Participación de Capital** (Módulo 1, Bancos) — sigue sin modelar; el usuario confirmó que la mención de Oliver es demasiado ambigua sin más contexto. Distinto de `AporteCapital` (agregado en v0.40), que no lo resuelve.
7. **Nota de crédito de proveedor** (Módulo 2) — sin modelar.
8. **Catálogo configurable de tipos de Novedad** (Módulo 6, "Crear Novedades" — `NovedadInventario`/`NovedadDespacho.tipo`) — sin resolver; distinto de `MotivoSalidaSinCobro` (agregado en v0.40), que no lo resuelve.
9. **Plantilla de Consignación** (Módulo 6, "Crear Consignación", puntos o lotes nuevos) — sin resolver.
10. **Checklist de `14-REQUISITOS-NO-FUNCIONALES.md`, sección 7** (5 ítems, ninguno bloqueante): retención legal, régimen de protección de datos, volumetría real de un cliente en operación, RPO/RTO de infraestructura, SLA de disponibilidad del backend/API.
11. **Artículo 31 de la Constitución** (formalizar el proceso de aprobación del pendiente #8 como regla constitucional) — el usuario pidió explícitamente NO hacerlo todavía; queda como tarea futura a su decisión, en sesión aparte.

## Cómo retomar

No hay una instrucción única "obvia" esta vez — los pendientes 1-3 (Cliente/Factura, validación contable, tratamiento de bajas) requieren un contador o al propio Oliver, no son tareas de modelado que se puedan resolver solo con el usuario actual. Los ítems 6-9 (Participación de Capital, nota de crédito de proveedor, catálogo de Novedades, plantilla de Consignación) son modelado de datos pendiente, mismo patrón ya usado en sesiones anteriores (identificar el gap, diseñar la solución mínima, registrar en `DECISIONES-ARQUITECTURALES.md`, actualizar `13-HISTORIAL-DE-VERSIONES.md`, comitear/publicar). El ítem 11 (Artículo 31) es la tarea más simple y autocontenida si se quiere retomar algo rápido.
