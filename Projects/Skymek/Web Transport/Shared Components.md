# Componentes Compartidos (Shared)

## Ubicacion
`src/app/shared/`

## Servicios Compartidos

### ModalService (`services/modal.ts`)
Servicio para dialogos modales globales.

```typescript
confirm(options: ConfirmOptions): Observable<boolean>
input(options: InputOptions): Observable<string | null>
alert(options: AlertOptions): Observable<void>
```

### ToastService (`services/toast.ts`)
Notificaciones emergentes.

```typescript
success(message: string, duration?: number): void
error(message: string, duration?: number): void
warning(message: string, duration?: number): void
info(message: string, duration?: number): void
```

### PageTitleService (`services/page-title.ts`)
Gestion de titulos de pagina.

```typescript
setTitle(title: string): void
getTitle(): string
```

### AppStateService (`services/app-state.ts`)
Estado global de la aplicacion.

```typescript
getState<T>(key: string): T
setState<T>(key: string, value: T): void
```

### CatalogService (`services/catalog.ts`)
Catalogos comunes.

```typescript
getCatalog(type: CatalogType): Observable<CatalogItem[]>
```

## Componentes

### alert
Componente de alertas.

```html
<app-alert type="success" message="Operacion exitosa"></app-alert>
<app-alert type="error" message="Error en la operacion"></app-alert>
<app-alert type="warning" message="Advertencia"></app-alert>
<app-alert type="info" message="Informacion"></app-alert>
```

### app-update
Notificaciones de actualizacion de la aplicacion.

```html
<app-update></app-update>
```

### charts
Componentes de graficos.

- `mini-bar-chart` - Grafico de barras mini

```html
<app-mini-bar-chart [data]="chartData"></app-mini-bar-chart>
```

### empty-state
Estado vacio para listas sin datos.

```html
<app-empty-state
  title="Sin resultados"
  message="No hay datos para mostrar"
  icon="fa-inbox">
</app-empty-state>
```

### global-modal
Modal global reutilizable.

```html
<app-global-modal></app-global-modal>
```

### maintenance
Componentes relacionados con mantenimiento.

### page-header
Encabezado de pagina con titulo y acciones.

```html
<app-page-header
  title="Mis Vehiculos"
  [showBackButton]="true">
  <button>Accion</button>
</app-page-header>
```

### paginator
Componente de paginacion.

```html
<app-paginator
  [totalItems]="100"
  [pageSize]="10"
  [currentPage]="1"
  (pageChange)="onPageChange($event)">
</app-paginator>
```

### progress-steps
Indicador de pasos de progreso.

```html
<app-progress-steps
  [steps]="['Paso 1', 'Paso 2', 'Paso 3']"
  [currentStep]="1">
</app-progress-steps>
```

### refresh-button
Boton de actualizacion.

```html
<app-refresh-button (refresh)="onRefresh()"></app-refresh-button>
```

### review-form
Formulario de resena/calificacion.

```html
<app-review-form
  (submit)="onReviewSubmit($event)">
</app-review-form>
```

### search-input
Input de busqueda con debounce.

```html
<app-search-input
  placeholder="Buscar..."
  (search)="onSearch($event)">
</app-search-input>
```

### skeleton
Skeleton loaders para carga.

```html
<app-skeleton type="card"></app-skeleton>
<app-skeleton type="list"></app-skeleton>
<app-skeleton type="text"></app-skeleton>
```

### spinner
Spinner de carga.

```html
<app-spinner size="small"></app-spinner>
<app-spinner size="medium"></app-spinner>
<app-spinner size="large"></app-spinner>
```

### star-rating
Calificacion por estrellas.

```html
<app-star-rating
  [rating]="4.5"
  [readonly]="false"
  (ratingChange)="onRatingChange($event)">
</app-star-rating>
```

### stat-card
Tarjeta de estadisticas.

```html
<app-stat-card
  title="Total Vehiculos"
  [value]="150"
  icon="fa-car"
  trend="up"
  [trendValue]="12">
</app-stat-card>
```

### status-badge
Badge de estado.

```html
<app-status-badge status="active">Activo</app-status-badge>
<app-status-badge status="pending">Pendiente</app-status-badge>
<app-status-badge status="completed">Completado</app-status-badge>
```

### toast-container
Contenedor de toasts.

```html
<app-toast-container></app-toast-container>
```

### unauthorized
Pagina de acceso no autorizado.

```html
<app-unauthorized></app-unauthorized>
```

## Layouts

### MainLayout (`layouts/main-layout.ts`)
Layout principal con sidebar y header.

```html
<app-main-layout>
  <router-outlet></router-outlet>
</app-main-layout>
```

Caracteristicas:
- Sidebar colapsible
- Header con navegacion
- Area de contenido principal
- Responsive (desktop/mobile)

## Navegacion

### Header (`navigation/components/header/`)
Encabezado principal de la aplicacion.

### Sidebar (`navigation/components/sidebar/`)
Sidebar de navegacion lateral.

### SidebarService (`navigation/service/sidebar.ts`)
Control del estado del sidebar.

```typescript
toggle(): void
open(): void
close(): void
isOpen(): boolean
```

## Directivas

### PageContextDirective (`directives/page-context.directive.ts`)
Directiva para establecer contexto de pagina.

```html
<div appPageContext="vehicles">
  <!-- contenido -->
</div>
```

## Utilidades

### resource.utils.ts
Utilidades para recursos HTTP.

### appointment-mappers.ts
Mappers para transformar datos de citas.

### mechanic-mappers.ts
Mappers para transformar datos de mecanicos.
