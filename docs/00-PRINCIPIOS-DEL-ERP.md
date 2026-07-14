# Principios del ERP

Estado:

> Vigente — pendiente de revisión y aprobación formal

Objetivo:

Desarrollar en detalle los principios operativos que ya se enuncian de forma breve en `01-VISION-ERP.md` (sección 4) y que se fijan como ley inquebrantable en `02-CONSTITUCION-ERP.md` (Artículo 1 y siguientes). Mientras la Visión explica el *porqué* y la Constitución fija la *regla formal*, este documento explica, principio por principio, **el fundamento, la implicación práctica y lo que cada principio prohíbe explícitamente** — sirviendo de referencia de consulta rápida para evaluar si un módulo o una decisión técnica se alinea con la filosofía del ERP.

Contenido:

## Cómo leer este documento

Cada principio se describe con la misma estructura:

- **Enunciado** — la afirmación central del principio.
- **Fundamento** — por qué existe.
- **Implica** — qué exige en la práctica de cualquier módulo o decisión técnica.
- **Prohíbe** — qué comportamiento queda explícitamente descartado por este principio.

## Principio 1 — El Código es la Clave Universal

**Enunciado:** Todo producto o entidad de negocio se identifica de forma funcional por su **código**, nunca por su nombre.

**Fundamento:** Los nombres cambian, se escriben distinto, se traducen o se repiten entre productos distintos. El código es estable y sin ambigüedad.

**Implica:** Toda búsqueda, referencia cruzada, clave foránea o integración usa el código como identificador. El nombre es solo una etiqueta de presentación en pantalla.

**Prohíbe:** Usar el nombre libre como clave de búsqueda, clave foránea o criterio de unicidad en ningún módulo.

## Principio 2 — Offline-First Obligatorio

**Enunciado:** El sistema opera primero en local; la sincronización con el servidor es automática, no una condición para trabajar.

**Fundamento:** La operación real (chofer, encargado, cuarto frío, punto de despacho) no siempre tiene conectividad estable en el momento del evento.

**Implica:** Todo módulo operativo de campo debe funcionar sin conexión, encolar sus eventos localmente y sincronizarlos sin pérdida ni duplicación al recuperar conectividad.

**Prohíbe:** Cualquier flujo crítico de campo que bloquee la operación del usuario por falta de conectividad, o que descarte silenciosamente datos capturados sin conexión.

## Principio 3 — Multiempresa desde el Origen

**Enunciado:** El ERP nace multiempresa. Polar Breeze es la primera empresa del ecosistema, no el límite de su diseño.

**Fundamento:** Retrofitting de aislamiento multiempresa sobre un sistema diseñado para una sola empresa es costoso y riesgoso; diseñarlo desde el modelo de datos inicial no lo es.

**Implica:** Toda entidad de negocio incluye `empresaId` y, cuando aplica a nivel de sede o unidad operativa, `sucursalId`. Todo query, cálculo y reporte está acotado explícitamente por esos campos.

**Prohíbe:** Cualquier módulo, catálogo o consulta que asuma implícitamente que existe una sola empresa, o que permita a una empresa leer, inferir o escribir datos de otra.

## Principio 4 — Compatibilidad Multiplataforma

**Enunciado:** Toda funcionalidad de operación de campo está disponible por igual en Android e iOS.

**Fundamento:** El personal de campo (chofer, encargado) no puede quedar limitado por el sistema operativo de su dispositivo.

**Implica:** El desarrollo planifica ambas plataformas como igual de prioritarias desde el diseño de cada funcionalidad, no como un port posterior.

**Prohíbe:** Lanzar una funcionalidad crítica de campo exclusiva de una sola plataforma.

## Principio 5 — Persistencia de Sesión Resistente a Interrupciones

**Enunciado:** El contexto de trabajo del usuario sobrevive a cierres inesperados, pérdida de conectividad o reinicio del dispositivo.

**Fundamento:** El trabajo de campo ocurre en condiciones no controladas (batería baja, señal intermitente, interrupciones); perder el progreso captado sería inaceptable.

**Implica:** El estado de una operación en curso (por ejemplo, un despacho a medio registrar) se persiste localmente de forma incremental, no solo al finalizar.

**Prohíbe:** Cualquier flujo que exija completarse en una sola sesión ininterrumpida para no perder lo ya capturado.

## Principio 6 — Una Sola Fuente de Verdad

**Enunciado:** Cada dato del sistema tiene un único origen autoritativo.

**Fundamento:** Cuando dos lugares del sistema afirman valores distintos para el mismo dato, se pierde la confianza en todos los datos, no solo en ese.

**Implica:** Toda pantalla, reporte o integración lee el dato de su fuente de verdad (directa o vía proyección declarada); nunca mantiene su propia copia editable en paralelo.

**Prohíbe:** Catálogos paralelos de la misma entidad para distintos módulos, y ediciones locales de un dato que en realidad pertenece a otro módulo.

## Principio 7 — Prohibición de Duplicar Información

**Enunciado:** Un dato de negocio se referencia por clave, no se copia con la intención de mantener dos copias sincronizadas manualmente.

**Fundamento:** Toda duplicación manual es una promesa de sincronización que, tarde o temprano, se rompe.

**Implica:** Cuando un módulo necesita un dato de otro, lo consulta o lo referencia; no lo reimplementa.

**Prohíbe:** Cualquier tabla, colección o catálogo que sea una copia editable de otro ya existente, salvo la excepción funcional y temporal de la captura offline (Principio 2), que se resuelve automáticamente al sincronizar.

## Principio 8 — Arquitectura Basada en Eventos

**Enunciado:** Todo cambio patrimonial relevante se representa como un evento inmutable, no como una edición directa de un valor.

**Fundamento:** Un valor editado sin dejar rastro pierde su historia; un evento nunca la pierde.

**Implica:** El estado actual de cualquier entidad es siempre el resultado de aplicar su historial de eventos. Las correcciones se modelan como eventos compensatorios, referenciando al evento original.

**Prohíbe:** Editar o eliminar un evento ya emitido, o modificar un valor de estado sin que exista un evento que lo respalde.

## Principio 9 — Arquitectura Basada en Flujos Patrimoniales

**Enunciado:** El sistema modela el negocio como tres flujos interconectados — capital, mercancía e información — no como módulos aislados.

**Fundamento:** La realidad operativa de la empresa ocurre así: un despacho mueve mercancía, puede generar una obligación de capital, y siempre genera información documental. Modelarlo como silos separados pierde esa coherencia.

**Implica:** Todo módulo nuevo se ubica explícitamente dentro de uno o más de estos tres flujos y emite eventos hacia el motor de flujos patrimoniales (`04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`).

**Prohíbe:** Que un módulo modifique estado patrimonial (capital o mercancía) directamente, sin pasar por el motor de flujos.

## Principio 10 — Trazabilidad Absoluta

**Enunciado:** Todo dato patrimonial es trazable de extremo a extremo: origen, momento, responsable y empresa/sucursal identificables.

**Fundamento:** Sin trazabilidad, ningún reporte ni auditoría puede considerarse confiable.

**Implica:** Debe ser posible reconstruir, para cualquier entidad, la cadena completa de eventos que la llevó a su estado actual.

**Prohíbe:** Cualquier acción que modifique estado patrimonial sin registrar quién la ejecutó y cuándo.

## Principio 11 — Auditoría Obligatoria

**Enunciado:** Toda creación, modificación, aprobación, anulación o soft delete genera un registro de auditoría independiente.

**Fundamento:** La auditoría es la evidencia objetiva que respalda la trazabilidad (Principio 10); sin ella, la trazabilidad es solo una intención.

**Implica:** El registro de auditoría es de solo lectura para todos los roles, incluidos los administrativos.

**Prohíbe:** Que cualquier rol, incluido el más privilegiado, pueda editar o eliminar el historial de auditoría.

## Principio 12 — Documentación antes que Código

**Enunciado:** Ninguna decisión importante de arquitectura se implementa antes de documentarse y aprobarse.

**Fundamento:** El código sin documentación previa tiende a codificar decisiones implícitas que nadie discutió ni registró; con el tiempo, eso hace el sistema imposible de razonar.

**Implica:** Todo módulo del `08-CATALOGO-DE-MODULOS.md` requiere documentación aprobada antes de iniciar desarrollo; toda decisión relevante se registra en `DECISIONES-ARQUITECTURALES.md` antes de implementarse.

**Prohíbe:** Desarrollar un módulo o tomar una decisión arquitectónica relevante sin su documentación correspondiente, e improvisar cambios de arquitectura directamente en el código.

## Relación con otros documentos

- `01-VISION-ERP.md` — explica el propósito y el contexto de negocio del que se derivan estos principios.
- `02-CONSTITUCION-ERP.md` — convierte estos principios en reglas formales, numeradas y de cumplimiento obligatorio para todo módulo.
- `09-ESTANDARES-DE-DESARROLLO.md` — traduce estos principios en reglas concretas del proceso de construcción de software.

Observaciones:

Este documento no introduce reglas nuevas respecto a `02-CONSTITUCION-ERP.md`; es su explicación razonada. Ante cualquier diferencia de matiz entre ambos, prevalece la Constitución.
