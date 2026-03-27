# Arquitectura del Proyecto Web-Transport

## Resumen Ejecutivo

**Web-Transport** es una aplicacion Angular 20 enterprise-grade para gestion integral de servicios de transporte y mantenimiento vehicular.

## Stack Tecnologico

| Tecnologia | Version | Proposito |
|------------|---------|-----------|
| Angular | 20.3.14 | Framework principal |
| TypeScript | 5.8.3 | Lenguaje de programacion |
| RxJS | 7.8.2 | Programacion reactiva |
| Tailwind CSS | 4.x | Estilos utilitarios |
| Leaflet | - | Mapas y geolocalizacion |
| Three.js | - | Visualizacion 3D |

## Estructura de Carpetas

```
src/app/
├── core/           # Servicios centrales, guards, interceptors
├── domains/        # 16 modulos de dominio
├── shared/         # 21 componentes compartidos
├── infrastructure/ # Adaptadores y utilidades
├── app.ts          # Componente raiz
├── app.config.ts   # Configuracion
└── app.routes.ts   # Rutas (702 lineas)
```

## Patrones Arquitectonicos

1. **Domain-Driven Design** - Organizacion por dominios de negocio
2. **Standalone Components** - Sin modulos tradicionales
3. **Zoneless Change Detection** - Angular 20
4. **Signals** - Estado reactivo
5. **Guard-Based Auth** - Proteccion por roles

## Roles de Usuario

- `system_admin` - Administrador del sistema
- `taller_admin` - Administrador de taller
- `vehicle_owner` - Propietario de vehiculo
- `mechanic` - Mecanico

## Documentacion Relacionada

- [DOMAINS.md](./DOMAINS.md)
- [CORE-SERVICES.md](./CORE-SERVICES.md)
- [SHARED-COMPONENTS.md](./SHARED-COMPONENTS.md)
- [ROUTES.md](./ROUTES.md)
- [SECURITY.md](./SECURITY.md)
