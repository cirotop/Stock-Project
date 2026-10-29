# Diagramas de Secuencia del Sistema (DSS) - Stock Proyect

> Nivel: DSS (sistema como caja negra). Los **procesos automáticos** (vencimiento de lote, alertas) no se incluyen porque no los inicia un actor.

---

## CU-01 - Iniciar sesión

```mermaid
sequenceDiagram
    autonumber
    actor U as Usuario
    participant S as :Sistema
    U->>S: iniciarSesion(nombreUsuario, contraseña)
    alt credenciales válidas
        S-->>U: rol habilitado + panel de alertas (stock bajo, próximos a vencer, vencidos)
    else usuario o contraseña incorrectos
        S-->>U: "usuario o contraseña incorrectos"
    end
```

---

## CU-02 - Gestionar usuario

```mermaid
sequenceDiagram
    autonumber
    actor A as Administrador
    participant S as :Sistema
    A->>S: altaUsuario(nombreUsuario, contraseña, nombreCompleto, rol)
    alt nombre de usuario disponible
        S-->>A: usuario creado (contraseña cifrada)
    else nombre de usuario ya existe
        S-->>A: "nombre de usuario no disponible"
    end
    Note over A,S: modificarUsuario(...) y darBajaUsuario(nombreUsuario)<br/>(baja lógica) siguen el mismo patrón
```

---

## CU-03 - Gestionar categoría

```mermaid
sequenceDiagram
    autonumber
    actor A as Administrador
    participant S as :Sistema
    A->>S: altaCategoria(nombre, descripcion)
    alt nombre disponible
        S-->>A: categoría creada
    else nombre ya registrado
        S-->>A: "categoría ya registrada"
    end
    Note over A,S: modificarCategoria(...) sigue el mismo patrón
```

---

## CU-04 - Gestionar producto

```mermaid
sequenceDiagram
    autonumber
    actor A as Administrador
    participant S as :Sistema
    A->>S: altaProducto(codigo, nombre, descripcion, categoria, stockMinimo)
    alt código disponible
        S-->>A: producto creado (activo, stock vigente = 0)
    else código ya registrado
        S-->>A: "código ya registrado"
    end
    Note over A,S: modificar y baja lógica análogos.<br/>El stock inicial se carga por ingreso (CU-05) o ajuste (CU-06)
```

---

## CU-05 - Registrar ingreso de mercadería

```mermaid
sequenceDiagram
    autonumber
    actor U as Usuario
    participant S as :Sistema
    U->>S: registrarIngreso(codigoProducto, nroLote, cantidad, fechaVencimiento)
    alt producto existe y datos válidos
        opt nroLote ya existe para ese producto
            S-->>U: muestra lote existente, ofrece sumar cantidad o corregir nro
        end
        opt fechaVencimiento ya pasó
            S-->>U: "mercadería vencida" (pide confirmar)
            U->>S: confirmar()
        end
        S-->>U: ingreso registrado + stock vigente actualizado
    else producto no existe
        S-->>U: ofrece dar de alta (CU-04) o cancelar
    else dato inválido o incompleto
        S-->>U: "dato inválido o incompleto"
    end
```

---

## CU-06 - Ajustar stock

```mermaid
sequenceDiagram
    autonumber
    actor A as Administrador
    participant S as :Sistema
    A->>S: seleccionarProducto(codigoProducto)
    S-->>A: lotes del producto (nroLote, fechaVencimiento, cantidad)
    A->>S: ajustarStock(nroLote, cantidadReal, motivo)
    alt datos válidos
        S-->>A: ajuste registrado + stock vigente recalculado
        opt stock vigente <= stock mínimo
            S-->>A: producto marcado en stock bajo
        end
    else cantidad inválida o falta motivo
        S-->>A: "dato inválido o incompleto"
    end
```

---

## CU-07 - Registrar retiro de mercadería vencida

```mermaid
sequenceDiagram
    autonumber
    actor U as Usuario
    participant S as :Sistema
    Note over U: El laboratorio retira físicamente la mercadería<br/>del depósito (no interactúa con el sistema)
    U->>S: leerEtiqueta(codigoEtiqueta)
    alt etiqueta encontrada
        S-->>U: producto, cantidad y fecha de vencimiento del lote
        U->>S: registrarRetiro(cantidadRetirada)
        alt cantidad <= cantidad del lote vencido
            S-->>U: retiro confirmado + stock vencido descontado
        else cantidad supera la del lote
            S-->>U: "la cantidad supera la del lote"
        end
    else etiqueta no encontrada
        S-->>U: "etiqueta no encontrada"
    end
```

---

## CU-08 - Consultar productos con stock bajo

```mermaid
sequenceDiagram
    autonumber
    actor U as Usuario
    participant S as :Sistema
    U->>S: consultarStockBajo()
    alt hay productos bajo el mínimo
        S-->>U: productos activos con stock vigente <= mínimo
    else no hay
        S-->>U: "no hay productos con stock bajo"
    end
```

---

## CU-09 - Consultar vencimientos y stock vencido

```mermaid
sequenceDiagram
    autonumber
    actor U as Usuario
    participant S as :Sistema
    U->>S: consultarVencimientos()
    alt hay lotes próximos a vencer
        S-->>U: lotes próximos a vencer (nroLote, producto, cantidad, fecha)
    else no hay
        S-->>U: "no hay lotes por vencer"
    end
    alt hay stock vencido pendiente
        S-->>U: lotes vencidos pendientes (etiqueta, nroLote, producto, cantidad, fecha)
    else no hay
        S-->>U: "no hay mercadería vencida en el depósito"
    end
    S-->>U: por producto, stock vigente + stock vencido = total físico
```
