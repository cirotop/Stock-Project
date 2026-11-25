# Casos de Uso - Stock Proyect

Documentación de los requerimientos funcionales del sistema de control de stock de la distribuidora de productos capilares desde el punto de vista de los usuarios. Versión alineada al nuevo alcance definido en la Charla Previa de Reestructuración y en la 6ta entrevista: el sistema se centra **exclusivamente en el control de stock**, con eje en el manejo de las fechas de vencimiento y del stock vencido.

**Fuera de alcance:** punto de venta / ventas, facturación, listas de precios, proveedores, clientes, cuenta corriente, medios de pago, caja y resúmenes, e integración con el sistema del laboratorio.

---

## Actores

**Primarios** (utilizan las funciones principales del sistema):

- **Administrador:** acceso total. Gestiona productos, categorías y usuarios, realiza ajustes de stock, registra ingresos de mercadería y retiros de mercadería vencida, y consulta los listados de stock bajo, de próximos a vencer y el stock de cada producto.
- **Empleado:** funciones limitadas. Registra los movimientos de stock habilitados (ingreso de mercadería y retiro de mercadería vencida) y consulta los listados de stock bajo, de próximos a vencer y el stock de cada producto; no puede dar de alta ni modificar productos y categorías, ni realizar ajustes de stock (RN-11).

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

**Caminos alternativos:**
2.a El usuario o la contraseña son incorrectos, o el usuario está inactivo.
2.a.1 El sistema muestra el mensaje "usuario o contraseña incorrectos" y vuelve al paso 1.

**Postcondiciones:** El usuario queda autenticado con los permisos de su rol.

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
4. El sistema guarda el producto en estado activo y sin lotes asociados, por lo que su stock vigente arranca en cero.

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
3. Ingresa el número de lote impreso en la caja, la cantidad recibida y la fecha de vencimiento, que vale para todas las unidades del lote (RN-04).
4. El sistema da de alta el lote con esa fecha de vencimiento y esa cantidad, asociado al producto (RN-14).
5. El sistema registra el movimiento con el usuario y la fecha (RN-07).
6. El sistema confirma que el ingreso quedó registrado.

**Caminos alternativos:**
2.a El producto no existe todavía.
2.a.1 El sistema informa que el producto no existe. Fin.
3.a La cantidad es inválida (cero o negativa), falta el número de lote o falta la fecha de vencimiento.
3.a.1 El sistema avisa que el dato no es válido o está incompleto y vuelve al paso 3.
3.b El número de lote ya existe para ese producto.
3.b.1 El sistema informa que el lote ya existe y ofrece sumar la cantidad a ese lote o corregir el número ingresado.
3.c La fecha de vencimiento ingresada ya pasó.
3.c.1 El sistema avisa que la mercadería ya está vencida y pide confirmar antes de continuar.

**Postcondiciones:** El lote queda registrado con su número, su cantidad y su fecha de vencimiento, el stock vigente del producto queda incrementado y el movimiento queda asentado con su usuario y fecha.

**Escenario de éxito:** el lote se dio de alta con su fecha de vencimiento y el stock vigente aumentó.
**Escenario de fracaso:** no se registró el ingreso porque el producto no existía o los datos eran inválidos.

---

## CU-06 - Ajustar stock

**Actores:** Administrador (primario).

**Precondiciones:** El Administrador debe estar logueado. El producto debe existir y tener al menos un lote cargado. (El Empleado no tiene este permiso, RN-11.)

**Camino básico:**
1. El Administrador selecciona el producto que necesita corregir.
2. El sistema muestra los lotes del producto con su número, su fecha de vencimiento y su cantidad registrada.
3. El Administrador selecciona el lote a corregir, e ingresa la cantidad real contada y el motivo del ajuste.
4. El sistema corrige la cantidad de ese lote con el valor indicado.
5. El sistema registra el movimiento con el usuario y la fecha (RN-07).
6. El sistema confirma que el ajuste quedó registrado.

**Caminos alternativos:**
3.a La cantidad es inválida (negativa) o falta el motivo.
3.a.1 El sistema avisa que el dato no es válido o está incompleto y vuelve al paso 3.

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
3. El usuario ingresa el código de la etiqueta del lote vencido para consultar el producto.
4. El sistema muestra el producto y la cantidad vencida de ese lote.
5. El usuario ingresa la cantidad retirada y confirma.
6. El sistema descuenta esa cantidad del stock vencido del lote (RN-18 y RN-20) y registra el movimiento con el usuario y la fecha (RN-07).
7. El sistema confirma el retiro.

**Caminos alternativos:**
3.a La etiqueta no corresponde a ningún lote vencido registrado, o ese lote ya fue retirado.
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
1. El usuario consulta los productos con stock bajo.
2. El sistema muestra los productos activos cuyo stock vigente está en o por debajo de su stock mínimo (RN-16 y RN-22).

**Caminos alternativos:**
2.a No hay productos por debajo del mínimo.
2.a.1 El sistema informa que no hay productos con stock bajo.

**Postcondiciones:** El usuario visualiza qué productos necesita reponer.

**Escenario de éxito:** el usuario obtuvo el listado de productos a reponer.
**Escenario de fracaso:** no se muestran productos porque ninguno está bajo el mínimo.

---

## CU-09 - Consultar próximos a vencer y stock

**Actores:** Administrador, Empleado (primario).

**Precondiciones:** El usuario debe estar logueado.

**Camino básico:**
1. El usuario consulta los lotes próximos a vencer.
2. El sistema muestra los lotes que están a 15 días o menos de su fecha de vencimiento, con su número de lote, su producto, su cantidad y su fecha (RN-17).
3. El usuario consulta el stock de un producto, ingresando su código.
4. El sistema muestra el stock vigente del producto (suma de sus lotes no vencidos), su stock vencido (suma de la mercadería vencida que todavía no se retiró) y el stock total físico, que es la suma de ambos (RN-19).

**Caminos alternativos:**
2.a No hay lotes próximos a vencer.
2.a.1 El sistema informa que no hay lotes por vencer.

**Postcondiciones:** El usuario visualiza qué lotes están próximos a vencer y los tres tipos de stock del producto consultado.

**Escenario de éxito:** el usuario obtuvo el listado de próximos a vencer y el stock del producto.
**Escenario de fracaso:** no se muestran lotes porque ninguno está próximo a vencer.

---

## Procesos automáticos del sistema

Comportamientos que el sistema ejecuta por sí mismo, sin que los inicie un actor, y que por eso no se documentan como casos de uso:

- **Vencimiento del stock (RN-15 y RN-21):** cuando un **lote** alcanza su fecha de vencimiento, el sistema traslada su cantidad a un registro de stock vencido, identificado por el código de la etiqueta que se le coloca a la caja. Ese stock deja de ser operativo pero se sigue contando dentro del stock total físico hasta su retiro (CU-07). Los demás lotes del mismo producto, con otras fechas de vencimiento, siguen vigentes.
- **Alerta de stock bajo (RN-16):** cuando el stock vigente de un producto queda en o por debajo de su mínimo, el sistema lo marca en el listado de stock bajo (CU-08).
- **Alerta de vencimiento (RN-17):** cuando un lote está a 15 días o menos de su fecha de vencimiento, el sistema lo marca en el listado de próximos a vencer (CU-09).
