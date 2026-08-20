# Requerimientos Funcionales - Stock Proyect

Funcionalidades que debe cumplir el sistema de gestión de la distribuidora de productos capilares, según lo relevado en las 5 entrevistas con Darío Fabián Conti.

## RF-01 Gestión de usuarios y seguridad
- Acceso al sistema mediante usuario y contraseña.
- Dos roles: Administrador (acceso total) y Empleado (funciones limitadas).
- Restricción de permisos: el Empleado no puede modificar precios ni stock, ni acceder a la caja diaria y los resúmenes mensuales; esas funciones son exclusivas del Administrador.
- ABM de usuarios (la baja es lógica: el usuario queda inactivo).

## RF-02 Gestión de productos, categorías y stock
- ABM de productos (baja lógica).
- ABM de categorías para agrupar los productos.
- Control de stock: actualización automática del inventario tras cada venta, ingreso de mercadería o ajuste.
- Definición de un stock mínimo por producto.
- Alerta de stock bajo mostrada como listado/panel al ingresar al sistema (consulta pasiva, no notificación push).
- Ajuste manual de stock con registro del motivo.

## RF-03 Vencimientos
- Fecha de vencimiento única por producto, que se actualiza en cada ingreso de mercadería.
- Alerta de productos próximos a vencer (15 días de anticipación) y de productos vencidos, sin bloquear la venta.

## RF-04 Ventas
- Registro de ventas con uno o más productos (cantidad y precio vigente al momento de la venta).
- Cálculo automático del total de la venta.
- Uno o más medios de pago combinables en una misma venta, cada uno con su monto.
- Anulación de ventas con reposición automática del stock (exclusivo del Administrador).
- Generación de comprobante en PDF opcional, únicamente a pedido del cliente.

## RF-05 Clientes y cuenta corriente
- Registro de clientes, solo para ventas a plazo o revendedores.
- Registro de la condición de responsable inscripto del cliente (dato de referencia para la facturación en ARCA, gestionada fuera del sistema).
- Cuenta corriente: la venta a plazo genera una deuda por el total, que el cliente abona en pagos parciales registrados (fecha y monto).
- Interés y fecha límite de pago definidos manualmente por el Administrador.
- Marcado manual de morosidad; al vender a un cliente moroso, el sistema muestra una alerta sin bloquear la venta.

## RF-06 Precios y listas de precios
- Un único precio de venta por producto.
- Modificación de precios exclusiva del Administrador.
- Generación de la lista de precios a partir de los productos y sus precios vigentes, con exportación o impresión (PDF).

## RF-07 Ingreso de mercadería
- Registro del ingreso con producto, proveedor, cantidad y fecha de vencimiento de la tanda.
- Actualización automática del stock y de la fecha de vencimiento del producto.
- Carga del gasto total de la compra (monto total, no por unidad) en el mismo momento del ingreso.

## RF-08 Caja y resúmenes
- Cierre de caja diario sobre una única caja, una vez por día: el sistema calcula el total del día según las ventas, el Empleado verifica contra la caja física y carga la diferencia (faltante o sobrante) con un comentario, sin necesitar al Administrador.
- Resúmenes mensuales (exclusivos del Administrador): total vendido, total de caja acumulado, cantidad de ventas y gasto del mes en compra de mercaderías.

## RF-09 Datos
- Migración de los datos existentes desde las fuentes actuales (papel y planilla de Google).

## Fuera de alcance / trabajo futuro
- Integración con el sistema del laboratorio (opcional, no crítica).
- Gestión de pedidos a proveedores dentro del sistema (hoy se resuelve por teléfono/WhatsApp; se apoya en la alerta de stock bajo).
