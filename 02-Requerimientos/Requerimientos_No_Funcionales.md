# Requerimientos No Funcionales - Stock Proyect

Cualidades y restricciones técnicas que debe cumplir el sistema, derivadas de lo relevado en las entrevistas con Darío Fabián Conti.

## RNF-01 Seguridad
- Acceso autenticado con usuario y contraseña.
- Las contraseñas se almacenan cifradas (hash), nunca en texto plano.
- Restricción de funciones según el rol del usuario (Administrador / Empleado).

## RNF-02 Integridad de los datos
- La información no se elimina físicamente: las bajas son lógicas (registros inactivos).
- Toda operación que modifica datos queda registrada con el usuario que la realizó y la fecha/hora.

## RNF-03 Integridad y consistencia de los datos financieros
- Los datos de caja, cuenta corriente y pagos deben mantenerse consistentes entre sí (la deuda refleja las ventas a plazo y los pagos registrados; el total de caja refleja las ventas del día), por tratarse de información sensible para el negocio.

## RNF-04 Rendimiento
- El sistema debe agilizar el registro de ventas, el cobro y la generación de listas de precios respecto del proceso manual actual.

## RNF-05 Usabilidad
- Interfaz simple que reduzca la curva de aprendizaje en la transición del papel y la planilla de Google a una herramienta digital.
- Las alertas de stock bajo y de vencimientos se presentan como un panel/listado de consulta al ingresar al sistema.

## RNF-06 Alcance y despliegue
- Aplicación de escritorio, de uso interno de la distribuidora, para un único local.

## RNF-07 Escalabilidad
- El sistema debe quedar preparado para incorporar a futuro nuevas funciones e integraciones (por ejemplo, el laboratorio) y soportar un mayor volumen de ventas.

## RNF-08 Disponibilidad y respaldo
- Respaldo automático de la información, dada la dependencia previa del papel y la planilla de Google, para asegurar la continuidad del negocio.
