# Servicios Centrales (Core)

## Ubicacion
`src/app/core/services/`

## Servicios Principales

### AuthService (`auth.ts`)
Autenticacion y gestion de sesiones.

```typescript
// Metodos principales
login(credentials: LoginRequest): Observable<AuthTokens>
register(data: RegisterRequest): Observable<User>
logout(): void
refreshToken(): Observable<AuthTokens>
getCurrentUser(): User | null
isAuthenticated(): boolean
hasRole(role: RoleEnum): boolean
```

### UserService (`user.ts`)
CRUD de usuarios.

```typescript
getUsers(): Observable<User[]>
getUserById(id: string): Observable<User>
createUser(user: CreateUserRequest): Observable<User>
updateUser(id: string, user: UpdateUserRequest): Observable<User>
deleteUser(id: string): Observable<void>
```

### AppointmentService (`appointment.ts`)
Gestion de citas.

```typescript
getAppointments(filters?: AppointmentFilters): Observable<Appointment[]>
getAppointmentById(id: string): Observable<Appointment>
createAppointment(data: CreateAppointmentRequest): Observable<Appointment>
updateAppointment(id: string, data: UpdateAppointmentRequest): Observable<Appointment>
cancelAppointment(id: string): Observable<void>
rescheduleAppointment(id: string, newDate: Date): Observable<Appointment>
```

### TallerAvailabilityService (`taller-availability.ts`)
Disponibilidad de talleres.

```typescript
getAvailability(tallerId: string, date: Date): Observable<TimeSlot[]>
checkAvailability(tallerId: string, dateTime: Date): Observable<boolean>
```

### MechanicAssignmentService (`mechanic-assignment.ts`)
Asignacion de mecanicos a tareas.

```typescript
assignMechanic(appointmentId: string, mechanicId: string): Observable<void>
unassignMechanic(appointmentId: string): Observable<void>
getMechanicWorkload(mechanicId: string): Observable<Workload>
```

### NotificationService (`notification.ts`)
Sistema de notificaciones.

```typescript
getNotifications(): Observable<Notification[]>
markAsRead(id: string): Observable<void>
markAllAsRead(): Observable<void>
getUnreadCount(): Observable<number>
```

### RolePermissionService (`role-permission.ts`)
Gestion de roles y permisos.

```typescript
getRoles(): Observable<Role[]>
getPermissions(): Observable<Permission[]>
assignRole(userId: string, roleId: string): Observable<void>
checkPermission(userId: string, permission: string): Observable<boolean>
```

### PreferencesService (`preferences.ts`)
Preferencias de usuario.

```typescript
getPreferences(): Observable<UserPreferences>
updatePreferences(prefs: Partial<UserPreferences>): Observable<UserPreferences>
```

### ReportsService (`reports.ts`)
Generacion de reportes.

```typescript
generateReport(type: ReportType, params: ReportParams): Observable<Report>
exportReport(reportId: string, format: 'pdf' | 'excel'): Observable<Blob>
```

### CashRegisterService (`cash-register.ts`)
Cuadre de caja.

```typescript
openRegister(tallerId: string, initialAmount: number): Observable<CashRegister>
closeRegister(registerId: string, finalAmount: number): Observable<CashRegisterClose>
addTransaction(registerId: string, transaction: Transaction): Observable<void>
```

### CalendarService (`calendar.ts`)
Gestion de calendario.

```typescript
getEvents(start: Date, end: Date): Observable<CalendarEvent[]>
createEvent(event: CreateEventRequest): Observable<CalendarEvent>
```

### InventoryService (`inventory.ts`)
Gestion de inventario.

```typescript
getInventory(tallerId: string): Observable<InventoryItem[]>
updateStock(itemId: string, quantity: number): Observable<InventoryItem>
```

### ProductionService (`production.ts`)
Gestion de produccion.

```typescript
getProductionStatus(): Observable<ProductionStatus>
updateProductionStatus(id: string, status: string): Observable<void>
```

### TravelService (`travel.ts`)
Gestion de viajes.

```typescript
getTravel(id: string): Observable<Travel>
createTravel(data: CreateTravelRequest): Observable<Travel>
```

### AnalyticsService (`analytics.ts`)
Analiticas y metricas.

```typescript
trackEvent(event: AnalyticsEvent): void
getMetrics(params: MetricsParams): Observable<Metrics>
```

### MaintenanceCatalogService (`maintenance-catalog.ts`)
Catalogo de mantenimiento.

```typescript
getCatalog(): Observable<MaintenanceItem[]>
getItemById(id: string): Observable<MaintenanceItem>
```

### TallerEquipmentService (`taller-equipment.ts`)
Equipos del taller.

```typescript
getEquipment(tallerId: string): Observable<Equipment[]>
updateEquipmentStatus(id: string, status: EquipmentStatus): Observable<void>
```

### WorkPerformedService (`work-performed.ts`)
Trabajo realizado.

```typescript
logWork(appointmentId: string, work: WorkLog): Observable<void>
getWorkHistory(appointmentId: string): Observable<WorkLog[]>
```

### PartsReplacedService (`parts-replaced.ts`)
Piezas reemplazadas.

```typescript
logPartReplacement(appointmentId: string, part: PartReplacement): Observable<void>
getReplacedParts(appointmentId: string): Observable<PartReplacement[]>
```

### ProductService (`product.ts`)
Gestion de productos.

```typescript
getProducts(): Observable<Product[]>
getProductById(id: string): Observable<Product>
```

### PageContextService (`page-context.ts`)
Contexto de pagina actual.

```typescript
setContext(context: PageContext): void
getContext(): PageContext
```

## Modelos del Core

### auth.model.ts
```typescript
interface LoginRequest {
  email: string;
  password: string;
}

interface RegisterRequest {
  email: string;
  password: string;
  firstName: string;
  lastName: string;
  profileType: ProfileType;
}

interface AuthTokens {
  accessToken: string;
  refreshToken: string;
  expiresIn: number;
}

type ProfileType = 'system_admin' | 'taller_admin' | 'vehicle_owner' | 'mechanic';
type RoleEnum = 'MECHANIC' | 'OWNER' | 'TALLER_ADMIN' | 'SYSTEM_ADMIN';
type UserStatusEnum = 'ACTIVE' | 'INACTIVE' | 'SUSPENDED' | 'PENDING_VERIFICATION';
```

### user.model.ts
```typescript
interface User {
  id: string;
  email: string;
  firstName: string;
  lastName: string;
  profileType: ProfileType;
  status: UserStatusEnum;
  createdAt: Date;
  updatedAt: Date;
}
```

### appointment.model.ts
```typescript
interface Appointment {
  id: string;
  vehicleId: string;
  tallerId: string;
  mechanicId?: string;
  status: AppointmentStatus;
  scheduledDate: Date;
  services: string[];
  notes?: string;
}

type AppointmentStatus = 'PENDING' | 'CONFIRMED' | 'IN_PROGRESS' | 'COMPLETED' | 'CANCELLED';
```
