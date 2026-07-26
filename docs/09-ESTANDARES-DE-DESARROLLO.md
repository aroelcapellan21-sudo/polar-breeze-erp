# Estándares de Desarrollo

Estado:

> Vigente — Aprobado por el Arquitecto/Product Owner del ERP el 2026-07-17 (baseline v0.41; ver Registro de Aprobaciones en `13-HISTORIAL-DE-VERSIONES.md`)

Objetivo:

Documentar las reglas del proyecto ERP Polar Breeze: reglas de arquitectura que todo módulo debe respetar y reglas de construcción que rigen cómo se desarrolla el código.

Contenido:

### Reglas de Arquitectura

1. El **código** es la clave universal del producto — nunca nombre libre
2. **Offline-first** obligatorio — guarda local, sincroniza automático
3. **Inventario del Chofer** e **Inventario del Encargado** son procesos separados
4. La **configuración variable** vive en Firestore `config/*` — nunca hardcodeada; incluye todo catálogo maestro compartido (Artículo 16.1), administrado desde el Hub Admin con patrón Agregar/Editar/Desactivar
5. **Compatibilidad obligatoria** Android + iOS
6. **Persistencia de sesión** resistente a interrupciones

### Reglas de Construcción

7. Nunca tocar código sin mostrar el plan primero
8. Nunca commitear sin prueba previa
9. Una sola cosa a la vez — no mezclar mejoras
10. Si algo no está claro, preguntar — nunca asumir
11. Confirmar que no rompe nada antes de tocar código
12. Toda decisión importante se documenta antes de implementarse
13. Ningún módulo se desarrolla sin su documentación aprobada

Observaciones:

Las Reglas de Arquitectura (1-6) son una versión condensada, de consulta rápida, de lo ya desarrollado en detalle en `00-PRINCIPIOS-DEL-ERP.md` y fijado como ley en `02-CONSTITUCION-ERP.md`; ante cualquier diferencia de matiz, prevalece la Constitución. Las Reglas de Construcción (7-13) son el detalle operativo del Artículo 26.2 y 29.4 de la Constitución (reglas para IA y para nuevos módulos) aplicado a cualquier persona o IA que modifique código en el futuro repositorio del ERP.

### Presentación de Información: Resumen por Defecto con Expansión Opcional

Este requerimiento aplica de forma transversal a todo el ERP: inventario, caja, facturas, cuarto frío, y cualquier módulo futuro. Cualquier información mostrada al usuario (dashboard, reportes, historiales, notificaciones) sigue este patrón:

- **Comportamiento por defecto:** el sistema presenta siempre la versión corta/resumida de la información primero.
- **Acción del usuario:** aparece un control visible (tipo "Ver más") al final del contenido resumido.
- **Al activarlo:** el sistema despliega la versión completa/detallada de esa misma información, sin navegar a otra pantalla.
- **Origen de los datos:** tanto la versión corta como la larga se generan a partir de la misma fuente de datos (la capa de Proyecciones/Lectura). El resumen no se guarda como un dato independiente de forma predeterminada — se calcula a partir del historial completo, para que un cambio futuro en la lógica del resumen no requiera tocar los datos originales.
- **Optimización permitida para rangos largos:** el sistema puede mantener cálculos pre-guardados (snapshots) que se actualizan con cada evento nuevo, para evitar recalcular todo desde cero en historiales extensos — siempre y cuando ese cálculo guardado siga siendo trazable al historial original de eventos (Artículo 3.2 de la Constitución: el historial de eventos es la única fuente de verdad; un snapshot es una optimización de lectura, nunca una fuente alternativa).
- **Referencia de UX:** mismo patrón usado en resultados de búsqueda de Google — resumen breve con opción de expandir ("truncar contenido con call to action").
