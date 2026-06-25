# Requerimientos Funcionales - Stock Proyect

Este documento detalla las funcionalidades principales requeridas para el sistema de gestión de la distribuidora[cite: 2].

## 1. Gestión de Usuarios y Seguridad
* **Inicio de Sesión:** El sistema debe permitir el acceso mediante usuario y contraseña[cite: 2].
* **Roles de Acceso:** Manejo de dos roles: Administrador (acceso total) y Empleado (funciones limitadas)[cite: 2].
* **Control de Permisos:** Restricción de modificación de precios, balances y control de stock según el rol del usuario[cite: 2].

## 2. Gestión de Productos y Stock
* **ABM de Productos:** Permitir el Alta, Baja y Modificación de productos[cite: 2].
* **Control de Stock:** Actualización automática del inventario tras cada venta o ingreso de mercadería[cite: 2].
* **Stock Mínimo:** Definición de límites mínimos por producto[cite: 2].
* **Alertas:** Notificaciones (push) automáticas al alcanzar el stock mínimo[cite: 2].

## 3. Ventas y Pedidos
* **Registro:** Capacidad para registrar las ventas realizadas[cite: 2].
* **Seguimiento:** Monitoreo rápido del estado de los pedidos[cite: 2].

## 4. Precios
* **Listas de Precios:** Generación y exportación de listas de precios actualizadas[cite: 2].
* **Actualización:** Gestión de precios exclusiva para el Administrador[cite: 2].

## 5. Datos e Integraciones
* **Migración:** Carga de datos existentes desde fuentes externas (papel/Google Sheets)[cite: 2].
* **Integración (Opcional):** Capacidad técnica para futura integración con servicios de laboratorio[cite: 2].