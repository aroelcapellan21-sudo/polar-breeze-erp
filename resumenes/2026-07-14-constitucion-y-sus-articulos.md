# Resumen — Trabajo realizado sobre 02-CONSTITUCION-ERP.md

Fecha: 2026-07-14
Documento: `docs/02-CONSTITUCION-ERP.md`
Archivo relacionado: `DECISIONES-ARQUITECTURALES.md` (razonamiento completo de cada cambio)

## Evolución del documento

| Etapa | Commit | Qué cambió |
|---|---|---|
| 1. Primera versión | `721be71` | 6 artículos iniciales, con la arquitectura multiempresa como Artículo 0 fundacional. Nomenclatura `empresa_id` (snake_case). |
| 2. Reescritura completa | `1108020` | Reescrito de 6 a **29 artículos**. Nomenclatura estandarizada a `empresaId`/`sucursalId` (camelCase). Estado pasó de "En construcción" a "Vigente". |
| 3. Ampliación con Artículo 30 | `4861440` | Se agregó el **Artículo 30 — Principio de Huella Permanente** (30.1 a 30.5), texto provisto por el usuario. La Constitución pasó de 29 a **30 artículos**. |

## Estado actual: 30 artículos, agrupados por tema

1. Principios del ERP
2. Arquitectura Multiempresa desde el Primer Día
3. Una Sola Fuente de Verdad
4. Prohibición de Duplicar Información
5. Arquitectura Basada en Eventos
6. Arquitectura Basada en Flujos Patrimoniales
7. Trazabilidad Absoluta
8. Auditoría Obligatoria
9. Soft Delete
10. Integridad Referencial
11. Versionado de Datos
12. Seguridad por Roles
13. Motor de Permisos
14. Inmutabilidad de Documentos Aprobados
15. Eventos del Sistema
16. Catálogos Maestros
17. Reglas de Inventario
18. Reglas Financieras
19. Reglas Contables
20. Reglas de Cuentas por Pagar
21. Reglas de Consignación
22. Reglas de Cuarto Frío
23. Reglas de Despacho
24. Reglas de Exportación
25. Reglas de Reportes
26. Reglas para IA
27. Reglas para Futuras Integraciones
28. Reglas para Crecimiento del ERP
29. Reglas para Nuevos Módulos
30. **Principio de Huella Permanente** (agregado más recientemente)

## Nota sobre historial

Las referencias a "29 artículos" que quedaron en decisiones y en `13-HISTORIAL-DE-VERSIONES.md` correspondientes a la etapa 2 **no se editaron retroactivamente** — son registro histórico válido de ese momento (Artículo 14.3 de la propia Constitución: nada se edita en silencio, se complementa con una nueva entrada). El conteo vigente hoy es 30.
