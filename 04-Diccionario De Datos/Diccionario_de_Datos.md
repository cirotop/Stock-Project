# Diccionario de Datos - Stock Proyect

Listado organizado con las definiciones precisas y rigurosas de los datos del sistema de gestión de la distribuidora de productos capilares. Describe el significado de cada almacenamiento, de sus datos elementales y de las relaciones entre componentes, siguiendo la notación de la metodología estructurada.

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

    Usuario = @idUsuario + nombreUsuario + contraseña + nombreCompleto + idRol + estadoUsuario + fechaAlta

    Categoria = @idCategoria + nombreCategoria + (descripcionCategoria)

    Proveedor = @idProveedor + razonSocial + cuit + (telefono) + (email) + (direccion) + estadoProveedor

    Producto = @idProducto + codigoProducto + nombreProducto + (descripcionProducto) + idCategoria + idProveedor + precioUnitario + stockActual + stockMinimo + estadoProducto

    Lote = @idLote + idProducto + idProveedor + cantidad + fechaIngreso + (fechaVencimiento)

    MovimientoStock = @idMovimiento + idProducto + (idLote) + tipoMovimiento + cantidad + fechaMovimiento + idUsuario + (motivo)

    Cliente = @idCliente + razonSocialCliente + (documento) + (telefono) + (email) + saldoCtaCte + estadoCliente

    Venta = @idVenta + fechaVenta + (idCliente) + idUsuario + medioPago + 1{DetalleVenta}n + totalVenta + estadoVenta

    DetalleVenta = @idDetalle + idVenta + idProducto + cantidad + precioUnitario + subtotal

    Comprobante = @idComprobante + idVenta + tipoComprobante + numeroComprobante + fechaEmision + totalComprobante

---

## Estructuras con relación de selección

Datos elementales cuyo valor se elige de un conjunto cerrado de alternativas:

    estado = [ activo | inactivo ]

    medioPago = [ efectivo | tarjeta | transferencia | cuentaCorriente ]

    tipoMovimiento = [ entrada | salida | ajuste ]

    estadoVenta = [ confirmada | anulada ]

    tipoComprobante = [ facturaA | facturaB | facturaC | ticket ]

---

## Datos elementales

Mínimas unidades indivisibles de datos, con su nombre, descripción, longitud, tipo y dominio de valores admisibles.

| Nombre | Descripción | Longitud | Tipo | Dominio |
| :--- | :--- | :---: | :--- | :--- |
| idRol | Identificador único del rol. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| nombreRol | Nombre del rol de usuario. | 30 | Alfanumérico | Discreto: {(ADM, Administrador); (EMP, Empleado)} |
| descripcionRol | Detalle de los permisos del rol. | 100 | Alfanumérico | Texto libre |
| idUsuario | Identificador único del usuario. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| nombreUsuario | Nombre de acceso al sistema (único). | 30 | Alfanumérico | Texto libre |
| contraseña | Contraseña almacenada cifrada (hash). | 255 | Alfanumérico | Texto libre |
| nombreCompleto | Nombre y apellido del usuario. | 80 | Alfanumérico | Texto libre |
| estadoUsuario | Estado del usuario. | 1 | Booleano | Discreto: {(1, activo); (0, inactivo)} |
| fechaAlta | Fecha y hora de creación del usuario. | — | Fecha/Hora | Continuo: {vi: 14/05/2026; vf: fecha actual} |
| idCategoria | Identificador único de la categoría. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| nombreCategoria | Nombre de la categoría de producto. | 50 | Alfanumérico | Texto libre |
| descripcionCategoria | Descripción de la categoría. | 150 | Alfanumérico | Texto libre |
| idProveedor | Identificador único del proveedor. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| razonSocial | Razón social del proveedor. | 100 | Alfanumérico | Texto libre |
| cuit | CUIT del proveedor. | 13 | Alfanumérico | Formato XX-XXXXXXXX-X |
| telefono | Teléfono de contacto. | 30 | Alfanumérico | Texto libre |
| email | Correo electrónico de contacto. | 80 | Alfanumérico | Texto libre |
| direccion | Domicilio. | 120 | Alfanumérico | Texto libre |
| estadoProveedor | Estado del proveedor. | 1 | Booleano | Discreto: {(1, activo); (0, inactivo)} |
| idProducto | Identificador único del producto. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| codigoProducto | Código interno o de barras (único). | 30 | Alfanumérico | Texto libre |
| nombreProducto | Nombre del producto. | 100 | Alfanumérico | Texto libre |
| descripcionProducto | Descripción del producto. | 200 | Alfanumérico | Texto libre |
| precioUnitario | Precio de venta vigente. | 12,2 | Numérico (decimal) | Continuo: {vi: 0; vf: n} |
| stockActual | Cantidad disponible en inventario. | — | Numérico (entero) | Continuo: {vi: 0; vf: n} |
| stockMinimo | Umbral mínimo que dispara la alerta. | — | Numérico (entero) | Continuo: {vi: 0; vf: n} |
| estadoProducto | Estado del producto. | 1 | Booleano | Discreto: {(1, activo); (0, inactivo)} |
| idLote | Identificador único del lote. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| cantidad | Cantidad de unidades involucradas. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| fechaIngreso | Fecha de ingreso del lote. | — | Fecha | Continuo: {vi: 14/05/2026; vf: fecha actual} |
| fechaVencimiento | Fecha de vencimiento del lote. | — | Fecha | Continuo: {vi: fecha actual; vf: n} |
| idMovimiento | Identificador único del movimiento. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| tipoMovimiento | Tipo de movimiento de stock. | 20 | Alfanumérico | Discreto: {(E, entrada); (S, salida); (A, ajuste)} |
| fechaMovimiento | Fecha y hora del movimiento. | — | Fecha/Hora | Continuo: {vi: 14/05/2026; vf: fecha actual} |
| motivo | Motivo o comentario del movimiento. | 150 | Alfanumérico | Texto libre |
| idCliente | Identificador único del cliente. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| razonSocialCliente | Nombre o razón social del cliente. | 100 | Alfanumérico | Texto libre |
| documento | CUIT o DNI del cliente. | 13 | Alfanumérico | Texto libre |
| saldoCtaCte | Saldo de cuenta corriente. | 12,2 | Numérico (decimal) | Continuo: {vi: 0; vf: n} |
| estadoCliente | Estado del cliente. | 1 | Booleano | Discreto: {(1, activo); (0, inactivo)} |
| idVenta | Identificador único de la venta. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| fechaVenta | Fecha y hora de la venta. | — | Fecha/Hora | Continuo: {vi: 14/05/2026; vf: fecha actual} |
| totalVenta | Importe total de la venta. | 12,2 | Numérico (decimal) | Continuo: {vi: 0; vf: n} |
| medioPago | Medio de pago utilizado. | 20 | Alfanumérico | Discreto: {(EF, efectivo); (TA, tarjeta); (TR, transferencia); (CC, cuenta corriente)} |
| estadoVenta | Estado de la venta. | 15 | Alfanumérico | Discreto: {(C, confirmada); (A, anulada)} |
| idDetalle | Identificador único del detalle de venta. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| subtotal | Subtotal de la línea (cantidad x precioUnitario). | 12,2 | Numérico (decimal) | Continuo: {vi: 0; vf: n} |
| idComprobante | Identificador único del comprobante. | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| tipoComprobante | Tipo de comprobante emitido. | 15 | Alfanumérico | Discreto: {(FA, factura A); (FB, factura B); (FC, factura C); (TK, ticket)} |
| numeroComprobante | Número de comprobante (único). | 20 | Alfanumérico | Texto libre |
| fechaEmision | Fecha y hora de emisión del comprobante. | — | Fecha/Hora | Continuo: {vi: 14/05/2026; vf: fecha actual} |
| totalComprobante | Importe total del comprobante. | 12,2 | Numérico (decimal) | Continuo: {vi: 0; vf: n} |
