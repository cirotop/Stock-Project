# Diccionario de Datos - Stock Proyect

Listado organizado con las definiciones precisas y rigurosas de los datos del sistema de control de stock de la distribuidora de productos capilares, siguiendo la notación de la metodología estructurada. Se documentan únicamente los datos que el usuario maneja: **no se incluyen identificadores internos (IDs autoincrementales)**, ya que no son visibles para el usuario; cada estructura se identifica por su **clave natural** (nombre o código). El stock vigente y el stock vencido se registran por separado.

---

## Notación

| Símbolo | Relación | Significado |
| :--- | :--- | :--- |
| `=` | Definición | "está compuesto de". |
| `+` | Secuencial | Componentes que siempre están presentes. |
| `[ \| ]` | Selección | Alternativas; solo se elige una. |
| `vi{ }vf` | Repetición | El componente se itera entre vi y vf veces. |
| `( )` | Opcional | El componente puede estar o no. |
| `@` | Identificador | Campo único que no se repite ni admite nulos (clave natural). |

---

## Almacenamientos

Flujos de datos en reposo. Cada uno se identifica por su clave natural (@):

    Rol = @nombreRol + (descripcionRol)

    Usuario = @nombreUsuario + contraseña + nombreCompleto + nombreRol + estadoUsuario

    Categoria = @nombreCategoria + (descripcionCategoria)

    Producto = @codigoProducto + nombreProducto + (descripcionProducto) + nombreCategoria + stockMinimo + estadoProducto

    Lote = @codigoProducto + @nroLote + fechaVencimiento + cantidadLote

    StockVencido = @codigoEtiqueta + codigoProducto + nroLote + cantidad

El vencimiento se maneja **por lote**: la mercadería ingresa en cajas y cada caja se carga con su número de lote, al que se le asigna la fecha de vencimiento que trae impresa (RN-03, RN-04). Un producto tiene tantos lotes como ingresos haya recibido, cada uno con su propia fecha de vencimiento y su propia cantidad. El número de lote no se repite dentro de un mismo producto, pero dos productos distintos pueden tener el mismo número: por eso el lote se identifica por el producto más su número.

---

## Datos derivados

No se almacenan: el sistema los calcula a partir de los lotes.

    stockVigente = suma de cantidadLote de los lotes del producto que todavía no vencieron
    stockVencido = suma de cantidad de los registros de StockVencido del producto
    stockTotalFisico = stockVigente + stockVencido          (RN-19)

---

## Flujos de datos (movimientos de stock)

Transacciones que actualizan el stock:

    IngresoMercaderia = @nroIngreso + codigoProducto + nroLote + cantidad + fechaIngreso + nombreUsuario

    AjusteStock = @nroAjuste + codigoProducto + nroLote + cantidadReal + motivo + fechaAjuste + nombreUsuario

    RetiroVencido = @nroRetiro + codigoEtiqueta + cantidad + fechaRetiro + nombreUsuario

El ingreso crea el lote con su fecha de vencimiento; el ajuste corrige la cantidad de un lote; el retiro descuenta del lote vencido identificado por su etiqueta.

---

## Estructuras con relación de selección

Datos elementales cuyo valor se elige de un conjunto cerrado de alternativas:

    nombreRol = [ Administrador | Empleado ]
    estadoUsuario = [ activo | inactivo ]
    estadoProducto = [ activo | inactivo ]

---

## Datos elementales

Mínimas unidades indivisibles de datos, con su nombre, descripción, longitud, tipo y dominio de valores admisibles.

| Nombre | Descripción | Longitud | Tipo | Dominio |
| :--- | :--- | :---: | :--- | :--- |
| nombreRol | Nombre del rol de usuario (clave). | 20 | Alfanumérico | Discreto: {(ADM, Administrador); (EMP, Empleado)} |
| descripcionRol | Detalle de los permisos del rol. | 100 | Alfanumérico | Texto libre |
| nombreUsuario | Nombre de acceso al sistema (clave, único). | 30 | Alfanumérico | Texto libre |
| contraseña | Contraseña almacenada cifrada (hash). | 255 | Alfanumérico | Texto libre |
| nombreCompleto | Nombre y apellido del usuario. | 80 | Alfanumérico | Texto libre |
| estadoUsuario | Estado del usuario (baja lógica). | 1 | Booleano | Discreto: {(1, activo); (0, inactivo)} |
| nombreCategoria | Nombre de la categoría de producto (clave). | 50 | Alfanumérico | Texto libre |
| descripcionCategoria | Descripción de la categoría. | 150 | Alfanumérico | Texto libre |
| codigoProducto | Código interno o de barras del producto (clave). | 30 | Alfanumérico | Texto libre |
| nombreProducto | Nombre del producto. | 100 | Alfanumérico | Texto libre |
| descripcionProducto | Descripción del producto. | 200 | Alfanumérico | Texto libre |
| stockMinimo | Umbral mínimo que dispara la alerta de reposición. | — | Numérico (entero) | Continuo: {vi: 0; vf: n} |
| estadoProducto | Estado del producto. | 1 | Booleano | Discreto: {(1, activo); (0, inactivo)} |
| nroLote | Número del lote que viene impreso en la caja (clave junto con codigoProducto). | 30 | Alfanumérico | Texto libre |
| fechaVencimiento | Fecha de vencimiento del lote, que vale para todas sus unidades. Puede ser anterior a la fecha actual si la mercadería llega vencida (CU-05, 3.c). | — | Fecha | Continuo: {vi: 01/01/2000; vf: n} |
| cantidadLote | Cantidad de unidades vigentes que quedan en el lote. | — | Numérico (entero) | Continuo: {vi: 0; vf: n} |
| codigoEtiqueta | Código de la etiqueta del lote vencido, que se lee al retirarlo (clave). | 30 | Alfanumérico | Texto libre |
| cantidad | Cantidad de unidades del registro o movimiento. En los movimientos es al menos 1; en StockVencido llega a 0 cuando se retira todo. | — | Numérico (entero) | Continuo: {vi: 0; vf: n} |
| nroIngreso | Identificador del ingreso de mercadería (clave). | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| fechaIngreso | Fecha y hora del ingreso de mercadería (RN-07). | — | Fecha y hora | Continuo: {vi: 14/05/2026; vf: fecha actual} |
| nroAjuste | Identificador del ajuste de stock (clave). | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| cantidadReal | Cantidad real contada que corrige la cantidad del lote. | — | Numérico (entero) | Continuo: {vi: 0; vf: n} |
| motivo | Motivo de la corrección del stock. | 150 | Alfanumérico | Texto libre |
| fechaAjuste | Fecha y hora del ajuste de stock (RN-07). | — | Fecha y hora | Continuo: {vi: 14/05/2026; vf: fecha actual} |
| nroRetiro | Identificador del retiro de mercadería vencida (clave). | — | Numérico (entero) | Continuo: {vi: 1; vf: n} |
| fechaRetiro | Fecha y hora del retiro de la mercadería vencida (RN-07). | — | Fecha y hora | Continuo: {vi: 14/05/2026; vf: fecha actual} |
