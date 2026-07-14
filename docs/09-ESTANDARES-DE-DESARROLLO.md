# Estándares de Desarrollo

Estado:

> En construcción

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

(Espacio reservado)
