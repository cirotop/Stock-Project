# Casos de Uso - Stock Proyect

Documentación de los requerimientos funcionales del sistema de gestión de la distribuidora de productos capilares desde el punto de vista de los usuarios.

---

## Actores

**Primarios** (utilizan las funciones principales del sistema):

- **Administrador:** acceso total. Gestiona productos, precios, stock, proveedores y usuarios, genera las listas de precios y también puede registrar ventas.
- **Empleado:** funciones limitadas. Registra ventas y consulta información; no puede modificar precios ni stock.

**Secundarios** (tareas de apoyo o sistemas externos):

- **Laboratorio:** sistema externo con el que se prevé una posible integración futura (opcional, no crítica).

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
2. El usuario agrega un producto (por código o nombre) e ingresa la cantidad.
3. El sistema verifica que haya stock suficiente y calcula el subtotal (cantidad x precio unitario).
4. El usuario repite el paso 2 por cada producto que se lleve el cliente.
5. El sistema calcula el total sumando todos los subtotales.
6. El usuario confirma la venta.
7. El sistema registra la venta con su fecha, hora y usuario, y descuenta el stock de cada producto vendido.

**Caminos alternativos:**
3.a El stock del producto es insuficiente.
3.a.1 El sistema avisa que no hay stock suficiente y no agrega el producto. Vuelve al paso 2.
6.a El usuario cancela la venta.
6.a.1 El sistema descarta la venta sin registrar cambios. Fin.
7.a Al descontar el stock, un producto queda en o por debajo de su stock mínimo.
7.a.1 El sistema genera una alerta de stock bajo para ese producto.

**A tener en cuenta:**
Los caminos alternativos del punto 6 se pueden realizar en cualquier momento pero focalizamos en este punto por contexto al realizar una compra.

**Postcondiciones:** La venta queda registrada y el stock de los productos vendidos queda actualizado.

**Escenario de éxito:** la venta se registró y el stock se descontó correctamente.
**Escenario de fracaso:** la venta no se registró por falta de stock o por cancelación del usuario.

---

## CU-03 - Registrar ingreso de mercadería

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado. El producto debe existir en el sistema.

**Camino básico:**
1. El Administrador selecciona la opción de ingreso de mercadería.
2. Selecciona el producto y el proveedor.
3. Ingresa la cantidad recibida.
4. El sistema suma esa cantidad al stock actual del producto.
5. El sistema confirma la actualización del stock.

**Caminos alternativos:**
2.a El producto o proveedor no existen todavía.
2.a.1 El sistema ofrece dar de alta el producto (CU-04) o cancelar la operación.

**Postcondiciones:** El stock del producto queda incrementado con la cantidad ingresada.

**Escenario de éxito:** el stock del producto se actualizó con la mercadería recibida.
**Escenario de fracaso:** no se registró el ingreso porque el producto no existía y se canceló.

---

## CU-04 - Gestionar producto (alta, baja y modificación)

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado.

**Camino básico (alta):**
1. El Administrador selecciona la opción de alta de producto.
2. Ingresa el código, nombre, descripción, categoría, proveedor, precio, stock inicial y stock mínimo.
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
