# Reglas de Negocio - Stock Proyect

---

## Hechos

- **RN-01** Cada producto tiene definido un stock mínimo de seguridad.
- **RN-02** Cada producto pertenece a una categoría.
- **RN-03** Cada producto tiene una fecha de vencimiento, que se registra al ingresar la mercadería.
- **RN-04** Los productos llegan en cajas, y todos los productos de una misma caja comparten la misma fecha de vencimiento.
- **RN-05** El stock de un producto se compone de stock vigente y stock vencido, registrados en tablas separadas: solo el stock vigente es operativo, mientras que el stock vencido se mantiene únicamente para el conteo físico hasta su retiro.
- **RN-06** El sistema maneja dos roles de usuario: Administrador (acceso total) y Empleado (acceso restringido).
- **RN-07** Toda operación que modifica el stock queda registrada con el usuario que la realizó y la fecha/hora.

## Restricciones

- **RN-08** No se puede modificar la cantidad de stock a mano: todo cambio se realiza mediante una operación registrada (ingreso de mercadería, ajuste o movimiento de stock vencido).
- **RN-09** No se puede repetir el código de un producto (debe ser único).
- **RN-10** Ningún registro se elimina: los productos se dan de baja de forma lógica (quedan inactivos). Solo el Administrador puede inactivarlos.
- **RN-11** Solo el Administrador puede dar de alta y modificar productos y categorías, y realizar ajustes de stock.
- **RN-12** Ningún usuario puede operar el sistema sin autenticarse con usuario y contraseña.
- **RN-13** El stock vencido no se puede descontar mientras el producto siga físicamente en el edificio: solo se descuenta al leer su etiqueta en el momento del retiro.

## Acciones disparadoras

- **RN-14** Al registrar un ingreso de mercadería, se suma la cantidad al stock vigente del producto y se registra la fecha de vencimiento de la caja.
- **RN-15** Cuando un producto alcanza su fecha de vencimiento, su stock vigente se traslada a un registro de stock vencido (tabla aparte).
- **RN-16** Cuando el stock vigente de un producto queda en o por debajo de su mínimo, el sistema lo marca en el listado de productos con stock bajo.
- **RN-17** Cuando un producto está próximo a vencer o ya vencido, el sistema lo marca en el listado de alertas de vencimiento.
- **RN-18** Cuando se retira mercadería vencida para devolverla al laboratorio y se lee su etiqueta, se descuenta esa cantidad del stock vencido.

## Cálculos

- **RN-19** El stock total físico de un producto se calcula como la suma de su stock vigente más su stock vencido (registrados en tablas separadas).
- **RN-20** Al leer la etiqueta en el retiro, el nuevo stock vencido se calcula como el stock vencido anterior menos la cantidad retirada.

## Inferencias

- **RN-21** Si un producto superó su fecha de vencimiento, se concluye que está vencido y su stock corresponde a stock vencido.
- **RN-22** Si el stock vigente de un producto llega o baja de su stock mínimo, se concluye que el producto requiere reposición.
