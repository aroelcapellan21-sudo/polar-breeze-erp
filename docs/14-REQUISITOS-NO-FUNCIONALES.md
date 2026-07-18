# Requisitos No Funcionales

Estado:

> Vigente — principios de diseño definidos; contiene un checklist de validaciones pendientes (sección 7: legal, infraestructura, volumetría real) que no bloquea ninguna fase del `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md`. Aprobado por el Arquitecto/Product Owner del ERP el 2026-07-17 (baseline v0.41; ver Registro de Aprobaciones en `13-HISTORIAL-DE-VERSIONES.md`).

Objetivo:

Definir, a nivel de **principio arquitectónico**, los requisitos no funcionales del ERP Polar Breeze: respaldos y recuperación, retención de datos, privacidad de datos personales, volumetría/escalabilidad y disponibilidad. Este documento no fija números concretos (RPO/RTO, SLA de disponibilidad, cifras de volumetría) ni un régimen legal específico — esas decisiones dependen de infraestructura real, jurisdicción real de cada empresa cliente, y operación real, ninguna de las cuales existe todavía. Fijarlas aquí sin esa base sería una suposición, no una definición de arquitectura (Artículo 26.2 de la Constitución: ante ambigüedad genuina, preguntar, nunca asumir).

Contenido:

## 1. Alcance y Naturaleza del Producto

El ERP Polar Breeze es un **producto multiempresa**, diseñado para operar múltiples clientes/empresas del ecosistema — Polar Breeze es únicamente la primera (`05-MODELO-DE-DATOS-MAESTRO.md`, sección 2; `README.md`). En consecuencia, todo requisito no funcional de este documento se expresa como **criterio de diseño escalable**, nunca como una cifra fija atada a la operación actual de Polar Breeze. Documentar un número específico de Polar Breeze como si fuera un requisito del sistema confundiría las necesidades de un cliente con la capacidad que el producto debe ofrecer a cualquier cliente futuro.

## 2. Respaldos y Recuperación (Backup & Recovery)

Principios:

- Ningún evento del historial inmutable (Artículo 5 de la Constitución) puede perderse por diseño: la arquitectura de persistencia debe garantizar redundancia (múltiples copias), nunca depender de un único punto de falla.
- El offline-first (Principio 2 de `00-PRINCIPIOS-DEL-ERP.md`; `03-ARQUITECTURA-GENERAL.md`, sección 3) ya cubre gran parte de la continuidad operativa en campo: una interrupción de red no es una pérdida de datos, es una sincronización diferida que se resuelve al reconectar (incluyendo el mecanismo de `ConflictoSincronizacion` cuando corresponda).
- Un respaldo solo cuenta como tal si su restauración se ha probado; generarlo sin verificar que es restaurable no cumple este principio.

**No fija en este documento:** objetivos numéricos de RPO (cuánto dato, en el peor caso, se podría perder) ni de RTO (cuánto tiempo tomaría recuperarse), ni la frecuencia concreta de respaldo — son decisiones de infraestructura que se toman cuando exista el repositorio de código y su proveedor de nube (`05-MODELO-DE-DATOS-MAESTRO.md`, Objetivo: este repositorio no fija tecnología de persistencia).

## 3. Retención de Datos

Por diseño, este ERP no tiene un problema de "cuándo se borra" — el Artículo 9 (Soft Delete) y el Artículo 5 (historial inmutable) de la Constitución ya garantizan que ningún dato de negocio se descarta. La pregunta real de retención es **cuánto tiempo se mantiene accesible en caliente frente a archivado**, y qué exige la ley fiscal o de protección de datos aplicable a cada empresa cliente — que puede variar de una empresa a otra dentro del mismo ecosistema (distintas jurisdicciones, distintas monedas — Artículo 28.1).

Este documento no asume una jurisdicción única ni un período de retención específico (ver checklist, sección 7, ítem 1).

## 4. Privacidad de Datos Personales

Alcance: los datos de `Usuario` (personal interno) y `Cliente` (terceros) son datos personales (`05-MODELO-DE-DATOS-MAESTRO.md`, secciones 2 y 4).

Principios ya cubiertos por reglas existentes, que este documento no duplica:

- Acceso restringido por rol y Motor de Permisos (Artículos 12 y 13 de la Constitución).
- Prohibición de duplicar información (Artículo 4) — limita por diseño la proliferación de copias de datos personales.
- Auditoría obligatoria de quién accede o modifica un registro (Artículo 8).

Principio nuevo de este documento — **minimización de datos**: cada entidad que contiene datos personales captura únicamente los campos estrictamente necesarios para su función de negocio; ningún módulo agrega un campo personal "por si acaso" (consistente con `Usuario` y `Cliente` en `05`, que hoy son mínimos).

Este documento no asume ningún régimen legal de protección de datos específico (ver checklist, sección 7, ítem 2).

## 5. Volumetría y Escalabilidad

Criterio de diseño: el sistema debe soportar, **sin cambio de arquitectura**, un rango que va desde una operación pequeña (una empresa, una sucursal, un puñado de usuarios) hasta una operación grande (múltiples empresas del ecosistema, múltiples sucursales/cuartos fríos/vehículos por empresa, alto volumen diario de eventos). El particionado por `empresaId`/`sucursalId` (Artículo 2 de la Constitución) y la arquitectura basada en eventos (Artículo 5) son, precisamente, lo que hace posible este rango sin rediseño — no un límite que deba probarse aparte.

**No se fija ningún número** (usuarios concurrentes, transacciones por día, tamaño de datos) como requisito: hacerlo describiría la operación actual de Polar Breeze, no una capacidad del producto.

Checklist a medir cuando un cliente real entra en operación (empezando por Polar Breeze — ver sección 7, ítem 3):

- Número de sucursales, cuartos fríos y vehículos activos.
- Número de usuarios concurrentes, por rol.
- Volumen de eventos por día (capital, mercancía, información).
- Tiempo real de sincronización offline→online bajo carga.
- Crecimiento esperado a 12 y 24 meses.

Esta medición retroalimenta, en su momento, los targets de infraestructura de la sección 2 (RPO/RTO) y de disponibilidad (sección 6) — nunca al revés: no se fijan targets de infraestructura a partir de una cifra supuesta.

## 6. Disponibilidad

La disponibilidad percibida por los roles de campo (chofer, encargado, despacho) ya está mitigada por el offline-first: una caída del backend no detiene su operación, solo retrasa la sincronización (sección 2). La disponibilidad crítica de este documento es la del **backend/API** para los roles que sí dependen de conectividad en el momento de operar — Hub Admin, reportes, arqueo consolidado.

**No se fija un SLA numérico** (por ejemplo, un porcentaje de uptime) en este documento — es una decisión de infraestructura futura, consistente con que este repositorio no fija tecnología (`05-MODELO-DE-DATOS-MAESTRO.md`, Objetivo).

## 7. Checklist de Validaciones Pendientes

Mismo patrón que `docs/anexos/01-PENDIENTE-VALIDACION-CONTABLE.md`: ningún ítem bloquea una fase del `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` a menos que se indique lo contrario, y cada ítem se cierra con una decisión registrada en `DECISIONES-ARQUITECTURALES.md`, nunca editando este documento en silencio.

| # | Ítem | Qué falta definir | Estado |
|---|---|---|---|
| 1 | Retención legal de comprobantes fiscales y datos personales | Períodos mínimos de retención, por jurisdicción de cada empresa cliente (sección 3) | Pendiente |
| 2 | Régimen de protección de datos personales aplicable | Ley(es) aplicable(s) por jurisdicción de cada empresa cliente, derechos del titular, plazos de respuesta, notificación de brechas (sección 4) | Pendiente |
| 3 | Volumetría real de cada cliente en operación | Medir el checklist de la sección 5 al entrar en operación, empezando por Polar Breeze | Pendiente — no bloquea ninguna fase |
| 4 | RPO/RTO y frecuencia de respaldo | Se define al elegir la infraestructura real, en el repositorio de código (sección 2) | Pendiente |
| 5 | SLA de disponibilidad del backend/API | Objetivo numérico de uptime (sección 6) | Pendiente |

## 8. Relación con Otros Documentos

- `02-CONSTITUCION-ERP.md` (Artículos 2, 4, 5, 8, 9, 12, 13, 26.2, 28.1) — las reglas que este documento traduce a requisitos no funcionales.
- `00-PRINCIPIOS-DEL-ERP.md` (Principio 2 — Offline-First) — la base de la continuidad operativa en campo.
- `03-ARQUITECTURA-GENERAL.md` (sección 3 — Capa de Persistencia Local y Sincronización) — donde vive el offline-first a nivel de componente.
- `05-MODELO-DE-DATOS-MAESTRO.md` — las entidades `Usuario` y `Cliente` cuya privacidad cubre la sección 4; la declaración de que este repositorio no fija tecnología de persistencia.
- `docs/anexos/01-PENDIENTE-VALIDACION-CONTABLE.md` — el mismo patrón de checklist de validaciones pendientes, aplicado aquí a un dominio distinto (no contable).
- `DECISIONES-ARQUITECTURALES.md` — donde se registra el cierre de cada ítem de la sección 7.

Observaciones:

Este documento resuelve el pendiente #7 señalado en `resumenes/2026-07-14-estado-y-pendientes-para-retomar.md`: la ausencia de cualquier documento que cubriera respaldos, retención, privacidad, volumetría y disponibilidad. Se optó deliberadamente por no fijar cifras ni citar una ley específica — el usuario confirmó que el ERP es un producto pensado para múltiples clientes futuros, no solo para Polar Breeze, y que documentar cifras o un régimen legal de un solo cliente como si fueran un requisito del producto sería una suposición contraria a esa naturaleza multiempresa. La volumetría en particular se documenta como rango de diseño escalable, no como meta fija, con un checklist de qué medir cuando cada cliente real entra en operación.
