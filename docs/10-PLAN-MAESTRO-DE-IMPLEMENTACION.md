# Plan Maestro de Implementación

Estado:

> Vigente — pendiente de revisión y aprobación formal

Objetivo:

Definir el orden en que se implementa el ERP Polar Breeze, desde los fundamentos arquitectónicos hasta el catálogo completo de módulos, de forma consistente con todo lo ya documentado (`00` a `09`). Este documento no describe código ni cronograma con fechas — describe **fases, dependencias y criterios de salida**, dejando la planificación temporal (fechas, sprints, recursos) al equipo de ejecución en el repositorio de código.

Contenido:

## 1. Principio de Secuenciación

El orden de implementación no es arbitrario: refleja las dependencias reales entre lo ya documentado. En particular:

- La **multiempresa** (Artículo 2 de la Constitución) no se agrega después — cualquier fase que la omita obliga a rehacer trabajo ya construido (Artículo 28.1).
- El **offline-first** (Principio 2) tampoco es una fase tardía — cada módulo se construye ya asumiendo captura local y sincronización, no se le "agrega" al final.
- Ningún módulo del `08-CATALOGO-DE-MODULOS.md` inicia su fase sin que su documentación esté aprobada (Artículo 29.1 de la Constitución).

## 2. Fase 0 — Fundamentos Arquitectónicos

**Objetivo:** construir la base sobre la que se apoya todo módulo posterior.

**Incluye:**
- Modelo de datos base: `Empresa`, `Usuario`, `Sucursal`, `Rol`, `Permiso` (`05-MODELO-DE-DATOS-MAESTRO.md`, secciones 2-3).
- Motor de Flujos Patrimoniales en su forma mínima: recepción, validación general, persistencia del historial de `Evento`, generación de `Registro de Auditoría` (`04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`).
- Motor de Permisos operando de forma centralizada (Artículo 13 de la Constitución).
- Capa de Persistencia Local y Sincronización offline-first (`03-ARQUITECTURA-GENERAL.md`, sección 3), probada en al menos un flujo de extremo a extremo antes de construir módulos sobre ella.
- Capa de Configuración de Plataforma (`config/*`, Artículo 16.3).

**Criterio de salida:** es posible crear una empresa, un usuario con rol, emitir un evento de prueba offline y verlo sincronizado, auditado y persistido de forma inmutable — sin que exista todavía ningún módulo de negocio.

## 3. Fase 1 — Catálogos Maestros Compartidos

**Objetivo:** construir los catálogos que todos los módulos consumirán, evitando la duplicación prohibida por el Artículo 4 de la Constitución.

**Incluye:** `Producto`, `Cuenta`, `Fondo`, `CuentaBancaria`, `Vendedor` (`05-MODELO-DE-DATOS-MAESTRO.md`, sección 4).

**Dependencia:** Fase 0 completa.

**Criterio de salida:** los cinco catálogos existen, particionados por `empresaId`, con código como clave de negocio (Principio 1), y ningún módulo posterior necesita reimplementarlos.

## 4. Fase 2 — Módulo 1: Flujo de Efectivo

**Objetivo:** implementar F1 (Ingreso de Capital), F10 (Pago de Cuentas por Pagar) y la base de F11 (Arqueo) descritos en `07-FLUJOS-DE-NEGOCIO.md`.

**Dependencia:** Fase 1 (requiere `Fondo` y `Cuenta`).

**Bloqueante:** el plan de cuentas de `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` (sección 3) está marcado como Borrador — **no debe iniciarse la implementación de este módulo hasta que ese plan de cuentas sea validado formalmente** por un responsable financiero (ver Observaciones de ese documento).

**Criterio de salida:** es posible registrar capital, clasificarlo en los cuatro fondos, gestionar las Cuentas 1-6 ya validadas, y crear cuentas bancarias.

## 5. Fase 3 — Módulo 2: Inventario y Almacén

**Objetivo:** implementar F3 (Conciliación Chofer/Encargado) y F4 (Novedades de Cuarto Frío).

**Dependencia:** Fase 1 (requiere `Producto`).

**Criterio de salida:** `InventarioChofer` e `InventarioEncargado` operan como procesos independientes (Artículo 17.1), con registro de novedades (dañados, rotos, mal estado, sobrantes, faltantes) y conciliación explícita entre ambos.

## 6. Fase 4 — Módulo 3: Despacho y Consignaciones

**Objetivo:** implementar F5 (Consignación y Despacho) y F6 (Solicitud y Justificación de Retiro).

**Dependencia:** Fase 3 (requiere inventario operando).

**Criterio de salida:** es posible crear consignaciones, despachar mercancía, registrar novedades de despacho, y ningún retiro se considera válido sin su justificación asociada (Artículo 21.3).

## 7. Fase 5 — Módulo 4: Facturación

**Objetivo:** implementar F7 (Facturación y Venta), F8 (Alta de Producto) y F9 (Nota de Crédito).

**Dependencia:** Fase 2 (capital) y Fase 3 (inventario) — una factura afecta ambos flujos de forma atómica (Artículo 6.2 de la Constitución).

**Criterio de salida:** una factura aprobada es inmutable, genera su contrapartida de capital e inventario en una sola unidad atómica, y una nota de crédito nunca edita la factura original.

## 8. Fase 6 — Módulo 5: Reportes

**Objetivo:** implementar F11 (Arqueo Manual, completo) y F12 (Exportación de Reportes).

**Dependencia:** Fases 2 a 5 (los reportes leen de todos los flujos anteriores).

**Criterio de salida:** todo reporte se genera desde la fuente de verdad o una proyección declarada, respeta el aislamiento por `empresaId`, y todo arqueo registra su diferencia como evento propio sin sobrescribir el saldo del sistema.

## 9. Fase 7 — Validación Multiempresa Real

**Objetivo:** incorporar una **segunda empresa** al ecosistema (no Polar Breeze) operando sobre la misma plataforma, como prueba de que el aislamiento multiempresa (Artículo 2 de la Constitución) funciona en la práctica y no solo en el diseño.

**Dependencia:** Fases 0 a 6 completas y en operación real para Polar Breeze.

**Criterio de salida:** la segunda empresa opera sin ver, inferir ni afectar datos de Polar Breeze, sin haber requerido cambios estructurales al modelo de datos ni al motor (Artículo 28.1 de la Constitución) — solo parametrización.

## 10. Fase 8 — Crecimiento Continuo

**Objetivo:** incorporar módulos nuevos, integraciones externas (Artículo 27 de la Constitución) y funcionalidades de IA asistiva (Artículo 26), siempre siguiendo el mismo ciclo de gobernanza: documentación aprobada antes de desarrollo (Artículo 29).

**Dependencia:** continua, sin fin definido — esta fase no "se completa", se mantiene mientras el ERP exista.

## 11. Gobernanza del Plan

- Ninguna fase se inicia sin que la documentación de los módulos que involucra esté aprobada (Artículo 29.1 de la Constitución).
- Todo cambio al orden de fases aquí descrito, o a sus criterios de salida, se registra como decisión en `DECISIONES-ARQUITECTURALES.md` antes de aplicarse.
- El estado de avance de cada fase (no las fechas) puede reflejarse en `13-HISTORIAL-DE-VERSIONES.md` a medida que se completen.

## 12. Riesgos Conocidos

- **Plan de cuentas no validado** (bloqueante de Fase 2): si se implementa el Módulo 1 sin validación contable formal, el trabajo puede requerir rehacerse.
- **Omisión de offline-first en fases tempranas**: si la Fase 0 no prueba de extremo a extremo la sincronización offline, los módulos posteriores heredarán esa deuda técnica de forma silenciosa.
- **Introducción prematura de una segunda empresa real** antes de la Fase 7: usar el ecosistema multiempresa como prueba de concepto antes de que los módulos base estén estables incrementa el riesgo de detectar tarde una fuga de aislamiento entre empresas.

## 13. Relación con Otros Documentos

- `02-CONSTITUCION-ERP.md` — las reglas que cada fase debe cumplir para considerarse completa.
- `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` y `05-MODELO-DE-DATOS-MAESTRO.md` — lo que se construye en la Fase 0 y Fase 1.
- `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` — el bloqueante de la Fase 2.
- `07-FLUJOS-DE-NEGOCIO.md` — los flujos F1-F12 que cada fase de módulo implementa.
- `08-CATALOGO-DE-MODULOS.md` — el catálogo de módulos que las Fases 2 a 6 construyen.
- `13-HISTORIAL-DE-VERSIONES.md` — donde se registra el avance real de estas fases.

Observaciones:

Este plan asume que las Fases 2 a 6 podrían, en la práctica, ejecutarse con cierto grado de paralelismo una vez completadas las Fases 0 y 1 (por ejemplo, Inventario y Flujo de Efectivo pueden avanzar simultáneamente), siempre que Facturación (Fase 5) no inicie hasta que ambas estén operativas, por su dependencia atómica de las dos. La decisión final de paralelizar o no queda en manos del equipo de ejecución, respetando las dependencias aquí descritas.
