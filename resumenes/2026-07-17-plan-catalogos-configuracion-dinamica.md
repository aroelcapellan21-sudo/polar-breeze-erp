# Plan — Catálogos de Configuración Dinámica (Hub Admin)

Fecha: 2026-07-17
Estado: Propuesta, pendiente de aprobación del usuario. No se ha tocado ningún documento de `docs/` todavía.

## Decisiones ya confirmadas con el usuario antes de este plan

1. **Cuentas Contables amplía/reemplaza la entidad `Cuenta` existente** (hoy fija a códigos "1" a "6"). Se elimina esa restricción; `Cuenta.código` pasa a ser un código de negocio libre, como cualquier otro catálogo.
2. **Todos los catálogos maestros — existentes y nuevos — viven bajo `config/{empresaId}/...`**. Esto corrige una inconsistencia real detectada en `03-ARQUITECTURA-GENERAL.md`: su sección 7 (Modelo de Datos Maestro) dice que los catálogos maestros viven ahí, mientras su sección 8 y la lista de capas dicen que viven en `config/*`. Se resuelve a favor de `config/*` para todos.
3. **"Capital de la Empresa" es distinto del pendiente "Participación de Capital"** (Módulo 1 — Bancos, dejado sin modelar por ambigüedad). Ambos quedan documentados como conceptos relacionados pero separados; el pendiente de Participación de Capital sigue abierto.
4. **Se formaliza "Hub Admin"** como la interfaz web de administración (rol Administrador/Oliver) en `03-ARQUITECTURA-GENERAL.md`, sección 2 (Capa de Presentación), hoy solo descrita de forma genérica.

## Catálogo 1 — Cuentas Bancarias

- **Entidad:** `CuentaBancaria`, ya existe (`05-MODELO-DE-DATOS-MAESTRO.md` sección 4; `11-DICCIONARIO-DE-DATOS.md` sección 5). No requiere entidad nueva.
- **Cambio de campos:** agregar `alias` (Texto corto, opcional) — hoy solo tiene `número`, `banco`, `cuentaAsociada`.
- **Módulo dueño:** Módulo 1 — Flujo de Efectivo y Bancos (ya establecido: "se crea desde el Módulo 1").
- **Firestore:** `config/{empresaId}/cuentasBancarias/{codigo}`.
- **Soft delete:** ya cubierto por el campo común `estado` (Artículo 9); no requiere campo nuevo, solo declarar el patrón Agregar/Editar/Desactivar en `08-CATALOGO-DE-MODULOS.md`.

## Catálogo 2 — Capital de la Empresa

- **Entidad nueva:** `AporteCapital`, diseñada como subcolección desde el inicio (no como campo suelto en `Empresa`), para admitir múltiples aportes sin remodelar después.
- **Campos:** `monto` (Monto, default 0), `fecha`, `descripción` (opcional), `estado`, `empresaId`.
- **Módulo dueño (propuesto, no es texto literal de Oliver):** Módulo 1 — Flujo de Efectivo y Bancos, mismo dominio patrimonial que `Fondo`/`Cuenta`/`MovimientoCapital`. Se declara explícitamente como contenido nuevo nuestro, no atribuido a Oliver.
- **Firestore:** `config/{empresaId}/capital/{codigo}`.
- **Pregunta abierta (no bloqueante):** ¿un aporte de capital genera un `MovimientoCapital` contra algún `Fondo`, o es puramente informativo por ahora? Igual de no-bloqueante que el ítem 7 del anexo 01.

## Catálogo 3 — Cuentas Contables (plan de cuentas)

- **Cambio de modelo:** se retira la restricción de `Cuenta.código` a "1"-"6" (`05` sección 4, `11` sección 5). Campos `nombre` y `naturaleza` (activo/pasivo/resultado) ya existen y cubren lo pedido ("nombre de la cuenta, tipo") — no se agregan campos nuevos, solo se abre la cardinalidad.
- **Efecto directo sobre el Borrador de `06-REGLAS-CONTABLES-Y-FINANCIERAS.md`:** resuelve los ítems **1** y **6** del anexo `01-PENDIENTE-VALIDACION-CONTABLE.md` (ya no hay "6 nombres fijos" que un contador deba validar; el propio Oliver los define desde el Hub Admin). **No resuelve** los ítems 2, 3, 4, 5 ni 7 — `06` sigue en Borrador hasta que esos se cierren aparte.
- **Sin impacto en relaciones existentes:** `CuentaBancaria.cuentaAsociada` y `MovimientoCapital.cuentaOrigen/cuentaDestino` siguen siendo Referencia a `Cuenta` sin cambios.
- **Módulo dueño:** Módulo 1 — Flujo de Efectivo y Bancos. No es una funcionalidad inventada: Oliver ya declaró "Cuenta 1...6 -> Mantenimiento / Crear Cuenta" en su estructura real (`docs/anexos/02`); esto corrige una restricción que nosotros mismos impusimos sobre algo que él ya pedía poder crear.
- **Firestore:** `config/{empresaId}/cuentas/{codigo}`.

## Catálogo 4 — Motivos de Salida sin Cobro

- **Cambio de modelo:** `BajaInventario.tipo` pasa de Enumeración cerrada a Referencia a nueva entidad `MotivoSalidaSinCobro`.
- **Entidad nueva:** `MotivoSalidaSinCobro` — `código`, `nombre`, `empresaId`, `estado`.
- **Módulo dueño:** Módulo 6 — Parámetros de Mantenimiento (crea el catálogo, coincide con el pendiente ya señalado "Crear Novedades... catálogo configurable"); consumido por Módulo 3 — Inventario y Cuarto Frío (donde vive `BajaInventarioRegistrada`, F13).
- **Firestore:** `config/{empresaId}/motivosSalidaSinCobro/{codigo}`.
- **Pregunta abierta (no bloqueante):** hoy `BajaInventario.tipo` tiene 6 valores (merma, pérdida, condonación, donación, bonificación, refrigerio); el mensaje del usuario solo listó 4 como semilla (Refrigerio, Donación, Bonificación, Merma). ¿Se siembran también Pérdida y Condonación en este catálogo, o quedan fuera de "salida sin cobro" y se tratan aparte?

## Transversal a los 4 catálogos

- **Patrón CRUD:** Agregar / Editar / Desactivar, vía el campo común `estado` (Artículo 9) — sin campo nuevo, solo declaración explícita en `08-CATALOGO-DE-MODULOS.md`.
- **Acceso:** rol Administrador/Oliver únicamente, vía el Motor de Permisos genérico ya existente (Artículo 13) — no requiere mecanismo nuevo, solo declarar el permiso al configurar el sistema real.
- **Firestore:** `config/{empresaId}/<colección>/{código}` para los 4, y retroactivamente para todos los catálogos maestros ya existentes (`Producto`, `Cuenta`, `CuentaBancaria`, `Fondo`, `Vendedor`, `Cliente`, `Proveedor`, `CondicionPago`, `Ruta`).

## Documentos a modificar (al implementar, tras aprobación)

- `03-ARQUITECTURA-GENERAL.md` — Hub Admin (sección 2); retirar catálogos maestros de la sección 7; confirmarlos en la sección 8.
- `05-MODELO-DE-DATOS-MAESTRO.md` — `Cuenta` (abre cardinalidad), `CuentaBancaria` (+`alias`), nuevas `AporteCapital` y `MotivoSalidaSinCobro`, `BajaInventario.tipo` (Enumeración → Referencia).
- `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` — sección 3, los 6 nombres dejan de ser propuesta fija; el documento **sigue en Borrador** (otros ítems del anexo 01 no se resuelven aquí).
- `08-CATALOGO-DE-MODULOS.md` — Módulo 1 (3 catálogos), Módulo 3 (consume motivos), Módulo 6 (crea motivos), patrón CRUD y rol Administrador.
- `09-ESTANDARES-DE-DESARROLLO.md` — reforzar regla 4 con "incluye catálogos maestros".
- `11-DICCIONARIO-DE-DATOS.md` — reflejar todos los campos/cambios de tipo.
- `docs/anexos/01-PENDIENTE-VALIDACION-CONTABLE.md` — cerrar/actualizar ítems 1 y 6.
- `DECISIONES-ARQUITECTURALES.md` — 2-3 entradas nuevas (ubicación `config/*` + Hub Admin; apertura de `Cuenta`; `MotivoSalidaSinCobro` reemplaza el enum).
- `13-HISTORIAL-DE-VERSIONES.md` — nueva entrada, versión MAYOR.

## Preguntas abiertas para el usuario (no bloquean la aprobación del resto)

1. ¿Semillas de `MotivoSalidaSinCobro` incluyen Pérdida y Condonación, o solo los 4 que mencionaste?
2. ¿Un aporte de capital genera `MovimientoCapital` contra algún `Fondo`, o es informativo por ahora?
