# Diagramas de Secuencia de Diseño (DSD) - Stock Proyect

> **Nivel:** DSD. El `:Sistema` de caja negra se reemplaza por un **controlador** por caso de uso (patrón Controlador), que recibe la operación del actor y la delega hacia la derecha en los objetos del [Diagrama de Clases](../07-Diagrama%20De%20Clases/Diagrama_de_Clases.puml).
>
> **Convenciones** (igual que en la teoría):
> - Flecha llena: mensaje sincrónico, con sus parámetros tipados.
> - Flecha punteada: retorno, con el valor devuelto entre comillas latinas: `«valor : Tipo»`.
> - `crear(...)` / `registrar(...)`: operación de clase (`{static}`) que crea el objeto; el objeto nuevo aparece en el punto donde se crea.
> - `loop` = bucle, `opt` = opcional, `alt` = alternativo.
> - `responsable:Usuario` es el usuario logueado que ejecuta la operación; las operaciones lo verifican con `esAdministrador()` (RN-11).
> - **⚠** marca un método que el caso de uso necesita y que **todavía no existe en el DCC**.
>
> Los **procesos automáticos** (vencimiento de lote, alertas) no se diagraman porque no los inicia un actor.

---

## CU-01 - Iniciar sesión

```mermaid
sequenceDiagram
    actor u as u:Usuario
    participant ci as ci:ControladorIniciarSesion
    participant us as us:Usuario
    participant cat as cat:Catalogo

    u->>+ci: iniciarSesion(nombreUsuario : String, contraseña : String)
    ci->>ci: us = buscarUsuario(nombreUsuario : String)
    ci->>ci: valida = validarContraseña(us : Usuario, contraseña : String)
    ci->>+us: estaActivo()
    us-->>-ci: «activo : boolean»
    alt credenciales válidas y usuario activo
        ci->>+us: esAdministrador()
        us-->>-ci: «esAdmin : boolean»
        Note right of ci: habilita las funciones del rol
        ci->>+cat: listarStockBajo()
        cat-->>-ci: «stockBajo : Producto[]»
        ci->>+cat: listarProximosAVencer(dias : int)
        cat-->>-ci: «proximos : Lote[]»
        ci->>+cat: listarVencidos()
        cat-->>-ci: «vencidos : StockVencido[]»
        ci-->>u: «funciones del rol + panel de alertas»
    else usuario o contraseña incorrectos
        ci-->>u: «"usuario o contraseña incorrectos"»
    end
    deactivate ci
```

> La búsqueda del usuario y la validación de la contraseña son del controlador: según la nota del DCC, la autenticación la resuelve el control de acceso del sistema, no la entidad `Usuario`.

---

## CU-02 - Gestionar usuario

### Alta

```mermaid
sequenceDiagram
    actor adm as adm:Administrador
    participant cgu as cgu:ControladorGestionarUsuario
    participant resp as responsable:Usuario

    adm->>+cgu: altaUsuario(nombreUsuario : String, contraseña : String, nombreCompleto : String, nombreRol : TipoRol)
    cgu->>cgu: existente = buscarUsuario(nombreUsuario : String)
    alt nombre de usuario disponible
        cgu->>cgu: rol = buscarRol(nombreRol : TipoRol)
        create participant nu as nuevo:Usuario
        cgu->>+nu: crear(nombreUsuario : String, contraseña : String, nombreCompleto : String, rol : Rol, responsable : Usuario)
        nu->>+resp: esAdministrador()
        resp-->>-nu: «true»
        Note right of nu: guarda la contraseña cifrada (hash)
        nu-->>-cgu: «nuevo : Usuario»
        cgu-->>adm: «usuario creado»
    else nombre de usuario ya existe
        cgu-->>adm: «"nombre de usuario no disponible"»
    end
    deactivate cgu
```

### Modificación y baja

```mermaid
sequenceDiagram
    actor adm as adm:Administrador
    participant cgu as cgu:ControladorGestionarUsuario
    participant us as us:Usuario
    participant resp as responsable:Usuario

    adm->>+cgu: modificarUsuario(nombreUsuario : String, nombreCompleto : String, nombreRol : TipoRol)
    cgu->>+resp: esAdministrador()
    resp-->>-cgu: «true»
    cgu->>cgu: us = buscarUsuario(nombreUsuario : String)
    cgu->>cgu: rol = buscarRol(nombreRol : TipoRol)
    cgu->>+us: modificar(nombreCompleto : String, rol : Rol)
    us-->>-cgu: 
    cgu-->>-adm: «usuario modificado»

    adm->>+cgu: darBajaUsuario(nombreUsuario : String)
    cgu->>+resp: esAdministrador()
    resp-->>-cgu: «true»
    cgu->>cgu: us = buscarUsuario(nombreUsuario : String)
    cgu->>+us: darBaja()
    Note right of us: baja lógica: estadoUsuario = inactivo
    us-->>-cgu: 
    cgu-->>-adm: «usuario dado de baja»
```

---

## CU-03 - Gestionar categoría

### Alta

```mermaid
sequenceDiagram
    actor adm as adm:Administrador
    participant cgc as cgc:ControladorGestionarCategoria
    participant cat as cat:Catalogo
    participant resp as responsable:Usuario

    adm->>+cgc: altaCategoria(nombreCategoria : String, descripcionCategoria : String)
    cgc->>+cat: buscarCategoria(nombreCategoria : String) ⚠
    cat-->>-cgc: «existente : Categoria»
    alt nombre disponible
        create participant c as c:Categoria
        cgc->>+c: crear(nombreCategoria : String, descripcionCategoria : String, responsable : Usuario)
        c->>+resp: esAdministrador()
        resp-->>-c: «true»
        c-->>-cgc: «c : Categoria»
        cgc-->>adm: «categoría creada»
    else nombre ya registrado
        cgc-->>adm: «"categoría ya registrada"»
    end
    deactivate cgc
```

### Modificación

```mermaid
sequenceDiagram
    actor adm as adm:Administrador
    participant cgc as cgc:ControladorGestionarCategoria
    participant cat as cat:Catalogo
    participant c as c:Categoria
    participant resp as responsable:Usuario

    adm->>+cgc: modificarCategoria(nombreActual : String, nombreCategoria : String, descripcionCategoria : String)
    cgc->>+resp: esAdministrador()
    resp-->>-cgc: «true»
    cgc->>+cat: buscarCategoria(nombreActual : String) ⚠
    cat-->>-cgc: «c : Categoria»
    cgc->>+c: modificar(nombreCategoria : String, descripcionCategoria : String)
    c-->>-cgc: 
    cgc-->>-adm: «categoría modificada»
```

---

## CU-04 - Gestionar producto

### Alta

```mermaid
sequenceDiagram
    actor adm as adm:Administrador
    participant cgp as cgp:ControladorGestionarProducto
    participant cat as cat:Catalogo
    participant resp as responsable:Usuario

    adm->>+cgp: altaProducto(codigoProducto : String, nombreProducto : String, descripcionProducto : String, nombreCategoria : String, stockMinimo : int)
    cgp->>+cat: buscarProducto(codigoProducto : String)
    cat-->>-cgp: «existente : Producto»
    alt código disponible
        cgp->>+cat: buscarCategoria(nombreCategoria : String) ⚠
        cat-->>-cgp: «categoria : Categoria»
        create participant p as p:Producto
        cgp->>+p: crear(codigoProducto : String, nombreProducto : String, descripcionProducto : String, categoria : Categoria, stockMinimo : int, responsable : Usuario)
        p->>+resp: esAdministrador()
        resp-->>-p: «true»
        Note right of p: queda activo y sin lotes<br/>(stock vigente = 0, RN-08)
        p-->>-cgp: «p : Producto»
        cgp-->>adm: «producto creado»
    else código ya registrado (RN-09)
        cgp-->>adm: «"código ya registrado"»
    end
    deactivate cgp
```

### Modificación y baja

```mermaid
sequenceDiagram
    actor adm as adm:Administrador
    participant cgp as cgp:ControladorGestionarProducto
    participant cat as cat:Catalogo
    participant p as p:Producto
    participant resp as responsable:Usuario

    adm->>+cgp: modificarProducto(codigoProducto : String, nombreProducto : String, descripcionProducto : String, nombreCategoria : String, stockMinimo : int)
    cgp->>+resp: esAdministrador()
    resp-->>-cgp: «true»
    cgp->>+cat: buscarProducto(codigoProducto : String)
    cat-->>-cgp: «p : Producto»
    cgp->>+cat: buscarCategoria(nombreCategoria : String) ⚠
    cat-->>-cgp: «categoria : Categoria»
    cgp->>+p: modificar(nombreProducto : String, descripcionProducto : String, categoria : Categoria, stockMinimo : int)
    p-->>-cgp: 
    cgp-->>-adm: «producto modificado»

    adm->>+cgp: darBajaProducto(codigoProducto : String)
    cgp->>+resp: esAdministrador()
    resp-->>-cgp: «true»
    cgp->>+cat: buscarProducto(codigoProducto : String)
    cat-->>-cgp: «p : Producto»
    cgp->>+p: darBaja()
    Note right of p: baja lógica: estadoProducto = inactivo (RN-10)
    p-->>-cgp: 
    cgp-->>-adm: «producto dado de baja»
```

---

## CU-05 - Registrar ingreso de mercadería

```mermaid
sequenceDiagram
    actor u as u:Usuario
    participant cri as cri:ControladorRegistrarIngreso
    participant cat as cat:Catalogo
    participant p as p:Producto
    participant lt as lt:Lote

    u->>+cri: registrarIngreso(codigoProducto : String, nroLote : String, cantidad : int, fechaVencimiento : Date)
    cri->>+cat: buscarProducto(codigoProducto : String)
    cat-->>-cri: «p : Producto»
    alt producto no existe
        cri-->>u: «ofrece dar de alta el producto (CU-04) o cancelar»
    else cantidad inválida, o falta nroLote o fechaVencimiento
        cri-->>u: «"dato inválido o incompleto"»
    else producto existe y datos válidos
        cri->>+p: buscarLote(nroLote : String)
        p-->>-cri: «lt : Lote»
        opt el lote ya existe para ese producto
            cri->>+lt: getFechaVencimiento()
            lt-->>-cri: «fechaVencimiento : Date»
            cri-->>u: «lote existente: ¿sumar la cantidad o corregir el número?»
            u->>cri: sumarAlLote()
        end
        opt la fecha de vencimiento ya pasó
            cri-->>u: «"la mercadería ya está vencida": pide confirmar»
            u->>cri: confirmar()
        end
        create participant ing as ing:IngresoMercaderia
        cri->>+ing: registrar(p : Producto, nroLote : String, cantidad : int, fechaVencimiento : Date, responsable : Usuario)
        ing-->>-cri: «ing : IngresoMercaderia»
        cri->>+ing: aplicar()
        alt lote nuevo
            create participant nl as nuevo:Lote
            ing->>+nl: crear(p : Producto, nroLote : String, cantidad : int, fechaVencimiento : Date)
            nl-->>-ing: «nuevo : Lote»
        else lote existente
            ing->>+lt: aumentarCantidad(cantidad : int)
            lt-->>-ing: «cantidadLote : int»
        end
        ing-->>-cri: «true»
        cri->>+p: getLotes() ⚠
        p-->>-cri: «lotes : Lote[]»
        Note right of cri: stock vigente = suma de getCantidadLote()<br/>de los lotes no vencidos
        cri-->>u: «ingreso registrado + stock vigente del producto»
    end
    deactivate cri
```

> El movimiento queda asentado con el usuario y la fecha (RN-07): `registrar(...)` recibe al `responsable`.

---

## CU-06 - Ajustar stock

```mermaid
sequenceDiagram
    actor adm as adm:Administrador
    participant cas as cas:ControladorAjustarStock
    participant cat as cat:Catalogo
    participant p as p:Producto
    participant lt as lt:Lote
    participant resp as responsable:Usuario

    adm->>+cas: seleccionarProducto(codigoProducto : String)
    cas->>+cat: buscarProducto(codigoProducto : String)
    cat-->>-cas: «p : Producto»
    cas->>+p: getLotes() ⚠
    p-->>-cas: «lotes : Lote[]»
    loop para cada lt en lotes
        cas->>+lt: getFechaVencimiento()
        lt-->>-cas: «fechaVencimiento : Date»
        cas->>+lt: getCantidadLote()
        lt-->>-cas: «cantidadLote : int»
    end
    cas-->>-adm: «lotes (nroLote, fechaVencimiento, cantidad)»

    adm->>+cas: ajustarStock(nroLote : String, cantidadReal : int, motivo : String)
    alt cantidad válida y motivo cargado
        cas->>+p: buscarLote(nroLote : String)
        p-->>-cas: «lt : Lote»
        create participant aj as aj:AjusteStock
        cas->>+aj: registrar(lt : Lote, cantidadReal : int, motivo : String, responsable : Usuario)
        aj->>+resp: esAdministrador()
        resp-->>-aj: «true»
        aj-->>-cas: «aj : AjusteStock»
        cas->>+aj: aplicar()
        aj->>+lt: corregirCantidad(cantidadReal : int)
        lt-->>-aj: «cantidadLote : int»
        aj-->>-cas: «true»
        cas->>+p: tieneStockBajo()
        p-->>-cas: «stockBajo : boolean»
        opt stockBajo = true
            Note right of cas: el producto queda en el listado<br/>de stock bajo (RN-16)
        end
        cas-->>adm: «ajuste registrado + stock vigente recalculado»
    else cantidad negativa o falta el motivo
        cas-->>adm: «"dato inválido o incompleto"»
    end
    deactivate cas
```

---

## CU-07 - Registrar retiro de mercadería vencida

```mermaid
sequenceDiagram
    actor u as u:Usuario
    participant crv as crv:ControladorRegistrarRetiro
    participant cat as cat:Catalogo
    participant sv as vencidos[i]:StockVencido
    participant lt as lt:Lote

    Note over u: el laboratorio retira físicamente la mercadería<br/>del depósito (no interactúa con el sistema)
    u->>+crv: leerEtiqueta(codigoEtiqueta : String)
    crv->>+cat: listarVencidos()
    cat-->>-crv: «vencidos : StockVencido[]»
    loop para cada sv en vencidos
        crv->>+sv: getCodigoEtiqueta() ⚠
        sv-->>-crv: «codigo : String»
    end
    Note right of crv: si codigo = codigoEtiqueta<br/>encontrado = vencidos[i]<br/>cortar iteración
    alt etiqueta encontrada
        crv->>+sv: getLote()
        sv-->>-crv: «lt : Lote»
        crv->>+lt: getProducto()
        lt-->>-crv: «p : Producto»
        crv->>+lt: getFechaVencimiento()
        lt-->>-crv: «fechaVencimiento : Date»
        crv->>+sv: getCantidad()
        sv-->>-crv: «cantidad : int»
        crv-->>u: «producto, cantidad y fecha de vencimiento del lote»
    else etiqueta no encontrada
        crv-->>u: «"etiqueta no encontrada"»
    end
    deactivate crv

    u->>+crv: registrarRetiro(cantidadRetirada : int)
    alt cantidadRetirada ≤ cantidad del lote vencido
        create participant rv as rv:RetiroVencido
        crv->>+rv: registrar(loteVencido : StockVencido, cantidad : int, responsable : Usuario)
        rv-->>-crv: «rv : RetiroVencido»
        crv->>+rv: aplicar()
        rv->>+sv: descontar(cantidad : int)
        sv-->>-rv: «cantidadRestante : int»
        rv-->>-crv: «true»
        crv-->>u: «retiro confirmado: stock vencido descontado (RN-18, RN-20)»
    else la cantidad supera la del lote
        crv-->>u: «"la cantidad supera la del lote"»
    end
    deactivate crv
```

> Si el usuario cancela antes de confirmar (5.b del CU), no se envía `registrarRetiro` y no hay cambios.

---

## CU-08 - Consultar productos con stock bajo

```mermaid
sequenceDiagram
    actor u as u:Usuario
    participant csb as csb:ControladorConsultarStockBajo
    participant cat as cat:Catalogo
    participant p as productos[i]:Producto

    u->>+csb: consultarStockBajo()
    csb->>+cat: listarStockBajo()
    loop para cada p en productos
        cat->>+p: estaActivo()
        p-->>-cat: «activo : boolean»
        cat->>+p: tieneStockBajo()
        p-->>-cat: «stockBajo : boolean»
    end
    Note right of cat: agrega p al resultado si está activo<br/>y su stock vigente ≤ stockMinimo (RN-16, RN-22)
    cat-->>-csb: «stockBajo : Producto[]»
    alt hay productos bajo el mínimo
        csb-->>u: «productos a reponer»
    else no hay
        csb-->>u: «"no hay productos con stock bajo"»
    end
    deactivate csb
```

---

## CU-09 - Consultar vencimientos y stock vencido

```mermaid
sequenceDiagram
    actor u as u:Usuario
    participant ccv as ccv:ControladorConsultarVencimientos
    participant cat as cat:Catalogo
    participant p as productos[i]:Producto
    participant lt as lotes[j]:Lote

    u->>+ccv: consultarVencimientos()
    ccv->>+cat: listarProximosAVencer(dias : int)
    Note right of cat: dias = 15 (RN-17)
    cat-->>-ccv: «proximos : Lote[]»
    ccv->>+cat: listarVencidos()
    cat-->>-ccv: «vencidos : StockVencido[]»
    ccv->>+cat: listarProductos()
    cat-->>-ccv: «productos : Producto[]»
    loop para cada p en productos
        ccv->>+p: getLotes() ⚠
        p-->>-ccv: «lotes : Lote[]»
        loop para cada lt en lotes
            ccv->>+lt: getCantidadLote()
            lt-->>-ccv: «cantidadLote : int»
        end
    end
    Note right of ccv: por producto (RN-19):<br/>vigente = suma de cantidadLote<br/>vencido = suma de StockVencido.cantidad<br/>total físico = vigente + vencido
    alt hay lotes próximos a vencer
        ccv-->>u: «lotes próximos a vencer (nroLote, producto, cantidad, fecha)»
    else no hay
        ccv-->>u: «"no hay lotes por vencer"»
    end
    alt hay stock vencido pendiente de retiro
        ccv-->>u: «lotes vencidos (etiqueta, nroLote, producto, cantidad, fecha)»
    else no hay
        ccv-->>u: «"no hay mercadería vencida en el depósito"»
    end
    ccv-->>u: «por producto: stock vigente, stock vencido y total físico»
    deactivate ccv
```

---

## Métodos que faltan en el DCC (⚠)

Los diagramas usan tres métodos que los casos de uso necesitan y el diagrama de clases todavía no tiene:

| Método | Lo usan | Para qué |
|---|---|---|
| `Catalogo.buscarCategoria(nombreCategoria : String) : Categoria` | CU-03, CU-04 | validar que el nombre no se repita y obtener la categoría del producto |
| `Producto.getLotes() : Lote[]` | CU-05, CU-06, CU-09 | listar los lotes de un producto y calcular su stock vigente |
| `StockVencido.getCodigoEtiqueta() : String` | CU-07 | encontrar el lote vencido por la etiqueta leída |

Los **controladores** (`ControladorIniciarSesion`, `ControladorGestionarUsuario`, etc.) son clases de diseño nuevas: no figuran en el DCC, que modela solo el dominio.
