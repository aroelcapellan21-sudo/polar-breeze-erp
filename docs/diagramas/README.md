# Diagramas

Estado:

> En construcción

Objetivo:

Almacenar los diagramas de arquitectura, flujos y modelos de datos del ERP Polar Breeze (por ejemplo, exportados desde herramientas como draw.io, Excalidraw, Mermaid, etc.), referenciados desde los documentos en `docs/`.

Contenido:

Seis diagramas `.drawio` creados con la estructura mínima de un archivo en blanco (sin contenido visual todavía — se completan directamente en draw.io, no en este repositorio como texto):

| Archivo | Representa visualmente a | Documento(s) que complementa |
|---|---|---|
| `flujo-capital.drawio` | El flujo de capital: fondos, cuentas, movimientos de ingreso/egreso | `01-VISION-ERP.md` (sección 6), `06-REGLAS-CONTABLES-Y-FINANCIERAS.md` |
| `flujo-mercancia.drawio` | El flujo de mercancía: inventario chofer/encargado, cuarto frío, consignación, despacho | `01-VISION-ERP.md` (sección 6), `07-FLUJOS-DE-NEGOCIO.md` |
| `flujo-informacion.drawio` | El flujo de información: documentos, aprobaciones, decisiones que respaldan a los otros dos flujos | `01-VISION-ERP.md` (sección 6) |
| `arquitectura-general.drawio` | Las 7 capas del sistema y el camino que sigue todo evento de extremo a extremo | `03-ARQUITECTURA-GENERAL.md` |
| `base-datos.drawio` | Las entidades del modelo de datos maestro y sus relaciones | `05-MODELO-DE-DATOS-MAESTRO.md`, `11-DICCIONARIO-DE-DATOS.md` |
| `eventos.drawio` | El catálogo de eventos del sistema | `12-GLOSARIO.md` (sección C), `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md` |

Cada diagrama queda pendiente de diagramarse visualmente; esta tabla documenta su propósito y alcance mientras eso ocurre.

Observaciones:

Las imágenes generadas a partir de los diagramas se almacenan en `imagenes/`.
