# Cuarta Entrevista - Stock Proyect

**Entrevistado:** Darío Fabián Conti  
**Contexto:** Distribuidora de productos capilares  
**Objetivo:** Definir si el sistema debe manejar lotes y fechas de vencimiento, y relevar en detalle el funcionamiento de clientes registrados y cuentas corrientes (venta a plazo), a partir de dudas surgidas al revisar las Reglas de Negocio propuestas (RN-02, RN-08, RN-12, RN-20, RN-21, RN-26).

---

## 1. Lotes y vencimientos

**Los productos capilares que manejás, ¿tienen fecha de vencimiento que te importe controlar, o son productos que no vencen (o vencen tan lejos en el tiempo que no te preocupa)? ¿Comprás la mercadería en distintas tandas o partidas que necesités diferenciar, o simplemente te interesa saber el total que tenés de cada producto?**
Sí, tienen fecha de vencimiento y hay que tenerla en cuenta, pero los productos suelen venderse antes de vencer. Además, cada vez que compran, todos los productos de esa compra vienen con la misma fecha de vencimiento. Compran todo de una sola tanda por vez, y cuando el stock queda bajo, se vuelve a pedir. Usan el modelo FIFO (primero que entra, primero que sale), no FEFO.

**Ya que compran de a una sola tanda por vez, ¿alcanza con que el producto tenga una sola fecha de vencimiento vigente (que se actualiza en cada ingreso de mercadería), en vez de manejar varios lotes con fechas distintas al mismo tiempo?**
Sí, alcanza, porque cada tipo de producto que se compra llega con una misma fecha de vencimiento, al ser de la misma tanda.

**Si un producto llega a la fecha de vencimiento sin haberse vendido, ¿qué necesitás que haga el sistema: que no te deje venderlo (bloqueo), o que solo te avise y ustedes deciden?**
Que avise que está vencido, nosotros decidimos qué se hace con el producto vencido.

**¿Con cuánta anticipación te gustaría que el sistema avise que un producto se está por vencer?**
15 días antes está bien, o una semana, para dar tiempo a pedir más mercadería.

## 2. Clientes y cuenta corriente

**Para los clientes que se registran (venta a plazo o revendedores), ¿qué datos necesitás guardar?**
Nombre o razón social, teléfono, dirección y email.

**¿Te sirve tener también el DNI/CUIT del cliente, o no hace falta?**
No es necesario, pero no está de más tenerlo.

**Sobre el mecanismo de cuenta corriente: cuando le vendés a un cliente a plazo, ¿cómo funciona? ¿La venta genera una deuda por el total que el cliente va pagando, o es un límite de crédito?**
La venta genera una deuda por el total, y el cliente la va pagando en cuotas. Generalmente es sin interés, o con muy bajo interés si es un cliente con el cual no hay mucha confianza.

**¿Necesitás que el sistema registre cada pago parcial (fecha y monto), o alcanza con anotar cuándo la deuda queda saldada del todo?**
Que vaya registrando cada pago que hace el cliente.

**Sobre el interés que mencionaste, ¿es algo que necesitás que calcule el sistema automáticamente, o lo definen ustedes a mano?**
Lo definimos nosotros a mano.

**Para que una deuda se considere vencida o el cliente pase a estar en mora, ¿usan una fecha límite de pago para cada deuda, o ven quién tiene saldo pendiente sin un límite formal?**
Se usa una fecha límite, pero solemos dejar unos días luego de esa fecha para que el cliente termine de saldar la deuda.

**¿Ese margen de días extra es un número fijo, o varía según el cliente?**
Varía según el cliente y la confianza que le tenemos. Hay clientes que compran hace muchos años, con ese perfil de clientes no hay ningún problema.

**Ya que ese margen es a criterio tuyo, ¿te sirve que el sistema marque automáticamente a un cliente como "moroso" apenas se pasa la fecha límite, o preferís marcarlo manualmente vos cuando lo consideres necesario?**
La ponemos nosotros.

**Una vez que un cliente está marcado como moroso, ¿qué necesitás que haga el sistema: que bloquee nuevas ventas a plazo, que muestre una alerta pero te deje decidir, o que sea solo un dato informativo?**
Con una alerta al venderle está bien.

**Volviendo a la fecha límite de pago: ¿la definís vos a mano en cada venta, o hay una regla fija para todos los clientes?**
La decidimos nosotros, dependiendo el cliente y el monto.

**¿Todas las ventas a un cliente registrado son a plazo, o un cliente registrado también puede comprar y pagar de contado, igual que uno sin registrar?**
El registro de clientes sería únicamente para ventas a plazo o revendedores.

## 3. Cierre

**Con todo lo que hablamos hoy, ¿sentís que quedó cubierto todo lo referido a vencimientos y a clientes/cuenta corriente? ¿Hay algo de estos dos temas que sientas que nos está faltando preguntar?**
Creería que nada.
