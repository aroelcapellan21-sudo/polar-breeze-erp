# Polar Breeze ERP — Biblioteca de Arquitectura

Este repositorio es la **Biblioteca de Arquitectura oficial** del ERP Polar Breeze. Contiene **exclusivamente documentación** — no contiene código fuente, componentes, APIs, frontend ni librerías.

El proyecto está diseñado bajo una **arquitectura ERP multiempresa basada en flujos patrimoniales** (capital, mercancía e información). Polar Breeze es únicamente la **primera empresa** del ecosistema; el sistema nunca asume una sola empresa, y toda entidad de negocio contempla `empresaId` y, cuando aplica, `sucursalId` desde su diseño (ver `docs/02-CONSTITUCION-ERP.md`, Artículo 2).

Es la **Constitución Arquitectónica** del proyecto: la fuente de verdad sobre visión, arquitectura, modelo de datos, reglas contables y financieras, flujos de negocio, catálogo de módulos, estándares de desarrollo y plan de implementación.

Todo desarrollo del ERP **debe basarse en estos documentos**. El código del sistema vive en un repositorio distinto.

## Regla importante

- Este repositorio nunca debe mezclarse con código fuente. No se crean componentes, APIs, frontend ni se agregan librerías aquí.
- Toda decisión importante de arquitectura debe documentarse aquí **antes** de implementarse.
- Ningún módulo puede desarrollarse sin tener previamente su documentación aprobada.
- Toda la documentación asume arquitectura multiempresa: ningún documento puede describir un flujo, módulo o entidad como si existiera una sola empresa.

## Estructura

```
polar-breeze-erp/
├── README.md
├── CLAUDE.md
├── DECISIONES-ARQUITECTURALES.md
├── resumenes/
└── docs/
    ├── 00-MANIFIESTO-DEL-ERP.md
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
        ├── README.md
        └── 01-PENDIENTE-VALIDACION-CONTABLE.md
```

## Convención

Toda decisión arquitectónica relevante (elección de tecnología, cambios en el modelo de datos, modificación de un flujo, etc.) se registra en [`DECISIONES-ARQUITECTURALES.md`](./DECISIONES-ARQUITECTURALES.md), de modo que el historial de decisiones sea tan valioso como el propio código y facilite la incorporación de nuevos desarrolladores o asistentes de IA al proyecto.
