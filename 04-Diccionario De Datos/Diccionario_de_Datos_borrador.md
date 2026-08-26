# diccionario de datos - stock (en proceso)

## notacion
= compuesto de
+ secuencial
[ | ] seleccion
{} repeticion
() opcional
@ clave

## almacenamientos
rol = @idRol + nombreRol + (descripcionRol)
usuario = @idUsuario + nombreUsuario + contraseña + nombreCompleto + idRol
categoria = @idCategoria + nombreCategoria + (descripcion)
producto = @idProducto + codigoProducto + nombreProducto + (descripcion) + idCategoria + stockVigente + stockVencido + stockMinimo + (fechaVencimiento) + estadoProducto
ingresoMercaderia = @idIngreso + idProducto + cantidad + fechaIngreso + fechaVencimiento + idUsuario
ajusteStock = @idAjuste + idProducto + cantidadReal + motivo + fechaAjuste + idUsuario
retiroVencido = @idRetiro + idProducto + cantidad + codigoEtiqueta + ... (falta, ver bien esto)

## seleccion
nombreRol = [ administrador | empleado ]
estado = [ activo | inactivo ]

## datos elementales (voy por la mitad)
idRol - nro entero, clave
nombreRol - alfanum 20 (adm / emp)
descripcionRol - alfanum 100
idUsuario - nro, clave
nombreUsuario - alfanum 30 unico
contraseña - alfanum 255
nombreCompleto - alfanum 80
idCategoria - nro clave
nombreCategoria - alfanum 50
idProducto - nro clave
codigoProducto - alfanum 30 unico
nombreProducto - alfanum 100
stockVigente - nro entero
stockVencido - nro entero
stockMinimo - nro entero
fechaVencimiento - fecha

## me falta
descripciones (categoria y producto)
estadoProducto (activo/inactivo)
dominios vi/vf de los stock
movimientos: idIngreso, cantidad, fechaIngreso, cantidadReal, motivo, fechaAjuste, idRetiro, codigoEtiqueta, fechaRetiro
revisar tipos y longitudes con el grupo
