# Seguridad y Autenticacion

## Mecanismo de Autenticacion

### JWT (JSON Web Tokens)
La aplicacion utiliza autenticacion basada en JWT con tokens de acceso y refresco.

```typescript
interface AuthTokens {
  accessToken: string;   // Token de corta duracion
  refreshToken: string;  // Token de larga duracion
  expiresIn: number;     // Tiempo de expiracion en segundos
}
```

### Flujo de Autenticacion

```
1. Usuario ingresa credenciales
2. Backend valida y retorna tokens
3. Frontend almacena tokens en localStorage
4. Interceptor agrega token a cada request
5. Token se refresca automaticamente antes de expirar
```

## Interceptor de Autenticacion

Ubicacion: `src/app/core/interceptors/auth.interceptor.ts`

### Funcionalidades

1. **Agregar Authorization Header**
```typescript
request = request.clone({
  setHeaders: {
    Authorization: `Bearer ${accessToken}`
  }
});
```

2. **Refresh Automatico de Token**
- Detecta respuesta 401 (Unauthorized)
- Intenta refrescar el token automaticamente
- Reintenta la peticion original con nuevo token

3. **Manejo de Errores**
- 401: Intenta refresh, si falla redirige a login
- 403: Redirige a pagina de acceso denegado

### Rutas Publicas (sin token)
```typescript
const publicUrls = [
  '/api/auth/login',
  '/api/auth/register',
  '/api/auth/refresh'
];
```

## Sistema de Roles

### Tipos de Perfil
```typescript
type ProfileType =
  | 'system_admin'    // Administrador del sistema
  | 'taller_admin'    // Administrador de taller
  | 'vehicle_owner'   // Propietario de vehiculo
  | 'mechanic';       // Mecanico
```

### Enum de Roles
```typescript
enum RoleEnum {
  MECHANIC = 'MECHANIC',
  OWNER = 'OWNER',
  TALLER_ADMIN = 'TALLER_ADMIN',
  SYSTEM_ADMIN = 'SYSTEM_ADMIN'
}
```

### Estados de Usuario
```typescript
enum UserStatusEnum {
  ACTIVE = 'ACTIVE',
  INACTIVE = 'INACTIVE',
  SUSPENDED = 'SUSPENDED',
  PENDING_VERIFICATION = 'PENDING_VERIFICATION'
}
```

## Guards de Ruta

Ubicacion: `src/app/core/guards/auth.ts`

### authGuard
Verifica que el usuario este autenticado.

```typescript
export const authGuard: CanActivateFn = (route, state) => {
  const authService = inject(AuthService);
  const router = inject(Router);

  if (authService.isAuthenticated()) {
    return true;
  }

  return router.createUrlTree(['/login'], {
    queryParams: { returnUrl: state.url }
  });
};
```

### guestGuard
Redirige usuarios autenticados (para login/register).

```typescript
export const guestGuard: CanActivateFn = () => {
  const authService = inject(AuthService);
  const router = inject(Router);

  if (!authService.isAuthenticated()) {
    return true;
  }

  return router.createUrlTree(['/app/dashboard']);
};
```

### roleGuard
Verifica rol especifico del usuario.

```typescript
export const roleGuard = (allowedRoles: RoleEnum[]): CanActivateFn => {
  return () => {
    const authService = inject(AuthService);
    const router = inject(Router);
    const user = authService.getCurrentUser();

    if (user && allowedRoles.includes(user.role)) {
      return true;
    }

    return router.createUrlTree(['/app/unauthorized']);
  };
};
```

### adminGuard
Permite acceso a administradores de sistema y taller.

```typescript
export const adminGuard: CanActivateFn = () => {
  return roleGuard([RoleEnum.SYSTEM_ADMIN, RoleEnum.TALLER_ADMIN])();
};
```

### systemAdminGuard
Solo permite acceso a administradores del sistema.

```typescript
export const systemAdminGuard: CanActivateFn = () => {
  return roleGuard([RoleEnum.SYSTEM_ADMIN])();
};
```

### vehicleOwnerGuard
Solo permite acceso a propietarios de vehiculos.

```typescript
export const vehicleOwnerGuard: CanActivateFn = () => {
  return roleGuard([RoleEnum.OWNER])();
};
```

### mechanicGuard
Solo permite acceso a mecanicos.

```typescript
export const mechanicGuard: CanActivateFn = () => {
  return roleGuard([RoleEnum.MECHANIC])();
};
```

### tallerAccessGuard
Verifica acceso a un taller especifico.

```typescript
export const tallerAccessGuard: CanActivateFn = (route) => {
  const authService = inject(AuthService);
  const tallerId = route.params['tallerId'];

  // Verifica que el usuario tenga acceso al taller
  return authService.hasAccessToTaller(tallerId);
};
```

### ownerAccessGuard
Verifica acceso a un propietario especifico.

```typescript
export const ownerAccessGuard: CanActivateFn = (route) => {
  const authService = inject(AuthService);
  const ownerId = route.params['ownerId'];

  // Verifica que el usuario tenga acceso al propietario
  return authService.hasAccessToOwner(ownerId);
};
```

## Almacenamiento de Tokens

### LocalStorage
```typescript
const TOKEN_KEY = 'access_token';
const REFRESH_TOKEN_KEY = 'refresh_token';

// Guardar tokens
localStorage.setItem(TOKEN_KEY, tokens.accessToken);
localStorage.setItem(REFRESH_TOKEN_KEY, tokens.refreshToken);

// Obtener tokens
const accessToken = localStorage.getItem(TOKEN_KEY);
const refreshToken = localStorage.getItem(REFRESH_TOKEN_KEY);

// Limpiar tokens (logout)
localStorage.removeItem(TOKEN_KEY);
localStorage.removeItem(REFRESH_TOKEN_KEY);
```

## Auto-Refresh de Token

El token se refresca automaticamente 1 minuto antes de expirar:

```typescript
private scheduleTokenRefresh(expiresIn: number): void {
  const refreshTime = (expiresIn - 60) * 1000; // 1 minuto antes

  setTimeout(() => {
    this.refreshToken().subscribe();
  }, refreshTime);
}
```

## Matriz de Permisos por Rol

| Funcionalidad | System Admin | Taller Admin | Owner | Mechanic |
|---------------|:------------:|:------------:|:-----:|:--------:|
| Gestionar usuarios | ✓ | - | - | - |
| Gestionar talleres | ✓ | - | - | - |
| Editar su taller | ✓ | ✓ | - | - |
| Ver todos los vehiculos | ✓ | ✓ | - | - |
| Ver sus vehiculos | ✓ | ✓ | ✓ | - |
| Crear citas | ✓ | ✓ | ✓ | - |
| Gestionar citas | ✓ | ✓ | - | ✓ |
| Ver tareas (Kanban) | - | - | - | ✓ |
| Cuadre de caja | ✓ | ✓ | - | - |
| Reportes | ✓ | ✓ | - | - |
| Roles y permisos | ✓ | - | - | - |

## Buenas Practicas Implementadas

1. **Tokens de corta duracion** - Access tokens expiran rapido
2. **Refresh tokens seguros** - Solo se usan para obtener nuevos access tokens
3. **Guards funcionales** - Proteccion declarativa de rutas
4. **Interceptor centralizado** - Logica de autenticacion en un solo lugar
5. **Manejo de errores HTTP** - Respuestas apropiadas a 401/403
6. **Roles granulares** - Control de acceso detallado por funcionalidad
