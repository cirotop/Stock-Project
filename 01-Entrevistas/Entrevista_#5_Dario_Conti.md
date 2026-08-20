# Quinta Entrevista - Stock Proyect

**Entrevistado:** Darío Fabián Conti  
**Contexto:** Distribuidora de productos capilares  
**Objetivo:** Aclarar incoherencias detectadas entre los documentos existentes (Reglas de Negocio, Requerimientos Funcionales/No Funcionales, Diccionario de Datos y Casos de Uso): el término "balances" mencionado en la primera entrevista, el manejo de caja y resúmenes mensuales, y los puntos pendientes sobre comprobante de venta, medios de pago e IVA.

---

## 1. Balances y caja

**En la primera entrevista mencionaste que el empleado no puede tocar precios, balances ni el stock. ¿A qué te referías exactamente con "balances"?**
Al total de caja del día, y a los resúmenes del mes.

**Sobre el total de caja del día: ¿esto sería algo que el sistema calcula solo, o es algo que cargan/cierran manualmente al final del día?**
El sistema lo calcula automáticamente. El empleado debe verificar ese total contra lo que hay físicamente en caja, y en caso de faltante o sobrante, cargarlo en el sistema.

**Sobre esa diferencia (faltante o sobrante): ¿el empleado puede cargarla él mismo, o queda restringida al Administrador? ¿Te sirve que quede anotado un motivo o comentario junto con la diferencia?**
El empleado debe poder hacerlo, para poder cerrar la caja sin necesitar que otra persona intervenga. Sí, estaría bien un comentario.

**Para que quede bien definido: ¿la regla sería que el empleado puede cerrar la caja (verificar el total y cargar faltante/sobrante con comentario), pero no puede editar ventas pasadas, precios, ni los resúmenes mensuales — eso queda solo para el Administrador?**
Exactamente. El empleado no puede tocar el stock, los precios de los productos, ni eliminar ventas.

**¿Qué información te gustaría que tengan los resúmenes del mes?**
Total vendido, total de caja acumulado, cantidad de ventas, y el gasto del mes en compra de mercaderías.

**Sobre el gasto en compra de mercaderías: ¿necesitás cargar el costo de compra por unidad de cada producto, o alcanza con un monto total del gasto de esa compra?**
No hace falta el costo individual. Se carga el gasto total de la compra. El precio individual de venta lo ponen ellos aparte.

**¿Es una sola caja del local por día, o cada empleado que atendió ese día hace su propio cierre por separado?**
Tienen una única caja en el local.

**¿El cierre de caja se hace una vez al día, o puede haber más de un cierre en el mismo día?**
Se hace una vez por día.

**Volviendo al gasto de mercadería: ¿lo cargás en el mismo momento en que registrás el ingreso de mercadería, o es algo separado?**
Se carga en el mismo momento del ingreso de mercadería.

## 2. Comprobante, medio de pago e IVA

**Cuando cobrás una venta, ¿el cliente puede combinar medios de pago (por ejemplo, parte en efectivo y parte con tarjeta), o siempre es un solo medio de pago por venta?**
El cliente puede combinar medios de pago.

**¿Necesitás que el sistema discrimine el IVA en cada venta? Y relacionado: hoy, ¿facturás las ventas?**
No hace falta que discrimine IVA. No se entregan facturas; se factura en ARCA aparte, solo para algunos clientes que son responsables inscriptos.

**Ya que mencionaste que algunos clientes son responsables inscriptos, ¿te sirve que el sistema tenga ese dato para identificarlos rápido a la hora de facturar en ARCA?**
Estaría bueno.

**Ya nos habías dicho que el comprobante de venta no es obligatorio, pero se puede generar un PDF. ¿Es algo que el empleado elige generar al cerrar cada venta, o se genera siempre automáticamente y ustedes deciden si lo entregan?**
Que sea opcional, que se genere únicamente cuando el cliente lo pide.

## 3. Cierre

**Con todo lo que hablamos hoy —balances/caja, resúmenes mensuales, medios de pago combinados, IVA/facturación y comprobante de venta—, ¿sentís que quedó cubierto todo, o hay algo de estos temas que sintamos que falta aclarar?**
Quedó todo cubierto.
