# Historial de Versiones

Estado:

> Vigente — se actualiza con cada cambio sustantivo a la documentación

Objetivo:

Registrar la evolución de la documentación arquitectónica del ERP Polar Breeze de forma cronológica y resumida, para saber qué existía en cada momento sin tener que reconstruirlo desde el historial de `git`. Este documento complementa a `DECISIONES-ARQUITECTURALES.md` (que explica el *por qué* de cada cambio relevante) con una vista rápida del *qué* y *cuándo*.

Contenido:

## Convención de Versionado

Mientras el repositorio contenga únicamente documentación (sin código de producto), se usa un esquema de **versión de documentación** `vMAYOR.MENOR`:

- **MAYOR** — cuando cambia una regla constitucional, se reestructura el modelo de datos, o se reemplaza (no se edita) una decisión previa (Artículo 14.3 de la Constitución).
- **MENOR** — cuando se agrega o completa contenido nuevo sin romper ni contradecir lo ya existente (por ejemplo, redactar el contenido completo de un documento que antes era placeholder).

Cuando exista código de producto, ese repositorio llevará su propio esquema de versionado semántico de software (a definir en su momento), independiente de este.

## Historial

| Versión | Fecha | Commit | Descripción |
|---|---|---|---|
| v0.1 | 2026-07-13 | `ab1f0f9` | Estructura inicial del repositorio: README, `DECISIONES-ARQUITECTURALES.md`, placeholders de `01` a `10`, carpetas `diagramas/` y `anexos/`. |
| v0.2 | 2026-07-13 | `9cef300` | Catálogo completo de los 5 módulos redactado en `08-CATALOGO-DE-MODULOS.md`. |
| v0.3 | 2026-07-13 | `9fa7a85` | Reglas de arquitectura y de construcción redactadas en `09-ESTANDARES-DE-DESARROLLO.md`. |
| v0.4 | 2026-07-13 | `721be71` | Estructura ampliada: placeholders de `00`, `11`, `12`, `13`, `99` y diagramas `.drawio` vacíos creados. Primera versión de `02-CONSTITUCION-ERP.md` (6 artículos), con la arquitectura multiempresa como principio fundacional. |
| v0.5 (MAYOR) | 2026-07-13 | `1108020` | `02-CONSTITUCION-ERP.md` reescrito por completo a 29 artículos; estandarización de nomenclatura a `empresaId`/`sucursalId`; README actualizado para reflejar la arquitectura multiempresa basada en flujos patrimoniales. |
| v0.6 | 2026-07-14 | `56c45b1` | `01-VISION-ERP.md` redactado por completo (16 secciones), a partir de un índice provisto por el usuario, sin documento fuente externo disponible. |
| v0.7 | 2026-07-14 | `5aec637` | `00-PRINCIPIOS-DEL-ERP.md` redactado: 12 principios con fundamento e implicaciones prácticas. |
| v0.8 | 2026-07-14 | `a765ace` | `03-ARQUITECTURA-GENERAL.md` redactado: arquitectura en 7 capas. |
| v0.9 | 2026-07-14 | `f096f1d` | `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` redactado: contrato de comportamiento del motor central. |
| v0.10 | 2026-07-14 | `e9e2e3d` | `05-MODELO-DE-DATOS-MAESTRO.md` redactado: entidades derivadas de los 5 módulos. |
| v0.11 | 2026-07-14 | `d51bd65` | `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` redactado, en estado **Borrador**: incluye una propuesta de plan de cuentas (Cuentas 1-6) pendiente de validación por un contador. |
| v0.12 | 2026-07-14 | `96018d6` | `07-FLUJOS-DE-NEGOCIO.md` redactado: 12 flujos de negocio de extremo a extremo (F1-F12). |
| v0.13 | 2026-07-14 | `7add52d` | `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` redactado: 9 fases de implementación con dependencias y criterios de salida. |
| v0.14 | 2026-07-14 | `1e86287` | `11-DICCIONARIO-DE-DATOS.md` redactado: detalle campo por campo de todas las entidades. |
| v0.15 | 2026-07-14 | `6f65e78` | `12-GLOSARIO.md` redactado: terminología consolidada y catálogo formal de los 20 eventos del sistema (Artículo 15 de la Constitución). |
| v0.16 | 2026-07-14 | `15d00c4` | `13-HISTORIAL-DE-VERSIONES.md` redactado. |
| v0.17 | 2026-07-14 | `51736ea` | `99-FILOSOFIA-DEL-SISTEMA.md` redactado: cierre reflexivo de la biblioteca, sin reglas ni entidades nuevas. Con esta entrada, todos los documentos numerados `00`-`13` y `99` quedan con contenido completo. |
| v0.18 (MAYOR) | 2026-07-14 | `4861440` | Se agrega el **Artículo 30 — Principio de Huella Permanente** a `02-CONSTITUCION-ERP.md`: todo dato deja huella permanente e inmutable, con contenido mínimo definido, incluyendo decisiones automáticas de sistema o IA. La Constitución pasa de 29 a 30 artículos. |
| v0.19 | 2026-07-14 | `89128be` | `docs/diagramas/README.md` completado con la lista y propósito de los 6 diagramas `.drawio`. `docs/anexos/README.md` se deja sin cambios deliberadamente: la carpeta no tiene anexos reales todavía. |
| v0.20 | 2026-07-14 | `89302fb` | Se crea `CLAUDE.md` con la regla permanente de entrega de resúmenes largos (>20 líneas) vía archivo en `resumenes/` en lugar de mostrarse completos en terminal. Se crea la carpeta `resumenes/` con su primer inventario de archivos. |
| v0.21 | 2026-07-14 | — (este commit) | Se crea el primer anexo real, `docs/anexos/VALIDACIONES-PENDIENTES-CONTADOR.md` (checklist de 6 ítems contables pendientes de validación), y se actualiza `docs/anexos/README.md` para listarlo. |

## Estado de Completitud de la Documentación (a la fecha de la última entrada)

| Documento | Estado |
|---|---|
| `README.md` | Vigente |
| `DECISIONES-ARQUITECTURALES.md` | Vigente, en crecimiento continuo |
| `00-PRINCIPIOS-DEL-ERP.md` | Vigente |
| `01-VISION-ERP.md` | Vigente (redactado sin documento fuente externo; sujeto a reemplazo si aparece el original) |
| `02-CONSTITUCION-ERP.md` | Vigente |
| `03-ARQUITECTURA-GENERAL.md` | Vigente |
| `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` | Vigente |
| `05-MODELO-DE-DATOS-MAESTRO.md` | Vigente |
| `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` | **Borrador** — bloquea la Fase 2 del Plan Maestro hasta validación contable |
| `07-FLUJOS-DE-NEGOCIO.md` | Vigente |
| `08-CATALOGO-DE-MODULOS.md` | Vigente |
| `09-ESTANDARES-DE-DESARROLLO.md` | Vigente |
| `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` | Vigente |
| `11-DICCIONARIO-DE-DATOS.md` | Vigente |
| `12-GLOSARIO.md` | Vigente |
| `13-HISTORIAL-DE-VERSIONES.md` | Vigente (este documento) |
| `99-FILOSOFIA-DEL-SISTEMA.md` | Vigente |
| `docs/diagramas/*.drawio` (6 archivos) | Creados vacíos; pendientes de contenido visual |
| `docs/anexos/VALIDACIONES-PENDIENTES-CONTADOR.md` | Vigente — checklist con 6 ítems en estado Pendiente |

## Relación con Otros Documentos

- `DECISIONES-ARQUITECTURALES.md` — el razonamiento detrás de cada versión de este historial.
- `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` — el avance de fases de implementación (una vez inicien) se refleja como nuevas entradas aquí.

Observaciones:

Este documento se actualiza como parte del mismo commit que agrega o modifica contenido sustantivo en cualquier documento de `docs/`. No reemplaza al historial de `git` ni a `DECISIONES-ARQUITECTURALES.md` — los complementa con una vista cronológica resumida y de fácil consulta.
