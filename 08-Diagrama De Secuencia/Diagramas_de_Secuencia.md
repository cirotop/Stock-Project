# Diagramas de Secuencia de Diseño (DSD) - Stock Proyect

Diagramas hechos en **PlantUML** siguiendo los ejemplos de `crearNuevaVenta` e `introducirArticulo` de la teoría. Hay **un diagrama por operación del sistema**: cada uno se muestra como imagen y debajo tiene su código (desplegable *Código PlantUML*).

## Convenciones (las de la teoría)

- **Un DSD por operación del sistema**: empieza con un único mensaje del actor al controlador y termina con su retorno. Si una operación usa algo que obtuvo la anterior, el controlador lo conserva (como `ventaActual`).
- **Controlador** por caso de uso (`cpv:ControladorProcesarVenta` en la teoría): es el único que habla con el actor y delega hacia la derecha.
- Objetos como `nombre:Clase` **subrayados**, con líneas de vida punteadas. El actor es el rol (`adm:Administrador`).
- Flecha llena: mensaje con sus parámetros tipados. Flecha punteada: retorno `«valor : Tipo»` (sin texto si no devuelve nada).
- **Ninguna flecha sale y vuelve a la misma línea de vida**: los cálculos y comparaciones van en una nota, como `subtotal = subtotal + (cantidad * precio)`.
- **Búsquedas y listados**: el dueño de la colección (el Catálogo, como `cp:CatalogoDeProductos` en la teoría) la recorre en un marco `bucle [para cada ...]`, le pide a cada `coleccion[i]` el dato con su getter, y una nota dice qué hace: `si ... cortar iteración` en una búsqueda, `agregar al resultado` en un listado. El objeto encontrado es otra línea de vida, como `especif[i]` y `ep`.
- `crear(...)` / `registrar(...)`: flecha punteada de creación; la caja del objeto nuevo aparece a esa altura.
- Marcos `alt`, `opt` y `bucle` con su condición de guarda, según la tabla de operadores de la teoría.

Los **procesos automáticos** (vencimiento de lote, alertas) no se diagraman porque no los inicia un actor. Los **controladores** son clases de diseño y no figuran en el [Diagrama de Clases](../07-Diagrama%20De%20Clases/Diagrama_de_Clases.puml), que modela solo el dominio; todos los métodos que los DSD invocan sobre objetos del dominio existen en el DCC.

## Índice

- **CU-01 - Iniciar sesión**: `iniciarSesion`
- **CU-02 - Gestionar usuario**: `altaUsuario`, `modificarUsuario`, `darBajaUsuario`
- **CU-03 - Gestionar categoría**: `altaCategoria`, `modificarCategoria`
- **CU-04 - Gestionar producto**: `altaProducto`, `modificarProducto`, `darBajaProducto`
- **CU-05 - Registrar ingreso de mercadería**: `registrarIngreso`, `confirmarIngreso`
- **CU-06 - Ajustar stock**: `seleccionarProducto`, `ajustarStock`
- **CU-07 - Registrar retiro de mercadería vencida**: `leerEtiqueta`, `registrarRetiro`
- **CU-08 - Consultar productos con stock bajo**: `consultarStockBajo`
- **CU-09 - Consultar vencimientos y stock vencido**: `consultarVencimientos`

---

## CU-01 - Iniciar sesión

![DSD CU-01 iniciarSesion](DSD_CU-01_iniciarSesion.png)

<details>
<summary>Código PlantUML</summary>

```plantuml
@startuml DSD_CU-01_iniciarSesion
' Diagrama de Secuencia de Diseño (DSD) - CU-01 Iniciar sesión - iniciarSesion
' Estilo de los ejemplos de la teoría (introducirArticulo / crearNuevaVenta).
hide footbox
skinparam shadowing false
skinparam defaultFontName Arial
skinparam defaultFontSize 12
skinparam sequenceMessageAlign center
skinparam ArrowColor black
skinparam ParticipantBackgroundColor white
skinparam ParticipantBorderColor black
skinparam ActorBackgroundColor white
skinparam ActorBorderColor black
skinparam LifeLineBorderColor black
skinparam LifeLineBackgroundColor white
skinparam SequenceGroupBackgroundColor white
skinparam SequenceGroupBorderColor black
skinparam SequenceGroupHeaderFontColor black
skinparam NoteBackgroundColor white
skinparam NoteBorderColor black

actor "<u>u:Administrador/Empleado</u>" as u
participant "<u>ci:ControladorIniciarSesion</u>" as ci
participant "<u>usuarios[i]:Usuario</u>" as ui
participant "<u>us:Usuario</u>" as us
participant "<u>cat:Catalogo</u>" as cat
participant "<u>productos[i]:Producto</u>" as pi
participant "<u>lotes[j]:Lote</u>" as li
participant "<u>vencidos[k]:StockVencido</u>" as vi

u -> ci ++ : iniciarSesion(nombreUsuario : String, contraseña : String)
group bucle [para cada usuario en usuarios]
  ci -> ui ++ : getNombreUsuario()
  ui --> ci -- : «nombre : String»
end
note right of ci
  si nombre = nombreUsuario
  us = usuarios[i]
  cortar iteración
end note
note right of ci : el control de acceso compara la contraseña\ningresada con la almacenada (hash)
alt us != null y contraseña correcta
  ci -> us ++ : estaActivo()
  us --> ci -- : «activo : boolean»
  alt activo = true
    ci -> us ++ : esAdministrador()
    us --> ci -- : «esAdmin : boolean»
    note right of ci : habilita las funciones del rol según esAdmin
    ci -> cat ++ : listarStockBajo()
    group bucle [para cada producto en productos]
      cat -> pi ++ : estaActivo()
      pi --> cat -- : «activo : boolean»
      cat -> pi ++ : tieneStockBajo()
      pi --> cat -- : «stockBajo : boolean»
    end
    note right of cat : si activo y stockBajo:\nagregar productos[i] al resultado
    cat --> ci -- : «stockBajo : Producto[]»
    ci -> cat ++ : listarProximosAVencer(dias : int)
    group bucle [para cada producto en productos]
      cat -> pi ++ : getLotes()
      pi --> cat -- : «lotes : Lote[]»
      group bucle [para cada lote en lotes]
        cat -> li ++ : estaProximoAVencer(dias : int)
        li --> cat -- : «proximo : boolean»
      end
    end
    note right of cat : dias = 15 (RN-17)\nsi proximo: agregar lotes[j] al resultado
    cat --> ci -- : «proximos : Lote[]»
    ci -> cat ++ : listarVencidos()
    group bucle [para cada stockVencido en vencidos]
      cat -> vi ++ : estaRetirado()
      vi --> cat -- : «retirado : boolean»
    end
    note right of cat : si retirado = false:\nagregar vencidos[k] al resultado
    cat --> ci -- : «vencidos : StockVencido[]»
    ci --> u : «funciones del rol + panel de alertas»
  else activo = false (usuario dado de baja)
    ci --> u : «"usuario o contraseña incorrectos"»
  end
else usuario inexistente o contraseña incorrecta
  ci --> u : «"usuario o contraseña incorrectos"»
end
deactivate ci

@enduml
```

</details>

---

## CU-02 - Gestionar usuario

### `altaUsuario`

![DSD CU-02 altaUsuario](DSD_CU-02_altaUsuario.png)

<details>
<summary>Código PlantUML</summary>

```plantuml
@startuml DSD_CU-02_altaUsuario
' Diagrama de Secuencia de Diseño (DSD) - CU-02 Gestionar usuario - altaUsuario
' Estilo de los ejemplos de la teoría (introducirArticulo / crearNuevaVenta).
hide footbox
skinparam shadowing false
skinparam defaultFontName Arial
skinparam defaultFontSize 12
skinparam sequenceMessageAlign center
skinparam ArrowColor black
skinparam ParticipantBackgroundColor white
skinparam ParticipantBorderColor black
skinparam ActorBackgroundColor white
skinparam ActorBorderColor black
skinparam LifeLineBorderColor black
skinparam LifeLineBackgroundColor white
skinparam SequenceGroupBackgroundColor white
skinparam SequenceGroupBorderColor black
skinparam SequenceGroupHeaderFontColor black
skinparam NoteBackgroundColor white
skinparam NoteBorderColor black

actor "<u>adm:Administrador</u>" as adm
participant "<u>cgu:ControladorGestionarUsuario</u>" as cgu
participant "<u>usuarios[i]:Usuario</u>" as ui
participant "<u>roles[i]:Rol</u>" as ri

adm -> cgu ++ : altaUsuario(nombreUsuario : String, contraseña : String, nombreCompleto : String, nombreRol : TipoRol)
group bucle [para cada usuario en usuarios]
  cgu -> ui ++ : getNombreUsuario()
  ui --> cgu -- : «nombre : String»
end
note right of cgu
  si nombre = nombreUsuario
  existente = usuarios[i]
  cortar iteración
end note
alt existente = null (nombre disponible)
  group bucle [para cada rol en roles]
    cgu -> ri ++ : getNombreRol()
    ri --> cgu -- : «nombre : TipoRol»
  end
  note right of cgu
    si nombre = nombreRol
    rol = roles[i]
    cortar iteración
  end note
  create participant "<u>nu:Usuario</u>" as nu
  cgu -->> nu : crear(nombreUsuario : String, contraseña : String, nombreCompleto : String, rol : Rol, responsable : Usuario)
  note right of nu : guarda la contraseña cifrada (hash)
  cgu --> adm : «usuario creado»
else existente != null (nombre ya usado)
  cgu --> adm : «"nombre de usuario no disponible"»
end
deactivate cgu

@enduml
```

</details>

### `modificarUsuario`

![DSD CU-02 modificarUsuario](DSD_CU-02_modificarUsuario.png)

<details>
<summary>Código PlantUML</summary>

```plantuml
@startuml DSD_CU-02_modificarUsuario
' Diagrama de Secuencia de Diseño (DSD) - CU-02 Gestionar usuario - modificarUsuario
' Estilo de los ejemplos de la teoría (introducirArticulo / crearNuevaVenta).
hide footbox
skinparam shadowing false
skinparam defaultFontName Arial
skinparam defaultFontSize 12
skinparam sequenceMessageAlign center
skinparam ArrowColor black
skinparam ParticipantBackgroundColor white
skinparam ParticipantBorderColor black
skinparam ActorBackgroundColor white
skinparam ActorBorderColor black
skinparam LifeLineBorderColor black
skinparam LifeLineBackgroundColor white
skinparam SequenceGroupBackgroundColor white
skinparam SequenceGroupBorderColor black
skinparam SequenceGroupHeaderFontColor black
skinparam NoteBackgroundColor white
skinparam NoteBorderColor black

actor "<u>adm:Administrador</u>" as adm
participant "<u>cgu:ControladorGestionarUsuario</u>" as cgu
participant "<u>usuarios[i]:Usuario</u>" as ui
participant "<u>roles[i]:Rol</u>" as ri
participant "<u>us:Usuario</u>" as us

adm -> cgu ++ : modificarUsuario(nombreUsuario : String, nombreCompleto : String, nombreRol : TipoRol)
group bucle [para cada usuario en usuarios]
  cgu -> ui ++ : getNombreUsuario()
  ui --> cgu -- : «nombre : String»
end
note right of cgu
  si nombre = nombreUsuario
  us = usuarios[i]
  cortar iteración
end note
group bucle [para cada rol en roles]
  cgu -> ri ++ : getNombreRol()
  ri --> cgu -- : «nombre : TipoRol»
end
note right of cgu
  si nombre = nombreRol
  rol = roles[i]
  cortar iteración
end note
cgu -> us ++ : modificar(nombreCompleto : String, rol : Rol)
us --> cgu --
cgu --> adm -- : «usuario modificado»

@enduml
```

</details>

### `darBajaUsuario`

![DSD CU-02 darBajaUsuario](DSD_CU-02_darBajaUsuario.png)

<details>
<summary>Código PlantUML</summary>

```plantuml
@startuml DSD_CU-02_darBajaUsuario
' Diagrama de Secuencia de Diseño (DSD) - CU-02 Gestionar usuario - darBajaUsuario
' Estilo de los ejemplos de la teoría (introducirArticulo / crearNuevaVenta).
hide footbox
skinparam shadowing false
skinparam defaultFontName Arial
skinparam defaultFontSize 12
skinparam sequenceMessageAlign center
skinparam ArrowColor black
skinparam ParticipantBackgroundColor white
skinparam ParticipantBorderColor black
skinparam ActorBackgroundColor white
skinparam ActorBorderColor black
skinparam LifeLineBorderColor black
skinparam LifeLineBackgroundColor white
skinparam SequenceGroupBackgroundColor white
skinparam SequenceGroupBorderColor black
skinparam SequenceGroupHeaderFontColor black
skinparam NoteBackgroundColor white
skinparam NoteBorderColor black

actor "<u>adm:Administrador</u>" as adm
participant "<u>cgu:ControladorGestionarUsuario</u>" as cgu
participant "<u>usuarios[i]:Usuario</u>" as ui
participant "<u>us:Usuario</u>" as us

adm -> cgu ++ : darBajaUsuario(nombreUsuario : String)
group bucle [para cada usuario en usuarios]
  cgu -> ui ++ : getNombreUsuario()
  ui --> cgu -- : «nombre : String»
end
note right of cgu
  si nombre = nombreUsuario
  us = usuarios[i]
  cortar iteración
end note
cgu -> us ++ : darBaja()
note right of us : baja lógica: estadoUsuario = inactivo
us --> cgu --
cgu --> adm -- : «usuario dado de baja»

@enduml
```

</details>

---

## CU-03 - Gestionar categoría

### `altaCategoria`

![DSD CU-03 altaCategoria](DSD_CU-03_altaCategoria.png)

<details>
<summary>Código PlantUML</summary>

```plantuml
@startuml DSD_CU-03_altaCategoria
' Diagrama de Secuencia de Diseño (DSD) - CU-03 Gestionar categoría - altaCategoria
' Estilo de los ejemplos de la teoría (introducirArticulo / crearNuevaVenta).
hide footbox
skinparam shadowing false
skinparam defaultFontName Arial
skinparam defaultFontSize 12
skinparam sequenceMessageAlign center
skinparam ArrowColor black
skinparam ParticipantBackgroundColor white
skinparam ParticipantBorderColor black
skinparam ActorBackgroundColor white
skinparam ActorBorderColor black
skinparam LifeLineBorderColor black
skinparam LifeLineBackgroundColor white
skinparam SequenceGroupBackgroundColor white
skinparam SequenceGroupBorderColor black
skinparam SequenceGroupHeaderFontColor black
skinparam NoteBackgroundColor white
skinparam NoteBorderColor black

actor "<u>adm:Administrador</u>" as adm
participant "<u>cgc:ControladorGestionarCategoria</u>" as cgc
participant "<u>cat:Catalogo</u>" as cat

adm -> cgc ++ : altaCategoria(nombreCategoria : String, descripcionCategoria : String)
cgc -> cat ++ : buscarCategoria(nombreCategoria : String)
cat --> cgc -- : «existente : Categoria»
alt existente = null (nombre disponible)
  create participant "<u>c:Categoria</u>" as c
  cgc -->> c : crear(nombreCategoria : String, descripcionCategoria : String, responsable : Usuario)
  cgc --> adm : «categoría creada»
else existente != null (nombre ya registrado)
  cgc --> adm : «"categoría ya registrada"»
end
deactivate cgc

@enduml
```

</details>

### `modificarCategoria`

![DSD CU-03 modificarCategoria](DSD_CU-03_modificarCategoria.png)

<details>
<summary>Código PlantUML</summary>

```plantuml
@startuml DSD_CU-03_modificarCategoria
' Diagrama de Secuencia de Diseño (DSD) - CU-03 Gestionar categoría - modificarCategoria
' Estilo de los ejemplos de la teoría (introducirArticulo / crearNuevaVenta).
hide footbox
skinparam shadowing false
skinparam defaultFontName Arial
skinparam defaultFontSize 12
skinparam sequenceMessageAlign center
skinparam ArrowColor black
skinparam ParticipantBackgroundColor white
skinparam ParticipantBorderColor black
skinparam ActorBackgroundColor white
skinparam ActorBorderColor black
skinparam LifeLineBorderColor black
skinparam LifeLineBackgroundColor white
skinparam SequenceGroupBackgroundColor white
skinparam SequenceGroupBorderColor black
skinparam SequenceGroupHeaderFontColor black
skinparam NoteBackgroundColor white
skinparam NoteBorderColor black

actor "<u>adm:Administrador</u>" as adm
participant "<u>cgc:ControladorGestionarCategoria</u>" as cgc
participant "<u>cat:Catalogo</u>" as cat
participant "<u>c:Categoria</u>" as c

adm -> cgc ++ : modificarCategoria(nombreActual : String, nombreCategoria : String, descripcionCategoria : String)
cgc -> cat ++ : buscarCategoria(nombreActual : String)
cat --> cgc -- : «c : Categoria»
cgc -> c ++ : modificar(nombreCategoria : String, descripcionCategoria : String)
c --> cgc --
cgc --> adm -- : «categoría modificada»

@enduml
```

</details>

---

## CU-04 - Gestionar producto

### `altaProducto`

![DSD CU-04 altaProducto](DSD_CU-04_altaProducto.png)

<details>
<summary>Código PlantUML</summary>

```plantuml
@startuml DSD_CU-04_altaProducto
' Diagrama de Secuencia de Diseño (DSD) - CU-04 Gestionar producto - altaProducto
' Estilo de los ejemplos de la teoría (introducirArticulo / crearNuevaVenta).
hide footbox
skinparam shadowing false
skinparam defaultFontName Arial
skinparam defaultFontSize 12
skinparam sequenceMessageAlign center
skinparam ArrowColor black
skinparam ParticipantBackgroundColor white
skinparam ParticipantBorderColor black
skinparam ActorBackgroundColor white
skinparam ActorBorderColor black
skinparam LifeLineBorderColor black
skinparam LifeLineBackgroundColor white
skinparam SequenceGroupBackgroundColor white
skinparam SequenceGroupBorderColor black
skinparam SequenceGroupHeaderFontColor black
skinparam NoteBackgroundColor white
skinparam NoteBorderColor black

actor "<u>adm:Administrador</u>" as adm
participant "<u>cgp:ControladorGestionarProducto</u>" as cgp
participant "<u>cat:Catalogo</u>" as cat

adm -> cgp ++ : altaProducto(codigoProducto : String, nombreProducto : String, descripcionProducto : String, nombreCategoria : String, stockMinimo : int)
cgp -> cat ++ : buscarProducto(codigoProducto : String)
cat --> cgp -- : «existente : Producto»
alt existente = null (código disponible)
  cgp -> cat ++ : buscarCategoria(nombreCategoria : String)
  cat --> cgp -- : «categoria : Categoria»
  create participant "<u>p:Producto</u>" as p
  cgp -->> p : crear(codigoProducto : String, nombreProducto : String, descripcionProducto : String, categoria : Categoria, stockMinimo : int, responsable : Usuario)
  note right of p : queda activo y sin lotes\n(stock vigente = 0, RN-08)
  cgp --> adm : «producto creado»
else existente != null (código ya registrado, RN-09)
  cgp --> adm : «"código ya registrado"»
end
deactivate cgp

@enduml
```

</details>

### `modificarProducto`

![DSD CU-04 modificarProducto](DSD_CU-04_modificarProducto.png)

<details>
<summary>Código PlantUML</summary>

```plantuml
@startuml DSD_CU-04_modificarProducto
' Diagrama de Secuencia de Diseño (DSD) - CU-04 Gestionar producto - modificarProducto
' Estilo de los ejemplos de la teoría (introducirArticulo / crearNuevaVenta).
hide footbox
skinparam shadowing false
skinparam defaultFontName Arial
skinparam defaultFontSize 12
skinparam sequenceMessageAlign center
skinparam ArrowColor black
skinparam ParticipantBackgroundColor white
skinparam ParticipantBorderColor black
skinparam ActorBackgroundColor white
skinparam ActorBorderColor black
skinparam LifeLineBorderColor black
skinparam LifeLineBackgroundColor white
skinparam SequenceGroupBackgroundColor white
skinparam SequenceGroupBorderColor black
skinparam SequenceGroupHeaderFontColor black
skinparam NoteBackgroundColor white
skinparam NoteBorderColor black

actor "<u>adm:Administrador</u>" as adm
participant "<u>cgp:ControladorGestionarProducto</u>" as cgp
participant "<u>cat:Catalogo</u>" as cat
participant "<u>p:Producto</u>" as p

adm -> cgp ++ : modificarProducto(codigoProducto : String, nombreProducto : String, descripcionProducto : String, nombreCategoria : String, stockMinimo : int)
cgp -> cat ++ : buscarProducto(codigoProducto : String)
cat --> cgp -- : «p : Producto»
cgp -> cat ++ : buscarCategoria(nombreCategoria : String)
cat --> cgp -- : «categoria : Categoria»
cgp -> p ++ : modificar(nombreProducto : String, descripcionProducto : String, categoria : Categoria, stockMinimo : int)
p --> cgp --
cgp --> adm -- : «producto modificado»

@enduml
```

</details>

### `darBajaProducto`

![DSD CU-04 darBajaProducto](DSD_CU-04_darBajaProducto.png)

<details>
<summary>Código PlantUML</summary>

```plantuml
@startuml DSD_CU-04_darBajaProducto
' Diagrama de Secuencia de Diseño (DSD) - CU-04 Gestionar producto - darBajaProducto
' Estilo de los ejemplos de la teoría (introducirArticulo / crearNuevaVenta).
hide footbox
skinparam shadowing false
skinparam defaultFontName Arial
skinparam defaultFontSize 12
skinparam sequenceMessageAlign center
skinparam ArrowColor black
skinparam ParticipantBackgroundColor white
skinparam ParticipantBorderColor black
skinparam ActorBackgroundColor white
skinparam ActorBorderColor black
skinparam LifeLineBorderColor black
skinparam LifeLineBackgroundColor white
skinparam SequenceGroupBackgroundColor white
skinparam SequenceGroupBorderColor black
skinparam SequenceGroupHeaderFontColor black
skinparam NoteBackgroundColor white
skinparam NoteBorderColor black

actor "<u>adm:Administrador</u>" as adm
participant "<u>cgp:ControladorGestionarProducto</u>" as cgp
participant "<u>cat:Catalogo</u>" as cat
participant "<u>p:Producto</u>" as p

adm -> cgp ++ : darBajaProducto(codigoProducto : String)
cgp -> cat ++ : buscarProducto(codigoProducto : String)
cat --> cgp -- : «p : Producto»
cgp -> p ++ : darBaja()
note right of p : baja lógica: estadoProducto = inactivo (RN-10)
p --> cgp --
cgp --> adm -- : «producto dado de baja»

@enduml
```

</details>

---

## CU-05 - Registrar ingreso de mercadería

### `registrarIngreso`

El usuario hace dos operaciones: primero carga los datos y el sistema le muestra qué va a pasar (lote nuevo o existente, y si la mercadería ya está vencida); después confirma con `confirmarIngreso`. Si quiere corregir el número de lote (3.b del CU), vuelve a enviar `registrarIngreso`.

![DSD CU-05 registrarIngreso](DSD_CU-05_registrarIngreso.png)

<details>
<summary>Código PlantUML</summary>

```plantuml
@startuml DSD_CU-05_registrarIngreso
' Diagrama de Secuencia de Diseño (DSD) - CU-05 Registrar ingreso de mercadería - registrarIngreso
' Estilo de los ejemplos de la teoría (introducirArticulo / crearNuevaVenta).
hide footbox
skinparam shadowing false
skinparam defaultFontName Arial
skinparam defaultFontSize 12
skinparam sequenceMessageAlign center
skinparam ArrowColor black
skinparam ParticipantBackgroundColor white
skinparam ParticipantBorderColor black
skinparam ActorBackgroundColor white
skinparam ActorBorderColor black
skinparam LifeLineBorderColor black
skinparam LifeLineBackgroundColor white
skinparam SequenceGroupBackgroundColor white
skinparam SequenceGroupBorderColor black
skinparam SequenceGroupHeaderFontColor black
skinparam NoteBackgroundColor white
skinparam NoteBorderColor black

actor "<u>u:Administrador/Empleado</u>" as u
participant "<u>cri:ControladorRegistrarIngreso</u>" as cri
participant "<u>cat:Catalogo</u>" as cat
participant "<u>p:Producto</u>" as p
participant "<u>lt:Lote</u>" as lt

u -> cri ++ : registrarIngreso(codigoProducto : String, nroLote : String, cantidad : int, fechaVencimiento : Date)
cri -> cat ++ : buscarProducto(codigoProducto : String)
cat --> cri -- : «p : Producto»
alt p = null (producto no existe)
  cri --> u : «ofrece dar de alta el producto (CU-04) o cancelar»
else cantidad inválida, o falta nroLote o fechaVencimiento
  cri --> u : «"dato inválido o incompleto"»
else producto existe y datos válidos
  cri -> p ++ : buscarLote(nroLote : String)
  p --> cri -- : «lt : Lote»
  opt lt != null (el lote ya existe)
    cri -> lt ++ : getFechaVencimiento()
    lt --> cri -- : «fechaLote : Date»
  end
  note right of cri : vencida = fechaVencimiento anterior a la fecha actual
  cri --> u : «a confirmar: lote nuevo o lote existente (con su fecha), y aviso si vencida = true»
end
deactivate cri

@enduml
```

</details>

### `confirmarIngreso`

![DSD CU-05 confirmarIngreso](DSD_CU-05_confirmarIngreso.png)

<details>
<summary>Código PlantUML</summary>

```plantuml
@startuml DSD_CU-05_confirmarIngreso
' Diagrama de Secuencia de Diseño (DSD) - CU-05 Registrar ingreso de mercadería - confirmarIngreso
' Estilo de los ejemplos de la teoría (introducirArticulo / crearNuevaVenta).
hide footbox
skinparam shadowing false
skinparam defaultFontName Arial
skinparam defaultFontSize 12
skinparam sequenceMessageAlign center
skinparam ArrowColor black
skinparam ParticipantBackgroundColor white
skinparam ParticipantBorderColor black
skinparam ActorBackgroundColor white
skinparam ActorBorderColor black
skinparam LifeLineBorderColor black
skinparam LifeLineBackgroundColor white
skinparam SequenceGroupBackgroundColor white
skinparam SequenceGroupBorderColor black
skinparam SequenceGroupHeaderFontColor black
skinparam NoteBackgroundColor white
skinparam NoteBorderColor black

actor "<u>u:Administrador/Empleado</u>" as u
participant "<u>cri:ControladorRegistrarIngreso</u>" as cri
participant "<u>p:Producto</u>" as p
participant "<u>lotes[i]:Lote</u>" as li
participant "<u>lt:Lote</u>" as lt

u -> cri ++ : confirmarIngreso()
note right of cri : usa p, lt, nroLote, cantidad y fechaVencimiento\nobtenidos en registrarIngreso
create participant "<u>ing:IngresoMercaderia</u>" as ing
cri -->> ing : registrar(p : Producto, nroLote : String, cantidad : int, fechaVencimiento : Date, responsable : Usuario)
cri -> ing ++ : aplicar()
alt lt = null (lote nuevo)
  create participant "<u>nl:Lote</u>" as nl
  ing -->> nl : crear(p : Producto, nroLote : String, cantidad : int, fechaVencimiento : Date)
else lt != null (se suma al lote existente)
  ing -> lt ++ : aumentarCantidad(cantidad : int)
  lt --> ing -- : «cantidadLote : int»
end
ing --> cri -- : «true»
cri -> p ++ : getLotes()
p --> cri -- : «lotes : Lote[]»
group bucle [para cada lote en lotes]
  cri -> li ++ : estaVencido(fecha : Date)
  li --> cri -- : «vencido : boolean»
  cri -> li ++ : getCantidadLote()
  li --> cri -- : «cantidadLote : int»
end
note right of cri : si vencido = false:\nstockVigente = stockVigente + cantidadLote
cri --> u -- : «ingreso registrado + stockVigente»

@enduml
```

</details>

> El movimiento queda asentado con el usuario y la fecha (RN-07): `registrar(...)` recibe al `responsable`.

---

## CU-06 - Ajustar stock

### `seleccionarProducto`

![DSD CU-06 seleccionarProducto](DSD_CU-06_seleccionarProducto.png)

<details>
<summary>Código PlantUML</summary>

```plantuml
@startuml DSD_CU-06_seleccionarProducto
' Diagrama de Secuencia de Diseño (DSD) - CU-06 Ajustar stock - seleccionarProducto
' Estilo de los ejemplos de la teoría (introducirArticulo / crearNuevaVenta).
hide footbox
skinparam shadowing false
skinparam defaultFontName Arial
skinparam defaultFontSize 12
skinparam sequenceMessageAlign center
skinparam ArrowColor black
skinparam ParticipantBackgroundColor white
skinparam ParticipantBorderColor black
skinparam ActorBackgroundColor white
skinparam ActorBorderColor black
skinparam LifeLineBorderColor black
skinparam LifeLineBackgroundColor white
skinparam SequenceGroupBackgroundColor white
skinparam SequenceGroupBorderColor black
skinparam SequenceGroupHeaderFontColor black
skinparam NoteBackgroundColor white
skinparam NoteBorderColor black

actor "<u>adm:Administrador</u>" as adm
participant "<u>cas:ControladorAjustarStock</u>" as cas
participant "<u>cat:Catalogo</u>" as cat
participant "<u>p:Producto</u>" as p
participant "<u>lotes[i]:Lote</u>" as li

adm -> cas ++ : seleccionarProducto(codigoProducto : String)
cas -> cat ++ : buscarProducto(codigoProducto : String)
cat --> cas -- : «p : Producto»
cas -> p ++ : getLotes()
p --> cas -- : «lotes : Lote[]»
group bucle [para cada lote en lotes]
  cas -> li ++ : getFechaVencimiento()
  li --> cas -- : «fechaVencimiento : Date»
  cas -> li ++ : getCantidadLote()
  li --> cas -- : «cantidadLote : int»
end
cas --> adm -- : «lotes (fechaVencimiento, cantidad)»

@enduml
```

</details>

### `ajustarStock`

![DSD CU-06 ajustarStock](DSD_CU-06_ajustarStock.png)

<details>
<summary>Código PlantUML</summary>

```plantuml
@startuml DSD_CU-06_ajustarStock
' Diagrama de Secuencia de Diseño (DSD) - CU-06 Ajustar stock - ajustarStock
' Estilo de los ejemplos de la teoría (introducirArticulo / crearNuevaVenta).
hide footbox
skinparam shadowing false
skinparam defaultFontName Arial
skinparam defaultFontSize 12
skinparam sequenceMessageAlign center
skinparam ArrowColor black
skinparam ParticipantBackgroundColor white
skinparam ParticipantBorderColor black
skinparam ActorBackgroundColor white
skinparam ActorBorderColor black
skinparam LifeLineBorderColor black
skinparam LifeLineBackgroundColor white
skinparam SequenceGroupBackgroundColor white
skinparam SequenceGroupBorderColor black
skinparam SequenceGroupHeaderFontColor black
skinparam NoteBackgroundColor white
skinparam NoteBorderColor black

actor "<u>adm:Administrador</u>" as adm
participant "<u>cas:ControladorAjustarStock</u>" as cas
participant "<u>p:Producto</u>" as p
participant "<u>lt:Lote</u>" as lt

adm -> cas ++ : ajustarStock(nroLote : String, cantidadReal : int, motivo : String)
note right of cas : p es el producto obtenido en seleccionarProducto
alt cantidadReal ≥ 0 y motivo cargado
  cas -> p ++ : buscarLote(nroLote : String)
  p --> cas -- : «lt : Lote»
  create participant "<u>aj:AjusteStock</u>" as aj
  cas -->> aj : registrar(lt : Lote, cantidadReal : int, motivo : String, responsable : Usuario)
  cas -> aj ++ : aplicar()
  aj -> lt ++ : corregirCantidad(cantidadReal : int)
  lt --> aj -- : «cantidadLote : int»
  aj --> cas -- : «true»
  cas -> p ++ : tieneStockBajo()
  p --> cas -- : «stockBajo : boolean»
  opt stockBajo = true
    note right of cas : el producto queda en el listado\nde stock bajo (RN-16)
  end
  cas --> adm : «ajuste registrado + stock vigente recalculado»
else cantidadReal negativa o falta el motivo
  cas --> adm : «"dato inválido o incompleto"»
end
deactivate cas

@enduml
```

</details>

---

## CU-07 - Registrar retiro de mercadería vencida

### `leerEtiqueta`

![DSD CU-07 leerEtiqueta](DSD_CU-07_leerEtiqueta.png)

<details>
<summary>Código PlantUML</summary>

```plantuml
@startuml DSD_CU-07_leerEtiqueta
' Diagrama de Secuencia de Diseño (DSD) - CU-07 Registrar retiro de mercadería vencida - leerEtiqueta
' Estilo de los ejemplos de la teoría (introducirArticulo / crearNuevaVenta).
hide footbox
skinparam shadowing false
skinparam defaultFontName Arial
skinparam defaultFontSize 12
skinparam sequenceMessageAlign center
skinparam ArrowColor black
skinparam ParticipantBackgroundColor white
skinparam ParticipantBorderColor black
skinparam ActorBackgroundColor white
skinparam ActorBorderColor black
skinparam LifeLineBorderColor black
skinparam LifeLineBackgroundColor white
skinparam SequenceGroupBackgroundColor white
skinparam SequenceGroupBorderColor black
skinparam SequenceGroupHeaderFontColor black
skinparam NoteBackgroundColor white
skinparam NoteBorderColor black

actor "<u>u:Administrador/Empleado</u>" as u
participant "<u>crv:ControladorRegistrarRetiro</u>" as crv
participant "<u>cat:Catalogo</u>" as cat
participant "<u>vencidos[i]:StockVencido</u>" as vi
participant "<u>sv:StockVencido</u>" as sv
participant "<u>lt:Lote</u>" as lt

note over u : el laboratorio retira físicamente la mercadería\ndel depósito (no interactúa con el sistema)
u -> crv ++ : leerEtiqueta(codigoEtiqueta : String)
crv -> cat ++ : listarVencidos()
group bucle [para cada stockVencido en vencidos]
  cat -> vi ++ : estaRetirado()
  vi --> cat -- : «retirado : boolean»
end
note right of cat : si retirado = false:\nagregar vencidos[i] al resultado
cat --> crv -- : «vencidos : StockVencido[]»
group bucle [para cada stockVencido en vencidos]
  crv -> vi ++ : getCodigoEtiqueta()
  vi --> crv -- : «codigo : String»
end
note right of crv
  si codigo = codigoEtiqueta
  sv = vencidos[i]
  cortar iteración
end note
alt sv != null (etiqueta encontrada)
  crv -> sv ++ : getLote()
  sv --> crv -- : «lt : Lote»
  crv -> lt ++ : getProducto()
  lt --> crv -- : «p : Producto»
  crv -> lt ++ : getFechaVencimiento()
  lt --> crv -- : «fechaVencimiento : Date»
  crv -> sv ++ : getCantidad()
  sv --> crv -- : «cantidad : int»
  crv --> u : «producto, cantidad y fecha de vencimiento del lote»
else sv = null (etiqueta no encontrada)
  crv --> u : «"etiqueta no encontrada"»
end
deactivate crv

@enduml
```

</details>

### `registrarRetiro`

![DSD CU-07 registrarRetiro](DSD_CU-07_registrarRetiro.png)

<details>
<summary>Código PlantUML</summary>

```plantuml
@startuml DSD_CU-07_registrarRetiro
' Diagrama de Secuencia de Diseño (DSD) - CU-07 Registrar retiro de mercadería vencida - registrarRetiro
' Estilo de los ejemplos de la teoría (introducirArticulo / crearNuevaVenta).
hide footbox
skinparam shadowing false
skinparam defaultFontName Arial
skinparam defaultFontSize 12
skinparam sequenceMessageAlign center
skinparam ArrowColor black
skinparam ParticipantBackgroundColor white
skinparam ParticipantBorderColor black
skinparam ActorBackgroundColor white
skinparam ActorBorderColor black
skinparam LifeLineBorderColor black
skinparam LifeLineBackgroundColor white
skinparam SequenceGroupBackgroundColor white
skinparam SequenceGroupBorderColor black
skinparam SequenceGroupHeaderFontColor black
skinparam NoteBackgroundColor white
skinparam NoteBorderColor black

actor "<u>u:Administrador/Empleado</u>" as u
participant "<u>crv:ControladorRegistrarRetiro</u>" as crv
participant "<u>sv:StockVencido</u>" as sv

u -> crv ++ : registrarRetiro(cantidadRetirada : int)
note right of crv : sv y cantidad son los obtenidos en leerEtiqueta
alt cantidadRetirada ≤ cantidad
  create participant "<u>rv:RetiroVencido</u>" as rv
  crv -->> rv : registrar(sv : StockVencido, cantidadRetirada : int, responsable : Usuario)
  crv -> rv ++ : aplicar()
  rv -> sv ++ : descontar(cantidadRetirada : int)
  sv --> rv -- : «cantidadRestante : int»
  rv --> crv -- : «true»
  crv --> u : «retiro confirmado: stock vencido descontado (RN-18, RN-20)»
else cantidadRetirada mayor que cantidad
  crv --> u : «"la cantidad supera la del lote"»
end
deactivate crv

@enduml
```

</details>

> Si el usuario cancela antes de confirmar (5.b del CU), no se envía `registrarRetiro` y no hay cambios.

---

## CU-08 - Consultar productos con stock bajo

![DSD CU-08 consultarStockBajo](DSD_CU-08_consultarStockBajo.png)

<details>
<summary>Código PlantUML</summary>

```plantuml
@startuml DSD_CU-08_consultarStockBajo
' Diagrama de Secuencia de Diseño (DSD) - CU-08 Consultar productos con stock bajo - consultarStockBajo
' Estilo de los ejemplos de la teoría (introducirArticulo / crearNuevaVenta).
hide footbox
skinparam shadowing false
skinparam defaultFontName Arial
skinparam defaultFontSize 12
skinparam sequenceMessageAlign center
skinparam ArrowColor black
skinparam ParticipantBackgroundColor white
skinparam ParticipantBorderColor black
skinparam ActorBackgroundColor white
skinparam ActorBorderColor black
skinparam LifeLineBorderColor black
skinparam LifeLineBackgroundColor white
skinparam SequenceGroupBackgroundColor white
skinparam SequenceGroupBorderColor black
skinparam SequenceGroupHeaderFontColor black
skinparam NoteBackgroundColor white
skinparam NoteBorderColor black

actor "<u>u:Administrador/Empleado</u>" as u
participant "<u>csb:ControladorConsultarStockBajo</u>" as csb
participant "<u>cat:Catalogo</u>" as cat
participant "<u>productos[i]:Producto</u>" as pi

u -> csb ++ : consultarStockBajo()
csb -> cat ++ : listarStockBajo()
group bucle [para cada producto en productos]
  cat -> pi ++ : estaActivo()
  pi --> cat -- : «activo : boolean»
  cat -> pi ++ : tieneStockBajo()
  pi --> cat -- : «stockBajo : boolean»
end
note right of cat
  si activo y stockBajo:
  agregar productos[i] al resultado
  (se recorren todos, no se corta)
end note
cat --> csb -- : «stockBajo : Producto[]»
alt stockBajo no está vacía
  csb --> u : «productos a reponer»
else stockBajo vacía
  csb --> u : «"no hay productos con stock bajo"»
end
deactivate csb

@enduml
```

</details>

---

## CU-09 - Consultar vencimientos y stock vencido

![DSD CU-09 consultarVencimientos](DSD_CU-09_consultarVencimientos.png)

<details>
<summary>Código PlantUML</summary>

```plantuml
@startuml DSD_CU-09_consultarVencimientos
' Diagrama de Secuencia de Diseño (DSD) - CU-09 Consultar vencimientos y stock vencido - consultarVencimientos
' Estilo de los ejemplos de la teoría (introducirArticulo / crearNuevaVenta).
hide footbox
skinparam shadowing false
skinparam defaultFontName Arial
skinparam defaultFontSize 12
skinparam sequenceMessageAlign center
skinparam ArrowColor black
skinparam ParticipantBackgroundColor white
skinparam ParticipantBorderColor black
skinparam ActorBackgroundColor white
skinparam ActorBorderColor black
skinparam LifeLineBorderColor black
skinparam LifeLineBackgroundColor white
skinparam SequenceGroupBackgroundColor white
skinparam SequenceGroupBorderColor black
skinparam SequenceGroupHeaderFontColor black
skinparam NoteBackgroundColor white
skinparam NoteBorderColor black

actor "<u>u:Administrador/Empleado</u>" as u
participant "<u>ccv:ControladorConsultarVencimientos</u>" as ccv
participant "<u>cat:Catalogo</u>" as cat
participant "<u>productos[i]:Producto</u>" as pi
participant "<u>lotes[j]:Lote</u>" as li
participant "<u>vencidos[k]:StockVencido</u>" as vi
participant "<u>lv:Lote</u>" as lv

u -> ccv ++ : consultarVencimientos()
ccv -> cat ++ : listarProximosAVencer(dias : int)
group bucle [para cada producto en productos]
  cat -> pi ++ : getLotes()
  pi --> cat -- : «lotes : Lote[]»
  group bucle [para cada lote en lotes]
    cat -> li ++ : estaProximoAVencer(dias : int)
    li --> cat -- : «proximo : boolean»
  end
end
note right of cat : dias = 15 (RN-17)\nsi proximo: agregar lotes[j] al resultado
cat --> ccv -- : «proximos : Lote[]»
ccv -> cat ++ : listarVencidos()
group bucle [para cada stockVencido en vencidos]
  cat -> vi ++ : estaRetirado()
  vi --> cat -- : «retirado : boolean»
end
note right of cat : si retirado = false:\nagregar vencidos[k] al resultado
cat --> ccv -- : «vencidos : StockVencido[]»
ccv -> cat ++ : listarProductos()
group bucle [para cada producto en productos]
  cat -> pi ++ : estaActivo()
  pi --> cat -- : «activo : boolean»
end
note right of cat : si activo: agregar productos[i] al resultado
cat --> ccv -- : «productos : Producto[]»
group bucle [para cada producto en productos]
  ccv -> pi ++ : getLotes()
  pi --> ccv -- : «lotes : Lote[]»
  group bucle [para cada lote en lotes]
    ccv -> li ++ : estaVencido(fecha : Date)
    li --> ccv -- : «vencido : boolean»
    ccv -> li ++ : getCantidadLote()
    li --> ccv -- : «cantidadLote : int»
  end
end
note right of ccv : si vencido = false:\nstockVigente(p) = stockVigente(p) + cantidadLote
group bucle [para cada stockVencido en vencidos]
  ccv -> vi ++ : getCantidad()
  vi --> ccv -- : «cantidad : int»
  ccv -> vi ++ : getLote()
  vi --> ccv -- : «lv : Lote»
  ccv -> lv ++ : getProducto()
  lv --> ccv -- : «p : Producto»
end
note right of ccv
  stockVencido(p) = stockVencido(p) + cantidad
  totalFisico(p) = stockVigente(p) + stockVencido(p) (RN-19)
end note
alt proximos no está vacía
  ccv --> u : «lotes próximos a vencer»
else proximos vacía
  ccv --> u : «"no hay lotes por vencer"»
end
alt vencidos no está vacía
  ccv --> u : «lotes vencidos pendientes de retiro»
else vencidos vacía
  ccv --> u : «"no hay mercadería vencida en el depósito"»
end
ccv --> u -- : «por producto: stockVigente, stockVencido y totalFisico»

@enduml
```

</details>
