# Visión del Proyecto — ERP de Flujos Patrimoniales

Estado:

> Vigente — primera versión redactada a partir del índice provisto por el usuario y del conocimiento acumulado del proyecto (Constitución, catálogo de módulos, reglas de arquitectura). Aprobado por el Arquitecto/Product Owner del ERP y por Oliver (dueño de Polar Breeze) el 2026-07-17 (baseline v0.41; ver Registro de Aprobaciones en `13-HISTORIAL-DE-VERSIONES.md`).

Objetivo:

Establecer la visión completa del ERP Polar Breeze: por qué existe, qué problema resuelve, qué filosofía lo guía y hacia dónde va. Este documento es la raíz de la que se deriva toda la Constitución (`02-CONSTITUCION-ERP.md`) y el resto de la documentación arquitectónica. Ante cualquier duda de propósito o dirección del proyecto que no esté resuelta en otro documento, esta Visión es la referencia.

Contenido:

## 1. Introducción

Polar Breeze ERP nace como un sistema de gestión empresarial diseñado no alrededor de módulos aislados (compras, ventas, inventario, contabilidad como silos independientes), sino alrededor de **flujos patrimoniales**: el movimiento continuo y trazable de capital, mercancía e información a través de una o varias empresas.

El nombre "Polar Breeze" identifica a la primera empresa que operará sobre este ERP, pero el sistema en sí mismo se concibe desde el día uno como una plataforma **multiempresa**: un motor capaz de operar el patrimonio de múltiples negocios, cada uno aislado de los demás, sobre una misma base arquitectónica común.

Este documento no describe código ni tecnología de implementación. Describe el **porqué** del sistema — el fundamento sobre el que se apoyan la Constitución y todos los documentos técnicos posteriores.

## 2. Problema que Resuelve el ERP

### ¿Por qué nace?

Nace de la necesidad operativa de una empresa de distribución (Polar Breeze) que mueve capital, mercancía e información a diario a través de choferes, encargados, cuartos fríos, puntos de despacho y consignaciones — y que no encuentra en los ERP genéricos del mercado un modelo que refleje fielmente cómo se mueve realmente su patrimonio.

Los sistemas tradicionales suelen forzar la operación real dentro de módulos rígidos de "compras", "ventas" e "inventario" que no capturan la realidad de flujos simultáneos de efectivo, mercancía física y decisiones documentadas. Polar Breeze ERP nace para modelar esa realidad tal como ocurre, no para adaptarla a un molde genérico.

### ¿Qué problemas elimina?

- La pérdida de trazabilidad entre lo que ocurre en campo (chofer, encargado, cuarto frío, despacho) y lo que queda registrado en el sistema.
- La duplicación de información entre módulos que deberían compartir una única fuente de verdad (catálogos de productos, cuentas, vendedores).
- La imposibilidad de operar sin conexión a internet en el punto donde realmente ocurre el trabajo (bodega, camión, punto de despacho).
- La ambigüedad sobre quién hizo qué cambio, cuándo y por qué, al no existir auditoría ni trazabilidad de eventos.
- La fragilidad de crecer de una empresa a varias, por haber sido diseñado originalmente para una sola.

### ¿Qué lo diferencia de un ERP tradicional?

Un ERP tradicional se organiza por **módulos funcionales** que a menudo terminan comportándose como sistemas independientes conectados por integraciones frágiles. Polar Breeze ERP se organiza por **flujos patrimoniales** que atraviesan los módulos: un mismo evento de negocio (por ejemplo, un despacho) impacta simultáneamente el flujo de mercancía, el flujo de capital (si genera una obligación de pago) y el flujo de información (el documento de despacho y su aprobación), todo de forma consistente y trazable desde un único motor de eventos.

Además, el ERP asume **multiempresa y offline-first como condiciones de diseño**, no como funcionalidades agregadas después.

## 3. Filosofía del Proyecto

El sistema se construye sobre una idea central: **el patrimonio de una empresa (o de varias) debe poder reconstruirse, en cualquier momento, a partir de su historia de eventos** — no a partir de valores editados directamente que perdieron su origen.

De esta idea central se desprenden tres compromisos filosóficos:

1. **Verdad antes que conveniencia.** El sistema nunca sacrifica trazabilidad, auditoría o integridad de datos por velocidad de desarrollo o comodidad de un módulo puntual.
2. **Documentación antes que código.** Ninguna decisión de arquitectura se improvisa en el código; se piensa, se documenta y se aprueba primero (ver Regla importante en `README.md` y Artículo 29 de la Constitución).
3. **Diseño para el tamaño futuro, no solo para el actual.** El sistema se diseña asumiendo que crecerá — en empresas, en sucursales, en módulos — para no tener que reescribirse cuando ese crecimiento llegue.

## 4. Principios Fundamentales

Estos principios son la base filosófica de la que se derivan las reglas formales del Artículo 1 de la Constitución:

- El **código** (no el nombre) es la identidad universal de cualquier entidad de negocio.
- El sistema es **offline-first**: la operación de campo nunca depende de tener conectividad en el instante exacto del evento.
- El sistema es **multiempresa desde el origen**: Polar Breeze es la primera empresa del ecosistema, no el límite de su diseño.
- Toda la plataforma es **multiplataforma** (Android e iOS) sin funcionalidad crítica exclusiva de un sistema operativo.
- Ninguna pérdida de datos es aceptable, ni por fallos de conectividad, ni por cierres inesperados de la aplicación.

## 5. El Motor del Sistema

En el centro del ERP existe un **motor de flujos patrimoniales** (documentado en detalle en `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`): el componente que garantiza que todo movimiento de capital, mercancía o información se registre como un evento coherente, balanceado y trazable.

Ningún módulo modifica el estado patrimonial directamente. Todo módulo emite eventos hacia el motor, y es el motor quien aplica las reglas, valida la integridad y actualiza el estado resultante. Esto es lo que permite que módulos tan distintos como Facturación, Despacho o Inventario compartan una misma noción coherente de "qué tiene la empresa" en todo momento.

## 6. Los Tres Grandes Flujos

El patrimonio de cada empresa se modela a través de tres flujos que, aunque distintos, están permanentemente interconectados por el motor de eventos:

### Flujo de Capital

El movimiento de efectivo y obligaciones financieras: ingresos, egresos, cuentas por pagar, cuentas bancarias, y su clasificación en **Costo, Venta, Distribución y Mantenimiento** (ver Módulo 1 en `08-CATALOGO-DE-MODULOS.md`). Todo movimiento de capital es un evento con origen, destino y clasificación explícitos.

### Flujo de Mercancía

El movimiento físico del inventario: desde su ingreso, pasando por almacén, cuarto frío, consignación y despacho, hasta su salida por venta, merma o devolución. Cada movimiento de mercancía es un evento vinculado a una ubicación y un responsable identificables.

### Flujo de Información

El movimiento de documentos, decisiones y aprobaciones: facturas, notas de crédito, solicitudes de retiro, justificaciones, reportes y decisiones arquitectónicas. Es el flujo que da contexto y respaldo documental a los otros dos — ningún movimiento de capital o mercancía relevante ocurre sin un documento o evento que lo sustente.

## 7. Concepto de Fondos

Un **fondo** es una agrupación patrimonial de capital con un propósito definido dentro de la empresa — por ejemplo, un fondo de Costo, un fondo de Venta, un fondo de Distribución o un fondo de Mantenimiento. Los fondos no son cuentas bancarias en sí mismas, sino clasificaciones de propósito que se aplican a los movimientos y saldos de capital, permitiendo saber no solo cuánto dinero existe, sino **para qué está destinado**.

Cada fondo pertenece a una `empresaId` y, cuando la operación lo requiere, a una `sucursalId`. El motor de flujos patrimoniales es responsable de mantener el balance de cada fondo de forma consistente con los eventos de capital registrados.

## 8. Flujo Principal del Ecosistema

De forma simplificada, el recorrido patrimonial típico dentro del ecosistema es:

1. **Capital ingresa** a la empresa (aporte, cobro, financiamiento) y se clasifica en un fondo.
2. **El capital se convierte en mercancía** (compra) o financia operación (costo, mantenimiento, distribución).
3. **La mercancía se mueve** a través de almacén, cuarto frío, consignación y despacho, generando eventos de flujo de mercancía en cada etapa.
4. **La mercancía se convierte de nuevo en capital** al venderse (facturación), cerrando el ciclo.
5. **Cada etapa genera información** (documentos, aprobaciones, novedades) que queda registrada como flujo de información y respalda la trazabilidad completa del ciclo.
6. **Los reportes y arqueos** leen este historial de eventos para dar visibilidad del estado patrimonial en cualquier momento, sin necesidad de recalcular manualmente.

Este ciclo se repite de forma continua y simultánea para cada empresa del ecosistema, de forma aislada entre sí (Artículo 2 de la Constitución).

## 9. Arquitectura General (Visión de Alto Nivel)

A alto nivel, el ERP se compone de:

- Un **modelo de datos maestro** multiempresa, donde toda entidad relevante lleva `empresaId` (y `sucursalId` cuando aplica).
- Un **motor de flujos patrimoniales** que centraliza la aplicación de eventos sobre capital, mercancía e información.
- Un **catálogo de módulos** (Flujo de Efectivo y Bancos, CXP-Facturación y Reportes, Inventario y Cuarto Frío, Consignaciones, Despacho-Novedades y Caja, Parámetros de Mantenimiento) que son puntos de entrada de eventos hacia el motor, no dueños independientes de su propio estado.
- Una **capa de sincronización offline-first** que permite operar en campo sin conectividad y sincronizar de forma segura al recuperarla.
- Un **motor de permisos** transversal que gobierna el acceso por empresa, rol y acción.

El detalle técnico de esta arquitectura se desarrolla en `03-ARQUITECTURA-GENERAL.md`; esta sección solo fija la visión de alto nivel que ese documento debe respetar.

## 10. Módulos del ERP

El ERP se organiza, en su primera fase, en seis módulos (detallados en `08-CATALOGO-DE-MODULOS.md`, reconciliado con `docs/anexos/02-ESTRUCTURA-OLIVER-FLUJOS-REALES.md`):

1. **Flujo de Efectivo y Bancos** — capital, fondos, cuentas bancarias.
2. **CXP, Facturación y Reportes** — obligaciones con proveedores, pagos, reportes de cuentas por pagar.
3. **Inventario y Cuarto Frío** — productos, novedades, cuarto frío, sobrantes y faltantes.
4. **Consignaciones** — consignaciones, rutas y vías, filtros.
5. **Despacho, Novedades y Caja** — despacho, retiros, facturación de venta, arqueo.
6. **Parámetros de Mantenimiento** — creación de catálogos base: proveedores, productos, condición de pago, vendedores, novedades, consignaciones.

Ningún módulo es una isla: todos comparten los catálogos maestros y emiten eventos hacia el mismo motor de flujos patrimoniales. El crecimiento futuro del ERP se da agregando módulos nuevos a este catálogo, nunca duplicando la lógica de uno existente.

## 11. Motor de Eventos

Todo lo que ocurre en el ERP con relevancia patrimonial se representa como un **evento inmutable**: un hecho registrado que no se edita ni se borra, solo se complementa con eventos compensatorios cuando es necesario corregir algo (Artículo 5 y Artículo 14 de la Constitución).

El motor de eventos es lo que permite:

- Reconstruir el estado de cualquier entidad a partir de su historial.
- Auditar quién hizo qué y cuándo, sin excepción.
- Sincronizar operación offline sin conflictos silenciosos: los eventos capturados sin conexión se integran al historial al reconectar, cada uno con su origen y momento real de captura.

El catálogo formal de eventos del sistema vive en `docs/diagramas/eventos.drawio` y se referencia desde `12-GLOSARIO.md`.

## 12. Beneficios Esperados

- **Trazabilidad total**: cualquier saldo, cualquier cantidad de inventario, cualquier documento puede explicarse mostrando su historia completa de eventos.
- **Confianza en los datos**: al existir una única fuente de verdad por dato y prohibirse la duplicación manual, se elimina la pregunta de "¿cuál número es el correcto?".
- **Operación resiliente**: la naturaleza offline-first evita que la falta de conectividad detenga la operación diaria.
- **Crecimiento sin reescritura**: la arquitectura multiempresa desde el origen permite incorporar nuevas empresas y sucursales como parametrización, no como refactorización estructural (Artículo 28 de la Constitución).
- **Gobernanza clara**: ningún módulo se construye sin documentación aprobada, lo que reduce el riesgo de decisiones técnicas no alineadas con la visión del negocio.

## 13. Reglas Generales del Sistema

Esta Visión no reemplaza a la Constitución — la origina. Las reglas formales e inquebrantables del sistema (una sola fuente de verdad, prohibición de duplicar información, arquitectura basada en eventos y en flujos patrimoniales, multiempresa, trazabilidad absoluta, auditoría obligatoria, soft delete, integridad referencial, versionado de datos, seguridad por roles, motor de permisos, inmutabilidad de documentos aprobados, y las reglas específicas por dominio) están desarrolladas en su totalidad en `02-CONSTITUCION-ERP.md`.

Como regla general de esta Visión: **ninguna decisión de producto puede contradecir la Constitución**; si surge esa tensión, se resuelve actualizando la Constitución de forma explícita (ver Convención de Cambios en ese documento), nunca ignorándola en la implementación.

## 14. Escalabilidad

El ERP está pensado para escalar en tres dimensiones simultáneas:

1. **Escalabilidad de empresas**: de Polar Breeze como primera empresa a un ecosistema con múltiples empresas operando de forma aislada sobre la misma plataforma.
2. **Escalabilidad de sucursales**: dentro de cada empresa, múltiples sucursales, cuartos fríos o puntos de despacho, todos identificados por `sucursalId`.
3. **Escalabilidad de módulos**: nuevos módulos que se incorporan al catálogo siguiendo el mismo patrón de eventos y de documentación previa, sin necesidad de modificar el motor central.

Esta escalabilidad es posible precisamente porque el sistema nunca asumió, ni siquiera en su primera versión, que existiría una sola empresa u operación (Artículo 2 de la Constitución).

## 15. Visión a Largo Plazo

A largo plazo, Polar Breeze ERP aspira a ser la plataforma sobre la que opere no solo Polar Breeze, sino un conjunto de empresas relacionadas o independientes que comparten la misma necesidad: gestionar su patrimonio como flujos trazables de capital, mercancía e información, con la confianza de que cada dato tiene un origen verificable y cada decisión importante quedó documentada antes de implementarse.

El sistema debe poder incorporar nuevas industrias y modelos de negocio adaptando su catálogo de módulos y su modelo de datos, sin necesidad de alterar los principios fundamentales descritos en este documento ni las reglas inquebrantables de la Constitución.

## 16. Objetivo Final

Que en cualquier momento, para cualquier empresa del ecosistema, cualquier persona autorizada pueda responder con certeza y evidencia — no con estimaciones — tres preguntas: **¿cuánto capital tenemos?, ¿cuánta mercancía tenemos?, y cómo llegamos exactamente a ese estado?**

Ese es el propósito último del ERP de Flujos Patrimoniales: convertir el patrimonio de la empresa en algo tan trazable y confiable como su propio historial de eventos.

Observaciones:

Esta versión fue redactada por un asistente de IA a partir del índice de 16 secciones provisto por el usuario y del conjunto de decisiones ya documentadas en este repositorio (Constitución, catálogo de módulos, estándares de desarrollo, decisiones arquitecturales). No es la transcripción literal de un documento externo preexistente — es la primera redacción formal de la Visión y queda sujeta a revisión y ajuste por parte del equipo.
