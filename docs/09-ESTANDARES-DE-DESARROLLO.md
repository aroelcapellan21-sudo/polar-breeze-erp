# Estándares de Desarrollo

Estado:

> Vigente — pendiente de revisión y aprobación formal

Objetivo:

Documentar las reglas del proyecto ERP Polar Breeze: reglas de arquitectura que todo módulo debe respetar y reglas de construcción que rigen cómo se desarrolla el código.

Contenido:

### Reglas de Arquitectura

1. El **código** es la clave universal del producto — nunca nombre libre
2. **Offline-first** obligatorio — guarda local, sincroniza automático
3. **Inventario del Chofer** e **Inventario del Encargado** son procesos separados
4. La **configuración variable** vive en Firestore `config/*` — nunca hardcodeada
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
