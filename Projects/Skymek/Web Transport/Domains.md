# Dominios de la Aplicacion

## Estructura de un Dominio

```
domain-name/
├── pages/           # Paginas/componentes de ruta
│   └── page-name/
│       ├── page-name.ts
│       ├── page-name.html
│       ├── desktop/   # Variante desktop
│       ├── mobile/    # Variante mobile
│       └── base/      # Clase base compartida
├── components/      # Componentes del dominio
├── services/        # Servicios especificos
└── models/          # Interfaces y tipos
```

## Lista de Dominios

### appointments
Gestion de citas y servicios vehiculares.

**Paginas:**
- `appointment-list` - Lista de citas (desktop/mobile)
- `appointment-calendar` - Vista calendario
- `appointment-schedule` - Agendar nueva cita
- `appointment-detail` - Detalle de cita
- `appointment-edit` - Editar cita
- `appointment-reschedule` - Reprogramar
- `appointment-manage` - Gestionar (mecanicos)
- `mechanic-kanban` - Tablero Kanban

### talleres
Gestion de talleres/workshops.

**Paginas:**
- `taller-list` - Lista de talleres
- `taller-detail` - Detalle del taller
- `taller-create` - Crear taller
- `taller-edit` - Editar taller
- `mechanic-list` - Mecanicos del taller
- `mechanic-create` - Crear mecanico
- `mechanic-detail` - Detalle de mecanico
- `service-list` - Servicios del taller
- `cash-register` - Cuadre de caja
- `nearby-talleres` - Talleres cercanos (Leaflet)

### vehicles
Gestion de vehiculos.

**Paginas:**
- `vehicle-list` - Lista de vehiculos
- `vehicle-detail` - Detalle del vehiculo
- `create-vehicle` - Crear vehiculo
- `edit-vehicle` - Editar vehiculo
- `vehicle-health-dashboard` - Panel de salud 3D (Three.js)
- `service-planning` - Planificar servicios

**Componentes especiales:**
- `CarDashboard` - Vista 3D del vehiculo
- `VehicleGauges` - Indicadores de salud
- `ConfidenceGauge` - Medidor de confianza
- `HealthGauge` - Medidor de salud

### owners
Gestion de propietarios de vehiculos.

**Paginas:**
- `owner-list` - Lista de propietarios
- `owner-detail` - Detalle del propietario

### mechanics
Gestion de mecanicos.

**Paginas:**
- `mechanic-list` - Lista de mecanicos
- `mechanic-detail` - Detalle de mecanico

### service-orders
Ordenes de servicio.

**Paginas:**
- `service-order-list` - Lista de ordenes
- `service-order-detail` - Detalle de orden

### parts
Gestion de piezas y repuestos.

**Paginas:**
- `parts-list` - Lista de piezas
- `sales-flow` - Flujo de ventas

### users
Gestion de usuarios (solo admin).

**Paginas:**
- `user-management` - Gestion de usuarios
- `user-create` - Crear usuario
- `user-edit` - Editar usuario
- `user-detail` - Detalle de usuario
- `user-import` - Importar usuarios
- `user-roles` - Roles de usuarios
- `user-reports` - Reportes de usuarios

### dashboard
Paneles de control por rol.

**Paginas:**
- `owner-dashboard` - Panel del propietario
- `system-admin-dashboard` - Panel del admin sistema
- `taller-admin-dashboard` - Panel del admin taller
- `mechanic-stats-dashboard` - Estadisticas del mecanico

### admin
Administracion del sistema.

**Paginas:**
- `catalog-admin` - Administracion de catalogos
- `roles-permissions` - Roles y permisos

### notifications
Sistema de notificaciones.

**Paginas:**
- `notification-center` - Centro de notificaciones

**Componentes:**
- `schedule-editor` - Editor de horarios
- `template-editor` - Editor de plantillas

### reports
Reportes y analisis.

**Paginas:**
- Multiples reportes segun dominio

### production
Gestion de produccion.

**Paginas:**
- `production-tracking` - Seguimiento de produccion

### inventory
Gestion de inventario.

**Paginas:**
- `inventory-create` - Crear inventario
- `inventory-stock` - Stock de inventario

### profile
Perfil de usuario.

**Paginas:**
- `personal-info` - Informacion personal
- `preferences` - Preferencias
- `notifications` - Configuracion de notificaciones

### settings
Configuracion de la aplicacion.

**Paginas:**
- Configuraciones generales
