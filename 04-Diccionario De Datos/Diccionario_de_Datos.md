# Diccionario de Datos - Stock Proyect

Listado organizado con las definiciones precisas y rigurosas de los datos del sistema de gestión de la distribuidora de productos capilares. Describe el significado de cada almacenamiento, de sus datos elementales y de las relaciones entre componentes, siguiendo la notación de la metodología estructurada. Versión alineada a las 5 entrevistas (incorpora vencimientos, clientes/cuenta corriente, medios de pago combinados, ingreso de mercadería y cierre de caja).

---

## Notación

| Símbolo | Relación | Significado |
| :--- | :--- | :--- |
| `=` | Definición | "está compuesto de". |
| `+` | Secuencial | Componentes que siempre están presentes. |
| `[ \| ]` | Selección | Alternativas; solo se elige una. |
| `vi{ }vf` | Repetición | El componente se itera entre vi y vf veces. |
| `( )` | Opcional | El componente puede estar o no (repetición 0{ }1). |
| `@` | Identificador | Campo único que no se repite ni admite nulos (clave primaria). |

---

## Almacenamientos

Los almacenamientos son los flujos de datos en reposo del sistema. Cada uno se define como una estructura de datos:

    Rol = @idRol + nombreRol + (descripcionRol)

    Usuario = @idUsuario + nombreUsuario + contraseña + nombreCompleto + idRol

    Categoria = @idCategoria + nombreCategoria + (descripcionCategoria)

    Proveedor = @idProveedor + razonSocial + cuit + (telefono) + (email) + (direccion)

    Producto = @idProducto + codigoProducto + nombreProducto + (descripcionProducto) + idCategoria + idProveedor + precioUnitario + stockActual + stockMinimo + (fechaVencimiento) + estadoProducto

    Cliente = @idCliente + razonSocial + (telefono) + (direccion) + (email) + (documento) + responsableInscripto + estadoMoroso + estadoCliente

    CuentaCorriente = @idCuentaCorriente + idCliente + deudaTotal + (fechaLimitePago) + (interes)

    Pago = @idPago + idCuentaCorriente + fechaPago + montoPago

    Venta = @idVenta + fechaVenta + idUsuario + (idCliente) + 1{DetalleVenta}n + 1{DetallePago}n + totalVenta + estadoVenta

    DetalleVenta = @idDetalle + idVenta + idProducto + cantidad + precioUnitario + subtotal

    DetallePago = @idDetallePago + idVenta + medioPago + monto

    IngresoMercaderia = @idIngreso + idProducto + idProveedor + cantidad + fechaIngreso + (fechaVencimiento) + gastoTotalCompra

    CierreCaja = @idCierreCaja + fechaCierre + totalCalculado + totalVerificado + diferenciaCaja + (comentario) + idUsuario

---

## Estructuras con relación de selección

Datos elementales cuyo valor se elige de un conjunto cerrado de alternativas:

    nombreRol = [ Administrador | Empleado ]
    estado = [ activo | inactivo ]
    estadoVenta = [ confirmada | anulada ]
    estadoMoroso = [ sí | no ]
    responsableInscripto = [ sí | no ]
    medioPago = [ efectivo | tarjeta | transferencia | cuenta corriente ]

---

## Datos elementales

Mínimas unidades indivisibles de datos, con su nombre, descripción, longitud, tipo y dominio de valores admisibles.

| Nombre | Descripción | Longitud | Tipo | Dominio |
| :--- | :--- | :---: | :--- | :--- |
| idRol | Identificador único del rol. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| nombreRol | Nombre del rol de usuario. | 20 | Alfanumérico | Discreto: {(ADM, Administrador); (EMP, Empleado)} |
| descripcionRol | Detalle de los permisos del rol. | 100 | Alfanumérico | Texto libre |
| idUsuario | Identificador único del usuario. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| nombreUsuario | Nombre de acceso al sistema (único). | 30 | Alfanumérico | Texto libre |
| contraseña | Contraseña almacenada cifrada (hash). | 255 | Alfanumérico | Texto libre |
| nombreCompleto | Nombre y apellido del usuario. | 80 | Alfanumérico | Texto libre |
| idCategoria | Identificador único de la categoría. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| nombreCategoria | Nombre de la categoría de producto. | 50 | Alfanumérico | Texto libre |
| descripcionCategoria | Descripción de la categoría. | 150 | Alfanumérico | Texto libre |
| idProveedor | Identificador único del proveedor. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| razonSocial | Razón social del proveedor o cliente. | 100 | Alfanumérico | Texto libre |
| cuit | CUIT del proveedor. | 13 | Alfanumérico | Formato XX-XXXXXXXX-X |
| telefono | Teléfono de contacto. | 30 | Alfanumérico | Texto libre |
| email | Correo electrónico de contacto. | 80 | Alfanumérico | Texto libre |
| direccion | Domicilio. | 120 | Alfanumérico | Texto libre |
| idProducto | Identificador único del producto. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| codigoProducto | Código interno o de barras (único). | 30 | Alfanumérico | Texto libre |
| nombreProducto | Nombre del producto. | 100 | Alfanumérico | Texto libre |
| descripcionProducto | Descripción del producto. | 200 | Alfanumérico | Texto libre |
| precioUnitario | Precio de venta vigente del producto. | 12,2 | Numérico (decimal) | Continuo: {vi: 0; vf: n} |
| stockActual | Cantidad disponible en inventario. | — | Numérico (entero) | Continuo: {vi: 0; vf: n} |
| stockMinimo | Umbral mínimo que dispara la alerta. | — | Numérico (entero) | Continuo: {vi: 0; vf: n} |
| fechaVencimiento | Fecha de vencimiento vigente del producto. | — | Fecha | Continuo: {vi: fecha actual; vf: n} |
| estadoProducto | Estado del producto (continuidad del producto). | 1 | Booleano | Discreto: {(1, activo); (0, inactivo)} |
| idCliente | Identificador único del cliente. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| documento | CUIT o DNI del cliente (opcional). | 13 | Alfanumérico | Texto libre |
| responsableInscripto | Indica si el cliente es responsable inscripto. | 1 | Booleano | Discreto: {(1, sí); (0, no)} |
| estadoMoroso | Indica si el cliente está marcado como moroso. | 1 | Booleano | Discreto: {(1, sí); (0, no)} |
| estadoCliente | Estado del cliente. | 1 | Booleano | Discreto: {(1, activo); (0, inactivo)} |
| idCuentaCorriente | Identificador único de la cuenta corriente. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| deudaTotal | Deuda pendiente del cliente. | 12,2 | Numérico (decimal) | Continuo: {vi: 0; vf: n} |
| fechaLimitePago | Fecha límite de pago definida por el Administrador. | — | Fecha | Continuo: {vi: fecha actual; vf: n} |
| interes | Interés cargado a mano (opcional). | 5,2 | Numérico (decimal) | Continuo: {vi: 0; vf: n} |
| idPago | Identificador único del pago. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| fechaPago | Fecha del pago del cliente. | — | Fecha | Continuo: {vi: 14/05/2026; vf: fecha actual} |
| montoPago | Monto del pago del cliente. | 12,2 | Numérico (decimal) | Continuo: {vi: 0; vf: n} |
| idVenta | Identificador único de la venta. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| fechaVenta | Fecha y hora de la venta. | — | Fecha/Hora | Continuo: {vi: 14/05/2026; vf: fecha actual} |
| totalVenta | Importe total de la venta. | 12,2 | Numérico (decimal) | Continuo: {vi: 0; vf: n} |
| estadoVenta | Estado de la venta. | 15 | Alfanumérico | Discreto: {(C, confirmada); (A, anulada)} |
| idDetalle | Identificador único del detalle de venta. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| cantidad | Cantidad de unidades del producto. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| subtotal | Subtotal de la línea (cantidad x precioUnitario). | 12,2 | Numérico (decimal) | Continuo: {vi: 0; vf: n} |
| idDetallePago | Identificador único del detalle de pago de la venta. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| medioPago | Medio de pago utilizado en la venta. | 20 | Alfanumérico | Discreto: {(EF, efectivo); (TA, tarjeta); (TR, transferencia); (CC, cuenta corriente)} |
| monto | Monto abonado con ese medio de pago. | 12,2 | Numérico (decimal) | Continuo: {vi: 0; vf: n} |
| idIngreso | Identificador único del ingreso de mercadería. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| fechaIngreso | Fecha del ingreso de mercadería. | — | Fecha | Continuo: {vi: 14/05/2026; vf: fecha actual} |
| gastoTotalCompra | Gasto total de la compra (no por unidad). | 12,2 | Numérico (decimal) | Continuo: {vi: 0; vf: n} |
| idCierreCaja | Identificador único del cierre de caja. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| fechaCierre | Fecha del cierre de caja. | — | Fecha | Continuo: {vi: 14/05/2026; vf: fecha actual} |
| totalCalculado | Total de caja calculado por el sistema. | 12,2 | Numérico (decimal) | Continuo: {vi: 0; vf: n} |
| totalVerificado | Total físico verificado por el Empleado. | 12,2 | Numérico (decimal) | Continuo: {vi: 0; vf: n} |
| diferenciaCaja | Diferencia (faltante o sobrante) de la caja. | 12,2 | Numérico (decimal) | Continuo: {vi: -n; vf: n} |
| comentario | Comentario sobre la diferencia de caja. | 200 | Alfanumérico | Texto libre |
