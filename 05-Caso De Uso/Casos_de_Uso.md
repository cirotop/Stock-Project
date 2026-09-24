# Casos de Uso - Stock Proyect

Documentación de los requerimientos funcionales del sistema de control de stock de la distribuidora de productos capilares desde el punto de vista de los usuarios. Versión alineada al nuevo alcance definido en la Charla Previa de Reestructuración y en la 6ta entrevista: el sistema se centra **exclusivamente en el control de stock**, con eje en el manejo de las fechas de vencimiento y del stock vencido.

**Fuera de alcance:** punto de venta / ventas, facturación, listas de precios, proveedores, clientes, cuenta corriente, medios de pago, caja y resúmenes, e integración con el sistema del laboratorio.

---

## Actores

**Primarios** (utilizan las funciones principales del sistema):

- **Administrador:** acceso total. Gestiona productos, categorías y usuarios, realiza ajustes de stock, registra ingresos de mercadería y retiros de mercadería vencida, y consulta las alertas.
- **Empleado:** funciones limitadas. Registra los movimientos de stock habilitados (ingreso de mercadería y retiro de mercadería vencida) y consulta las alertas; no puede dar de alta ni modificar productos y categorías, ni realizar ajustes de stock (RN-11).

**Secundarios:**

- **Laboratorio:** destino físico de la mercadería vencida. No interactúa con el sistema: la integración con su sistema quedó fuera de alcance.

---

## CU-01 - Iniciar sesión

**Actores:** Administrador, Empleado (primario).

**Precondiciones:** El usuario debe estar dado de alta en el sistema con un nombre de usuario y una contraseña.

**Camino básico:**
1. El usuario ingresa su nombre de usuario y su contraseña.
2. El sistema valida las credenciales.
3. El sistema identifica el rol del usuario (Administrador o Empleado).
4. El sistema habilita las funciones correspondientes a ese rol.
5. El sistema muestra el panel de alertas de stock bajo, productos próximos a vencer y productos vencidos (RNF-03).

**Caminos alternativos:**
2.a El usuario o la contraseña son incorrectos.
2.a.1 El sistema muestra el mensaje "usuario o contraseña incorrectos" y vuelve al paso 1.

**Postcondiciones:** El usuario queda autenticado con los permisos de su rol y visualiza el panel de alertas.

**Escenario de éxito:** el usuario ingresó al sistema con los permisos de su rol.
**Escenario de fracaso:** el usuario no pudo ingresar por credenciales inválidas.

---

## CU-02 - Gestionar usuario (alta, baja y modificación)

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
c. Baja: el Administrador da de baja un usuario (queda inactivo, no se elimina).

**Postcondiciones:** El usuario queda dado de alta, modificado o inactivo según la operación.

**Escenario de éxito:** el usuario se registró o se actualizó correctamente.
**Escenario de fracaso:** el alta no se completó por un nombre de usuario repetido.

---

## CU-03 - Gestionar categoría (alta y modificación)

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado. (El Empleado no tiene este permiso, RN-11.)

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

## CU-04 - Gestionar producto (alta, baja y modificación)

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado. Debe existir al menos una categoría cargada. (El Empleado no tiene este permiso, RN-11.)

**Camino básico (alta):**
1. El Administrador selecciona la opción de alta de producto.
2. Ingresa el código, el nombre, la descripción, la categoría y el stock mínimo.
3. El sistema valida que el código no esté repetido (RN-09).
4. El sistema guarda el producto con stock vigente en cero y en estado activo.

**Caminos alternativos:**
3.a El código ya está registrado.
3.a.1 El sistema muestra el mensaje "código ya registrado" y vuelve al paso 2.
b. Modificación: el Administrador busca un producto, edita sus datos y guarda los cambios.
c. Baja: el Administrador busca un producto y lo da de baja (queda inactivo, no se elimina, RN-10).

**Postcondiciones:** El producto queda dado de alta, modificado o inactivo según la operación. El stock inicial no se carga a mano: se incorpora mediante un ingreso de mercadería (CU-05) o un ajuste de stock (CU-06), según la RN-08.

**Escenario de éxito:** el producto se registró o se actualizó correctamente.
**Escenario de fracaso:** el alta no se completó por un código repetido.

---

## CU-05 - Registrar ingreso de mercadería

**Actores:** Administrador, Empleado (primario).

**Precondiciones:** El usuario debe estar logueado. El producto debe existir y estar activo.

**Camino básico:**
1. El usuario selecciona la opción de ingreso de mercadería.
2. Selecciona el producto (por código o nombre).
3. Ingresa la cantidad recibida y la fecha de vencimiento impresa en la caja, que vale para todas las unidades que contiene (RN-04).
4. El sistema suma la cantidad al stock vigente del producto y registra la fecha de vencimiento de esa caja (RN-14).
5. El sistema registra el movimiento con el usuario y la fecha (RN-07).
6. El sistema confirma la actualización.

**Caminos alternativos:**
2.a El producto no existe todavía.
2.a.1 El sistema ofrece darlo de alta (CU-04) o cancelar la operación.
3.a La cantidad es inválida (cero o negativa) o falta la fecha de vencimiento.
3.a.1 El sistema avisa que el dato no es válido o está incompleto y vuelve al paso 3.
3.b La fecha de vencimiento ingresada ya pasó.
3.b.1 El sistema avisa que la mercadería ya está vencida y pide confirmar antes de continuar.

**Postcondiciones:** El stock vigente del producto queda incrementado, con la fecha de vencimiento de la caja registrada, y el movimiento queda asentado con su usuario y fecha.

**Escenario de éxito:** el stock vigente aumentó y quedó registrada la fecha de vencimiento.
**Escenario de fracaso:** no se registró el ingreso porque el producto no existía o los datos eran inválidos.

---

## CU-06 - Ajustar stock

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado. El producto debe existir en el sistema. (El Empleado no tiene este permiso, RN-11.)

**Camino básico:**
1. El Administrador selecciona el producto que necesita corregir.
2. El sistema muestra el stock vigente registrado.
3. El Administrador ingresa la cantidad real contada y el motivo del ajuste.
4. El sistema corrige el stock vigente del producto con la cantidad indicada.
5. El sistema registra el movimiento con el usuario y la fecha (RN-07).
6. El sistema confirma el ajuste.

**Caminos alternativos:**
3.a La cantidad es inválida (negativa) o falta el motivo.
3.a.1 El sistema avisa que el dato no es válido o está incompleto y vuelve al paso 3.
4.a El stock vigente corregido queda en o por debajo del stock mínimo.
4.a.1 El sistema marca el producto en el listado de stock bajo (RN-16).

**Postcondiciones:** El stock vigente del producto queda corregido con el valor real y el ajuste queda registrado con su motivo, usuario y fecha. El ajuste no modifica el stock vencido.

**Escenario de éxito:** el stock vigente del producto se corrigió con la cantidad real.
**Escenario de fracaso:** no se realizó el ajuste por un dato inválido o incompleto.

---

## CU-07 - Registrar retiro de mercadería vencida

**Actores:** Administrador, Empleado (primario).

**Precondiciones:** El usuario debe estar logueado. Debe existir mercadería vencida registrada, con su etiqueta, en el depósito de vencidos.

**Camino básico:**
1. El laboratorio retira la mercadería vencida del depósito.
2. El usuario selecciona la opción de retiro de mercadería vencida.
3. El usuario lee el código de la etiqueta del lote vencido.
4. El sistema muestra el producto, la cantidad y la fecha de vencimiento de ese lote.
5. El usuario ingresa la cantidad retirada y confirma.
6. El sistema descuenta esa cantidad del stock vencido del lote (RN-18 y RN-20) y registra el movimiento con el usuario y la fecha (RN-07).
7. El sistema confirma el retiro.

**Caminos alternativos:**
3.a La etiqueta no corresponde a ningún lote vencido registrado.
3.a.1 El sistema avisa "etiqueta no encontrada" y vuelve al paso 3.
5.a La cantidad retirada supera la cantidad registrada en el lote.
5.a.1 El sistema avisa que la cantidad supera la del lote y vuelve al paso 5.
5.b El usuario cancela el retiro.
5.b.1 El sistema no realiza cambios. Fin.

**Postcondiciones:** El stock vencido del lote queda descontado y, por lo tanto, también el stock total físico del producto. Mientras la mercadería vencida siga en el edificio no se descuenta (RN-13).

**Escenario de éxito:** la mercadería retirada se descontó del stock vencido al leer su etiqueta.
**Escenario de fracaso:** no se descontó porque la etiqueta no existía o la cantidad era inválida.

---

## CU-08 - Consultar productos con stock bajo

**Actores:** Administrador, Empleado (primario).

**Precondiciones:** El usuario debe estar logueado.

**Camino básico:**
1. El usuario accede al panel de alertas de stock.
2. El sistema muestra los productos activos cuyo stock vigente está en o por debajo de su stock mínimo (RN-16 y RN-22).

**Caminos alternativos:**
2.a No hay productos por debajo del mínimo.
2.a.1 El sistema informa que no hay productos con stock bajo.

**Postcondiciones:** El usuario visualiza qué productos necesita reponer.

**Escenario de éxito:** el usuario obtuvo el listado de productos a reponer.
**Escenario de fracaso:** no se muestran productos porque ninguno está bajo el mínimo.

---

## CU-09 - Consultar vencimientos y stock vencido

**Actores:** Administrador, Empleado (primario).

**Precondiciones:** El usuario debe estar logueado.

**Camino básico:**
1. El usuario accede al panel de vencimientos.
2. El sistema muestra los productos que están a 15 días o menos de su fecha de vencimiento (RN-17).
3. El sistema muestra los lotes de stock vencido pendientes de retiro, con su etiqueta, su producto, su cantidad y su fecha de vencimiento.
4. Para cada producto, el sistema muestra su stock vigente, su stock vencido y el stock total físico, que es la suma de ambos (RN-19).

**Caminos alternativos:**
2.a No hay productos próximos a vencer.
2.a.1 El sistema informa que no hay productos por vencer.
3.a No hay lotes vencidos pendientes de retiro.
3.a.1 El sistema informa que no hay mercadería vencida en el depósito.

**Postcondiciones:** El usuario visualiza qué productos están próximos a vencer y cuánta mercadería vencida hay pendiente de retiro.

**Escenario de éxito:** el usuario obtuvo el listado de vencimientos y de stock vencido pendiente.
**Escenario de fracaso:** no se muestran productos porque ninguno está próximo a vencer ni hay lotes vencidos.

---

## Procesos automáticos del sistema

Comportamientos que el sistema ejecuta por sí mismo, sin que los inicie un actor, y que por eso no se documentan como casos de uso:

- **Vencimiento del stock (RN-15 y RN-21):** cuando un producto alcanza su fecha de vencimiento, el sistema traslada su stock vigente a un registro de stock vencido, identificado por el código de su etiqueta. Ese stock deja de ser operativo pero se sigue contando dentro del stock total físico hasta su retiro (CU-07).
- **Alerta de stock bajo (RN-16):** cuando el stock vigente de un producto queda en o por debajo de su mínimo, el sistema lo marca en el listado de stock bajo (CU-08).
- **Alerta de vencimiento (RN-17):** cuando un producto está próximo a vencer o ya venció, el sistema lo marca en el listado de vencimientos (CU-09).
