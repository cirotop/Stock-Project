# Requerimientos - Stock Proyect

---

## 1. Requerimientos Funcionales

### RF-01 Usuarios y seguridad
- Acceso mediante usuario y contraseña.
- Dos roles: Administrador (acceso total) y Empleado (funciones limitadas).
- El Administrador gestiona productos, categorías y ajustes de stock; el Empleado consulta y registra los movimientos de stock habilitados.
- ABM de usuarios.

### RF-02 Productos y categorías
- ABM de productos (baja lógica: el producto queda inactivo, no se elimina).
- ABM de categorías para agrupar los productos.
- Cada producto tiene código único, nombre, descripción y categoría.

### RF-03 Control de stock
- Registro de ingreso de mercadería que incrementa el stock del producto.
- Actualización automática del stock ante cada ingreso, movimiento o ajuste.
- Definición de un stock mínimo por producto.
- Alerta de stock bajo mostrada como listado/panel al ingresar al sistema.
- Ajuste manual de stock con registro del motivo.

### RF-04 Vencimiento y stock vencido (eje principal)
- Registro de la fecha de vencimiento al ingresar la mercadería (una por caja).
- Distinción del stock de cada producto entre **vigente** y **vencido**.
- Cuando un producto alcanza su fecha de vencimiento, su stock pasa a contabilizarse como stock vencido.
- El stock vencido se descuenta del total únicamente al retirarse para su devolución al laboratorio, mediante la lectura de su etiqueta.
- Alerta de productos próximos a vencer y de productos vencidos, mostrada como listado/panel.

### RF-05 Datos
- Migración de los datos de stock existentes desde las fuentes actuales (papel y planilla de Google).

### Fuera de alcance
Punto de venta / ventas, facturación, listas de precios, proveedores, clientes, cuenta corriente, medios de pago, caja y resúmenes, e integración con el sistema del laboratorio (el laboratorio se contempla solo como destino físico de la mercadería vencida).

---

## 2. Requerimientos No Funcionales


### 2.1 De producto

- **RNF-01 Seguridad:** acceso autenticado con usuario y contraseña; restricción de funciones según el rol.
- **RNF-02 Fiabilidad e integridad:** las bajas son lógicas (no se elimina información) y toda operación que modifica el stock queda registrada con el usuario y la fecha/hora.
- **RNF-03 Usabilidad:** interfaz simple que reduzca la curva de aprendizaje en la transición del papel y la planilla de Google; las alertas de stock bajo, próximos a vencer y vencidos se presentan como un panel de consulta al ingresar.
- **RNF-04 Eficiencia:** el sistema debe agilizar el registro y la consulta del stock respecto del proceso manual actual, con tiempos de respuesta ágiles.
- **RNF-05 Disponibilidad:** respaldo automático de la información, dada la dependencia previa del papel y la planilla de Google.

### 2.2 De proceso (organizacionales)

- **RNF-06 Estándares de desarrollo:** el sistema se modela con UML (casos de uso, clases, secuencia), se documenta la base de datos con el modelo Entidad-Relación y se versiona y resguarda en un repositorio (GitHub), bajo metodología ágil.
- **RNF-07 Entorno de despliegue:** aplicación de escritorio, de uso interno, para un único local (con su depósito principal y el depósito de mercadería vencida en el mismo edificio).

### 2.3 Externos

- **RNF-08 Interoperabilidad:** en esta etapa el sistema no requiere integrarse con sistemas externos; la integración con el sistema del laboratorio queda fuera de alcance.

---

## 3. Requerimientos del Dominio

- **RD-01** Los productos llegan en cajas, y todos los productos de una misma caja comparten la misma fecha de vencimiento.
- **RD-02** El total de stock de un producto debe coincidir siempre con sus existencias físicas. El stock vigente y el stock vencido se registran en tablas separadas, y el total físico surge de la suma de ambos (stock vigente + stock vencido). El stock vencido permanece físicamente separado en otro depósito del mismo edificio, pero se sigue contabilizando dentro de ese total Es decir solo se trabaja con el stock vigente, el otro es solo para un conteo.
- **RD-03** La mercadería vencida no se descuenta del stock mientras siga en el edificio; recién se descuenta cuando se retira para devolverla al laboratorio y se lee su etiqueta.
