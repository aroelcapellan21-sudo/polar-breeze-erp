# Filosofía del Sistema

Estado:

> Vigente — Aprobado por el Arquitecto/Product Owner del ERP y por Oliver (dueño de Polar Breeze) el 2026-07-17 (baseline v0.41; ver Registro de Aprobaciones en `13-HISTORIAL-DE-VERSIONES.md`)

Objetivo:

Cerrar la biblioteca de arquitectura con la reflexión que está por encima de cualquier regla concreta: por qué este ERP se construye como se construye, y qué mentalidad debe sostener a cualquier persona o IA que trabaje en él después de esta versión. `00-PRINCIPIOS-DEL-ERP.md` explica el fundamento de cada regla; `02-CONSTITUCION-ERP.md` fija esas reglas como ley. Este documento explica el **espíritu** del que ambos se derivan — por eso se numera `99`: no porque sea lo último en importancia, sino porque es lo que queda cuando todo lo demás se ha leído.

Contenido:

## 1. La Filosofía en una Frase

**El patrimonio de una empresa debe ser tan confiable como su historia, y su historia debe ser tan trazable como si nunca se hubiera olvidado nada.**

Todo lo demás en este repositorio — multiempresa, eventos inmutables, fuente única de verdad, documentación antes que código — es una consecuencia técnica de tomar esa frase en serio.

## 2. El Patrimonio como Verdad, no como Opinión

En muchos sistemas, el "saldo actual" es lo que alguien escribió la última vez que lo tocó. Si dos personas dan números distintos, no hay forma objetiva de saber quién tiene razón — solo hay opiniones almacenadas en campos editables.

Este ERP rechaza esa forma de trabajar. El patrimonio (capital, mercancía, información) no es una opinión que se sobrescribe: es el resultado matemático de aplicar una historia de hechos (Artículo 5 de `02-CONSTITUCION-ERP.md`). Cuando el sistema dice "hay tanto capital" o "hay tanta mercancía", esa afirmación siempre puede demostrarse reconstruyendo el camino que llevó hasta ahí. No se le pide al sistema que sea honesto por buena voluntad — se le hace **estructuralmente incapaz de no serlo**.

## 3. La Documentación como Memoria Colectiva

Un equipo de desarrollo —humano o asistido por IA— olvida. Las personas rotan, el contexto de una decisión se pierde, y sin registro, cada nueva persona (o cada nueva conversación con una IA) reinterpreta el sistema desde cero, a veces contradiciendo decisiones ya tomadas por buenas razones que nadie recuerda.

`DECISIONES-ARQUITECTURALES.md` y el resto de esta biblioteca existen para que el proyecto **no dependa de la memoria de nadie en particular**. La regla de "documentar antes de implementar" (Artículo 29 de la Constitución) no es burocracia: es la decisión consciente de que el conocimiento del proyecto vive en el repositorio, no en la cabeza de quien lo escribió.

## 4. Crecer sin Reescribir

La decisión de nacer multiempresa (`02-CONSTITUCION-ERP.md`, Artículo 2) no es una preferencia técnica: es una postura filosófica sobre el tiempo. Un sistema que asume que siempre será pequeño, tarde o temprano, traiciona esa asunción — y el costo de esa traición lo paga quien tenga que reescribirlo bajo presión, con datos reales de por medio.

Diseñar para el tamaño futuro no significa sobre-construir para escenarios improbables. Significa identificar las pocas decisiones —como la partición por `empresaId`— que son baratas de tomar hoy y extremadamente caras de deshacer después, y tomarlas bien desde la primera línea de documentación.

## 5. Humildad Arquitectónica

No todo en esta biblioteca es definitivo, y eso es intencional. El plan de cuentas de `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` está marcado como **Borrador** porque nadie con criterio contable real lo ha validado todavía. La Visión de `01-VISION-ERP.md` está marcada como redactada por una IA a partir de un índice, no como transcripción de un documento original que nunca llegó a existir en esta conversación.

Un sistema serio no finge certeza que no tiene. Marcar explícitamente lo que está pendiente de validación —en lugar de presentarlo con la misma autoridad que una regla ya probada— es parte de la misma disciplina de trazabilidad que exige el Artículo 7 de la Constitución para los datos: si no se puede demostrar el origen y la confianza de una afirmación, se declara abiertamente su estado, en vez de disfrazarla.

## 6. El Rol de la IA en Este Proyecto

Este repositorio se ha construido, en gran parte, en conversación entre una persona y una IA. Eso no es un detalle incidental: es parte de la filosofía del proyecto.

Una IA que participa en este sistema no está exenta de sus reglas (Artículo 26 de la Constitución) — está sujeta a ellas exactamente igual que un desarrollador humano. Pero además de cumplir la regla, debe entender por qué existe: la IA no tiene memoria propia entre conversaciones a menos que algo quede escrito. Por eso, cuando una IA participa en decisiones de este proyecto, su primera responsabilidad no es producir la respuesta más rápida, sino asegurarse de que quede constancia suficiente para que la siguiente conversación —con la misma IA o con otra— no tenga que adivinar lo que ya se decidió.

## 7. Qué Significa "Terminar" Este ERP

No hay una versión final. La Fase 8 del `10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` ("Crecimiento Continuo") se describe explícitamente como una fase que no se completa. Eso es coherente con la filosofía de este documento: un sistema que modela el patrimonio de una empresa viva no puede aspirar a un estado final, porque la empresa tampoco lo tiene.

"Terminar" una parte de este ERP no significa que deje de cambiar — significa que alcanzó un estado en el que puede confiarse, documentarse como tal, y servir de base estable para lo siguiente, sin dejar de estar sujeta a revisión si la realidad del negocio cambia.

## 8. Mensaje a Quien Continúe Este Proyecto

Si estás leyendo esto para retomar el proyecto —seas una persona nueva en el equipo o una IA en una conversación distinta a la que escribió esta versión— esto es lo que necesitas saber antes que cualquier detalle técnico:

- No asumas que puedes deducir una regla por lógica propia si ya está escrita aquí; búscala primero.
- Si algo te parece contradictorio entre dos documentos, la Constitución (`02`) gana, y ese conflicto merece registrarse como una decisión, no resolverse en silencio.
- Si vas a tomar una decisión de arquitectura, escríbela en `DECISIONES-ARQUITECTURALES.md` antes de implementarla, no después.
- Si algo no está claro, pregunta. Esa no es una regla de cortesía — es la única forma en que este sistema puede seguir siendo confiable a medida que crece más allá de lo que cualquiera de nosotros puede recordar de memoria.

## Relación con Otros Documentos

- `00-PRINCIPIOS-DEL-ERP.md` — los principios operativos que esta filosofía fundamenta.
- `02-CONSTITUCION-ERP.md` — las reglas formales que esta filosofía justifica.
- `DECISIONES-ARQUITECTURALES.md` — la memoria concreta que hace posible la sección 3.
- `13-HISTORIAL-DE-VERSIONES.md` — la evidencia de que esta biblioteca, como el propio ERP, crece por versiones sin pretender estar nunca "terminada".

Observaciones:

Este documento es, por diseño, el único de la biblioteca que no impone reglas nuevas ni introduce entidades de datos. Su única función es dar contexto a por qué el resto existe como existe.
