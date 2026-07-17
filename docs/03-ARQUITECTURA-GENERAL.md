# Arquitectura General

Estado:

> Vigente — pendiente de revisión y aprobación formal

Objetivo:

Desarrollar en detalle técnico la visión de alto nivel esbozada en `01-VISION-ERP.md` (sección 9): describir las capas que componen el ERP, cómo se relacionan entre sí, y cómo cada una respeta los principios de `00-PRINCIPIOS-DEL-ERP.md` y las reglas de `02-CONSTITUCION-ERP.md`. Este documento describe **componentes y responsabilidades arquitectónicas**, no tecnologías de implementación específicas ni código — esas decisiones viven en `09-ESTANDARES-DE-DESARROLLO.md` y en el repositorio de código, no aquí.

El diagrama complementario de esta visión vive en `docs/diagramas/arquitectura-general.drawio`.

Contenido:

## 1. Vista de Capas

El ERP se organiza en siete capas, cada una con una responsabilidad única y fronteras claras hacia las demás:

1. **Capa de Presentación** — aplicaciones móviles (Android/iOS) y, cuando aplique, interfaces web, usadas por chofer, encargado, administración y roles de reportes.
2. **Capa de Persistencia Local y Sincronización** — almacenamiento local en el dispositivo y la cola de sincronización offline-first.
3. **Capa de API / Puerta de Entrada** — el punto único por el que toda operación de un módulo llega al backend, aplicando primero el Motor de Permisos.
4. **Capa de Módulos de Negocio** — los módulos del catálogo (`08-CATALOGO-DE-MODULOS.md`): Flujo de Efectivo y Bancos, CXP-Facturación y Reportes, Inventario y Cuarto Frío, Consignaciones, Despacho-Novedades y Caja, Parámetros de Mantenimiento.
5. **Motor de Flujos Patrimoniales** — el núcleo del sistema (`04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`): valida, aplica y persiste eventos sobre capital, mercancía e información.
6. **Modelo de Datos Maestro** — la persistencia autoritativa, particionada por `empresaId`/`sucursalId` (`05-MODELO-DE-DATOS-MAESTRO.md`).
7. **Capa de Configuración de Plataforma** — configuración variable de negocio (`config/*`), catálogos maestros compartidos y parámetros por empresa.

Ninguna capa se salta a otra hacia abajo: los módulos de negocio (capa 4) nunca escriben directamente sobre el modelo de datos maestro (capa 6) sin pasar por el motor de flujos patrimoniales (capa 5), consistente con el Principio 9 de `00-PRINCIPIOS-DEL-ERP.md`.

## 2. Capa de Presentación

Responsable de la experiencia del usuario en campo y en oficina. Debe cumplir, sin excepción:

- Paridad funcional entre Android e iOS (Principio 4).
- Capacidad de operar completamente offline en los flujos de campo (inventario del chofer, inventario del encargado, despacho).
- Persistencia de sesión resistente a interrupciones (Principio 5): el progreso de una operación en curso no depende de completarla en una sola sesión ininterrumpida.
- Selector explícito de empresa/sucursal activa cuando el usuario tiene acceso a más de una (Artículo 2.7 de la Constitución) — nunca un contexto implícito o adivinado.

Dentro de esta capa, el rol Administrador/Oliver opera desde el **Hub Admin**: una interfaz web de administración, distinta de las apps móviles de campo (chofer, encargado, despacho), donde vive la gestión de los catálogos de configuración dinámica de la Capa de Configuración de Plataforma (sección 8) — Agregar/Editar/Desactivar sobre `config/*`, nunca eliminación física (Artículo 9 de la Constitución). Ningún otro rol tiene acceso a estos catálogos; lo restringe el Motor de Permisos (sección 9), no la interfaz por sí sola.

## 3. Capa de Persistencia Local y Sincronización

Responsable de que la capa de presentación nunca dependa de conectividad instantánea:

- Toda operación crítica se escribe primero en almacenamiento local, con su `empresaId`/`sucursalId` y usuario ya asignados en el momento de la captura.
- Una cola de sincronización envía los eventos pendientes al backend en cuanto hay conectividad, preservando el orden y el momento real de captura (no el momento de sincronización).
- Los conflictos de sincronización nunca se resuelven descartando datos silenciosamente: se detectan, se registran como `ConflictoSincronizacion` (`05-MODELO-DE-DATOS-MAESTRO.md`) y quedan disponibles para resolución humana explícita — el contrato completo de detección y resolución vive en `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`, sección 13 (Principio 2 y Artículo 1.3 de la Constitución).

## 4. Capa de API / Puerta de Entrada

Es el único punto de entrada de cualquier operación hacia el backend. Sus responsabilidades:

- Autenticar al usuario y resolver su empresa/sucursal activa.
- Invocar al Motor de Permisos (`02-CONSTITUCION-ERP.md`, Artículo 13) antes de permitir que cualquier operación llegue a un módulo de negocio.
- Rechazar cualquier solicitud que no incluya `empresaId` explícito o que intente operar fuera del alcance autorizado del usuario.

Ningún módulo de negocio implementa su propia verificación de permisos paralela; todos dependen de esta capa.

## 5. Capa de Módulos de Negocio

Cada módulo del catálogo (`08-CATALOGO-DE-MODULOS.md`) es un punto de entrada de eventos hacia el Motor de Flujos Patrimoniales, no un dueño independiente de su propio estado patrimonial. Responsabilidades de un módulo:

- Validar las reglas específicas de su dominio (por ejemplo, que una consignación tenga responsable asignado) antes de emitir el evento correspondiente.
- Emitir eventos bien formados hacia el motor, incluyendo `empresaId`, `sucursalId` (si aplica), usuario y payload de negocio (Artículo 15.2 de la Constitución).
- Consumir catálogos maestros existentes (productos, cuentas, vendedores) en lugar de mantener copias propias (Principio 6 y 7).

Ningún módulo nuevo se incorpora a esta capa sin documentación aprobada previamente (Artículo 29 de la Constitución).

## 6. Motor de Flujos Patrimoniales

Es el núcleo arquitectónico del sistema, desarrollado en detalle en `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`. A nivel de arquitectura general, sus responsabilidades son:

- Recibir eventos de los módulos de negocio y validarlos contra las reglas de los tres flujos (capital, mercancía, información).
- Aplicar cada evento de forma atómica: un evento que afecta más de un flujo (por ejemplo, un despacho que genera una obligación de pago) se aplica de forma consistente en ambos, o no se aplica en ninguno.
- Persistir el evento de forma inmutable (Artículo 5 de la Constitución) y actualizar las proyecciones de estado (saldos, existencias) que de él se derivan.
- Servir como única vía de escritura sobre el Modelo de Datos Maestro para cualquier dato con impacto patrimonial.

## 7. Modelo de Datos Maestro

La persistencia autoritativa del sistema, descrita en detalle en `05-MODELO-DE-DATOS-MAESTRO.md`. A nivel de arquitectura general:

- Toda colección o tabla de datos de negocio está particionada por `empresaId` (y por `sucursalId` cuando la entidad opera a nivel de sede).
- Contiene las entidades transaccionales y patrimoniales de cada módulo (`MovimientoCapital`, `Obligacion`, `Despacho`, `Factura`, etc.) y su historial de eventos — los catálogos maestros que estas entidades referencian viven en la Capa de Configuración de Plataforma (sección 8), no aquí.
- El historial de eventos (Artículo 5 de la Constitución) se conserva de forma completa; las proyecciones de estado (saldos actuales, existencias actuales) son vistas derivadas de ese historial, reconstruibles en cualquier momento.

## 8. Capa de Configuración de Plataforma

Contiene la configuración variable de negocio bajo `config/*` (Artículo 16.3 de la Constitución): tasas, clasificaciones de fondos, parámetros de flujo, y **todos los catálogos maestros compartidos** (Artículo 16.1) — `Producto`, `Cuenta`, `CuentaBancaria`, `Fondo`, `Vendedor`, `Cliente`, `Proveedor`, `CondicionPago`, `Ruta`, `AporteCapital` y `MotivoSalidaSinCobro`, particionados por `empresaId` bajo `config/{empresaId}/<colección>/{código}`. Ningún valor de esta capa se hardcodea en la capa de presentación ni en la capa de módulos de negocio. Esta capa es administrada, en su totalidad, desde el Hub Admin (sección 2), con el patrón Agregar/Editar/Desactivar (nunca eliminación física, Artículo 9) y acceso restringido al rol Administrador/Oliver.

*Nota (2026-07-17):* hasta esta versión, la sección 7 ubicaba los catálogos maestros dentro del Modelo de Datos Maestro, en contradicción con esta sección y con la lista de capas de la sección 1. Se resuelve a favor de esta sección: todo catálogo maestro vive en `config/*` (`DECISIONES-ARQUITECTURALES.md`, decisión "Catálogos de Configuración Dinámica (Hub Admin) y unificación de catálogos maestros bajo `config/*`").

## 9. Motor de Permisos (Transversal)

Aunque se invoca principalmente desde la Capa de API (sección 4), el Motor de Permisos (`02-CONSTITUCION-ERP.md`, Artículo 13) es una capa transversal que puede consultarse desde cualquier otra capa cuando sea necesario verificar una acción sensible (aprobar, anular, exportar, cambiar configuración), incluso si la interfaz ya ocultó esa opción al usuario. Los permisos se evalúan siempre por combinación de `empresaId` + rol + acción + entidad (y `sucursalId` cuando aplique).

## 10. Multiempresa a Nivel Arquitectónico

El aislamiento multiempresa (Artículo 2 de la Constitución) no es responsabilidad de una sola capa: es un requisito que atraviesa las siete capas descritas en este documento.

- **Presentación:** selector explícito de empresa/sucursal activa.
- **Sincronización:** todo evento local nace con su `empresaId` asignado.
- **API:** rechaza operaciones sin `empresaId` o fuera de alcance autorizado.
- **Módulos:** ningún módulo asume empresa única.
- **Motor de Flujos Patrimoniales:** aplica eventos y actualiza proyecciones sin mezclar datos entre empresas.
- **Modelo de Datos Maestro:** partición física o lógica por `empresaId`/`sucursalId`.
- **Configuración de Plataforma:** valores particionados por empresa.

## 11. Arquitectura Orientada a Eventos (Vista Arquitectónica)

Complementando el Principio 8 y el Artículo 5 de la Constitución, a nivel de arquitectura el flujo de un evento sigue siempre el mismo camino:

`Presentación → Persistencia Local → Sincronización → API (permisos) → Módulo de Negocio (validación de dominio) → Motor de Flujos Patrimoniales (aplicación y persistencia) → Modelo de Datos Maestro (historial + proyecciones)`

Ningún componente se salta pasos de este camino, ni siquiera por razones de performance. Si una necesidad de performance lo exige, se documenta como una proyección derivada explícita (Artículo 3.3 de la Constitución), nunca como una vía de escritura alterna.

## 12. Reportes y Exportación

La función de reportes y exportación (parte del Módulo 5 — Despacho, Novedades y Caja en `08-CATALOGO-DE-MODULOS.md`, sección "Arqueo de Caja y Facturar") es un **consumidor de solo lectura** del Modelo de Datos Maestro y sus proyecciones; nunca una vía de escritura. Toda exportación respeta el aislamiento por `empresaId` (Artículo 24 de la Constitución) y declara explícitamente su alcance (empresa, sucursal, rango de fechas, versión de reglas usada).

## 13. Integraciones Futuras (Vista Arquitectónica)

Las integraciones externas (pasarelas de pago, facturación electrónica, bancos) se conectan en el límite del sistema, a través de la Capa de API o como consumidores/emisores de eventos — nunca escribiendo directamente sobre el Modelo de Datos Maestro ni sobre los catálogos maestros (Artículo 27 de la Constitución).

## 14. Relación con Otros Documentos

- `00-PRINCIPIOS-DEL-ERP.md` — los principios que esta arquitectura debe respetar en cada capa.
- `01-VISION-ERP.md` — el propósito de negocio del que se deriva esta arquitectura (sección 9, en particular).
- `02-CONSTITUCION-ERP.md` — las reglas formales que cada capa está obligada a cumplir.
- `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` — detalle del componente central descrito en la sección 6.
- `05-MODELO-DE-DATOS-MAESTRO.md` — detalle del modelo de datos descrito en la sección 7.
- `08-CATALOGO-DE-MODULOS.md` — el detalle de cada módulo que vive en la capa de la sección 5.
- `14-REQUISITOS-NO-FUNCIONALES.md` — los principios de continuidad, respaldo y disponibilidad que desarrollan la Capa de Persistencia Local y Sincronización (sección 3) y la Capa de Configuración de Plataforma (sección 8).
- `docs/diagramas/arquitectura-general.drawio` — representación visual de las capas descritas aquí (pendiente de diagramar).

Observaciones:

Este documento fija la arquitectura a nivel de capas y responsabilidades; no prescribe stack tecnológico específico (lenguajes, frameworks, proveedores de nube). Esas decisiones, cuando se tomen, se registran en `DECISIONES-ARQUITECTURALES.md` y deben ser consistentes con las capas y flujos aquí descritos.
