# Constitución ERP

Estado:

> En construcción

Objetivo:

Establecer las reglas inquebrantables del ERP Polar Breeze: los principios que ningún módulo, flujo, integración o decisión técnica puede violar, bajo ninguna circunstancia, sin pasar antes por una modificación explícita de este documento (ver convención de cambios al final).

Este documento es de rango superior a cualquier otro documento del repositorio, salvo `01-VISION-ERP.md`, del cual se deriva. Ante un conflicto entre esta Constitución y cualquier otro documento (`03` a `13`), prevalece la Constitución.

Contenido:

## Artículo 0 — Principio Fundacional: Multiempresa desde el Origen

El ERP Polar Breeze **nace multiempresa**. No existe, ni existirá, una versión "de una sola empresa" que luego se adapte a varias. Esta regla condiciona a todas las demás.

0.1. Toda entidad de datos del sistema pertenece a una y solo una empresa (`empresa_id` o equivalente), sin excepción. No hay tablas, colecciones ni estructuras "globales sin dueño" salvo el propio catálogo de empresas y la configuración de plataforma.

0.2. Ningún módulo puede asumir que existe una sola empresa en el sistema. Todo query, todo cálculo, todo reporte debe estar acotado explícitamente por empresa.

0.3. El aislamiento entre empresas es de datos, de configuración y de flujo — una empresa nunca puede leer, escribir, ni inferir información de otra empresa por ningún camino (UI, API, reportes, exportaciones, notificaciones).

0.4. Los catálogos que parezcan "universales" (productos, cuentas, vendedores, bancos, etc.) están, salvo declaración explícita en `05-MODELO-DE-DATOS-MAESTRO.md`, scoped a la empresa. Cualquier catálogo verdaderamente compartido entre empresas debe documentarse explícitamente como tal, incluyendo por qué es una excepción.

0.5. Un mismo usuario puede pertenecer a más de una empresa, pero cada sesión opera dentro del contexto de **una sola empresa activa a la vez**. El cambio de empresa activa es una acción explícita y auditable, nunca implícita.

0.6. Todo módulo nuevo del `08-CATALOGO-DE-MODULOS.md` debe declarar, en su documentación, cómo respeta el aislamiento multiempresa antes de ser aprobado para desarrollo.

## Artículo 1 — Identidad y Claves

1.1. El **código** es la clave universal de todo producto. Nunca se usa el nombre libre como identificador funcional, clave de búsqueda ni clave foránea. El nombre es solo una etiqueta de presentación.

1.2. Todo código de producto es único **dentro de su empresa** (ver Artículo 0). No se garantiza unicidad global entre empresas.

1.3. Ninguna clave de negocio (código de producto, número de factura, número de cuenta, etc.) puede reutilizarse una vez asignada, ni siquiera si el registro original se elimina o anula.

## Artículo 2 — Datos y Persistencia

2.1. El sistema es **offline-first** de forma obligatoria en todos los módulos operativos (inventario, despacho, facturación en punto de venta). Toda operación crítica se guarda primero en local y sincroniza automáticamente cuando hay conectividad.

2.2. Ninguna operación offline puede perderse silenciosamente. Todo conflicto de sincronización debe ser detectable, registrado y resoluble — nunca se descarta información sin dejar rastro.

2.3. La configuración variable del sistema vive exclusivamente en Firestore, bajo `config/*`, particionada por empresa según el Artículo 0. Ningún valor de configuración de negocio (tasas, clasificaciones, catálogos de cuentas, parámetros de flujo) se hardcodea en el código de la aplicación.

2.4. Todo dato patrimonial (efectivo, inventario, mercancía en tránsito) debe ser trazable: cada cambio de estado tiene un origen, un momento y un responsable identificables.

## Artículo 3 — Procesos Operativos Separados

3.1. El **Inventario del Chofer** y el **Inventario del Encargado** son procesos independientes, con sus propios registros, novedades y responsables. Uno nunca sustituye ni se fusiona automáticamente con el otro; su conciliación es un proceso explícito y auditable.

3.2. Las novedades (dañados, rotos, en mal estado, sobrantes, faltantes) se registran en el proceso donde ocurren y no se reclasifican retroactivamente sin dejar evidencia del cambio.

## Artículo 4 — Plataforma y Continuidad

4.1. Compatibilidad obligatoria con **Android e iOS** en toda funcionalidad orientada a operación de campo (chofer, encargado, despacho). Ninguna funcionalidad crítica puede quedar disponible en una plataforma y no en la otra.

4.2. La **persistencia de sesión** debe ser resistente a interrupciones: cierres inesperados de la app, pérdida de conectividad o reinicio del dispositivo no pueden causar pérdida de contexto de trabajo ni de datos capturados.

## Artículo 5 — Gobernanza Documental

5.1. Toda decisión arquitectónica relevante se documenta en `DECISIONES-ARQUITECTURALES.md` **antes** de implementarse, no después.

5.2. Ningún módulo del `08-CATALOGO-DE-MODULOS.md` puede comenzar a desarrollarse sin tener su documentación aprobada previamente.

5.3. Ningún cambio de código se realiza sin mostrar el plan primero, sin prueba previa, y sin confirmar que no rompe funcionalidad existente (ver `09-ESTANDARES-DE-DESARROLLO.md` para el detalle operativo de estas reglas).

5.4. Ante ambigüedad, se pregunta — nunca se asume. Esto aplica tanto a desarrollo humano como asistido por IA.

## Convención de Cambios a esta Constitución

Ninguna de las reglas anteriores se modifica por conveniencia de una implementación puntual. Un cambio a este documento requiere:

1. Registrar la propuesta y su justificación en `DECISIONES-ARQUITECTURALES.md`.
2. Evaluar el impacto sobre los módulos ya documentados en `08-CATALOGO-DE-MODULOS.md`.
3. Actualizar este documento solo después de lo anterior, nunca antes ni en paralelo a una implementación que ya la esté violando.

Observaciones:

(Espacio reservado)
