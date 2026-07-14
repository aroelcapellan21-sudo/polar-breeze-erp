# Resumen Completo del Repositorio — polar-breeze-erp

Fecha: 2026-07-14
Repositorio: https://github.com/aroelcapellan21-sudo/polar-breeze-erp
Rama: main — 21 commits — último commit: `937972d`
Estado de sincronización: local y remoto (`origin/main`) coinciden, árbol de trabajo limpio.

## Qué es este repositorio

Biblioteca de arquitectura exclusivamente documental del ERP Polar Breeze — sin código, componentes, APIs ni frontend. Diseñado desde el origen como **multiempresa** (`empresaId`/`sucursalId` en toda entidad), organizado alrededor de tres **flujos patrimoniales**: capital, mercancía e información. Polar Breeze es la primera empresa del ecosistema, no el límite del diseño.

## Estructura completa y estado de cada archivo

### Raíz

| Archivo | Estado |
|---|---|
| `README.md` | Vigente — describe propósito documental y arquitectura multiempresa. |
| `CLAUDE.md` | Vigente — regla permanente: resúmenes >20 líneas van a `resumenes/`. |
| `DECISIONES-ARQUITECTURALES.md` | Vigente, en crecimiento continuo — 21 decisiones registradas. |

### docs/ — 15 documentos numerados (todos con contenido completo)

| Doc | Título | Estado |
|---|---|---|
| 00 | Principios del ERP | Vigente — 12 principios |
| 01 | Visión del ERP | Vigente — redactado sin documento fuente externo |
| 02 | Constitución ERP | Vigente — **30 artículos** (incl. Art. 30, Huella Permanente) |
| 03 | Arquitectura General | Vigente — 7 capas |
| 04 | Motor de Flujos Patrimoniales | Vigente |
| 05 | Modelo de Datos Maestro | Vigente — ~24 entidades |
| 06 | Reglas Contables y Financieras | **Borrador** — plan de cuentas sin validar por contador |
| 07 | Flujos de Negocio | Vigente — 12 flujos (F1-F12) |
| 08 | Catálogo de Módulos | Vigente — 5 módulos |
| 09 | Estándares de Desarrollo | Vigente |
| 10 | Plan Maestro de Implementación | Vigente — 9 fases |
| 11 | Diccionario de Datos | Vigente |
| 12 | Glosario | Vigente — 20 eventos formalizados |
| 13 | Historial de Versiones | Vigente — v0.1 a v0.20 |
| 99 | Filosofía del Sistema | Vigente |

### docs/diagramas/ y docs/anexos/

| Archivo | Estado |
|---|---|
| `docs/diagramas/README.md` | Vigente — lista los 6 diagramas y qué documento complementa cada uno. |
| `docs/diagramas/*.drawio` (6) | En blanco a propósito, estructura mínima válida, listos para draw.io. |
| `docs/diagramas/imagenes/.gitkeep` | Sin cambios. |
| `docs/anexos/README.md` | Sección "Contenido" vacía a propósito — no hay anexos reales todavía. |

### resumenes/ (carpeta de trabajo, fuera de la biblioteca oficial)

| Archivo | Contenido |
|---|---|
| `2026-07-14-archivos-creados-y-modificados.md` | Inventario de archivos con su estado. |
| `2026-07-14-constitucion-y-sus-articulos.md` | Evolución de la Constitución (6 → 29 → 30 artículos). |
| `2026-07-14-resumen-completo-del-repositorio.md` | Este mismo documento. |

## Pendientes conocidos

1. Validación contable formal del plan de cuentas de `06` (bloquea Fase 2 del plan de implementación).
2. Contenido visual de los 6 `.drawio` (fuera de alcance de este flujo de texto).
3. `docs/anexos/README.md` cuando exista al menos un anexo real.

## Gobernanza

Toda decisión relevante está registrada en `DECISIONES-ARQUITECTURALES.md` antes de haberse implementado. Ninguna decisión ya registrada se edita retroactivamente — se reemplaza dejando constancia explícita (Artículo 14.3 de la Constitución).
