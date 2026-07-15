# Estado del Trabajo y Pendientes para Retomar

Fecha: 2026-07-14
Último commit en `main`: `e947f0d` (rama al día con `origin/main`, árbol de trabajo limpio).

## Contexto de esta sesión

Se partió de una evaluación completa del repositorio (`resumenes/2026-07-14-evaluacion-completa-documentacion.md`), que identificó 5 prioridades y una lista de pendientes secundarios. Se ejecutaron, en orden, las 5 prioridades y luego 3 de los pendientes de la lista, cada uno como su propio commit ya publicado en `main`:

| Commit | Qué resolvió |
|---|---|
| `cb61ea1` | Prioridad 1 — Entidades `Cliente`, `Proveedor`, `Obligacion` en el modelo de datos. |
| `97f67d0` | Prioridad 2 — Campo `moneda` (ISO 4217) en `Empresa`, moneda funcional por empresa. |
| `5e9b263` | Prioridad 3 — `08-CATALOGO-DE-MODULOS.md` elevado al estándar del Artículo 29.2. |
| `eab5539` | Prioridad 4 — Jerarquía Constitución > Visión fijada; colisión de prefijo `00` formalizada (ambas consultadas al usuario). |
| `445d5dc` | Prioridad 5 — Limpieza de referencias cruzadas rotas (Artículo 1.3 mal citado, etc.) y alineación de estado de `09`. |
| `425f8e2` | Revisión final — sincronización de `10-PLAN-MAESTRO` con los 7 catálogos ya agregados. |
| `5e86e1f` | Documento de revisión final post-correcciones (`resumenes/2026-07-14-revision-final-post-correcciones.md`), que generó la lista de pendientes 1-10 usada después. |
| `2560181` | Pendiente #3 — Campo duplicado `Cuenta.número` vs `Cuenta.código` eliminado. |
| `0f61a9c` | Pendiente #4 — `NovedadInventario.tipo` separado en ubicación (`sucursalId`) vs condición (enum). |
| `135caa5` | Pendiente #5 — Flujo F13 y entidad `BajaInventario` (merma/pérdida/condonación), Artículo 6.3 respaldado estructuralmente. |
| `e947f0d` | Pendiente #6 — Mecanismo de detección y resolución de conflictos de sincronización offline (`ConflictoSincronizacion`, evento `ConflictoSincronizacionDetectado`). |

## Pendientes que NO se han abordado todavía

De la lista original de `resumenes/2026-07-14-revision-final-post-correcciones.md`, quedan sin tocar:

1. **Pendiente #7 — Requisitos no funcionales.** No existe ningún documento que cubra respaldos/recuperación, retención de datos, privacidad de datos personales (usuarios, clientes), volumetría esperada, ni disponibilidad. Sería contenido nuevo, probablemente un documento nuevo o una sección nueva en `03-ARQUITECTURA-GENERAL.md`.

2. **Pendiente #8 — Proceso de aprobación formal.** Casi todos los documentos dicen "Vigente — pendiente de revisión y aprobación formal", pero ningún documento define quién aprueba, cómo, ni cómo se registra esa aprobación. Requiere decidir un mecanismo (¿un campo en `13-HISTORIAL-DE-VERSIONES.md`? ¿una sección nueva en `DECISIONES-ARQUITECTURALES.md`? ¿un anexo?).

Además, siguen abiertos (documentados como exclusiones de alcance deliberadas, no como descuidos):

3. **Cliente no vinculado a Factura** (ventas a crédito) — decisión de negocio pendiente de confirmar con el usuario, no una corrección de modelo.
4. **Plan de cuentas de `06-REGLAS-CONTABLES-Y-FINANCIERAS.md`** sigue en Borrador — bloqueado por el anexo `01-PENDIENTE-VALIDACION-CONTABLE.md` (ahora 7 ítems, todos Pendiente; requiere un contador real).
5. **Tratamiento contable de `BajaInventario`** (ítem 7 del anexo, agregado en esta sesión) — no bloquea ninguna fase, pero sigue sin resolver.
6. **6 diagramas `.drawio`** sin contenido visual — excluido explícitamente por instrucción previa del usuario.
7. **Multi-moneda dentro de una misma empresa** (cuentas en divisa extranjera, tipo de cambio) — excluido deliberadamente al diseñar el campo `moneda`, documentado como extensión futura.

## Cómo retomar

Para continuar, la instrucción natural sería: *"continúa con el pendiente 7"* (requisitos no funcionales) o *"continúa con el pendiente 8"* (proceso de aprobación formal). Ambos requieren diseñar contenido nuevo (no son correcciones de referencia), siguiendo el mismo patrón ya usado en esta sesión: identificar el gap, diseñar la solución mínima consistente con lo ya establecido, registrar la decisión en `DECISIONES-ARQUITECTURALES.md`, actualizar `13-HISTORIAL-DE-VERSIONES.md`, y comitear/publicar.
