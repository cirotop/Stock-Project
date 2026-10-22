# Diagramas de Secuencia - Stock Proyect

Un diagrama por caso de uso. Cada bloque se pega por separado en Mermaid (mermaid.live).
Participantes: actor, Interfaz (pantalla), Controlador (lógica del sistema) y Base de Datos.

---

## CU-01 - Iniciar sesión

```mermaid
sequenceDiagram
    actor U as Usuario
    participant I as Interfaz
    participant C as Controlador
    participant BD as Base de Datos

    U->>I: Ingresa usuario y contraseña
    I->>C: validarCredenciales(usuario, contraseña)
    C->>BD: buscarUsuario(usuario)
    BD-->>C: datos del usuario
    alt Credenciales válidas
        C->>C: identificarRol()
        C-->>I: habilitar funciones del rol
        C->>BD: consultarAlertas()
        BD-->>C: stock bajo, próximos a vencer y vencidos
        C-->>I: datos del panel de alertas
        I-->>U: Muestra panel de alertas
    else Usuario o contraseña incorrectos
        C-->>I: error de autenticación
        I-->>U: Muestra "usuario o contraseña incorrectos"
    end
```

---

## CU-02 - Gestionar usuario (alta, baja y modificación)

```mermaid
sequenceDiagram
    actor A as Administrador
    participant I as Interfaz
    participant C as Controlador
    participant BD as Base de Datos

    alt Alta
        A->>I: Selecciona alta de usuario
        A->>I: Ingresa usuario, contraseña, nombre completo y rol
        I->>C: altaUsuario(datos)
        C->>BD: existeUsuario(usuario)
        BD-->>C: resultado
        alt Nombre de usuario disponible
            C->>C: cifrarContraseña()
            C->>BD: guardarUsuario(datos)
            BD-->>C: OK
            C-->>I: usuario registrado
            I-->>A: Confirma el alta
        else Nombre de usuario repetido
            C-->>I: error
            I-->>A: Muestra "nombre de usuario no disponible"
        end
    else Modificación
        A->>I: Edita datos o rol de un usuario
        I->>C: modificarUsuario(datos)
        C->>BD: actualizarUsuario(datos)
        BD-->>C: OK
        C-->>I: usuario actualizado
        I-->>A: Confirma la modificación
    else Baja
        A->>I: Da de baja un usuario
        I->>C: bajaUsuario(idUsuario)
        C->>BD: marcarInactivo(idUsuario)
        BD-->>C: OK
        C-->>I: usuario inactivo
        I-->>A: Confirma la baja
    end
```

---

## CU-03 - Gestionar categoría (alta y modificación)

```mermaid
sequenceDiagram
    actor A as Administrador
    participant I as Interfaz
    participant C as Controlador
    participant BD as Base de Datos

    alt Alta
        A->>I: Selecciona alta de categoría
        A->>I: Ingresa nombre y descripción opcional
        I->>C: altaCategoria(datos)
        C->>BD: existeCategoria(nombre)
        BD-->>C: resultado
        alt Nombre disponible
            C->>BD: guardarCategoria(datos)
            BD-->>C: OK
            C-->>I: categoría registrada
            I-->>A: Confirma el alta
        else Nombre repetido
            C-->>I: error
            I-->>A: Muestra "categoría ya registrada"
        end
    else Modificación
        A->>I: Busca una categoría
        I->>C: buscarCategoria(criterio)
        C->>BD: consultarCategoria(criterio)
        BD-->>C: categoría
        C-->>I: datos de la categoría
        A->>I: Edita nombre o descripción y guarda
        I->>C: modificarCategoria(datos)
        C->>BD: actualizarCategoria(datos)
        BD-->>C: OK
        C-->>I: categoría actualizada
        I-->>A: Confirma la modificación
    end
```

---

## CU-04 - Gestionar producto (alta, baja y modificación)

```mermaid
sequenceDiagram
    actor A as Administrador
    participant I as Interfaz
    participant C as Controlador
    participant BD as Base de Datos

    alt Alta
        A->>I: Selecciona alta de producto
        A->>I: Ingresa código, nombre, descripción, categoría y stock mínimo
        I->>C: altaProducto(datos)
        C->>BD: existeCodigo(codigo)
        BD-->>C: resultado
        alt Código disponible
            C->>BD: guardarProducto(datos, estado activo, sin lotes)
            BD-->>C: OK
            C-->>I: producto registrado con stock vigente en cero
            I-->>A: Confirma el alta
        else Código repetido
            C-->>I: error
            I-->>A: Muestra "código ya registrado"
        end
    else Modificación
        A->>I: Busca un producto
        I->>C: buscarProducto(criterio)
        C->>BD: consultarProducto(criterio)
        BD-->>C: producto
        C-->>I: datos del producto
        A->>I: Edita los datos y guarda
        I->>C: modificarProducto(datos)
        C->>BD: actualizarProducto(datos)
        BD-->>C: OK
        C-->>I: producto actualizado
        I-->>A: Confirma la modificación
    else Baja
        A->>I: Busca un producto y lo da de baja
        I->>C: bajaProducto(idProducto)
        C->>BD: marcarInactivo(idProducto)
        BD-->>C: OK
        C-->>I: producto inactivo
        I-->>A: Confirma la baja
    end
```

---

## CU-05 - Registrar ingreso de mercadería

```mermaid
sequenceDiagram
    actor U as Usuario
    participant I as Interfaz
    participant C as Controlador
    participant BD as Base de Datos

    U->>I: Selecciona ingreso de mercadería
    U->>I: Selecciona producto por código o nombre
    I->>C: buscarProducto(criterio)
    C->>BD: consultarProducto(criterio)
    BD-->>C: resultado
    alt Producto no existe
        C-->>I: producto no encontrado
        I-->>U: Ofrece darlo de alta (CU-04) o cancelar
    else Producto activo
        C-->>I: datos del producto
        U->>I: Ingresa número de lote, cantidad y fecha de vencimiento
        I->>C: registrarIngreso(lote, cantidad, fechaVencimiento)
        C->>C: validarDatos()
        alt Datos inválidos o incompletos
            C-->>I: error de validación
            I-->>U: Avisa que el dato no es válido o está incompleto
        else Datos válidos
            opt Fecha de vencimiento ya pasó
                C-->>I: advertencia de mercadería vencida
                I-->>U: Pide confirmar
                U->>I: Confirma
                I->>C: confirmarIngreso()
            end
            C->>BD: existeLote(idProducto, numeroLote)
            BD-->>C: resultado
            alt Lote ya existe
                C-->>I: lote existente con su fecha de vencimiento
                I-->>U: Ofrece sumar la cantidad o corregir el número
                U->>I: Elige sumar al lote existente
                I->>C: sumarCantidadLote()
                C->>BD: actualizarCantidadLote(lote, cantidad)
            else Lote nuevo
                C->>BD: altaLote(idProducto, lote, cantidad, fechaVencimiento)
            end
            BD-->>C: OK
            C->>BD: registrarMovimiento(usuario, fecha)
            BD-->>C: OK
            C->>BD: calcularStockVigente(idProducto)
            BD-->>C: stock vigente
            C-->>I: ingreso registrado
            I-->>U: Confirma y muestra el stock vigente
        end
    end
```

---

## CU-06 - Ajustar stock

```mermaid
sequenceDiagram
    actor A as Administrador
    participant I as Interfaz
    participant C as Controlador
    participant BD as Base de Datos

    A->>I: Selecciona el producto a corregir
    I->>C: obtenerLotes(idProducto)
    C->>BD: consultarLotes(idProducto)
    BD-->>C: lotes con número, vencimiento y cantidad
    C-->>I: listado de lotes
    I-->>A: Muestra los lotes del producto
    A->>I: Selecciona lote, ingresa cantidad real y motivo
    I->>C: ajustarStock(idLote, cantidadReal, motivo)
    C->>C: validarDatos()
    alt Cantidad negativa o falta el motivo
        C-->>I: error de validación
        I-->>A: Avisa que el dato no es válido o está incompleto
    else Datos válidos
        C->>BD: actualizarCantidadLote(idLote, cantidadReal)
        BD-->>C: OK
        C->>BD: registrarAjuste(motivo, usuario, fecha)
        BD-->>C: OK
        C->>BD: calcularStockVigente(idProducto)
        BD-->>C: stock vigente
        opt Stock vigente en o por debajo del mínimo
            C->>BD: marcarStockBajo(idProducto)
            BD-->>C: OK
        end
        C-->>I: ajuste realizado
        I-->>A: Confirma el ajuste y muestra el stock recalculado
    end
```

---

## CU-07 - Registrar retiro de mercadería vencida

```mermaid
sequenceDiagram
    actor L as Laboratorio
    actor U as Usuario
    participant I as Interfaz
    participant C as Controlador
    participant BD as Base de Datos

    L->>U: Retira la mercadería vencida del depósito
    U->>I: Selecciona retiro de mercadería vencida
    U->>I: Lee el código de la etiqueta del lote
    I->>C: buscarStockVencido(etiqueta)
    C->>BD: consultarStockVencido(etiqueta)
    BD-->>C: resultado
    alt Etiqueta no encontrada
        C-->>I: error
        I-->>U: Muestra "etiqueta no encontrada"
    else Etiqueta válida
        C-->>I: producto, cantidad y fecha de vencimiento
        I-->>U: Muestra los datos del lote vencido
        U->>I: Ingresa la cantidad retirada
        alt Usuario cancela
            I-->>U: No se realizan cambios
        else Usuario confirma
            I->>C: registrarRetiro(etiqueta, cantidad)
            alt Cantidad supera la del lote
                C-->>I: error de cantidad
                I-->>U: Avisa que la cantidad supera la del lote
            else Cantidad válida
                C->>BD: descontarStockVencido(etiqueta, cantidad)
                BD-->>C: OK
                C->>BD: registrarRetiro(usuario, fecha)
                BD-->>C: OK
                C-->>I: retiro registrado
                I-->>U: Confirma el retiro
            end
        end
    end
```

---

## CU-08 - Consultar productos con stock bajo

```mermaid
sequenceDiagram
    actor U as Usuario
    participant I as Interfaz
    participant C as Controlador
    participant BD as Base de Datos

    U->>I: Accede al panel de alertas de stock
    I->>C: consultarStockBajo()
    C->>BD: productosActivosBajoMinimo()
    BD-->>C: listado de productos
    alt Hay productos bajo el mínimo
        C-->>I: listado de productos a reponer
        I-->>U: Muestra los productos con stock bajo
    else Ninguno bajo el mínimo
        C-->>I: listado vacío
        I-->>U: Informa que no hay productos con stock bajo
    end
```

---

## CU-09 - Consultar vencimientos y stock vencido

```mermaid
sequenceDiagram
    actor U as Usuario
    participant I as Interfaz
    participant C as Controlador
    participant BD as Base de Datos

    U->>I: Accede al panel de vencimientos
    I->>C: consultarVencimientos()
    C->>BD: lotesProximosAVencer(15 días)
    BD-->>C: lotes próximos a vencer
    alt Hay lotes próximos a vencer
        C-->>I: lote, producto, cantidad y fecha
    else No hay lotes próximos
        C-->>I: informa que no hay lotes por vencer
    end
    C->>BD: stockVencidoPendiente()
    BD-->>C: lotes vencidos pendientes de retiro
    alt Hay stock vencido pendiente
        C-->>I: etiqueta, lote, producto, cantidad y fecha
    else No hay stock vencido
        C-->>I: informa que no hay mercadería vencida en el depósito
    end
    C->>BD: totalesPorProducto()
    BD-->>C: stock vigente y stock vencido
    C->>C: calcularStockTotalFisico()
    C-->>I: vigente, vencido y total físico por producto
    I-->>U: Muestra el panel de vencimientos
```
