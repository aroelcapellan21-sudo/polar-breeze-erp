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
