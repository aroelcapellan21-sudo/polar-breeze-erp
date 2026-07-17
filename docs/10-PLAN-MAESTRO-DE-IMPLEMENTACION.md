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
- Mecanismo de detección y registro de `ConflictoSincronizacion` (`04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`, sección 13), aunque su interfaz y proceso de resolución humana pueden refinarse en fases posteriores.
- Capa de Configuración de Plataforma (`config/*`, Artículo 16.3).

**Criterio de salida:** es posible crear una empresa, un usuario con rol, emitir un evento de prueba offline y verlo sincronizado, auditado y persistido de forma inmutable; y verificar que un evento offline que ya no es válido al sincronizar se registra como `ConflictoSincronizacion` en lugar de aplicarse o descartarse silenciosamente — sin que exista todavía ningún módulo de negocio.

## 3. Fase 1 — Catálogos Maestros Compartidos

**Objetivo:** construir los catálogos que todos los módulos consumirán, evitando la duplicación prohibida por el Artículo 4 de la Constitución.

**Incluye:** `Producto`, `Cuenta`, `Fondo`, `CuentaBancaria`, `Vendedor`, `Cliente`, `Proveedor`, `AporteCapital`, `MotivoSalidaSinCobro` (`05-MODELO-DE-DATOS-MAESTRO.md`, sección 4) — todos administrables desde el Hub Admin (`08-CATALOGO-DE-MODULOS.md`, "Gobernanza de Catálogos de Configuración Dinámica").

**Dependencia:** Fase 0 completa.

**Criterio de salida:** los catálogos existen, particionados por `empresaId`, con código como clave de negocio (Principio 1), y ningún módulo posterior necesita reimplementarlos. Esta fase cubre funcionalmente el Módulo 6 — Parámetros de Mantenimiento de `08-CATALOGO-DE-MODULOS.md` (Crear Suplidor, Crear Producto, Crear Vendedor, Motivos de Salida sin Cobro) y la parte de "Mantenimiento" del Módulo 1 (Crear Cuenta, Capital de la Empresa); por eso Módulo 6 no tiene su propia fase de implementación más adelante.

## 4. Fase 2 — Módulo 1: Flujo de Efectivo y Bancos

**Objetivo:** implementar F1 (Ingreso de Capital) descrito en `07-FLUJOS-DE-NEGOCIO.md`.

**Dependencia:** Fase 1 (requiere `Fondo`, `Cuenta`, `CuentaBancaria`).

**Bloqueante (reducido en v0.40):** la cardinalidad del plan de cuentas ya no bloquea — `Cuenta` es un catálogo abierto gestionado por el negocio desde el Hub Admin (ítems 1 y 6 del anexo `01-PENDIENTE-VALIDACION-CONTABLE.md`, resueltos). Sigue bloqueando la clasificación de movimientos en Fondos (`06-REGLAS-CONTABLES-Y-FINANCIERAS.md`, sección 2 — ítem 2 del anexo, Pendiente): **no debe iniciarse la implementación de F1 hasta que un responsable financiero valide las cuatro clasificaciones de `Fondo`**.

**Criterio de salida:** es posible registrar capital, clasificarlo en los fondos ya validados, gestionar el plan de cuentas (catálogo abierto, creado en la Fase 1 — Módulo 6), y crear cuentas bancarias.

## 5. Fase 3 — Módulo 2: CXP, Facturación y Reportes

**Objetivo:** implementar F2 (Compra y Recepción de Mercancía, parte de capital: obligación registrada) y F10 (Pago de Cuentas por Pagar) descritos en `07-FLUJOS-DE-NEGOCIO.md`.

**Dependencia:** Fase 1 (requiere `Proveedor` y la Cuenta 4 — Cuentas por Pagar — creada en el plan de cuentas, ya no bloqueada en su cardinalidad) y Fase 2 (requiere `Fondo` validado).

**Bloqueante:** las reglas de Cuentas por Pagar y pagos parciales de `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` (sección 5 — ítem 3 del anexo `01-PENDIENTE-VALIDACION-CONTABLE.md`, Pendiente) — **no debe iniciarse la implementación de este módulo hasta que un responsable financiero las valide.**

**Criterio de salida:** es posible registrar una `Obligacion` (factura de un `Proveedor`) y pagarla, total o parcialmente, referenciando siempre la obligación original (Artículo 20.2).

## 6. Fase 4 — Módulo 3: Inventario y Cuarto Frío

**Objetivo:** implementar F2 (Compra y Recepción de Mercancía, parte de mercancía: recepción física), F3 (Conciliación Chofer/Encargado), F4 (Novedades de Cuarto Frío) y F13 (Baja de Mercancía por Merma, Pérdida o Condonación).

**Dependencia:** Fase 1 (requiere `Producto`) y Fase 3 (F2 es un evento atómico entre `ObligacionRegistrada` de este Módulo 2 y `MercanciaRecibida` de este Módulo 3 — ambas fases deben completarse juntas antes de que F2 pueda operar de extremo a extremo).

**Criterio de salida:** `InventarioChofer` e `InventarioEncargado` operan como procesos independientes (Artículo 17.1), con registro de novedades (dañados, rotos, mal estado, sobrantes, faltantes, rotura de cadena de frío), conciliación explícita entre ambos, y capacidad de dar de baja mercancía (merma, pérdida, condonación, donación, bonificación o refrigerio) reduciendo la existencia proyectada de forma explícita (Artículo 6.3). El tratamiento de capital de una baja permanece pendiente de validación contable y no bloquea esta fase.

## 7. Fase 5 — Módulo 4: Consignaciones

**Objetivo:** implementar la parte de creación de F5 (Consignación) descrita en `07-FLUJOS-DE-NEGOCIO.md`.

**Dependencia:** Fase 4 (requiere inventario operando, como origen de la mercancía a consignar).

**Criterio de salida:** es posible crear, retirar, listar e historiar consignaciones, con su existencia proyectada correctamente reducida cuando el Módulo 5 ejecuta el despacho asociado.

## 8. Fase 6 — Módulo 5: Despacho, Novedades y Caja

**Objetivo:** implementar la parte de despacho de F5, F6 (Solicitud y Justificación de Retiro), F7 (Facturación y Venta), F8 (Alta de Producto — nota: este flujo se ejecuta operativamente en la Fase 1/Módulo 6, no aquí), F9 (Nota de Crédito), F11 (Arqueo Manual, completo) y F12 (Exportación de Reportes).

**Dependencia:** Fase 5 (requiere `Consignacion` como origen del despacho), Fase 4 (inventario) y Fase 2 (capital) — una factura afecta capital e inventario de forma atómica (Artículo 6.2 de la Constitución).

**Criterio de salida:** es posible despachar mercancía, registrar novedades de despacho, procesar solicitudes de retiro con su justificación obligatoria (Artículo 21.3), emitir una factura de venta inmutable que genera su contrapartida de capital e inventario en una sola unidad atómica, corregirla con una nota de crédito sin editar el original, y todo reporte y arqueo se genera desde la fuente de verdad o una proyección declarada, respetando el aislamiento por `empresaId`.

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

- **Reglas contables no validadas** (bloqueante de las Fases 2 y 3): si se implementan el Módulo 1 (clasificación de Fondos) o el Módulo 2 (reglas de Cuentas por Pagar) sin validación financiera formal, el trabajo puede requerir rehacerse. La cardinalidad del plan de cuentas ya no es parte de este riesgo desde v0.40 (`Cuenta` es un catálogo abierto).
- **Omisión de offline-first en fases tempranas**: si la Fase 0 no prueba de extremo a extremo la sincronización offline, los módulos posteriores heredarán esa deuda técnica de forma silenciosa.
- **Introducción prematura de una segunda empresa real** antes de la Fase 7: usar el ecosistema multiempresa como prueba de concepto antes de que los módulos base estén estables incrementa el riesgo de detectar tarde una fuga de aislamiento entre empresas.

## 13. Relación con Otros Documentos

- `02-CONSTITUCION-ERP.md` — las reglas que cada fase debe cumplir para considerarse completa.
- `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` y `05-MODELO-DE-DATOS-MAESTRO.md` — lo que se construye en la Fase 0 y Fase 1.
- `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` — el bloqueante de las Fases 2 y 3.
- `07-FLUJOS-DE-NEGOCIO.md` — los flujos F1-F13 que cada fase de módulo implementa.
- `08-CATALOGO-DE-MODULOS.md` — el catálogo de 6 módulos que las Fases 2 a 6 construyen (el Módulo 6 se cubre en la Fase 1, ver sección 3).
- `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md` — la fuente de verdad de módulos con la que se reconciliaron estas fases.
- `13-HISTORIAL-DE-VERSIONES.md` — donde se registra el avance real de estas fases.

Observaciones:

Este plan asume que las Fases 2 a 6 podrían, en la práctica, ejecutarse con cierto grado de paralelismo una vez completadas las Fases 0 y 1 (por ejemplo, la Fase 4 — Inventario y Cuarto Frío y la Fase 2 — Flujo de Efectivo y Bancos pueden avanzar simultáneamente), siempre que la Fase 6 — Despacho, Novedades y Caja no inicie hasta que las Fases 2, 4 y 5 estén operativas, por su dependencia atómica de todas ellas (facturación de venta). La decisión final de paralelizar o no queda en manos del equipo de ejecución, respetando las dependencias aquí descritas.

Esta reconciliación (Fases 1 a 6) redistribuyó el trabajo de implementación entre 6 módulos en lugar de 5, siguiendo `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md`: la Fase 3 (CXP) se separó de la antigua Fase 2 (Flujo de Efectivo), y la antigua Fase 4 (Despacho y Consignaciones) se dividió en la actual Fase 5 (Consignaciones) y Fase 6 (Despacho, Novedades y Caja, que además absorbió la antigua Fase 6 — Reportes y la antigua Fase 5 — Facturación). Ningún criterio de salida ni dependencia técnica cambió de fondo — solo su agrupación en fases.
