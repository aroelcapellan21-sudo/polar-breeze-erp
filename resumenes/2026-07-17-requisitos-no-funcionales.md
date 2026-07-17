# Pendiente #7 — Requisitos No Funcionales, implementado

Fecha: 2026-07-17
Estado: Implementado en el árbol de trabajo, **sin commitear ni pushear todavía** — pendiente de tu aprobación.

Para ver el diff completo: `git diff` / `git status --short`. Archivos nuevos: `docs/14-REQUISITOS-NO-FUNCIONALES.md`.

## Qué se hizo

- **`docs/14-REQUISITOS-NO-FUNCIONALES.md`** (documento nuevo): respaldos/recuperación, retención de datos, privacidad de datos personales, volumetría/escalabilidad y disponibilidad — todo a nivel de principio arquitectónico, sin cifras ni ley específica asumidas. Sección 1 fija explícitamente que el ERP es un producto multiempresa para clientes futuros, no solo Polar Breeze. Sección 5 documenta la volumetría como rango de diseño escalable con checklist de qué medir al onboardear un cliente real. Sección 7 consolida un checklist de 5 validaciones pendientes (retención legal, régimen de privacidad, volumetría real, RPO/RTO, SLA), ninguna bloqueante.
- **`README.md`** — se agrega `14` al árbol; se corrige el árbol de `anexos/` (le faltaba el `02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md`, creado en v0.36 pero nunca agregado).
- **`docs/anexos/README.md`** — se corrige la descripción del anexo 01 (todavía decía "ítems 1-6 bloquean", desactualizado desde la sesión anterior donde 1 y 6 pasaron a "No aplica").
- **`docs/03-ARQUITECTURA-GENERAL.md`** — se agrega referencia cruzada a `14` en "Relación con Otros Documentos".
- **`docs/13-HISTORIAL-DE-VERSIONES.md`** — nueva entrada v0.41 (MENOR); tabla de completitud actualizada.
- **`DECISIONES-ARQUITECTURALES.md`** — nueva decisión "Requisitos no funcionales como criterios de diseño escalable, no cifras de un cliente".

## Pendiente de tu decisión

Ninguno bloqueante. Si apruebas, el siguiente paso es commit y push a `main`.
