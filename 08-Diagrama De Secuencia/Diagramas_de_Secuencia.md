# Diagramas de Secuencia de Diseño (DSD) - Stock Proyect

> **Nivel:** DSD. El `:Sistema` de caja negra se reemplaza por un **controlador** por caso de uso (patrón Controlador), que recibe la operación del actor y la delega hacia la derecha en los objetos del [Diagrama de Clases](../07-Diagrama%20De%20Clases/Diagrama_de_Clases.puml).
>
> **Convenciones** (las mismas de los ejemplos de `introducirArticulo` de la teoría):
> - Flecha llena: mensaje sincrónico, con sus parámetros tipados.
> - Flecha punteada: retorno, con el valor devuelto entre comillas latinas: `«valor : Tipo»`. Un retorno sin texto indica que el método no devuelve nada.
> - **No hay mensajes de un objeto a sí mismo.** Los cálculos y comparaciones se escriben en una nota (como `subtotal = subtotal + (cantidad * precio)` en la teoría).
> - **Las búsquedas y los listados se dibujan recorriendo la colección** en un `loop` (bucle): a cada elemento `coleccion[i]` se le pide el dato con su getter, y una nota dice qué se hace (`si ... cortar iteración` en una búsqueda, `agregar al resultado` en un listado). El objeto encontrado es otra línea de vida, como `especif[i]` y `ep` en la teoría.
> - `buscarProducto`, `buscarCategoria`, `buscarLote` y `listarVencidos` son operaciones del Catálogo y del Producto en el DCC: se muestran como un mensaje con su retorno, porque el DCC no tiene los getters ni las asociaciones para dibujar su recorrido.
> - Flecha punteada con punta abierta `crear(...)` / `registrar(...)`: creación; la caja del objeto nuevo aparece a esa altura.
> - `loop` = bucle, `opt` = opcional, `alt` = alternativo.
> - `responsable:Usuario` es el usuario logueado; donde la operación es exclusiva del Administrador, el controlador lo verifica con `esAdministrador()` (RN-11).
>
> Los **procesos automáticos** (vencimiento de lote, alertas) no se diagraman porque no los inicia un actor.

---

## CU-01 - Iniciar sesión

```mermaid
%%{init: {"sequence": {"mirrorActors": false}, "themeCSS": ".loopLine { stroke-dasharray: none !important; stroke-width: 1.5px !important; }"}}%%
sequenceDiagram
    actor u as u:Usuario
    participant ci as ci:ControladorIniciarSesion
    participant ui as usuarios[i]:Usuario
    participant us as us:Usuario
    participant cat as cat:Catalogo

    u->>+ci: iniciarSesion(nombreUsuario : String, contraseña : String)
    loop para cada usuario en usuarios
        ci->>+ui: getNombreUsuario()
        ui-->>-ci: «nombre : String»
    end
    Note right of ci: si nombre = nombreUsuario<br/>us = usuarios[i]<br/>cortar iteración
    Note right of ci: el control de acceso compara la contraseña<br/>ingresada con la almacenada (hash)
    alt us != null y contraseña correcta
        ci->>+us: estaActivo()
        us-->>-ci: «activo : boolean»
        alt activo = true
            ci->>+us: esAdministrador()
            us-->>-ci: «esAdmin : boolean»
            ci->>+cat: listarStockBajo()
            cat-->>-ci: «stockBajo : Producto[]»
            ci->>+cat: listarProximosAVencer(dias : int)
            cat-->>-ci: «proximos : Lote[]»
            ci->>+cat: listarVencidos()
            cat-->>-ci: «vencidos : StockVencido[]»
            Note right of cat: el recorrido de cada listado<br/>se detalla en CU-08 y CU-09
            ci-->>u: «funciones del rol + panel de alertas»
        else activo = false (usuario dado de baja)
            ci-->>u: «"usuario o contraseña incorrectos"»
        end
    else usuario inexistente o contraseña incorrecta
        ci-->>u: «"usuario o contraseña incorrectos"»
    end
    deactivate ci
```

---

## CU-02 - Gestionar usuario

### Alta

```mermaid
%%{init: {"sequence": {"mirrorActors": false}, "themeCSS": ".loopLine { stroke-dasharray: none !important; stroke-width: 1.5px !important; }"}}%%
sequenceDiagram
    actor adm as adm:Administrador
    participant cgu as cgu:ControladorGestionarUsuario
    participant resp as responsable:Usuario
    participant ui as usuarios[i]:Usuario
    participant ri as roles[i]:Rol

    adm->>+cgu: altaUsuario(nombreUsuario : String, contraseña : String, nombreCompleto : String, nombreRol : TipoRol)
    cgu->>+resp: esAdministrador()
    resp-->>-cgu: «esAdmin : boolean»
    loop para cada usuario en usuarios
        cgu->>+ui: getNombreUsuario()
        ui-->>-cgu: «nombre : String»
    end
    Note right of cgu: si nombre = nombreUsuario<br/>existente = usuarios[i]<br/>cortar iteración
    alt existente = null (nombre disponible)
        loop para cada rol en roles
            cgu->>+ri: getNombreRol()
            ri-->>-cgu: «nombre : TipoRol»
        end
        Note right of cgu: si nombre = nombreRol<br/>rol = roles[i]<br/>cortar iteración
        create participant nu as nu:Usuario
        cgu--)nu: crear(nombreUsuario : String, contraseña : String, nombreCompleto : String, rol : Rol, responsable : Usuario)
        Note right of nu: guarda la contraseña cifrada (hash)
        cgu-->>adm: «usuario creado»
    else existente != null (nombre ya usado)
        cgu-->>adm: «"nombre de usuario no disponible"»
    end
    deactivate cgu
```

### Modificación y baja

```mermaid
%%{init: {"sequence": {"mirrorActors": false}, "themeCSS": ".loopLine { stroke-dasharray: none !important; stroke-width: 1.5px !important; }"}}%%
sequenceDiagram
    actor adm as adm:Administrador
    participant cgu as cgu:ControladorGestionarUsuario
    participant resp as responsable:Usuario
    participant ui as usuarios[i]:Usuario
    participant ri as roles[i]:Rol
    participant us as us:Usuario

    adm->>+cgu: modificarUsuario(nombreUsuario : String, nombreCompleto : String, nombreRol : TipoRol)
    cgu->>+resp: esAdministrador()
    resp-->>-cgu: «esAdmin : boolean»
    loop para cada usuario en usuarios
        cgu->>+ui: getNombreUsuario()
        ui-->>-cgu: «nombre : String»
    end
    Note right of cgu: si nombre = nombreUsuario<br/>us = usuarios[i]<br/>cortar iteración
    loop para cada rol en roles
        cgu->>+ri: getNombreRol()
        ri-->>-cgu: «nombre : TipoRol»
    end
    Note right of cgu: si nombre = nombreRol<br/>rol = roles[i]<br/>cortar iteración
    cgu->>+us: modificar(nombreCompleto : String, rol : Rol)
    us-->>-cgu: 
    cgu-->>-adm: «usuario modificado»

    adm->>+cgu: darBajaUsuario(nombreUsuario : String)
    cgu->>+resp: esAdministrador()
    resp-->>-cgu: «esAdmin : boolean»
    loop para cada usuario en usuarios
        cgu->>+ui: getNombreUsuario()
        ui-->>-cgu: «nombre : String»
    end
    Note right of cgu: si nombre = nombreUsuario<br/>us = usuarios[i]<br/>cortar iteración
    cgu->>+us: darBaja()
    Note right of us: baja lógica: estadoUsuario = inactivo
    us-->>-cgu: 
    cgu-->>-adm: «usuario dado de baja»
```

---

## CU-03 - Gestionar categoría

### Alta

```mermaid
%%{init: {"sequence": {"mirrorActors": false}, "themeCSS": ".loopLine { stroke-dasharray: none !important; stroke-width: 1.5px !important; }"}}%%
sequenceDiagram
    actor adm as adm:Administrador
    participant cgc as cgc:ControladorGestionarCategoria
    participant resp as responsable:Usuario
    participant cat as cat:Catalogo

    adm->>+cgc: altaCategoria(nombreCategoria : String, descripcionCategoria : String)
    cgc->>+resp: esAdministrador()
    resp-->>-cgc: «esAdmin : boolean»
    cgc->>+cat: buscarCategoria(nombreCategoria : String)
    cat-->>-cgc: «existente : Categoria»
    alt existente = null (nombre disponible)
        create participant c as c:Categoria
        cgc--)c: crear(nombreCategoria : String, descripcionCategoria : String, responsable : Usuario)
        cgc-->>adm: «categoría creada»
    else existente != null (nombre ya registrado)
        cgc-->>adm: «"categoría ya registrada"»
    end
    deactivate cgc
```

### Modificación

```mermaid
%%{init: {"sequence": {"mirrorActors": false}, "themeCSS": ".loopLine { stroke-dasharray: none !important; stroke-width: 1.5px !important; }"}}%%
sequenceDiagram
    actor adm as adm:Administrador
    participant cgc as cgc:ControladorGestionarCategoria
    participant resp as responsable:Usuario
    participant cat as cat:Catalogo
    participant c as c:Categoria

    adm->>+cgc: modificarCategoria(nombreActual : String, nombreCategoria : String, descripcionCategoria : String)
    cgc->>+resp: esAdministrador()
    resp-->>-cgc: «esAdmin : boolean»
    cgc->>+cat: buscarCategoria(nombreActual : String)
    cat-->>-cgc: «c : Categoria»
    cgc->>+c: modificar(nombreCategoria : String, descripcionCategoria : String)
    c-->>-cgc: 
    cgc-->>-adm: «categoría modificada»
```

---

## CU-04 - Gestionar producto

### Alta

```mermaid
%%{init: {"sequence": {"mirrorActors": false}, "themeCSS": ".loopLine { stroke-dasharray: none !important; stroke-width: 1.5px !important; }"}}%%
sequenceDiagram
    actor adm as adm:Administrador
    participant cgp as cgp:ControladorGestionarProducto
    participant resp as responsable:Usuario
    participant cat as cat:Catalogo

    adm->>+cgp: altaProducto(codigoProducto : String, nombreProducto : String, descripcionProducto : String, nombreCategoria : String, stockMinimo : int)
    cgp->>+resp: esAdministrador()
    resp-->>-cgp: «esAdmin : boolean»
    cgp->>+cat: buscarProducto(codigoProducto : String)
    cat-->>-cgp: «existente : Producto»
    alt existente = null (código disponible)
        cgp->>+cat: buscarCategoria(nombreCategoria : String)
        cat-->>-cgp: «categoria : Categoria»
        create participant p as p:Producto
        cgp--)p: crear(codigoProducto : String, nombreProducto : String, descripcionProducto : String, categoria : Categoria, stockMinimo : int, responsable : Usuario)
        Note right of p: queda activo y sin lotes<br/>(stock vigente = 0, RN-08)
        cgp-->>adm: «producto creado»
    else existente != null (código ya registrado, RN-09)
        cgp-->>adm: «"código ya registrado"»
    end
    deactivate cgp
```

### Modificación y baja

```mermaid
%%{init: {"sequence": {"mirrorActors": false}, "themeCSS": ".loopLine { stroke-dasharray: none !important; stroke-width: 1.5px !important; }"}}%%
sequenceDiagram
    actor adm as adm:Administrador
    participant cgp as cgp:ControladorGestionarProducto
    participant resp as responsable:Usuario
    participant cat as cat:Catalogo
    participant p as p:Producto

    adm->>+cgp: modificarProducto(codigoProducto : String, nombreProducto : String, descripcionProducto : String, nombreCategoria : String, stockMinimo : int)
    cgp->>+resp: esAdministrador()
    resp-->>-cgp: «esAdmin : boolean»
    cgp->>+cat: buscarProducto(codigoProducto : String)
    cat-->>-cgp: «p : Producto»
    cgp->>+cat: buscarCategoria(nombreCategoria : String)
    cat-->>-cgp: «categoria : Categoria»
    cgp->>+p: modificar(nombreProducto : String, descripcionProducto : String, categoria : Categoria, stockMinimo : int)
    p-->>-cgp: 
    cgp-->>-adm: «producto modificado»

    adm->>+cgp: darBajaProducto(codigoProducto : String)
    cgp->>+resp: esAdministrador()
    resp-->>-cgp: «esAdmin : boolean»
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
%%{init: {"sequence": {"mirrorActors": false}, "themeCSS": ".loopLine { stroke-dasharray: none !important; stroke-width: 1.5px !important; }"}}%%
sequenceDiagram
    actor u as u:Usuario
    participant cri as cri:ControladorRegistrarIngreso
    participant cat as cat:Catalogo
    participant p as p:Producto
    participant li as lotes[i]:Lote
    participant lt as lt:Lote

    u->>+cri: registrarIngreso(codigoProducto : String, nroLote : String, cantidad : int, fechaVencimiento : Date)
    cri->>+cat: buscarProducto(codigoProducto : String)
    cat-->>-cri: «p : Producto»
    alt p = null (producto no existe)
        cri-->>u: «ofrece dar de alta el producto (CU-04) o cancelar»
    else cantidad inválida, o falta nroLote o fechaVencimiento
        cri-->>u: «"dato inválido o incompleto"»
    else producto existe y datos válidos
        cri->>+p: buscarLote(nroLote : String)
        p-->>-cri: «lt : Lote»
        opt lt != null (el lote ya existe)
            cri->>+lt: getFechaVencimiento()
            lt-->>-cri: «fechaVencimiento : Date»
            cri-->>u: «lote existente: ¿sumar la cantidad o corregir el número?»
            u->>cri: sumarAlLote()
        end
        opt fechaVencimiento anterior a la fecha actual
            cri-->>u: «"la mercadería ya está vencida": pide confirmar»
            u->>cri: confirmar()
        end
        create participant ing as ing:IngresoMercaderia
        cri--)ing: registrar(p : Producto, nroLote : String, cantidad : int, fechaVencimiento : Date, responsable : Usuario)
        cri->>+ing: aplicar()
        alt lt = null (lote nuevo)
            create participant nl as nl:Lote
            ing--)nl: crear(p : Producto, nroLote : String, cantidad : int, fechaVencimiento : Date)
        else lt != null (lote existente)
            ing->>+lt: aumentarCantidad(cantidad : int)
            lt-->>-ing: «cantidadLote : int»
        end
        ing-->>-cri: «true»
        cri->>+p: getLotes()
        p-->>-cri: «lotes : Lote[]»
        loop para cada lote en lotes
            cri->>+li: estaVencido(fecha : Date)
            li-->>-cri: «vencido : boolean»
            cri->>+li: getCantidadLote()
            li-->>-cri: «cantidadLote : int»
        end
        Note right of cri: si vencido = false:<br/>stockVigente = stockVigente + cantidadLote
        cri-->>u: «ingreso registrado + stockVigente»
    end
    deactivate cri
```

> El movimiento queda asentado con el usuario y la fecha (RN-07): `registrar(...)` recibe al `responsable`.

---

## CU-06 - Ajustar stock

```mermaid
%%{init: {"sequence": {"mirrorActors": false}, "themeCSS": ".loopLine { stroke-dasharray: none !important; stroke-width: 1.5px !important; }"}}%%
sequenceDiagram
    actor adm as adm:Administrador
    participant cas as cas:ControladorAjustarStock
    participant resp as responsable:Usuario
    participant cat as cat:Catalogo
    participant p as p:Producto
    participant li as lotes[i]:Lote
    participant lt as lt:Lote

    adm->>+cas: seleccionarProducto(codigoProducto : String)
    cas->>+cat: buscarProducto(codigoProducto : String)
    cat-->>-cas: «p : Producto»
    cas->>+p: getLotes()
    p-->>-cas: «lotes : Lote[]»
    loop para cada lote en lotes
        cas->>+li: getFechaVencimiento()
        li-->>-cas: «fechaVencimiento : Date»
        cas->>+li: getCantidadLote()
        li-->>-cas: «cantidadLote : int»
    end
    cas-->>-adm: «lotes (fechaVencimiento, cantidad)»

    adm->>+cas: ajustarStock(nroLote : String, cantidadReal : int, motivo : String)
    cas->>+resp: esAdministrador()
    resp-->>-cas: «esAdmin : boolean»
    alt cantidadReal ≥ 0 y motivo cargado
        cas->>+p: buscarLote(nroLote : String)
        p-->>-cas: «lt : Lote»
        create participant aj as aj:AjusteStock
        cas--)aj: registrar(lt : Lote, cantidadReal : int, motivo : String, responsable : Usuario)
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
    else cantidadReal negativa o falta el motivo
        cas-->>adm: «"dato inválido o incompleto"»
    end
    deactivate cas
```

---

## CU-07 - Registrar retiro de mercadería vencida

```mermaid
%%{init: {"sequence": {"mirrorActors": false}, "themeCSS": ".loopLine { stroke-dasharray: none !important; stroke-width: 1.5px !important; }"}}%%
sequenceDiagram
    actor u as u:Usuario
    participant crv as crv:ControladorRegistrarRetiro
    participant cat as cat:Catalogo
    participant vi as vencidos[i]:StockVencido
    participant sv as sv:StockVencido
    participant lt as lt:Lote

    Note over u: el laboratorio retira físicamente la mercadería<br/>del depósito (no interactúa con el sistema)
    u->>+crv: leerEtiqueta(codigoEtiqueta : String)
    crv->>+cat: listarVencidos()
    cat-->>-crv: «vencidos : StockVencido[]»
    loop para cada stockVencido en vencidos
        crv->>+vi: getCodigoEtiqueta()
        vi-->>-crv: «codigo : String»
    end
    Note right of crv: si codigo = codigoEtiqueta<br/>sv = vencidos[i]<br/>cortar iteración
    alt sv != null (etiqueta encontrada)
        crv->>+sv: getLote()
        sv-->>-crv: «lt : Lote»
        crv->>+lt: getProducto()
        lt-->>-crv: «p : Producto»
        crv->>+lt: getFechaVencimiento()
        lt-->>-crv: «fechaVencimiento : Date»
        crv->>+sv: getCantidad()
        sv-->>-crv: «cantidad : int»
        crv-->>u: «producto, cantidad y fecha de vencimiento del lote»
    else sv = null (etiqueta no encontrada)
        crv-->>u: «"etiqueta no encontrada"»
    end
    deactivate crv

    u->>+crv: registrarRetiro(cantidadRetirada : int)
    alt cantidadRetirada ≤ cantidad
        create participant rv as rv:RetiroVencido
        crv--)rv: registrar(sv : StockVencido, cantidadRetirada : int, responsable : Usuario)
        crv->>+rv: aplicar()
        rv->>+sv: descontar(cantidadRetirada : int)
        sv-->>-rv: «cantidadRestante : int»
        rv-->>-crv: «true»
        crv-->>u: «retiro confirmado: stock vencido descontado (RN-18, RN-20)»
    else cantidadRetirada mayor que cantidad
        crv-->>u: «"la cantidad supera la del lote"»
    end
    deactivate crv
```

> Si el usuario cancela antes de confirmar (5.b del CU), no se envía `registrarRetiro` y no hay cambios.

---

## CU-08 - Consultar productos con stock bajo

```mermaid
%%{init: {"sequence": {"mirrorActors": false}, "themeCSS": ".loopLine { stroke-dasharray: none !important; stroke-width: 1.5px !important; }"}}%%
sequenceDiagram
    actor u as u:Usuario
    participant csb as csb:ControladorConsultarStockBajo
    participant cat as cat:Catalogo
    participant pi as productos[i]:Producto

    u->>+csb: consultarStockBajo()
    csb->>+cat: listarStockBajo()
    loop para cada producto en productos
        cat->>+pi: estaActivo()
        pi-->>-cat: «activo : boolean»
        cat->>+pi: tieneStockBajo()
        pi-->>-cat: «stockBajo : boolean»
    end
    Note right of cat: si activo y stockBajo:<br/>agregar productos[i] al resultado<br/>(se recorren todos, no se corta)
    cat-->>-csb: «stockBajo : Producto[]»
    alt stockBajo no está vacía
        csb-->>u: «productos a reponer»
    else stockBajo vacía
        csb-->>u: «"no hay productos con stock bajo"»
    end
    deactivate csb
```

---

## CU-09 - Consultar vencimientos y stock vencido

```mermaid
%%{init: {"sequence": {"mirrorActors": false}, "themeCSS": ".loopLine { stroke-dasharray: none !important; stroke-width: 1.5px !important; }"}}%%
sequenceDiagram
    actor u as u:Usuario
    participant ccv as ccv:ControladorConsultarVencimientos
    participant cat as cat:Catalogo
    participant pi as productos[i]:Producto
    participant li as lotes[j]:Lote
    participant vi as vencidos[k]:StockVencido
    participant lv as lv:Lote

    u->>+ccv: consultarVencimientos()
    ccv->>+cat: listarProximosAVencer(dias : int)
    loop para cada producto en productos
        cat->>+pi: getLotes()
        pi-->>-cat: «lotes : Lote[]»
        loop para cada lote en lotes
            cat->>+li: estaProximoAVencer(dias : int)
            li-->>-cat: «proximo : boolean»
        end
    end
    Note right of cat: dias = 15 (RN-17)<br/>si proximo: agregar lotes[j] al resultado
    cat-->>-ccv: «proximos : Lote[]»
    ccv->>+cat: listarVencidos()
    cat-->>-ccv: «vencidos : StockVencido[]»
    ccv->>+cat: listarProductos()
    loop para cada producto en productos
        cat->>+pi: estaActivo()
        pi-->>-cat: «activo : boolean»
    end
    Note right of cat: si activo: agregar productos[i] al resultado
    cat-->>-ccv: «productos : Producto[]»
    loop para cada producto en productos
        ccv->>+pi: getLotes()
        pi-->>-ccv: «lotes : Lote[]»
        loop para cada lote en lotes
            ccv->>+li: estaVencido(fecha : Date)
            li-->>-ccv: «vencido : boolean»
            ccv->>+li: getCantidadLote()
            li-->>-ccv: «cantidadLote : int»
        end
    end
    Note right of ccv: si vencido = false:<br/>stockVigente(p) = stockVigente(p) + cantidadLote
    loop para cada stockVencido en vencidos
        ccv->>+vi: getCantidad()
        vi-->>-ccv: «cantidad : int»
        ccv->>+vi: getLote()
        vi-->>-ccv: «lv : Lote»
        ccv->>+lv: getProducto()
        lv-->>-ccv: «p : Producto»
    end
    Note right of ccv: stockVencido(p) = stockVencido(p) + cantidad<br/>totalFisico(p) = stockVigente(p) + stockVencido(p) (RN-19)
    alt proximos no está vacía
        ccv-->>u: «lotes próximos a vencer»
    else proximos vacía
        ccv-->>u: «"no hay lotes por vencer"»
    end
    alt vencidos no está vacía
        ccv-->>u: «lotes vencidos pendientes de retiro»
    else vencidos vacía
        ccv-->>u: «"no hay mercadería vencida en el depósito"»
    end
    ccv-->>u: «por producto: stockVigente, stockVencido y totalFisico»
    deactivate ccv
```

---

## Controladores

Los **controladores** (`ControladorIniciarSesion`, `ControladorGestionarUsuario`, etc.) son clases de diseño: no figuran en el DCC, que modela solo el dominio. Todos los métodos que los diagramas invocan sobre objetos del dominio existen en el [Diagrama de Clases](../07-Diagrama%20De%20Clases/Diagrama_de_Clases.puml).
