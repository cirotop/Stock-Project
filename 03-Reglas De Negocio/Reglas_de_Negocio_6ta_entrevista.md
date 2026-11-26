# Reglas de Negocio - Stock Proyect

---

## Hechos

- **RN-01** Cada producto tiene definido un stock mínimo de seguridad.
- **RN-02** Cada producto pertenece a una categoría.
- **RN-03** Cada lote de un producto tiene una fecha de vencimiento, que se registra al ingresar la mercadería.
- **RN-04** Los productos llegan en cajas, y todos los productos de una misma caja comparten la misma fecha de vencimiento.
- **RN-05** El stock de un producto se compone de stock vigente, stock pendiente de traslado y stock vencido: solo el stock vigente es operativo; el pendiente de traslado es mercadería ya vencida que sigue en el depósito principal, y el stock vencido (tabla aparte) es la que ya se trasladó al depósito de vencidos. Los dos últimos se mantienen únicamente para el conteo físico.
- **RN-06** El sistema maneja dos roles de usuario: Administrador (acceso total) y Empleado (acceso restringido).
- **RN-07** Toda operación que modifica el stock queda registrada con el usuario que la realizó y la fecha/hora.
- **RN-27** El número de lote y la fecha de vencimiento los imprime el fabricante en la caja. Un lote se identifica por el producto, su número y su fecha de vencimiento: si llega el mismo número con otra fecha, es un lote distinto.

## Restricciones

- **RN-08** No se puede modificar la cantidad de stock a mano: todo cambio se realiza mediante una operación registrada (ingreso o egreso de mercadería, ajuste, traslado o retiro de mercadería vencida).
- **RN-09** No se puede repetir el código de un producto (debe ser único).
- **RN-10** Ningún registro se elimina: los productos se dan de baja de forma lógica (quedan inactivos). Solo el Administrador puede inactivarlos.
- **RN-11** Solo el Administrador puede dar de alta, modificar y dar de baja usuarios y productos, dar de alta y modificar categorías, y realizar ajustes de stock.
- **RN-12** Ningún usuario puede operar el sistema sin autenticarse con usuario y contraseña.
- **RN-13** El stock vencido no se puede descontar mientras el producto siga físicamente en el edificio: solo se descuenta en el momento del retiro, al leer el código de barras del producto, el número de lote y la fecha de vencimiento de la caja.
- **RN-26** No se puede sumar mercadería ni ajustar la cantidad de un lote que ya está vencido: la mercadería vencida solo se mueve con el traslado y el retiro.

## Acciones disparadoras

- **RN-14** Al registrar un ingreso de mercadería, se suma la cantidad al stock vigente del producto y se registra el lote con la fecha de vencimiento de la caja.
- **RN-15** Cuando un lote alcanza su fecha de vencimiento, su cantidad deja de ser stock vigente y queda como stock pendiente de traslado, y el sistema lo muestra en el listado de pendientes de traslado.
- **RN-16** Cuando el stock vigente de un producto queda en o por debajo de su mínimo, el sistema lo marca en el listado de productos con stock bajo.
- **RN-17** Cuando un lote está a 15 días o menos de su fecha de vencimiento, el sistema lo marca en el listado de próximos a vencer.
- **RN-18** Cuando se retira mercadería vencida para devolverla al laboratorio y se leen el código del producto, el número de lote y la fecha de vencimiento, se descuenta esa cantidad del stock vencido.
- **RN-23** Cuando el usuario traslada un lote vencido al depósito de vencidos y se leen el código del producto, el número de lote y la fecha de vencimiento, toda la cantidad que queda en el lote pasa al stock vencido (tabla aparte) y el lote queda en 0.
- **RN-24** Al registrar un egreso de mercadería, la cantidad que sale se descuenta del stock vigente del producto, empezando por el lote con la fecha de primer ingreso más antigua (FIFO); los lotes vencidos no se descuentan. Si un lote recibe más mercadería (mismo número y misma fecha de vencimiento), es la misma partida del fabricante y conserva su fecha de primer ingreso. El egreso registra cuánto se descontó de cada lote.
- **RN-25** Si la mercadería ingresa ya vencida, el usuario elige dónde se guarda: en el depósito principal queda como stock pendiente de traslado; en el depósito de vencidos pasa directamente a stock vencido.

## Cálculos

- **RN-19** El stock total físico de un producto se calcula como la suma de su stock vigente, su stock pendiente de traslado y su stock vencido.
- **RN-20** Al leer el producto y el lote en el retiro, el nuevo stock vencido se calcula como el stock vencido anterior menos la cantidad retirada.

## Inferencias

- **RN-21** Si un lote alcanzó su fecha de vencimiento, se concluye que está vencido: su cantidad corresponde a stock pendiente de traslado hasta que se registra el traslado, y desde ahí a stock vencido.
- **RN-22** Si el stock vigente de un producto llega o baja de su stock mínimo, se concluye que el producto requiere reposición.
