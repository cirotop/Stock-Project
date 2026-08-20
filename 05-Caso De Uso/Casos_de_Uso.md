# Casos de Uso - Stock Proyect

Documentación de los requerimientos funcionales del sistema de gestión de la distribuidora de productos capilares desde el punto de vista de los usuarios. Versión alineada a las 5 entrevistas.

---

## Actores

**Primarios** (utilizan las funciones principales del sistema):

- **Administrador:** acceso total. Gestiona productos, precios, stock, proveedores, usuarios, categorías y clientes; genera listas de precios y resúmenes mensuales; registra ventas, ingresos de mercadería, pagos y anulaciones.
- **Empleado:** funciones limitadas. Registra ventas, cierra la caja diaria y consulta información; no puede modificar precios ni stock, ni anular ventas.

**Secundarios** (sistemas externos):

- **Laboratorio:** sistema externo con el que se prevé una posible integración futura (opcional, no crítica). Ningún caso de uso lo referencia todavía; se documenta como trabajo futuro y se desarrollará cuando se aborde esa integración.

---

## CU-01 - Iniciar sesión

**Actores:** Administrador, Empleado (primario).

**Precondiciones:** El usuario debe estar dado de alta en el sistema con un nombre de usuario y una contraseña.

**Camino básico:**
1. El usuario ingresa su nombre de usuario y su contraseña.
2. El sistema valida las credenciales.
3. El sistema identifica el rol del usuario (Administrador o Empleado).
4. El sistema habilita las funciones correspondientes a ese rol.

**Caminos alternativos:**
2.a El usuario o la contraseña son incorrectos.
2.a.1 El sistema muestra el mensaje "usuario o contraseña incorrectos" y vuelve al paso 1.

**Postcondiciones:** El usuario queda autenticado con los permisos de su rol.

**Escenario de éxito:** el usuario ingresó al sistema con los permisos de su rol.
**Escenario de fracaso:** el usuario no pudo ingresar por credenciales inválidas.

---

## CU-02 - Registrar venta

**Actores:** Empleado (primario), Administrador.

**Precondiciones:** El usuario debe estar logueado. Deben existir productos cargados con stock disponible.

**Camino básico:**
1. El usuario selecciona la opción de registrar una nueva venta.
2. (Opcional) Si es una venta a plazo, el usuario asocia un cliente registrado.
3. El usuario agrega un producto (por código o nombre) e ingresa la cantidad.
4. El sistema verifica que haya stock suficiente y calcula el subtotal (cantidad x precio unitario).
5. El usuario repite el paso 3 por cada producto que se lleve el cliente.
6. El sistema calcula el total sumando todos los subtotales.
7. El usuario ingresa uno o más medios de pago con sus montos hasta cubrir el total.
8. El usuario confirma la venta.
9. El sistema registra la venta con su fecha, hora y usuario, descuenta el stock de cada producto y, si es a plazo, genera la deuda en la cuenta corriente del cliente.
10. (Opcional) Si el cliente lo solicita, el sistema genera el comprobante en PDF.

**Caminos alternativos:**
2.a El cliente asociado está marcado como moroso.
2.a.1 El sistema muestra una alerta pero permite continuar con la venta.
4.a El stock del producto es insuficiente.
4.a.1 El sistema avisa que no hay stock suficiente y no agrega el producto. Vuelve al paso 3.
7.a La suma de los medios de pago no coincide con el total.
7.a.1 El sistema avisa la diferencia y vuelve al paso 7.
8.a El usuario cancela la venta.
8.a.1 El sistema descarta la venta sin registrar cambios. Fin.
9.a Al descontar el stock, un producto queda en o por debajo de su stock mínimo.
9.a.1 El sistema marca el producto en el listado de stock bajo.

**Postcondiciones:** La venta queda registrada y el stock actualizado. Si es a plazo, queda registrada la deuda en la cuenta corriente. Si se pidió, queda generado el comprobante.

**Escenario de éxito:** la venta se registró, el stock se descontó y (si correspondía) se generó la deuda o el comprobante.
**Escenario de fracaso:** la venta no se registró por falta de stock o por cancelación del usuario.

---

## CU-03 - Registrar ingreso de mercadería

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado.

**Camino básico:**
1. El Administrador selecciona la opción de ingreso de mercadería.
2. Selecciona el producto y el proveedor.
3. Ingresa la cantidad recibida, la fecha de vencimiento de esa tanda y el gasto total de la compra.
4. El sistema suma la cantidad al stock del producto, actualiza su fecha de vencimiento y registra el gasto de la compra.
5. El sistema confirma la actualización.

**Caminos alternativos:**
2.a El producto o el proveedor no existen todavía.
2.a.1 El sistema ofrece darlos de alta (CU-04 / CU-07) o cancelar la operación.

**Postcondiciones:** El stock y la fecha de vencimiento del producto quedan actualizados, y queda registrado el gasto de la compra.

**Escenario de éxito:** el stock y el vencimiento se actualizaron y se registró el gasto.
**Escenario de fracaso:** no se registró el ingreso porque el producto/proveedor no existía y se canceló.

---

## CU-04 - Gestionar producto (alta, baja y modificación)

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado.

**Camino básico (alta):**
1. El Administrador selecciona la opción de alta de producto.
2. Ingresa el código, nombre, descripción, categoría, proveedor, precio, stock inicial, stock mínimo y fecha de vencimiento.
3. El sistema valida que el código no esté repetido.
4. El sistema guarda el producto.

**Caminos alternativos:**
3.a El código ya está registrado.
3.a.1 El sistema muestra el mensaje "código ya registrado" y vuelve al paso 2.
b. Modificación: el Administrador busca un producto, edita sus datos y guarda los cambios.
c. Baja: el Administrador busca un producto y lo da de baja (queda inactivo, no se elimina).

**Postcondiciones:** El producto queda dado de alta, modificado o inactivo según la operación.

**Escenario de éxito:** el producto se registró o se actualizó correctamente.
**Escenario de fracaso:** el alta no se completó por un código repetido.

---

## CU-05 - Modificar precio de producto

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado. El producto debe existir. (El Empleado no tiene este permiso.)

**Camino básico:**
1. El Administrador busca el producto.
2. Ingresa el nuevo precio de venta.
3. El sistema guarda el nuevo precio como precio vigente del producto.

**Caminos alternativos:**
2.a El precio ingresado no es válido (vacío o negativo).
2.a.1 El sistema avisa que el precio no es válido y vuelve al paso 2.

**Postcondiciones:** El producto queda con su nuevo precio vigente, que se usará en las próximas ventas y listas de precios.

**Escenario de éxito:** el precio del producto se actualizó.
**Escenario de fracaso:** el precio no se modificó por ser un valor inválido.

---

## CU-06 - Generar lista de precios

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado. Deben existir productos activos con su precio cargado.

**Camino básico:**
1. El Administrador selecciona la opción de generar lista de precios.
2. El sistema toma todos los productos activos con su precio vigente.
3. El sistema arma y muestra la lista de precios.
4. El Administrador la exporta o la imprime.

**Caminos alternativos:**
2.a No hay productos activos cargados.
2.a.1 El sistema avisa que no hay productos para listar. Fin.

**Postcondiciones:** La lista de precios queda generada y disponible para exportar o imprimir.

**Escenario de éxito:** la lista de precios se generó con los productos y precios vigentes.
**Escenario de fracaso:** no se generó la lista porque no había productos cargados.

---

## CU-07 - Gestionar proveedor (alta, baja y modificación)

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado.

**Camino básico (alta):**
1. El Administrador selecciona la opción de alta de proveedor.
2. Ingresa la razón social, el CUIT y los datos de contacto (teléfono, email, dirección).
3. El sistema valida que el CUIT no esté repetido.
4. El sistema guarda el proveedor.

**Caminos alternativos:**
3.a El CUIT ya está registrado.
3.a.1 El sistema muestra el mensaje "proveedor ya registrado" y vuelve al paso 2.
b. Modificación: el Administrador busca un proveedor, edita sus datos y guarda.
c. Baja: el Administrador da de baja un proveedor (queda inactivo).

**Postcondiciones:** El proveedor queda dado de alta, modificado o inactivo según la operación.

**Escenario de éxito:** el proveedor se registró o se actualizó correctamente.
**Escenario de fracaso:** el alta no se completó por un CUIT repetido.

---

## CU-08 - Gestionar usuario (alta, baja y modificación)

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado.

**Camino básico (alta):**
1. El Administrador selecciona la opción de alta de usuario.
2. Ingresa el nombre de usuario, la contraseña, el nombre completo y el rol (Administrador o Empleado).
3. El sistema valida que el nombre de usuario no esté repetido.
4. El sistema guarda el usuario con la contraseña cifrada.

**Caminos alternativos:**
3.a El nombre de usuario ya existe.
3.a.1 El sistema muestra el mensaje "nombre de usuario no disponible" y vuelve al paso 2.
b. Modificación: el Administrador edita los datos o el rol de un usuario y guarda.
c. Baja: el Administrador da de baja un usuario (queda inactivo).

**Postcondiciones:** El usuario queda dado de alta, modificado o inactivo según la operación.

**Escenario de éxito:** el usuario se registró o se actualizó correctamente.
**Escenario de fracaso:** el alta no se completó por un nombre de usuario repetido.

---

## CU-09 - Consultar productos con stock bajo

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado.

**Camino básico:**
1. El Administrador accede a la vista de stock / alertas.
2. El sistema muestra los productos cuyo stock actual está en o por debajo de su stock mínimo.

**Caminos alternativos:**
2.a No hay productos por debajo del mínimo.
2.a.1 El sistema informa que no hay productos con stock bajo.

**Postcondiciones:** El Administrador visualiza qué productos necesita reponer.

**Escenario de éxito:** el Administrador obtuvo el listado de productos a reponer.
**Escenario de fracaso:** no se muestran productos porque ninguno está bajo el mínimo.

---

## CU-10 - Anular venta

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado. La venta debe existir y no haber sido anulada previamente. (El Empleado no tiene este permiso.)

**Camino básico:**
1. El Administrador busca la venta que quiere anular.
2. El sistema muestra el detalle de la venta.
3. El Administrador confirma la anulación.
4. El sistema marca la venta como anulada y repone al stock las cantidades de cada producto que tenía la venta.

**Caminos alternativos:**
1.a La venta no existe.
1.a.1 El sistema avisa "venta no encontrada" y vuelve al paso 1.
3.a La venta ya estaba anulada.
3.a.1 El sistema avisa que la venta ya fue anulada y no realiza cambios. Fin.

**Postcondiciones:** La venta queda anulada y el stock de esos productos vuelve a estar disponible.

**Escenario de éxito:** la venta se anuló y el stock se repuso correctamente.
**Escenario de fracaso:** no se anuló porque la venta no existía o ya estaba anulada.

---

## CU-11 - Ajustar stock

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado. El producto debe existir en el sistema.

**Camino básico:**
1. El Administrador selecciona el producto que necesita corregir.
2. Ingresa la cantidad real de stock y el motivo del ajuste.
3. El sistema actualiza el stock actual del producto con la cantidad indicada.
4. El sistema confirma el ajuste.

**Caminos alternativos:**
2.a La cantidad es inválida (negativa) o falta el motivo.
2.a.1 El sistema avisa que el dato no es válido o está incompleto y vuelve al paso 2.

**Postcondiciones:** El stock actual del producto queda corregido con el valor real.

**Escenario de éxito:** el stock del producto se corrigió con la cantidad real.
**Escenario de fracaso:** no se realizó el ajuste por un dato inválido o incompleto.

---

## CU-12 - Gestionar categoría (alta y modificación)

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado.

**Camino básico (alta):**
1. El Administrador selecciona la opción de alta de categoría.
2. Ingresa el nombre y, opcionalmente, una descripción.
3. El sistema valida que el nombre no esté repetido.
4. El sistema guarda la categoría.

**Caminos alternativos:**
3.a El nombre ya está registrado.
3.a.1 El sistema muestra el mensaje "categoría ya registrada" y vuelve al paso 2.
b. Modificación: el Administrador busca una categoría, edita su nombre o descripción y guarda los cambios.

**Postcondiciones:** La categoría queda dada de alta o modificada.

**Escenario de éxito:** la categoría se registró o se actualizó correctamente.
**Escenario de fracaso:** el alta no se completó por un nombre repetido.

---

## CU-13 - Gestionar cliente (alta, baja y modificación)

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado. Aplica a clientes registrados para ventas a plazo o revendedores.

**Camino básico (alta):**
1. El Administrador selecciona la opción de alta de cliente.
2. Ingresa la razón social o nombre, teléfono, dirección, email, opcionalmente el documento (CUIT/DNI) e indica si es responsable inscripto.
3. El sistema guarda el cliente.

**Caminos alternativos:**
b. Modificación: el Administrador busca un cliente, edita sus datos y guarda.
c. Baja: el Administrador da de baja un cliente (queda inactivo, no se elimina).

**Postcondiciones:** El cliente queda dado de alta, modificado o inactivo según la operación.

**Escenario de éxito:** el cliente se registró o se actualizó correctamente.
**Escenario de fracaso:** no se completó la operación por datos incompletos.

---

## CU-14 - Registrar pago de cliente

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado. El cliente debe tener una cuenta corriente con deuda pendiente.

**Camino básico:**
1. El Administrador selecciona el cliente.
2. El sistema muestra la deuda pendiente.
3. El Administrador ingresa la fecha y el monto del pago.
4. El sistema registra el pago y descuenta el monto de la deuda de la cuenta corriente.

**Caminos alternativos:**
3.a El monto ingresado supera la deuda pendiente.
3.a.1 El sistema avisa que el monto supera la deuda y vuelve al paso 3.

**Postcondiciones:** Queda registrado el pago y la deuda del cliente queda actualizada.

**Escenario de éxito:** el pago se registró y la deuda disminuyó.
**Escenario de fracaso:** no se registró el pago por un monto inválido.

---

## CU-15 - Marcar cliente como moroso

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado. El cliente debe estar registrado.

**Camino básico:**
1. El Administrador selecciona el cliente.
2. Lo marca (o desmarca) como moroso.
3. El sistema guarda el estado del cliente.

**Postcondiciones:** El cliente queda marcado como moroso; en las próximas ventas a ese cliente el sistema mostrará una alerta sin bloquear la venta.

**Escenario de éxito:** el estado de morosidad del cliente se actualizó.
**Escenario de fracaso:** no se actualizó el estado.

---

## CU-16 - Cerrar caja diaria

**Actores:** Empleado (primario), Administrador.

**Precondiciones:** El usuario debe estar logueado. No debe existir un cierre de caja previo para el día en curso.

**Camino básico:**
1. El usuario selecciona la opción de cierre de caja.
2. El sistema calcula el total de caja del día a partir de las ventas registradas.
3. El usuario cuenta la caja física e ingresa el total verificado.
4. Si hay diferencia (faltante o sobrante), el usuario ingresa un comentario.
5. El sistema registra el cierre con el total calculado, el total verificado, la diferencia, el comentario y el usuario.

**Caminos alternativos:**
1.a Ya se realizó el cierre del día.
1.a.1 El sistema avisa que la caja de hoy ya fue cerrada. Fin.

**Postcondiciones:** Queda registrado el cierre de caja del día con su diferencia y comentario.

**Escenario de éxito:** la caja del día se cerró correctamente.
**Escenario de fracaso:** no se cerró porque ya existía un cierre para el día.

---

## CU-17 - Consultar resumen mensual

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado. (Función exclusiva del Administrador.)

**Camino básico:**
1. El Administrador selecciona el mes a consultar.
2. El sistema muestra el total vendido, el total de caja acumulado, la cantidad de ventas y el gasto del mes en compra de mercaderías.

**Caminos alternativos:**
2.a No hay datos registrados para el mes seleccionado.
2.a.1 El sistema informa que no hay información para ese mes.

**Postcondiciones:** El Administrador visualiza el resumen del mes.

**Escenario de éxito:** se mostró el resumen mensual con sus totales.
**Escenario de fracaso:** no se mostró el resumen por falta de datos en el mes.

---

## CU-18 - Consultar productos próximos a vencer

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado.

**Camino básico:**
1. El Administrador accede a la vista de vencimientos.
2. El sistema muestra los productos que están a 15 días o menos de su fecha de vencimiento, y los que ya están vencidos.

**Caminos alternativos:**
2.a No hay productos próximos a vencer ni vencidos.
2.a.1 El sistema informa que no hay productos por vencer.

**Postcondiciones:** El Administrador visualiza qué productos están próximos a vencer o vencidos.

**Escenario de éxito:** el Administrador obtuvo el listado de productos por vencer.
**Escenario de fracaso:** no se muestran productos porque ninguno está próximo a vencer.
