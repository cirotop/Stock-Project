# Segunda Entrevista - Stock Proyect

**Entrevistado:** Darío Fabián Conti  
**Contexto:** Distribuidora de productos capilares  
**Objetivo:** Profundizar en el detalle de la información que va a manejar el sistema (productos, proveedores, ventas y usuarios) para terminar de definir cómo se van a cargar y guardar los datos.

---

## 1. Usuarios y accesos

**¿Quiénes van a usar el sistema en el día a día? ¿Cuántas personas más o menos?**
Somos pocos. Estoy yo, que soy el dueño, y un par de empleados que atienden y cargan las ventas. No más de tres o cuatro personas en total.

**Para que cada uno entre al sistema, ¿te sirve que tengan un nombre de usuario y una contraseña?**
Sí, me parece bien. Que cada uno tenga su usuario y su contraseña propia para entrar, así no anda todo el mundo con el mismo acceso.

**Además del nombre de usuario, ¿querés que quede registrado el nombre y apellido de cada persona?**
Sí, me gusta saber quién es cada uno. Que figure el nombre y apellido completo de la persona además del usuario.

**¿Qué cosas puede hacer el Administrador y en qué queda limitado el Empleado?**
El Administrador soy yo: hago todo, cargo productos, cambio precios, manejo el stock y veo todos los datos. El Empleado tiene funciones limitadas, básicamente carga las ventas y consulta, pero no puede tocar precios ni modificar el stock.

**¿El Empleado tendría que poder ver precios y stock, o solamente cargar ventas?**
Ver el precio para poder vender, sí. Pero que no lo pueda cambiar. El control del stock y los precios lo manejo yo nada más.

## 2. Productos

**¿Qué datos necesitás tener a mano de cada producto?**
El nombre del producto principalmente, con la marca y la presentación, que a veces cambia (por ejemplo el mismo shampoo en distintos tamaños).

**¿Cada producto tiene un código propio o lo identificás por el nombre?**
Sí, cada producto tiene su código. Algunos vienen con código de barras y a los que no, les pongo uno interno mío. Cada código es único, no se repite.

**Aparte del nombre, ¿te sirve una descripción o detalle del producto?**
Sí, una descripción corta viene bien, para aclarar la presentación o el tamaño cuando el nombre solo no alcanza.

**¿Tenés los productos agrupados de alguna manera?**
Sí, los tengo separados por tipo: shampoo, acondicionador, tinturas, fijadores y así. Me conviene tenerlos ordenados por esas categorías para encontrarlos rápido.

**¿Un mismo producto lo comprás siempre al mismo proveedor o puede venir de varios?**
Por lo general cada producto lo traigo siempre del mismo proveedor, así que con saber quién me lo provee alcanza.

## 3. Control de stock y alertas

**Hoy, ¿cómo te das cuenta de que un producto se está por terminar?**
A ojo, revisando las estanterías. El problema es que muchas veces me entero tarde, cuando ya casi no me queda y tengo que salir a reponer a las apuradas.

**¿Te gustaría que el sistema te avise cuando un producto queda bajo? ¿A partir de qué cantidad?**
Sí, eso es justo lo que necesito, un aviso cuando queda poco. La cantidad mínima depende de cada producto, porque hay algunos que vendo mucho y otros que casi no rotan, así que le pondría un mínimo distinto a cada uno.

**Cuando entra mercadería nueva o se vende algo, ¿querés que el stock se actualice automáticamente?**
Sí, eso me ahorraría un montón de trabajo. Que cuando vendo se descuente solo y cuando entra mercadería se sume, sin tener que estar anotando aparte.

**¿Te interesa poder hacer un ajuste manual cuando hay una diferencia?**
Sí, porque a veces pasa que hay un faltante o algo que se rompe. Estaría bueno poder corregir la cantidad a mano cuando el número no coincide.

## 4. Proveedores

**¿Qué datos guardás de cada proveedor?**
El nombre o la razón social del proveedor, y los datos para contactarlo cuando tengo que hacer un pedido.

**¿Necesitás tener el CUIT de cada proveedor?**
Sí, el CUIT lo necesito para la parte administrativa y los papeles, así que tiene que estar.

**Para contactarlos, ¿te sirve guardar teléfono, correo y dirección?**
Sí, todo eso me sirve. El teléfono y el correo para pedirles mercadería, y la dirección para tenerla a mano.

**¿Llevás registro de qué productos le comprás a cada proveedor?**
Sí, más o menos sé qué le compro a cada uno. Me ayuda a saber a quién pedirle cuando se me termina algo.

## 5. Precios y listas de precios

**¿Cada producto tiene un solo precio de venta o manejás distintos precios?**
Un solo precio de venta por producto. No manejo precios distintos por cliente ni nada de eso, es el mismo para todos.

**Cuando armás la lista de precios, ¿la sacás de los productos con su precio actual?**
Sí, la lista es directamente el listado de productos con el precio que tienen en ese momento. No es algo aparte.

**¿Cada cuánto cambian los precios y quién debería poder modificarlos?**
Cambian bastante seguido por la inflación. Y eso lo tengo que manejar solo yo, el Administrador. El empleado no puede andar tocando precios.

**La lista que hoy tenés en la hoja de cálculo de Google, ¿cómo te gustaría poder usarla?**
Hoy la tengo en una planilla de Google y es medio incómodo. Me gustaría poder generarla desde el sistema y sacarla para imprimir o pasarla en PDF cuando la necesito.

## 6. Ventas

**Cuando hacés una venta, ¿qué datos anotás hoy?**
Anoto la fecha, los productos que se llevó y el total. Hoy es todo en papel, así que muchas veces queda incompleto.

**En una misma venta, ¿solés cargar varios productos o de a uno?**
Casi siempre varios. Es raro que alguien lleve un solo producto, por lo general se llevan varias cosas juntas.

**Por cada producto de la venta, ¿anotás la cantidad y el precio de ese momento?**
Sí, la cantidad de cada uno y el precio al que se lo vendí ese día, porque como los precios cambian, me importa que quede el precio de esa venta.

**¿Te sirve que el sistema calcule solo el total de la venta?**
Sí, total. Que sume todo solo y me dé el total, así no me equivoco haciendo las cuentas a mano.

**¿Necesitás que quede registrado qué usuario hizo cada venta y en qué fecha y hora?**
Sí, quiero saber quién cargó cada venta y cuándo. Eso me sirve para tener control de lo que pasa en el local.

## 7. Cierre

**¿Hay algún dato o situación del negocio que sientas que nos está faltando contemplar?**
Por ahora no, con esto que hablamos estaría cubierto lo principal, que es tener el stock y las ventas ordenados y no depender del papel.

**Pensando a futuro, ¿hay algo que te gustaría que el sistema permita más adelante?**
Más adelante estaría bueno poder enlazarlo con el laboratorio, pero no es algo urgente ni indispensable. Primero quiero tener resuelto el control de stock y las ventas.
