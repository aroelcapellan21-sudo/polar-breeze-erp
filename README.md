# Polar Breeze ERP — Documentación Maestra

Este repositorio contiene **exclusivamente la arquitectura del ERP Polar Breeze**.

No contiene código fuente, componentes ni APIs. Es la **Constitución Arquitectónica** del proyecto: la fuente de verdad sobre visión, arquitectura, modelo de datos, reglas contables y financieras, flujos de negocio, catálogo de módulos, estándares de desarrollo y plan de implementación.

Todo desarrollo del ERP **debe basarse en estos documentos**. El código del sistema vive en un repositorio distinto.

## Regla importante

- Este repositorio nunca debe mezclarse con código fuente.
- Toda decisión importante de arquitectura debe documentarse aquí **antes** de implementarse.
- Ningún módulo puede desarrollarse sin tener previamente su documentación aprobada.

## Estructura

```
polar-breeze-erp/
├── README.md
├── DECISIONES-ARQUITECTURALES.md
└── docs/
    ├── 00-PRINCIPIOS-DEL-ERP.md
    ├── 01-VISION-ERP.md
    ├── 02-CONSTITUCION-ERP.md
    ├── 03-ARQUITECTURA-GENERAL.md
    ├── 04-MOTOR-DE-FLUJOS-PATRIMONIALES.md
    ├── 05-MODELO-DE-DATOS-MAESTRO.md
    ├── 06-REGLAS-CONTABLES-Y-FINANCIERAS.md
    ├── 07-FLUJOS-DE-NEGOCIO.md
    ├── 08-CATALOGO-DE-MODULOS.md
    ├── 09-ESTANDARES-DE-DESARROLLO.md
    ├── 10-PLAN-MAESTRO-DE-IMPLEMENTACION.md
    ├── 11-DICCIONARIO-DE-DATOS.md
    ├── 12-GLOSARIO.md
    ├── 13-HISTORIAL-DE-VERSIONES.md
    ├── 99-FILOSOFIA-DEL-SISTEMA.md
    ├── diagramas/
    │   ├── README.md
    │   ├── imagenes/
    │   ├── flujo-capital.drawio
    │   ├── flujo-mercancia.drawio
    │   ├── flujo-informacion.drawio
    │   ├── arquitectura-general.drawio
    │   ├── base-datos.drawio
    │   └── eventos.drawio
    └── anexos/
        └── README.md
```

## Convención

Toda decisión arquitectónica relevante (elección de tecnología, cambios en el modelo de datos, modificación de un flujo, etc.) se registra en [`DECISIONES-ARQUITECTURALES.md`](./DECISIONES-ARQUITECTURALES.md), de modo que el historial de decisiones sea tan valioso como el propio código y facilite la incorporación de nuevos desarrolladores o asistentes de IA al proyecto.
