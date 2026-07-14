# Motor de Flujos Patrimoniales

Estado:

> Vigente — pendiente de revisión y aprobación formal

Objetivo:

Detallar el componente central del ERP, ya introducido en `01-VISION-ERP.md` (secciones 5 y 11) y `03-ARQUITECTURA-GENERAL.md` (sección 6): el Motor de Flujos Patrimoniales. Este documento especifica cómo el motor recibe, valida, aplica y persiste eventos sobre capital, mercancía e información, y cómo garantiza que el estado patrimonial del sistema sea siempre reconstruible, coherente y aislado por empresa.

Este documento describe **comportamiento y contrato**, no implementación técnica (lenguaje, framework, proveedor). El detalle de implementación, cuando se decida, se registra en `DECISIONES-ARQUITECTURALES.md` y en el repositorio de código.

Contenido:

## 1. Qué es el Motor de Flujos Patrimoniales

Es el componente que centraliza toda modificación de estado patrimonial del sistema. Ningún módulo de negocio (`08-CATALOGO-DE-MODULOS.md`) actualiza saldos, existencias o documentos directamente: todos emiten **eventos** hacia el motor, y es el motor quien decide si el evento es válido, lo aplica y deja constancia inmutable de que ocurrió (Principio 8 y 9 de `00-PRINCIPIOS-DEL-ERP.md`; Artículos 5 y 6 de `02-CONSTITUCION-ERP.md`).

El motor no conoce reglas de presentación ni de experiencia de usuario. Conoce únicamente: eventos, reglas de los tres flujos patrimoniales, y el estado que resulta de aplicarlos.

## 2. Responsabilidades Centrales

1. **Recibir** eventos emitidos por los módulos de negocio, ya validados en su dominio específico por el módulo emisor.
2. **Validar** el evento contra las reglas generales de flujo patrimonial (balance, existencia de las entidades referenciadas, aislamiento por empresa).
3. **Aplicar** el evento de forma atómica sobre el o los flujos que afecta.
4. **Persistir** el evento de forma inmutable en el historial (Artículo 5.4 de la Constitución).
5. **Actualizar** las proyecciones de estado (saldos, existencias) derivadas de ese historial.
6. **Rechazar**, sin aplicar ni persistir como válido, cualquier evento que no cumpla las reglas — dejando registro del intento y su motivo de rechazo.

## 3. El Evento como Unidad Atómica

Todo evento que llega al motor tiene una estructura mínima obligatoria (consistente con el Artículo 15.2 de la Constitución):

- **Tipo de evento** — del catálogo documentado en `docs/diagramas/eventos.drawio` y `12-GLOSARIO.md` (por ejemplo, `ProductoDespachado`, `PagoRegistrado`, `ConsignacionCreada`).
- **`empresaId`** — obligatorio, sin excepción.
- **`sucursalId`** — cuando el evento ocurre a nivel de una sede u operación local.
- **Entidad afectada** — la referencia (por código, nunca por nombre; Principio 1) al recurso de negocio que el evento modifica.
- **Payload de negocio** — los datos específicos del evento (montos, cantidades, clasificaciones, responsables).
- **Usuario emisor** — quién generó el evento.
- **Momento de captura** — el instante real en que ocurrió el hecho, que puede ser anterior al instante de sincronización (Artículo 1.3 de la Constitución y sección 3 de `03-ARQUITECTURA-GENERAL.md`).

Un evento, una vez aceptado por el motor, es inmutable. No se edita ni se borra (Artículo 5.4 de la Constitución); toda corrección es un evento nuevo (sección 8 de este documento).

## 4. Ciclo de Vida de un Evento

1. **Emisión** — un módulo de negocio construye el evento tras validar sus propias reglas de dominio.
2. **Recepción** — el motor recibe el evento a través de la Capa de API, ya autenticado y con permisos verificados (Artículo 13 de la Constitución).
3. **Validación de motor** — el motor verifica: pertenencia a la empresa/sucursal correcta, existencia de las entidades referenciadas (Artículo 10, integridad referencial), balance del movimiento (sección 6), y que el evento no repita una clave de negocio ya retirada (Artículo 9.3, soft delete).
4. **Aplicación** — si es válido, el motor aplica el efecto del evento sobre el flujo o los flujos correspondientes.
5. **Persistencia** — el evento se escribe en el historial inmutable, y las proyecciones de estado afectadas se actualizan.
6. **Confirmación o rechazo** — el motor devuelve al módulo emisor el resultado; un evento rechazado nunca queda persistido como aplicado, aunque el intento puede quedar registrado con fines de auditoría (Artículo 8).

## 5. Los Tres Flujos y su Tratamiento en el Motor

### Flujo de Capital

El motor mantiene el balance de cada fondo (`01-VISION-ERP.md`, sección 7) por `empresaId`. Todo evento de capital debe declarar su clasificación (Costo, Venta, Distribución, Mantenimiento — Artículo 18.1 de la Constitución) y su cuenta o fondo de origen/destino. El motor rechaza cualquier evento de capital que no pueda vincularse a un fondo y una clasificación válidos.

### Flujo de Mercancía

El motor mantiene la existencia de inventario por ubicación (almacén, cuarto frío, vehículo del chofer, punto de despacho) dentro de cada `sucursalId`. Todo evento de mercancía declara origen, destino (cuando aplica) y responsable. El Inventario del Chofer y el Inventario del Encargado se tratan como procesos independientes dentro del motor (Artículo 17.1 de la Constitución): sus eventos no se fusionan automáticamente; una conciliación entre ambos es, en sí misma, un evento explícito.

### Flujo de Información

El motor conserva todo documento y decisión (facturas, notas de crédito, aprobaciones, justificaciones) como parte del historial de eventos, vinculado a los eventos de capital o mercancía que respalda. Un evento de capital o mercancía relevante que carezca de su respaldo documental correspondiente es rechazado por el motor.

## 6. Eventos Multi-Flujo y Atomicidad

Muchos eventos de negocio impactan más de un flujo a la vez. Por ejemplo, un despacho con condición de pago genera simultáneamente:

- Un evento de flujo de mercancía (salida de inventario).
- Un evento de flujo de capital (cuenta por pagar pendiente).
- Un evento de flujo de información (el documento de despacho).

El motor aplica estos efectos como una **unidad atómica**: o los tres se registran de forma consistente, o ninguno se aplica. No existe un estado intermedio donde la mercancía ya salió pero la obligación de pago o el documento no se generaron.

## 7. Proyecciones de Estado

Los saldos de fondos, las existencias de inventario y cualquier "valor actual" que el usuario ve en pantalla son **proyecciones**: vistas calculadas a partir de aplicar el historial completo de eventos, no valores editados directamente. Esto significa que:

- Una proyección siempre puede reconstruirse desde cero reprocesando el historial de eventos (Artículo 7.2 de la Constitución).
- Una proyección puede optimizarse (por ejemplo, cachear el saldo actual en lugar de recalcularlo en cada consulta), pero esa optimización nunca sustituye al historial como fuente de verdad (Artículo 3 de la Constitución).
- Toda proyección respeta el aislamiento por `empresaId`/`sucursalId` de los eventos que la componen.

## 8. Eventos Compensatorios y Corrección de Errores

Ningún evento ya persistido se edita ni se elimina. Cuando un evento resultó incorrecto (por ejemplo, una cantidad mal capturada), la corrección se modela como un **evento compensatorio** que:

- Referencia explícitamente al evento original que corrige.
- Declara el motivo de la corrección.
- Se persiste como un evento nuevo, con su propio usuario y momento.

El estado resultante después de la corrección es siempre el resultado de aplicar el evento original **y** su compensatorio, en ese orden — nunca el resultado de "como si el original nunca hubiera ocurrido".

## 9. Validación de Dominio (Módulo) vs. Validación de Flujo (Motor)

Existe una separación clara de responsabilidades:

- **El módulo de negocio** valida las reglas específicas de su dominio antes de emitir el evento (por ejemplo, que una consignación tenga un responsable asignado, o que una factura tenga al menos un producto).
- **El motor** valida las reglas generales de flujo patrimonial que aplican a cualquier evento, sin importar de qué módulo provenga (balance, integridad referencial, aislamiento por empresa, no reutilización de claves retiradas).

Un módulo nunca puede "convencer" al motor de aplicar un evento que rompa estas reglas generales, sin importar qué tan válido sea ese evento dentro de la lógica particular del módulo.

## 10. Multiempresa en el Motor

El motor no procesa eventos "en general": procesa eventos **dentro del contexto de una empresa** (y, cuando aplica, una sucursal). Ninguna validación, aplicación o proyección cruza el límite de `empresaId` (Artículo 2.5 de la Constitución). El motor está diseñado para operar múltiples empresas de forma concurrente, cada una con su propio historial de eventos y proyecciones completamente aislados.

## 11. Versionado de Reglas del Motor

Las reglas de validación del motor (clasificaciones válidas, tasas, parámetros de flujo) pueden cambiar en el tiempo. Cuando cambian:

- Los eventos ya persistidos se interpretan siempre con la versión de reglas vigente en el momento de su captura (Artículo 11.2 de la Constitución), no con la versión actual.
- Un recálculo con reglas nuevas sobre eventos históricos es una operación explícita y versionada, nunca un efecto secundario automático de actualizar la configuración.

## 12. Rechazo y Manejo de Errores

Cuando el motor rechaza un evento, el módulo emisor recibe el motivo específico del rechazo (por ejemplo, "fondo no existe para esta empresa", "cantidad negativa no permitida en este tipo de evento"). El rechazo no genera un evento de negocio válido, pero el intento puede registrarse con fines de auditoría y diagnóstico, sin mezclarse nunca con el historial de eventos aplicados exitosamente.

## 13. Relación con la Sincronización Offline

Los eventos capturados sin conexión (`03-ARQUITECTURA-GENERAL.md`, sección 3) llegan al motor en el momento de la sincronización, pero conservan su momento real de captura. El motor los procesa en el orden que corresponde a ese momento de captura, no al orden de llegada por sincronización, de forma que el historial resultante refleje la secuencia real de los hechos.

## 14. Relación con Otros Documentos

- `00-PRINCIPIOS-DEL-ERP.md` (Principios 8 y 9) — el fundamento filosófico de este motor.
- `02-CONSTITUCION-ERP.md` (Artículos 5, 6, 7, 8, 9, 11, 13, 14, 15) — las reglas formales que el motor está obligado a cumplir.
- `03-ARQUITECTURA-GENERAL.md` (secciones 6 y 11) — la ubicación del motor dentro de las capas del sistema y el camino que sigue todo evento.
- `05-MODELO-DE-DATOS-MAESTRO.md` — la estructura de datos donde el motor persiste eventos y proyecciones.
- `12-GLOSARIO.md` y `docs/diagramas/eventos.drawio` — el catálogo formal de tipos de evento que el motor reconoce.

Observaciones:

Este documento define el contrato de comportamiento del motor. La implementación técnica específica (por ejemplo, si el historial de eventos vive en una colección de Firestore, en un event store dedicado, etc.) se define en `05-MODELO-DE-DATOS-MAESTRO.md` y en decisiones registradas en `DECISIONES-ARQUITECTURALES.md`, siempre respetando el comportamiento aquí descrito.
