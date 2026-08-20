# Reglas de Negocio

> Versión alineada a las 5 entrevistas. Se corrigieron las reglas basadas en supuestos de lotes/FEFO y se agregaron las reglas de cuenta corriente, caja y vencimientos. Se eliminaron RN-12 (FEFO) y RN-27 (baja rotación) por no tener respaldo en ninguna entrevista; los números se conservan para no romper referencias previas.

---

## Hechos — condiciones que deben ser verdaderas

- **RN-01** Cada producto tiene definido un stock mínimo de seguridad.
- **RN-02** Cada producto tiene una única fecha de vencimiento vigente, que se actualiza en cada ingreso de mercadería (cada compra llega en una sola tanda con la misma fecha).
- **RN-03** Cada producto está asociado al menos a un proveedor.
- **RN-04** El comprobante de venta no es obligatorio: se genera un PDF únicamente cuando el cliente lo solicita.
- **RN-05** El sistema maneja dos roles de usuario: Administrador (acceso total) y Empleado (acceso restringido).
- **RN-06** Toda operación que modifica datos queda registrada con el usuario que la realizó y la fecha/hora.
- **RN-07** Una venta puede pagarse combinando más de un medio de pago (cada uno con su monto).
- **RN-08** El gasto de mercadería se carga como un monto total de la compra (no por unidad), en el mismo momento del ingreso de mercadería.
- **RN-09** Se registra si un cliente es responsable inscripto, como dato de referencia para la facturación en ARCA, que se gestiona fuera del sistema.
- **RN-10** El local tiene una única caja y se realiza un solo cierre de caja por día.
- **RN-11** Solo los clientes registrados (ventas a plazo o revendedores) tienen cuenta corriente.

## Restricciones — acciones prohibidas según una condición

- **RN-12** No se puede confirmar una venta si la cantidad solicitada supera el stock disponible del producto.
- **RN-13** El sistema no bloquea la venta de un producto vencido: solo avisa que está vencido y el usuario decide si lo vende.
- **RN-14** No se puede modificar la cantidad de stock a mano: todo cambio se realiza mediante una operación registrada (venta, ingreso de mercadería o ajuste de stock).
- **RN-15** No se puede confirmar una venta sin al menos un producto cargado.
- **RN-16** Una venta confirmada no se puede editar; únicamente puede anularse.
- **RN-17** Solo el Administrador puede modificar precios, listas de precios y saldos.
- **RN-18** Ningún registro se elimina: los productos, proveedores y usuarios se dan de baja de forma lógica (quedan inactivos). Solo el Administrador puede inactivar registros y anular ventas.
- **RN-19** Ningún usuario puede operar el sistema sin autenticarse con usuario y contraseña.
- **RN-20** Las contraseñas no se pueden almacenar en texto plano: se guardan cifradas (hash).
- **RN-21** No se puede repetir el código de un producto ni el CUIT de un proveedor (deben ser únicos).
- **RN-22** La condición de moroso de un cliente no es automática: la coloca el Administrador manualmente, según su criterio, ya que el margen de tolerancia varía con cada cliente.
- **RN-23** Al vender a un cliente marcado como moroso, el sistema muestra una alerta pero no bloquea la venta.

## Acciones disparadoras — disparan una acción al cumplirse una condición

- **RN-24** Al confirmar una venta, se descuenta automáticamente del stock la cantidad de cada producto vendido.
- **RN-25** Cuando el stock de un producto queda en o por debajo de su mínimo, el sistema lo marca para mostrarlo en el listado de productos con stock bajo.
- **RN-26** Cuando un producto está a 15 días o menos de su fecha de vencimiento, el sistema emite una alerta de producto próximo a vencer.
- **RN-27** Al registrar un ingreso de mercadería, se suma la cantidad al stock del producto y se actualiza su fecha de vencimiento.
- **RN-28** Al anular una venta, se repone automáticamente el stock que había sido descontado.
- **RN-29** Una venta a plazo a un cliente registrado genera una deuda por el total de la venta en su cuenta corriente.
- **RN-30** Al registrar un pago de un cliente, el monto se descuenta de la deuda de su cuenta corriente.

## Cálculos — calculan un valor a partir de otros

- **RN-31** El subtotal de cada línea de venta se calcula como cantidad × precio unitario.
- **RN-32** El total de una venta se calcula como la suma de los subtotales de todos sus detalles.
- **RN-33** El precio unitario de cada producto se toma del precio vigente del producto al momento de la venta.
- **RN-34** El total de caja del día se calcula a partir de las ventas registradas en ese día.
- **RN-35** La diferencia de caja (faltante o sobrante) se calcula como el total físico verificado por el Empleado menos el total calculado por el sistema.
- **RN-36** El resumen mensual (exclusivo del Administrador) incluye total vendido, total de caja acumulado, cantidad de ventas y gasto del mes en compra de mercaderías.


