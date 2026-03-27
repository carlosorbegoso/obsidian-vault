# Finace Store

PWA para comerciantes y vendedores ambulantes. Digitaliza el registro diario de ventas, gastos y perdidas usando fotos de libretas/recibos y genera reportes financieros automaticos.

## Stack

- **Framework**: Angular 21 (standalone components, zoneless change detection, OnPush)
- **Styling**: Tailwind CSS 4 + DaisyUI 5
- **Charts**: ECharts 6 via ngx-echarts 21 (lazy-loaded)
- **Database**: Dexie.js (IndexedDB) — sin backend por ahora
- **Package Manager**: Bun
- **Testing**: Vitest
- **Linting**: ESLint + Angular ESLint + SonarJS + domain boundaries

## Commands

```bash
bun start          # Dev server
bun run build      # Build produccion
bun run lint       # ESLint
bun run lint:fix   # ESLint autofix
bun run test       # Tests
bun run quality    # Lint + Knip
```

## Architecture

```
src/app/
├── core/          # Singletons: database, guards, providers, services, interceptors
├── shared/        # Reutilizable: components, facades, layouts, pipes, utils, constants
├── domains/       # Features aislados (dashboard, records, capture, reports, settings)
└── infrastructure/ # Tipos y utilidades de bajo nivel
```

### Domain Boundaries

Cada dominio solo puede importar de `@core/`, `@shared/`, `@app/`, y de si mismo. Nunca entre dominios. Esto se enforce via ESLint (`no-restricted-imports`).

### Path Aliases

- `@app/*` → `src/app/*`
- `@core/*` → `src/app/core/*`
- `@shared/*` → `src/app/shared/*`
- `@domains/*` → `src/app/domains/*`
- `@env/*` → `src/environments/*`

## Conventions

- **Components**: standalone, OnPush, siempre HTML y TS en archivos separados (templateUrl)
- **DI**: Usar `inject()`, nunca constructor injection
- **State**: Angular signals (`signal()`, `computed()`, `input()`, `output()`)
- **Types**: `type` imports, no `any` (enforced por ESLint)
- **Naming**: kebab-case para archivos, PascalCase para clases sin sufijo Component
- **Facades**: Patron facade para abstraer data layer (Dexie → futuro HTTP API)
- **Models**: Suffix `.model.ts` con barrel exports via `index.ts`
- **Routes**: Suffix `.routes.ts`, lazy-loaded via `loadComponent`/`loadChildren`

## Skills (skills.sh)

The following skills.sh guides are installed in `.claude/skills/`:
- `frontend-design.md` — Production-grade frontend design: typography, color, motion, spatial composition
- `typescript-advanced-types.md` — Generics, conditional types, mapped types, template literals, utility types
- `design-system-patterns.md` — Design tokens, theming infrastructure, component architecture patterns

### Angular Component Convention
- HTML templates and TypeScript must always be in separate files
- Use `templateUrl: './component.html'` — never inline `template: \`...\``
- Each component has: `component.ts` + `component.html` (+ optional `component.css`)

## Data Layer

- IndexedDB via Dexie.js (`FinaceDatabase` en `@core/database/`)
- Tables: `transactions`, `photos`, `businessProfile`
- Preferencias en localStorage via `PreferencesService`
- Arquitectura lista para swap a HTTP API via facade pattern

## OCR/AI (futuro)

- Interfaz abstracta: `OcrAdapter` con `OCR_ADAPTER` InjectionToken
- MVP usa `MockOcrAdapter` (no-op)
- Swap futuro: Tesseract.js (client-side) o API externa
