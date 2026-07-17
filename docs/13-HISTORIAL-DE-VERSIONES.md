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
| v0.21 | 2026-07-14 | `27acb3b` | Se crea el primer anexo real, `docs/anexos/VALIDACIONES-PENDIENTES-CONTADOR.md` (checklist de 6 ítems contables pendientes de validación), y se actualiza `docs/anexos/README.md` para listarlo. |
| v0.22 | 2026-07-14 | `dfc4a7e` | Se renombra el anexo a `docs/anexos/01-PENDIENTE-VALIDACION-CONTABLE.md`, adoptando prefijo numérico para anexos. Se actualiza `docs/anexos/README.md`. |
| v0.23 | 2026-07-14 | `221f1dd` | Se crea `docs/00-MANIFIESTO-DEL-ERP.md`, síntesis declarativa de la Constitución. Coexiste con `docs/00-PRINCIPIOS-DEL-ERP.md` bajo el mismo prefijo `00` — colisión conocida y documentada, no resuelta a la espera de decisión del usuario. |
| v0.24 | 2026-07-14 | `d52ab54` | Se fortalece `docs/00-MANIFIESTO-DEL-ERP.md`: Objetivo ampliado con el marco de "Sistema Operativo Empresarial basado en Flujos Patrimoniales"; se agregan las secciones "Nada existe aislado." y "El patrimonio cambia de forma. Nunca aparece ni desaparece."; se agrega declaración de cierre final. Contenido previo sin cambios. |
| v0.25 | 2026-07-14 | `cb61ea1` | Se agregan las entidades `Cliente`, `Proveedor` y `Obligacion` (Cuenta por Pagar) a `05-MODELO-DE-DATOS-MAESTRO.md` y `11-DICCIONARIO-DE-DATOS.md`, cerrando el vacío del Artículo 16.1 de la Constitución (catálogos de clientes y proveedores) y dando soporte estructural a las obligaciones de flujo de capital (Artículo 20). Se agrega el campo `obligacionReferenciada` a `MovimientoCapital` y dos reglas de integridad referencial. Queda pendiente vincular `Factura` a `Cliente` para ventas a crédito. |
| v0.26 | 2026-07-14 | `97f67d0` | Se agrega el campo `moneda` (ISO 4217) a `Empresa` en `05-MODELO-DE-DATOS-MAESTRO.md` y `11-DICCIONARIO-DE-DATOS.md`, fijando una moneda funcional por empresa (Artículo 28.1). Se actualiza el tipo conceptual `Monto` para declarar que siempre se expresa en la moneda de su `empresaId`. Deliberadamente no se duplica el campo en cada entidad monetaria (`MovimientoCapital`, `Obligacion`, etc.); multi-moneda dentro de una misma empresa queda fuera de alcance y documentada como tal. |
| v0.27 | 2026-07-14 | `5e9b263` | Se eleva `08-CATALOGO-DE-MODULOS.md` al estándar del Artículo 29.2 de la Constitución: cada uno de los cinco módulos declara ahora su alcance por `empresaId`/`sucursalId`, los eventos que emite y los de otros módulos que le afectan, y los catálogos maestros que crea o consume. Estado del documento pasa de "En construcción" a "Vigente", resolviendo la inconsistencia con su fila en la tabla de completitud de este mismo historial. Se ajustan las entradas de `Cliente` y `Proveedor` en `05` para declarar su módulo de creación. |
| v0.28 (MAYOR) | 2026-07-14 | `eab5539` | Se fija la jerarquía documental en `02-CONSTITUCION-ERP.md`: la Constitución prevalece ante cualquier conflicto, incluido con `01-VISION-ERP.md` (que queda como origen/fundamento, no instancia superior de apelación). Se elimina la frase "superada únicamente por `01-VISION-ERP.md`" del Objetivo. Decisión tomada por el usuario tras consulta explícita; no fue necesario modificar `01` ni `99-FILOSOFIA-DEL-SISTEMA.md`, ya compatibles. |
| v0.29 | 2026-07-14 | `eab5539` | Se formaliza como definitiva la coexistencia de `00-MANIFIESTO-DEL-ERP.md` y `00-PRINCIPIOS-DEL-ERP.md` bajo el prefijo `00` (nivel fundacional compartido por diseño), cerrando la excepción que estaba "a la espera de decisión del usuario" desde v0.23. Decisión tomada por el usuario tras consulta explícita. Ningún archivo se renombra. |
| v0.30 | 2026-07-14 | `445d5dc` | Limpieza de referencias cruzadas: se corrigen 4 citas erróneas de "Artículo 1.3" a "Artículo 9.3" en `05` y `11` (no reutilización de claves); se corrige la referencia del Artículo 9.3 de la Constitución de `10-PLAN-MAESTRO` a `05-MODELO-DE-DATOS-MAESTRO.md`; se corrige el Artículo 6.2 (llamaba "Artículo" a un documento); se retira de `99-FILOSOFIA` una cita a una regla inexistente en `09`; se eleva el Estado de `09-ESTANDARES-DE-DESARROLLO.md` a "Vigente", alineándolo con la tabla de completitud; se actualiza el árbol de `README.md` con `CLAUDE.md` y `resumenes/`. Con esta entrada, ningún documento numerado (`00`-`13`, `99`) queda en Estado "En construcción". |
| v0.31 | 2026-07-14 | `5e86e1f` | Revisión final: se sincroniza `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` (Fases 1 y 2) con los 7 catálogos maestros ya definidos en `05` (`Cliente` y `Proveedor` habían quedado fuera desde v0.25); se agrega a la Fase 2 la dependencia de `Proveedor` y el criterio de salida de `Obligacion`. Se retira de `05` la calificación obsoleta "(pendiente de redactar)" sobre `11-DICCIONARIO-DE-DATOS.md`, completo desde v0.14. |
| v0.32 | 2026-07-14 | `2560181` | Se elimina el campo duplicado `número` de `Cuenta` en `11-DICCIONARIO-DE-DATOS.md`: el campo `código` heredado queda como única clave de negocio, con su rango acotado a "1"-"6". `05-MODELO-DE-DATOS-MAESTRO.md` ya no requería cambios, pues siempre describió a `Cuenta` con un único campo. |
| v0.33 | 2026-07-14 | `0f61a9c` | Se corrige la enumeración `tipo` de `NovedadInventario` en `05` y `11`: el valor `cuarto_frío` (una ubicación) se reemplaza por `rotura_cadena_frio` (una condición, Artículo 22.2). La ubicación queda documentada como responsabilidad de `sucursalId` + `Sucursal.tipo` y del `tipoEvento` de origen, ya existentes — sin agregar campos nuevos. |
| v0.34 | 2026-07-14 | `135caa5` | Se agrega el flujo F13 (`07-FLUJOS-DE-NEGOCIO.md`) y la entidad `BajaInventario` (`05`, `11`) para mercancía dada de baja por merma, pérdida o condonación, respaldando estructuralmente el Artículo 6.3. Nuevo evento `BajaInventarioRegistrada` en `12-GLOSARIO.md` (20→21 eventos). El tratamiento de capital de una baja queda deliberadamente sin definir — ítem 7 nuevo en el anexo `01-PENDIENTE-VALIDACION-CONTABLE.md`, que no bloquea ninguna fase. Actualizados `08-CATALOGO-DE-MODULOS.md`, `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` (Fase 3) y `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` (referencia cruzada). |
| v0.35 | 2026-07-14 | `e947f0d` | Se define el mecanismo de detección y resolución de conflictos de sincronización offline: `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`, sección 13, expandida con la definición precisa de conflicto y su tratamiento; nueva entidad `ConflictoSincronizacion` (`05`, `11`) con `estado` modelado como proyección, nunca campo editado; nuevo evento `ConflictoSincronizacionDetectado` en `12-GLOSARIO.md` (21→22 eventos), el primero emitido por el motor y no por un flujo Fx. Actualizados `03-ARQUITECTURA-GENERAL.md` y `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` (Fase 0). |
| v0.36 | 2026-07-14 | `60a52a4` | Se crea `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md`: transcripción literal (sin interpretar ni modificar) de la estructura de 6 módulos definida por Oliver, declarada fuente de verdad para el diseño de módulos. Se señala, sin resolverla, la divergencia con los 5 módulos de `08-CATALOGO-DE-MODULOS.md`. Se actualiza `docs/anexos/README.md` para listarlo. |
| v0.37 (MAYOR) | 2026-07-14 | `aaf73c1` | Reconciliación completa con la Estructura Oliver: `08-CATALOGO-DE-MODULOS.md` reescrito con los 6 módulos exactos de Oliver; propagada a `01-VISION`, `02-CONSTITUCION` (Artículos 18.1, 21.1, 22.1, 25.3 enmendados), `03-ARQUITECTURA`, `05-MODELO` y `11-DICCIONARIO` (entidades redistribuidas: `Obligacion`→Módulo 2, `Despacho`/`Factura`/`NotaCredito`/etc.→Módulo 5), `06-REGLAS` y `10-PLAN-MAESTRO` (Fases 2-6 redistribuidas). Conceptos sin respaldo formal (NCF, ITBIS, Condición de Pago, Participación de Capital, Rutas y Vías, R1/R2/R3, Refrigerios/Bonificaciones) marcados "Pendiente de modelar", no inventados. Confirmado con el usuario que la "Factura" del Módulo 2 (CXP) y la "Facturación" del Módulo 5 (venta) son documentos distintos. Señalada, sin resolver, una discrepancia entre Oliver (3 clasificaciones de Flujo de Efectivo) y el Artículo 18.1 (4 clasificaciones). |
| v0.38 | 2026-07-14 | `50e758e` | Se corrige `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md`: la sección [FLUJO DE EFECTIVO] pasa de "Venta / Costo / Distribución" a "Venta / Costo / Distribución / Mantenimiento", tras confirmar el usuario que la fuente de verdad de Oliver sí incluye Mantenimiento — la omisión era un error de transcripción, no una diferencia real de diseño. Se actualiza `08-CATALOGO-DE-MODULOS.md` (Módulo 1) para reemplazar la nota de "discrepancia" por la confirmación. El Artículo 18.1 de la Constitución no requirió cambios: ya era correcto. |
| v0.39 | 2026-07-14 | `b588fe3` | Se modelan 6 de los 7 conceptos pendientes de la reconciliación con Oliver: NCF/ITBIS/Fecha de Factura y `CondicionPago` (referenciada por `Obligacion`) en el Módulo 2; `Ruta` en el Módulo 4; Reportes R1/R2/R3 documentados como proyecciones; `BajaInventario.tipo` ampliado con donación/bonificación/refrigerio. Se agregan `medioDePago`/`numeroTransaccion`/`comprobanteImagen` a `MovimientoCapital` y el tipo conceptual Archivo/Imagen. Participación de Capital queda explícitamente pendiente, por decisión del usuario (demasiado ambigua sin más contexto de Oliver). |
| v0.40 (MAYOR) | 2026-07-17 | `8a0c3ce` | Se agregan 4 catálogos de configuración dinámica administrables desde el **Hub Admin** (rol Administrador/Oliver, patrón Agregar/Editar/Desactivar): Cuentas Bancarias (`CuentaBancaria` +`alias`), Capital de la Empresa (nueva entidad `AporteCapital`, informativa), Cuentas Contables (`Cuenta` deja de estar restringida a "1"-"6", pasa a catálogo abierto) y Motivos de Salida sin Cobro (nueva entidad `MotivoSalidaSinCobro`; `BajaInventario.tipo` cambia de Enumeración a Referencia). Se unifica la ubicación de todos los catálogos maestros — existentes y nuevos — bajo Firestore `config/{empresaId}/<colección>/{código}`, corrigiendo una inconsistencia entre las secciones 7 y 8 de `03-ARQUITECTURA-GENERAL.md`. Se resuelven los ítems 1 y 6 del anexo `01-PENDIENTE-VALIDACION-CONTABLE.md` (pasan a "No aplica"); los ítems 2-5 siguen pendientes y `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` sigue en Borrador. Se actualiza `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` (Fase 1 incluye los 2 catálogos nuevos; el bloqueante de las Fases 2-3 se reformula a los ítems 2 y 3 del anexo, ya no a "todo el plan de cuentas"). |
| v0.41 | 2026-07-17 | — (este commit) | Se redacta `14-REQUISITOS-NO-FUNCIONALES.md` (documento nuevo), cerrando el pendiente #7 de `resumenes/2026-07-14-estado-y-pendientes-para-retomar.md`: respaldos y recuperación, retención de datos, privacidad de datos personales, volumetría/escalabilidad y disponibilidad, todos a nivel de principio arquitectónico. Volumetría se documenta como criterio de diseño escalable (rango de operación pequeña a grande), no como cifra fija de Polar Breeze, con checklist de qué medir al entrar en operación. Ningún régimen legal ni número de infraestructura se asume; ambos quedan en un checklist de validaciones pendientes (sección 7) que no bloquea ninguna fase. Se corrige `README.md`: árbol actualizado con `14` y con el anexo `02` (faltaba desde v0.36). |

## Estado de Completitud de la Documentación (a la fecha de la última entrada)

| Documento | Estado |
|---|---|
| `README.md` | Vigente |
| `DECISIONES-ARQUITECTURALES.md` | Vigente, en crecimiento continuo |
| `00-MANIFIESTO-DEL-ERP.md` | Vigente (comparte prefijo `00` con `00-PRINCIPIOS-DEL-ERP.md` — nivel fundacional compartido por diseño, decisión formalizada en v0.29) |
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
| `14-REQUISITOS-NO-FUNCIONALES.md` | Vigente — checklist de 5 ítems Pendientes (sección 7, ninguno bloquea fases) |
| `99-FILOSOFIA-DEL-SISTEMA.md` | Vigente |
| `docs/diagramas/*.drawio` (6 archivos) | Creados vacíos; pendientes de contenido visual |
| `docs/anexos/01-PENDIENTE-VALIDACION-CONTABLE.md` | Vigente — 5 ítems Pendientes (2, 3, 4, 5, 7; el 7 no bloquea ninguna fase) y 2 en "No aplica" (1 y 6, resueltos por arquitectura en v0.40) |
| `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md` | Fuente de verdad — transcripción literal, reconciliada contra `08-CATALOGO-DE-MODULOS.md` y la documentación técnica en v0.37 |

## Relación con Otros Documentos

- `DECISIONES-ARQUITECTURALES.md` — el razonamiento detrás de cada versión de este historial.
- `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` — el avance de fases de implementación (una vez inicien) se refleja como nuevas entradas aquí.

Observaciones:

Este documento se actualiza como parte del mismo commit que agrega o modifica contenido sustantivo en cualquier documento de `docs/`. No reemplaza al historial de `git` ni a `DECISIONES-ARQUITECTURALES.md` — los complementa con una vista cronológica resumida y de fácil consulta.
