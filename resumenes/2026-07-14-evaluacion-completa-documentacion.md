# Evaluación Completa de la Documentación — Polar Breeze ERP

Fecha: 2026-07-14
Alcance: los 25 archivos Markdown del repositorio (README, CLAUDE.md, DECISIONES-ARQUITECTURALES.md, `docs/00`–`13` y `99`, anexos, diagramas y resúmenes).

---

## 1. Qué está bien

1. **Coherencia conceptual sostenida.** La idea central (patrimonio = historia de eventos inmutables; estado actual = proyección) se mantiene sin desviaciones a través de Visión, Constitución, Motor (`04`), Modelo de Datos (`05`), Reglas Contables (`06`) y Flujos (`07`). Es raro ver una biblioteca documental de este tamaño sin contradicciones de fondo en su tesis principal.

2. **Cadena de derivación clara y trazable.** Cada documento declara de qué otro se deriva y qué detalle delega: `08` (funcionalidades) → `05` (entidades) → `11` (campos); `02` (regla) → `06` (detalle contable); `07` (nombres de evento propuestos) → `12` (catálogo formal). El diccionario incluso se autoimpone la regla de consistencia bidireccional con el modelo maestro.

3. **Gobernanza documental ejemplar.** `DECISIONES-ARQUITECTURALES.md` registra cada decisión con contexto, alternativas y consecuencias; `13-HISTORIAL-DE-VERSIONES.md` da la vista cronológica; el Artículo 14.3 (no editar decisiones, reemplazarlas) se cumple en la práctica (véanse las notas de reemplazo).

4. **Humildad arquitectónica real, no declarativa.** El plan de cuentas está marcado Borrador con bloqueo explícito de la Fase 2; la Visión declara ser redacción de IA sin documento fuente; el anexo `01-PENDIENTE-VALIDACION-CONTABLE.md` convierte lo pendiente en checklist accionable con procedimiento de cierre. Esto es coherente con `99-FILOSOFIA` sección 5.

5. **Multiempresa aplicada de forma transversal y consistente.** `empresaId`/`sucursalId` aparecen coherentemente en Constitución, arquitectura por capas, motor, modelo de datos, diccionario y flujos. La Fase 7 (segunda empresa real como prueba de aislamiento) es un hito de validación bien pensado.

6. **El plan maestro ordena por dependencias reales**, no por numeración de módulos, y documenta sus riesgos (plan de cuentas sin validar, offline-first postergado, segunda empresa prematura).

---

## 2. Inconsistencias encontradas entre documentos

### 2.1 Jerarquía normativa ambigua (01 vs 02)
- `02-CONSTITUCION` (Objetivo): es "la norma de más alto rango… **superada únicamente por `01-VISION-ERP.md`**".
- `99-FILOSOFIA` (sección 8): "Si algo te parece contradictorio entre dos documentos, **la Constitución (`02`) gana**" — sin excepción para la Visión.
- Además, la Visión fue redactada *después* y *a partir de* la Constitución (decisión del 2026-07-14), de modo que el documento "superior" se derivó del "inferior".
**Impacto:** ante un conflicto real entre `01` y `02` no hay regla única de resolución. Conviene decidir una jerarquía y alinearla en los tres documentos.

### 2.2 Referencias al "Artículo 1.3" incorrectas (error repetido en 3 lugares)
El Artículo 1.3 de la Constitución es **offline-first**. Sin embargo:
- `05-MODELO-DE-DATOS-MAESTRO.md` §1: "Un código, una vez asignado, no se reutiliza (Artículo 1.3)".
- `11-DICCIONARIO-DE-DATOS.md` §1 (tipo Código): "Nunca se reutiliza una vez retirado (Artículo 1.3)".
- `11-DICCIONARIO-DE-DATOS.md` §9 (Factura): "Nunca reutilizable… (Artículo 1.3)".
La regla de no reutilización de claves es el **Artículo 9.3** (y la de código como clave, el 1.2). Tres documentos citan el artículo equivocado.

### 2.3 Estados contradictorios entre `13-HISTORIAL` y los propios documentos
La tabla de completitud de `13` marca `08-CATALOGO-DE-MODULOS.md` y `09-ESTANDARES-DE-DESARROLLO.md` como **Vigente**, pero ambos archivos declaran en su encabezado **"En construcción"** y mantienen Observaciones "(Espacio reservado)". Uno de los dos lados está desactualizado.

### 2.4 Tecnología prescrita en la Constitución pese a la regla de "sin stack"
- `02-CONSTITUCION` Artículo 16.3 y `09-ESTANDARES` regla 4 fijan que la configuración vive en **Firestore** (`config/*`).
- `03-ARQUITECTURA` (Observaciones) y `04-MOTOR` (Observaciones) afirman que **no se ha decidido stack tecnológico** y que esa decisión se registrará en `DECISIONES-ARQUITECTURALES.md` — donde no existe ninguna decisión que elija Firestore.
**Impacto:** o Firestore ya es una decisión (y falta su ADR), o la Constitución está prescribiendo tecnología que el resto de la biblioteca dice no haber elegido.

### 2.5 Entidades exigidas por la Constitución que no existen en el modelo
El Artículo 16.1 lista como catálogos maestros: "productos, cuentas, vendedores, bancos, **clientes, proveedores**". Ni `05-MODELO` ni `11-DICCIONARIO` definen `Cliente` ni `Proveedor`. Consecuencias en cascada:
- `Factura` no tiene campo de cliente — imposible sostener "Cuentas por Cobrar" (Cuenta 3 del plan de cuentas) sin saber quién debe.
- El Artículo 20.1 exige registrar obligaciones "con contraparte", pero la contraparte no tiene catálogo.

### 2.6 La obligación (Cuenta por Pagar) no existe como entidad
`F2`/`F10`, `06` §5 y el evento `ObligacionRegistrada` dependen de una obligación con monto, contraparte, vencimiento y saldo pendiente proyectado. Pero ni `05` ni `11` definen una entidad `Obligacion`/`CuentaPorPagar` — violando la regla de consistencia bidireccional que `11` se autoimpone ("ninguna entidad se detalla aquí sin existir allí, y viceversa") en su sentido inverso: hay conceptos operativos sin entidad.

### 2.7 Ventas a crédito sin flujo ni eventos
El plan de cuentas propone **Cuenta 3 — Cuentas por Cobrar** (ventas a crédito), pero `F7 — Facturación` genera `CapitalIngresado` de inmediato (asume venta de contado). No existe flujo de venta a crédito ni evento de cobro posterior (`CobroRegistrado` o similar). El plan de cuentas y los flujos de negocio describen negocios distintos.

### 2.8 Nomenclatura de eventos que viola su propia convención
El Artículo 5.3 fija "verbo en pasado + entidad" y da como ejemplo `ProductoDespachado`. El catálogo formal de `12-GLOSARIO` registra **`Despachado`** (sin entidad) — el único de los 20 eventos sin sustantivo, contradiciendo el ejemplo de la propia Constitución.

### 2.9 Referencias rotas menores
- `99-FILOSOFIA` §4 cita "la regla de no añadir abstracción prematura en `09-ESTANDARES-DE-DESARROLLO.md`" — esa regla no existe en `09` (la más cercana es "una sola cosa a la vez").
- `02-CONSTITUCION` Artículo 9.3 remite a "`10-PLAN-MAESTRO-DE-IMPLEMENTACION.md` sobre unicidad de claves" — el Plan Maestro no desarrolla ese tema.
- `02` Artículo 6.2 dice "(Artículo `04-MOTOR-DE-FLUJOS-PATRIMONIALES.md`)" — llama "Artículo" a un documento.
- `13-HISTORIAL` v0.24 tiene el commit como "— (este commit)"; el hash real (`d52ab54`) nunca se rellenó.
- El árbol de estructura del `README.md` no incluye `CLAUDE.md` ni `resumenes/`, que ya existen.

### 2.10 `Cuenta`: ¿el código es el número o son dos campos?
`05` define "código de cuenta (1 a 6)" como la clave. `11` hereda el campo común `código` **y además** agrega `número` (entero 1-6). Dos campos para el mismo concepto, sin regla de correspondencia.

### 2.11 Novedades de cuarto frío: ubicación mezclada con condición
`11` define `NovedadInventario.tipo` como enumeración (`cuarto_frío` / `dañado` / `roto` / `mal_estado` / `sobrante` / `faltante`): mezcla un **lugar** (cuarto frío) con **condiciones**. Un producto dañado *en* el cuarto frío solo puede registrar uno de los dos hechos. Además, su `procesoOrigen` solo admite `InventarioChofer`/`InventarioEncargado`, pero el cuarto frío es una `Sucursal` (Artículo 22.1), no uno de esos dos procesos. El Artículo 22.2 pide que las novedades de cuarto frío sean "distinguibles" — el modelo actual lo logra a costa de perder la condición del producto.

### 2.12 Colisión de prefijo `00` (conocida, sin resolver)
`00-MANIFIESTO` y `00-PRINCIPIOS` comparten prefijo. Está documentada como excepción a la espera de decisión del usuario — sigue pendiente de resolverse (sugerencia en §4).

---

## 3. Qué falta

1. **Entidades `Cliente` y `Proveedor`** (exigidas por Artículo 16.1) y la entidad **`Obligacion`/`CuentaPorPagar`** — ver §2.5 y §2.6.
2. **Flujo y eventos de merma/pérdida/condonación.** El Artículo 6.3 y el propio Manifiesto ("cuando existe una pérdida, una merma, un daño o una condonación, el sistema… la registra") lo exigen, pero no hay flujo Fx ni evento (`MermaRegistrada`, `PerdidaRegistrada`, `CondonacionRegistrada`) en el catálogo de 20 eventos. Las `Novedades` registran la detección, pero no el evento patrimonial de baja que balancea el flujo de mercancía/capital.
3. **Moneda.** El Artículo 28.1 anticipa "nuevas monedas", pero el tipo `Monto` no tiene divisa, y ni `Empresa` ni `MovimientoCapital` declaran moneda. Si el ecosistema crecerá a varios países, la moneda por empresa (mínimo) debería fijarse ya en el modelo — es exactamente el tipo de decisión "barata hoy, carísima después" que `99` §4 describe.
4. **Resolución de conflictos de sincronización.** `03` §3 dice que los conflictos "se detectan, se registran y quedan disponibles para resolución explícita", pero ningún documento define quién resuelve, con qué criterios, ni qué pasa con eventos dependientes de un conflicto pendiente. Para un sistema offline-first, este es el vacío técnico más relevante.
5. **Confiabilidad del `momentoCaptura`.** El motor ordena por momento de captura (reloj del dispositivo), pero nada trata la desincronización o manipulación de relojes de dispositivos offline — afecta directamente al Artículo 30 (huella) y al ordenamiento del historial.
6. **Requisitos no funcionales:** respaldos y recuperación, retención de datos, privacidad de datos personales (usuarios, clientes), volumetría esperada, disponibilidad. Nada de esto tiene documento ni sección.
7. **Ciclo de vida de sesión/autenticación** más allá de "credenciales (detalle técnico fuera de alcance)": expiración, revocación de membresías, offboarding de un usuario con eventos históricos.
8. **Contenido visual de los 6 diagramas `.drawio`** (excluido por instrucción previa, pero sigue siendo el faltante material más visible).
9. **Proceso de "aprobación formal".** Casi todos los documentos están "Vigente — pendiente de revisión y aprobación formal", pero ningún documento define quién aprueba, cómo, ni cómo se registra. La categoría "Vigente pero nunca aprobado" puede volverse permanente sin ese proceso.

---

## 4. Qué mejoraría

1. **Elevar `08-CATALOGO-DE-MODULOS.md` al estándar que la propia Constitución exige.** El Artículo 29.2 pide que todo módulo declare alcance `empresaId`/`sucursalId`, eventos que emite/consume y catálogos que consume. Ninguno de los 5 módulos del catálogo (listas de viñetas) cumpliría hoy su propio criterio de aprobación. Es el documento fundacional más débil de la biblioteca y del que todo lo demás se derivó.
2. **Resolver la colisión `00`:** propuesta simple — el Manifiesto es la "puerta de entrada para sentir el proyecto" (su propio Objetivo); dejarlo como `00` y renumerar Principios como parte del bloque conceptual, o adoptar convención de letras (`00A`, `00B`). Cualquiera de las dos, registrada como decisión.
3. **Definir la jerarquía documental completa en un solo lugar** (por ejemplo, en el README): Manifiesto → Visión → Constitución → documentos técnicos → anexos, con la regla única de resolución de conflictos, y corregir `02`/`99` para que digan lo mismo.
4. **Pase de limpieza de referencias cruzadas** (los errores de §2.2 y §2.9): barato de hacer ahora, caro cuando haya código citando artículos equivocados.
5. **Decidir formalmente lo de Firestore:** o registrar el ADR "elección de Firestore para configuración" o desespecificar el Artículo 16.3 y la regla 4 de `09` a "almacén de configuración de plataforma".
6. **Extender el anexo de validaciones o crear un anexo 02** con las preguntas de negocio no contables que hoy están implícitas: ¿hay ventas a crédito? ¿quiénes son las contrapartes? ¿moneda única? ¿periodicidad de conciliación chofer/encargado? El mecanismo del anexo 01 funcionó bien; replicarlo.
7. **Separar ubicación y condición en `NovedadInventario`** (dos campos: `ubicacionOrigen` y `condicion`) — resuelve §2.11 sin romper el Artículo 22.2.
8. **Automatizar la consistencia que hoy es manual:** las reglas "13 se actualiza en el mismo commit", "ningún evento fuera del catálogo", "entidad en 05 ⇔ entidad en 11" son verificables con scripts/CI en el futuro repositorio de código; documentar esa intención evita que la disciplina dependa solo de memoria.

---

## 5. Priorización sugerida

| Prioridad | Acción | Motivo |
|---|---|---|
| 1 | Agregar `Cliente`, `Proveedor`, `Obligacion` al modelo (05+11) y decidir ventas contado/crédito | Bloquea la coherencia del plan de cuentas y de F2/F7/F10 |
| 2 | Definir moneda en el modelo | Decisión barata hoy, estructural después |
| 3 | Elevar `08` al estándar del Artículo 29.2 | Es el criterio de aprobación de todo módulo |
| 4 | Resolver jerarquía 01/02 y colisión `00` | Ambigüedad normativa de base |
| 5 | Limpieza de referencias y estados (§2.2, §2.3, §2.9) | Bajo costo, alto retorno en confiabilidad |
| 6 | Flujo de mermas/pérdidas + resolución de conflictos de sync | Vacíos funcionales/técnicos reales |

---

## 6. Conclusión

La biblioteca es notablemente sólida para su etapa: tesis arquitectónica coherente, gobernanza real y honestidad sobre lo pendiente. Sus debilidades no son de visión sino de **completitud del dominio** (clientes, proveedores, obligaciones, crédito, moneda, mermas) y de **higiene de referencias cruzadas**. Ninguna inconsistencia encontrada contradice la tesis central del sistema; casi todas son resolubles con una ronda de decisiones registradas en `DECISIONES-ARQUITECTURALES.md` antes de que exista la primera línea de código — que es exactamente cuando este proyecto quiere resolverlas.
