# Pendiente #11 — Artículo 31 (Proceso de Aprobación Formal), implementado

Fecha: 2026-07-17
Estado: Implementado en el árbol de trabajo, **sin commitear ni pushear todavía** — pendiente de tu aprobación.

Para ver el diff completo: `git diff` / `git status --short`. 3 archivos modificados.

## Qué se hizo

- **`docs/02-CONSTITUCION-ERP.md`** — nuevo **Artículo 31 — Proceso de Aprobación Formal de la Documentación** (31.1 a 31.6), elevando a regla constitucional exactamente lo ya decidido en v0.42 (doble autoridad, aprobación por baseline, re-aprobación solo ante cambios MAYOR, distinción con el Artículo 14). La Constitución pasa de 30 a 31 artículos. Su propio Estado se actualiza como **re-aprobación** (Arquitecto/Product Owner + Oliver), aplicando de inmediato el mecanismo que el propio Artículo 31.4 exige para cambios MAYOR.
- **`docs/13-HISTORIAL-DE-VERSIONES.md`** — nueva entrada v0.43 (MAYOR); nueva fila en "Registro de Aprobaciones" para la re-aprobación de `02-CONSTITUCION-ERP.md`; se rellenó el hash de v0.42 (`3d6dc0e`), que había quedado como placeholder.
- **`DECISIONES-ARQUITECTURALES.md`** — nueva decisión "Formalización del Artículo 31", siguiendo los 3 pasos de la "Convención de Cambios a esta Constitución" (registrar antes, evaluar impacto en `08-CATALOGO-DE-MODULOS.md` — ninguno —, y solo entonces editar la Constitución).

## Pendiente de tu decisión

Ninguno bloqueante. Si apruebas, el siguiente paso es commit y push a `main`.
