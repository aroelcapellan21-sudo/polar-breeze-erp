# Decisiones Arquitecturales

Estado:

> En construcción

## Objetivo

Registrar todas las decisiones importantes de arquitectura del ERP Polar Breeze: por qué se eligió una tecnología, por qué cambió un modelo de datos, por qué se modificó un flujo, etc.

Este historial es tan valioso como el propio código: facilita la incorporación de nuevos desarrolladores o asistentes de IA al proyecto y evita que se repitan discusiones ya resueltas.

## Formato sugerido por decisión

```
### [Fecha] Título de la decisión

**Contexto:**
(¿Qué problema o disyuntiva motivó esta decisión?)

**Decisión:**
(¿Qué se decidió?)

**Alternativas consideradas:**
(¿Qué otras opciones se evaluaron y por qué se descartaron?)

**Consecuencias:**
(¿Qué implica esta decisión hacia adelante?)
```

## Historial de decisiones

### [2026-07-13] Arquitectura multiempresa desde el origen

**Contexto:**
El ERP necesitaba definirse desde el inicio como un sistema para una sola empresa o como un sistema multiempresa (multi-tenant). Postergar esta decisión habría obligado a reescribir el modelo de datos, las reglas de aislamiento y buena parte de los módulos una vez construidos sobre un supuesto de empresa única.

**Decisión:**
El ERP Polar Breeze se diseña multiempresa desde el día uno. Toda entidad de datos pertenece a una empresa (`empresa_id` o equivalente), el aislamiento entre empresas es obligatorio en datos, configuración y flujo, y ningún módulo puede asumir que existe una sola empresa en el sistema. Este principio quedó formalizado como Artículo 0 de `docs/02-CONSTITUCION-ERP.md`, de rango superior al resto de las reglas.

**Alternativas consideradas:**
Construir primero para una sola empresa y migrar a multiempresa más adelante. Se descartó porque el costo de retrofitting del aislamiento de datos (particionado, seguridad, catálogos "globales" que en realidad no lo son) es mucho mayor que diseñarlo desde el modelo de datos inicial.

**Consecuencias:**
Todo módulo nuevo debe declarar explícitamente cómo respeta el aislamiento multiempresa antes de ser aprobado para desarrollo (Artículo 0.6 de la Constitución). El modelo de datos maestro (`05-MODELO-DE-DATOS-MAESTRO.md`) deberá reflejar `empresa_id` como llave de partición desde su primera versión.

> **Nota de reemplazo (2026-07-13):** esta decisión queda superada en su nomenclatura y ubicación por la decisión "Reescritura extendida de la Constitución y estandarización de `empresaId`/`sucursalId`" registrada más abajo. El principio de fondo (multiempresa desde el origen) se mantiene sin cambios; lo que cambia es la convención de nombres y la numeración del artículo que lo formaliza.

### [2026-07-13] Reescritura extendida de la Constitución y estandarización de `empresaId`/`sucursalId`

**Contexto:**
La primera versión de `02-CONSTITUCION-ERP.md` cubría un conjunto inicial de reglas (identidad, datos, procesos operativos, plataforma, gobernanza) usando `empresa_id` en snake_case. El proyecto requería una Constitución de nivel "arquitectura empresarial" que cubriera explícitamente: fuente única de verdad, prohibición de duplicar información, arquitectura basada en eventos, arquitectura basada en flujos patrimoniales, trazabilidad, auditoría, soft delete, integridad referencial, versionado de datos, seguridad por roles, motor de permisos, inmutabilidad de documentos aprobados, catálogo de eventos, catálogos maestros, y reglas específicas de inventario, finanzas, contabilidad, cuentas por pagar, consignación, cuarto frío, despacho, exportación, reportes, IA, integraciones futuras, crecimiento del ERP y nuevos módulos.

**Decisión:**
Se reescribió `02-CONSTITUCION-ERP.md` completo, organizado en 29 artículos numerados, cubriendo todos los temas anteriores. Se estandarizó la nomenclatura multiempresa a **`empresaId`** y, cuando aplica a nivel de sede/unidad operativa, **`sucursalId`** (camelCase, consistente con el resto del diccionario de datos), reemplazando la referencia previa a `empresa_id`. El Estado del documento pasó de "En construcción" a "Vigente — sujeta a enmienda formal mediante `DECISIONES-ARQUITECTURALES.md`", dado que ahora cubre el alcance mínimo exigido para gobernar el desarrollo de módulos.

**Alternativas consideradas:**
Mantener la Constitución como un documento breve de principios generales y mover el detalle de cada regla (inventario, contabilidad, permisos, etc.) únicamente a sus documentos específicos (`06`, `08`, etc.). Se descartó porque el propósito explícito de la Constitución es fijar las reglas que **ningún módulo puede romper**; dejar esas reglas solo en documentos de detalle debilita su carácter inquebrantable y dificulta detectar violaciones al revisar un módulo nuevo.

**Consecuencias:**
- Todo módulo nuevo se evalúa contra los 29 artículos antes de aprobarse (Artículo 29.2).
- El diccionario de datos (`11-DICCIONARIO-DE-DATOS.md`) y el modelo de datos maestro (`05-MODELO-DE-DATOS-MAESTRO.md`) deben usar `empresaId`/`sucursalId` como convención de nombres, no `empresa_id`.
- Cualquier documento futuro que mencione el aislamiento multiempresa debe alinearse a esta nomenclatura.
- El catálogo de eventos (`docs/diagramas/eventos.drawio`) y `12-GLOSARIO.md` quedan como referencia obligatoria para el Artículo 15 (Eventos del Sistema).
