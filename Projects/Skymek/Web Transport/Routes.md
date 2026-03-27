# Sistema de Rutas

## Ubicacion
`src/app/app.routes.ts`

## Estructura General

```
/                           -> Redirect a /app/dashboard
/login                      -> Login (guestGuard)
/register                   -> Registro (guestGuard)
/error/*                    -> Paginas de error

/app/**                     -> Rutas protegidas (authGuard)
```

## Rutas Publicas

| Ruta | Componente | Guard |
|------|------------|-------|
| `/login` | LoginPage | guestGuard |
| `/register` | RegisterPage | guestGuard |
| `/error/404` | NotFoundPage | - |
| `/error/500` | ServerErrorPage | - |
| `/error/502` | BadGatewayPage | - |

## Rutas Protegidas (/app)

Todas las rutas bajo `/app` requieren `authGuard`.

### Dashboard
| Ruta | Componente | Guard Adicional |
|------|------------|-----------------|
| `/app/dashboard` | DashboardPage | - |

### Usuarios
| Ruta | Componente | Guard Adicional |
|------|------------|-----------------|
| `/app/users` | UserManagementPage | adminGuard |
| `/app/users/create` | UserCreatePage | adminGuard |
| `/app/users/edit/:id` | UserEditPage | adminGuard |
| `/app/users/detail/:id` | UserDetailPage | adminGuard |
| `/app/users/management` | UserManagementPage | adminGuard |
| `/app/users/management/desktop` | UserManagementDesktop | adminGuard |
| `/app/users/management/mobile` | UserManagementMobile | adminGuard |
| `/app/users/import` | UserImportPage | adminGuard |
| `/app/users/roles` | UserRolesPage | adminGuard |
| `/app/users/reports` | UserReportsPage | adminGuard |

### Propietarios
| Ruta | Componente | Guard Adicional |
|------|------------|-----------------|
| `/app/owners` | OwnerListPage | adminGuard |
| `/app/owners/:id` | OwnerDetailPage | adminGuard |

### Talleres
| Ruta | Componente | Guard Adicional |
|------|------------|-----------------|
| `/app/talleres` | TallerListPage | adminGuard |
| `/app/talleres/create` | TallerCreatePage | adminGuard |
| `/app/talleres/nearby` | NearbyTalleresPage | - |
| `/app/talleres/conventions/create` | ConventionCreatePage | adminGuard |
| `/app/talleres/cash-register` | CashRegisterPage | adminGuard |
| `/app/talleres/:tallerId/edit` | TallerEditPage | adminGuard |
| `/app/talleres/:tallerId` | TallerDetailPage | - |
| `/app/talleres/:tallerId/mechanics` | MechanicListPage | adminGuard |
| `/app/talleres/:tallerId/mechanics/create` | MechanicCreatePage | adminGuard |
| `/app/talleres/:tallerId/mechanics/:mechanicId` | MechanicDetailPage | adminGuard |

### Vehiculos
| Ruta | Componente | Guard Adicional |
|------|------------|-----------------|
| `/app/my-vehicles` | MyVehiclesPage | - |
| `/app/vehicles/create` | CreateVehiclePage | - |
| `/app/vehicles/edit/:id` | EditVehiclePage | - |
| `/app/vehicles/detail/:id` | VehicleDetailPage | - |
| `/app/vehicles/health/:id` | VehicleHealthDashboard | - |
| `/app/vehicles/service-planning` | ServicePlanningPage | - |
| `/app/vehicles/services` | VehicleServicesPage | - |
| `/app/vehicles/maintenance` | MaintenancePage | - |

### Citas
| Ruta | Componente | Guard Adicional |
|------|------------|-----------------|
| `/app/appointments/schedule` | AppointmentSchedulePage | - |
| `/app/appointments/list` | AppointmentListPage | - |
| `/app/appointments/calendar` | AppointmentCalendarPage | - |
| `/app/appointments/:id/reschedule` | AppointmentReschedulePage | - |
| `/app/appointments/:id/edit` | AppointmentEditPage | - |
| `/app/appointments/:id/manage` | AppointmentManagePage | mechanicGuard |
| `/app/appointments/:id` | AppointmentDetailPage | - |

### Tareas (Mecanicos)
| Ruta | Componente | Guard Adicional |
|------|------------|-----------------|
| `/app/my-tasks` | MechanicKanbanPage | mechanicGuard |

### Clientes
| Ruta | Componente | Guard Adicional |
|------|------------|-----------------|
| `/app/customers` | CustomerListPage | adminGuard |

### Servicios del Taller
| Ruta | Componente | Guard Adicional |
|------|------------|-----------------|
| `/app/services` | ServiceListPage | adminGuard |
| `/app/services/create` | ServiceCreatePage | adminGuard |
| `/app/services/edit/:id` | ServiceEditPage | adminGuard |
| `/app/services/detail/:id` | ServiceDetailPage | adminGuard |

### Piezas/Repuestos
| Ruta | Componente | Guard Adicional |
|------|------------|-----------------|
| `/app/parts` | PartsListPage | - |
| `/app/parts/sales` | SalesFlowPage | - |

### Ordenes de Servicio
| Ruta | Componente | Guard Adicional |
|------|------------|-----------------|
| `/app/service-orders` | ServiceOrderListPage | - |
| `/app/service-orders/:id` | ServiceOrderDetailPage | - |

### Notificaciones
| Ruta | Componente | Guard Adicional |
|------|------------|-----------------|
| `/app/notifications` | NotificationCenterPage | - |

### Reportes
| Ruta | Componente | Guard Adicional |
|------|------------|-----------------|
| `/app/reports` | ReportsPage | adminGuard |

### Produccion
| Ruta | Componente | Guard Adicional |
|------|------------|-----------------|
| `/app/production` | ProductionPage | adminGuard |

### Inventario
| Ruta | Componente | Guard Adicional |
|------|------------|-----------------|
| `/app/inventory` | InventoryPage | adminGuard |
| `/app/inventory/create` | InventoryCreatePage | adminGuard |
| `/app/inventory/stock` | InventoryStockPage | adminGuard |

### Perfil
| Ruta | Componente | Guard Adicional |
|------|------------|-----------------|
| `/app/profile` | ProfilePage | - |
| `/app/profile/personal-info` | PersonalInfoPage | - |
| `/app/profile/preferences` | PreferencesPage | - |
| `/app/profile/notifications` | NotificationsSettingsPage | - |

### Administracion
| Ruta | Componente | Guard Adicional |
|------|------------|-----------------|
| `/app/admin/catalogs` | CatalogAdminPage | systemAdminGuard |
| `/app/admin/roles-permissions` | RolesPermissionsPage | systemAdminGuard |

## Guards Disponibles

| Guard | Descripcion |
|-------|-------------|
| `authGuard` | Verifica autenticacion |
| `guestGuard` | Redirige si ya esta autenticado |
| `adminGuard` | Admin del sistema o taller |
| `systemAdminGuard` | Solo admin del sistema |
| `vehicleOwnerGuard` | Propietarios de vehiculos |
| `mechanicGuard` | Mecanicos |
| `tallerAccessGuard` | Acceso a taller especifico |
| `ownerAccessGuard` | Acceso a propietario especifico |

## Lazy Loading

Todas las rutas de dominio utilizan lazy loading:

```typescript
{
  path: 'appointments',
  loadChildren: () => import('@domains/appointments/routes')
}
```
