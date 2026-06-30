# Reglas de Negocio

---

## Hechos — condiciones que deben ser verdaderas

- **RN-01** Cada producto tiene definido un stock mínimo de seguridad.
- **RN-02** Cada lote registra cantidad, fecha de vencimiento y proveedor asociado.
- **RN-03** Cada producto está asociado al menos a un proveedor.
- **RN-04** Toda venta genera un comprobante con fecha, detalle de productos, total y medio de pago.
- **RN-05** El sistema maneja dos roles de usuario: Administrador (acceso total) y Empleado/Cajero (acceso restringido).
- **RN-06** Toda operación que modifica datos queda registrada con el usuario que la realizó y la fecha/hora.

## Restricciones — acciones prohibidas según una condición

- **RN-07** No se puede confirmar una venta si la cantidad solicitada supera el stock disponible del producto.
- **RN-08** No se puede vender un producto perteneciente a un lote vencido.
- **RN-09** No se puede modificar la cantidad de stock a mano: todo cambio se realiza mediante un movimiento registrado (venta, ingreso o ajuste).
- **RN-10** No se puede confirmar una venta sin al menos un producto cargado.
- **RN-11** Una venta confirmada no se puede editar; únicamente puede anularse.
- **RN-12** La salida de mercadería respeta el criterio FEFO: no se puede descontar de un lote más nuevo si existe stock de uno con vencimiento más próximo.
- **RN-13** Solo el Administrador puede modificar precios, listas de precios y saldos.
- **RN-14** Solo el Administrador puede eliminar registros (productos, ventas, proveedores) y anular ventas.
- **RN-15** Ningún usuario puede operar el sistema sin autenticarse con usuario y contraseña.
- **RN-16** Las contraseñas no se pueden almacenar en texto plano: se guardan cifradas (hash).
- **RN-17** No se puede repetir el código de un producto ni el CUIT de un proveedor (deben ser únicos).

## Acciones disparadoras — disparan una acción al cumplirse una condición

- **RN-18** Al confirmar una venta, se descuenta automáticamente del stock la cantidad de cada producto vendido.
- **RN-19** Cuando el stock de un producto llega o cae por debajo del mínimo, el sistema genera una alerta (amarilla si está cerca del mínimo, roja si hay quiebre).
- **RN-20** Cuando un lote está a 30 días o menos de su vencimiento, el sistema emite una alerta de producto próximo a vencer.
- **RN-21** Al registrar un ingreso de mercadería, se genera o amplía el lote correspondiente y se actualiza el stock.
- **RN-22** Al anular una venta, se repone automáticamente el stock que había sido descontado.

## Cálculos — calculan un valor a partir de otros

- **RN-23** El subtotal de cada línea de venta se calcula como cantidad × precio unitario.
- **RN-24** El total de una venta se calcula como la suma de los subtotales de todos sus detalles.
- **RN-25** El precio unitario de cada producto se toma de la lista de precios vigente al momento de la venta.

## Inferencias — concluyen algo al cumplirse una condición

- **RN-26** Si un cliente con cuenta corriente supera la fecha de vencimiento de pago sin saldar, su cuenta se considera morosa.
- **RN-27** Si un producto no registra ventas durante un período prolongado, se considera de baja rotación para los reportes.
