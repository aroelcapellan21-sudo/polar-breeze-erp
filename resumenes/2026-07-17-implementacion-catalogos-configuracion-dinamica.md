# Implementación — Catálogos de Configuración Dinámica (Hub Admin)

Fecha: 2026-07-17
Estado: Implementado en el árbol de trabajo, **sin commitear ni pushear todavía** — pendiente de tu revisión final.
Plan aprobado: `resumenes/2026-07-17-plan-catalogos-configuracion-dinamica.md`.

Para ver el diff completo: `git diff` (o `git diff --stat` para el resumen por archivo).

## Respuestas a las preguntas abiertas, ya aplicadas

1. `MotivoSalidaSinCobro` se sembró con los 6 valores: merma, pérdida, condonación, donación, bonificación, refrigerio.
2. `AporteCapital` es puramente informativo — no genera `MovimientoCapital` ni afecta ningún `Fondo`.

## Cambios por archivo

- **`docs/05-MODELO-DE-DATOS-MAESTRO.md`** — `Cuenta` abierta (ya no "1"-"6"); `CuentaBancaria` gana `alias`; nuevas entidades `AporteCapital` (Módulo 1) y `MotivoSalidaSinCobro` (catálogos compartidos); `BajaInventario.tipo` pasa de Enumeración a Referencia; regla de integridad referencial agregada; Observaciones actualizadas.
- **`docs/11-DICCIONARIO-DE-DATOS.md`** — mismos cambios a nivel de campo/tipo, más nota de convención Firestore (`config/{empresaId}/<colección>/{código}`) en la sección de Catálogos Maestros Compartidos.
- **`docs/03-ARQUITECTURA-GENERAL.md`** — sección 2: Hub Admin formalizado. Sección 7: se retiran los catálogos maestros (quedan solo entidades transaccionales). Sección 8: se listan explícitamente todos los catálogos maestros bajo `config/*`, con nota de la inconsistencia corregida.
- **`docs/06-REGLAS-CONTABLES-Y-FINANCIERAS.md`** — sección 3 (plan de cuentas) pasa de propuesta cerrada a datos semilla; Estado y Observaciones aclaran que solo los ítems 2-5 del anexo siguen bloqueando (el documento sigue en Borrador).
- **`docs/08-CATALOGO-DE-MODULOS.md`** — nueva sección "Gobernanza de Catálogos de Configuración Dinámica"; Módulo 1 (Cuenta abierta + `AporteCapital`, distinto del pendiente Participación de Capital), Módulo 3 (consume `MotivoSalidaSinCobro`), Módulo 6 (crea `MotivoSalidaSinCobro`, distinto del pendiente Crear Novedades).
- **`docs/09-ESTANDARES-DE-DESARROLLO.md`** — regla 4 aclara que incluye catálogos maestros.
- **`docs/anexos/01-PENDIENTE-VALIDACION-CONTABLE.md`** — ítems 1 y 6 pasan a "No aplica" (resueltos por arquitectura); ítems 2, 3, 4, 5, 7 siguen Pendientes.
- **`docs/10-PLAN-MAESTRO-DE-IMPLEMENTACION.md`** (no listado en el plan original, actualizado por consistencia directa) — Fase 1 incluye los 2 catálogos nuevos; el bloqueante de Fases 2-3 se reformula a los ítems 2 y 3 del anexo (ya no "todo el plan de cuentas").
- **`DECISIONES-ARQUITECTURALES.md`** — nueva decisión "Catálogos de Configuración Dinámica (Hub Admin) y unificación de catálogos maestros bajo `config/*`".
- **`docs/13-HISTORIAL-DE-VERSIONES.md`** — nueva entrada v0.40 (MAYOR); tabla de completitud actualizada; se rellenó el hash de v0.39 (`b588fe3`), que había quedado como placeholder.

## Pendiente de tu decisión

Ninguno bloqueante — el plan se implementó completo según lo aprobado. Si confirmas, el siguiente paso es el commit y push.
