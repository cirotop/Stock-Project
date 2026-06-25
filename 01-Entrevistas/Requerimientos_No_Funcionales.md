# Análisis y Comparación de Requerimientos No Funcionales (RNF)

Este documento compara la estructura de requerimientos no funcionales propuesta con las necesidades detectadas en la entrevista a Darío Fabián Conti.

## 1. Análisis de RNF Propuestos
La lista actual de RNF es sólida y cubre las dimensiones críticas para una aplicación de gestión interna:

* **Seguridad e Integridad (RNF1, RNF2):** Cruciales dado que la entrevista enfatiza restricciones de acceso para modificar precios y balances.
* **Rendimiento (RNF3):** Alineado con el objetivo de "agilizar" la creación de listas y el control de pedidos.
* **Usabilidad (RNF4):** Correctamente identificada. Al reemplazar procesos manuales (papel/hoja de cálculo), la interfaz debe reducir la curva de aprendizaje.
* **Alcance y Despliegue (RNF5, RNF6):** Reflejan con precisión que el sistema es de uso interno y para una única ubicación física.
* **Escalabilidad y Disponibilidad (RNF7, RNF8):** Consideraciones técnicas necesarias para asegurar que el sistema soporte la proyección a futuro (integraciones, volumen de ventas).

## 2. Comparativa con la Entrevista

| Requerimiento (RNF) | Prioridad / Justificación | Relación con la Entrevista |
| :--- | :--- | :--- |
| **Seguridad** | Alta | Restricción estricta de usuarios (admin/empleado). |
| **Rendimiento** | Alta | Necesidad de agilizar ventas y seguimiento. |
| **Usabilidad** | Media-Alta | Transición de gestión manual a digital. |
| **Integridad** | Alta | Protección de datos contra modificaciones no autorizadas. |

## 3. Recomendaciones Adicionales
Para fortalecer la estructura técnica, se sugieren los siguientes ajustes:
* **RNF-Backup (Nuevo):** Dada la dependencia actual del papel y Google Sheets, un requerimiento de respaldo automático es vital para asegurar la continuidad.
* **RNF-Tiempo de Respuesta (Específico):** En lugar de solo "agilizar", definir una métrica (ej: "las alertas push deben enviarse en <5 segundos tras la venta").