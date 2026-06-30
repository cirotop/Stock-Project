# Requerimientos Funcionales - Stock Proyect

Este documento detalla las funcionalidades principales requeridas para el sistema de gestión de la distribuidora.

## 1. Gestión de Usuarios y Seguridad
* **Inicio de Sesión:** El sistema debe permitir el acceso mediante usuario y contraseña.
* **Roles de Acceso:** Manejo de dos roles: Administrador (acceso total) y Empleado (funciones limitadas).
* **Control de Permisos:** Restricción de modificación de precios, balances y control de stock según el rol del usuario.

## 2. Gestión de Productos y Stock
* **ABM de Productos:** Permitir el Alta, Baja y Modificación de productos.
* **Control de Stock:** Actualización automática del inventario tras cada venta o ingreso de mercadería.
* **Stock Mínimo:** Definición de límites mínimos por producto.
* **Alertas:** Notificaciones (push) automáticas al alcanzar el stock mínimo.

## 3. Ventas y Pedidos
* **Registro:** Capacidad para registrar las ventas realizadas.
* **Seguimiento:** Monitoreo rápido del estado de los pedidos.

## 4. Precios
* **Listas de Precios:** Generación y exportación de listas de precios actualizadas.
* **Actualización:** Gestión de precios exclusiva para el Administrador.

## 5. Datos e Integraciones
* **Migración:** Carga de datos existentes desde fuentes externas (papel/Google Sheets).
* **Integración (Opcional):** Capacidad técnica para futura integración con servicios de laboratorio.
