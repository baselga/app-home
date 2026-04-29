---
stepsCompleted:
  - "step-01-init"
  - "step-02-context"
  - "step-03-starter"
  - "step-04-decisions"
  - "step-05-patterns"
  - "step-06-structure"
  - "step-07-validation"
  - "step-08-complete"
inputDocuments:
  - "_bmad-output/planning-artifacts/prd.md"
workflowType: "architecture"
project_name: "home-app-new"
user_name: "Dani"
date: "2026-04-29"
lastStep: 8
status: "complete"
completedAt: "2026-04-29"
---

# Architecture Decision Document

_Este documento se construye de forma colaborativa a través del descubrimiento paso a paso. Las secciones se van añadiendo a medida que tomamos decisiones arquitectónicas juntos._

## Project Context Analysis

### Requirements Overview

**Functional Requirements:**
35 FRs en 7 áreas: Autenticación (FR1-5), Inventario (FR6-11), Consumibles (FR12-16), Lista de Compra (FR17-20), Búsqueda (FR21-24), Sincronización/Colaboración (FR25-28), Estado del Sistema y Navegación (FR29-35).

El core del MVP es el ciclo: marcar faltante → ver en lista de compra → marcar comprado → actualizar stock. Cada FR apoya este flujo o lo hace confiable y accesible.

**Non-Functional Requirements:**
- Performance: LCP ≤ 2s (4G), búsqueda ≤ 1s, acciones ≤ 500ms percibidos — impulsa SSR y API optimizada.
- Seguridad: HTTPS/TLS 1.2+, hash de credenciales, sesiones expirables, aislamiento estricto por hogar — impulsa middleware de autorización en cada capa.
- Accessibilidad: WCAG 2.1 AA completo, compatibilidad con lectores de pantalla — impulsa marcado semántico y gestión de foco sistemática.
- Fiabilidad: disponibilidad ≥ 99%, sin pérdida de datos, errores siempre explícitos.

**Scale & Complexity:**
- Dominio primario: full-stack web (MPA + backend API + cloud DB)
- Complejidad: baja-media (alcance acotado, sin tiempo real, sin integraciones externas en MVP)
- Componentes arquitectónicos estimados: ~6-8 (auth, inventario, consumibles, lista compra, búsqueda, sync/conflict, estado sistema, shell/navegación)

### Technical Constraints & Dependencies

- MPA obligatoria (no SPA, no PWA, no service workers en MVP)
- Sin WebSockets ni SSE en MVP — sincronización por recarga
- Despliegue cloud (plataforma no definida aún)
- 1-2 personas full-stack — arquitectura debe ser mantenible por equipo pequeño
- Diseño extensible: Fase 2 añade biblioteca, alertas, fotos; Fase 3 añade API pública

### Cross-Cutting Concerns Identified

1. **Auth/Authz — dos capas independientes:**
   - Capa de sesión: middleware que valida token/sesión antes de cada ruta
   - Capa de datos: filtro `household_id` en cada consulta ORM/repo
   - Decisión pendiente: sesiones stateful (server-side store) vs JWT stateless (implica blacklist o tokens de corta vida para cumplir NFR11)

2. **Aislamiento multi-hogar** — filtro `household_id` en CADA query; nunca confiar en parámetros del cliente para definir el hogar

3. **Gestión de conflictos (versionado optimista):**
   - Cada entidad mutable lleva campo `updated_at` o ETag
   - Backend rechaza escrituras obsoletas con HTTP 409
   - Cliente muestra conflicto y solicita resolución explícita (FR26-27)
   - Este es un contrato de datos, no un feature de UI

4. **Estrategia de indexación** — decisión arquitectónica Fase 1 (no optimización posterior):
   - Índices compuestos por `(household_id, nombre)`, `(household_id, categoría)`, `(household_id, estado)`
   - Crítico para búsqueda ≤ 1s (NFR2) a medida que crece el inventario

5. **Estado de sincronización** — indicador visual global; retry manual siempre disponible
6. **Accessibilidad** — marcado semántico, ARIA, gestión de foco en todos los componentes
7. **Error handling** — nunca estado ambiguo; cada operación fallida tiene mensaje accionable

## Starter Template Evaluation

### Primary Technology Domain

Full-stack web (MPA server-rendered + API integrada + cloud DB) basado en análisis de requisitos. Perfil del equipo: 1 persona full-stack con mayor experiencia en frontend — el stack debe minimizar complejidad operativa en backend y despliegue.

### Starter Options Considered

| Opción | Pros | Contras |
|---|---|---|
| Next.js + Supabase (seleccionado) | Experiencia previa del equipo, RLS nativo, Auth SSR, Vercel nativo | Navegación client-side por defecto (aceptada explícitamente) |
| SvelteKit + Supabase | MPA pura, menor bundle | Curva de aprendizaje, menor experiencia del equipo |
| Next.js + PostgreSQL propio | Control total del esquema | Operaciones de BD propias, más complejidad para equipo de 1 |

### Clarificación Arquitectónica: MPA en Contexto Next.js

El PRD especifica MPA. Decisión acordada: Next.js App Router con Server Components satisface la intención del requisito — renderizado server-side, sin estado global en cliente, sin service workers, sin PWA. La navegación client-side vía `<Link>` se acepta explícitamente como mejora de performance percibida (NFR3/NFR4), no como contradicción del requisito.

### Selected Starter: Next.js + Supabase (with-supabase template)

**Initialization Command:**

```bash
npx create-next-app -e with-supabase home-app-new
```

**Rationale for Selection:**
- Experiencia previa del equipo con Next.js — reduce fricción de arranque
- Supabase RLS implementa aislamiento multi-hogar (NFR9-10) a nivel de BD
- Auth basada en cookies SSR — sesiones server-side expirables (NFR8/NFR11), resuelve la decisión pendiente stateful vs JWT a favor de stateful sin infraestructura adicional
- PostgreSQL con índices compuestos nativos — cumple búsqueda ≤ 1s (NFR2)
- Supabase Storage disponible para fotos en Fase 2 sin cambio arquitectónico
- Vercel + Next.js: integración nativa, previews automáticos, edge middleware

**Architectural Decisions Provided by Starter:**

**Language & Runtime:**
TypeScript estricto. Next.js App Router (React 19+). Node.js en Vercel Functions.

**Auth & Session:**
Supabase Auth con cookies SSR vía `@supabase/ssr`. Middleware Next.js pre-configurado. Clientes separados `lib/supabase/server.ts` y `lib/supabase/client.ts` — error común de hidratación ya resuelto. Flujo PKCE completo en `app/auth/callback/route.ts`. Sesiones server-side: invalidación en logout (NFR11), expiración por inactividad (NFR8).

**Styling Solution:**
Tailwind CSS — utility-first, mobile-first por defecto. shadcn/ui — componentes accesibles (WCAG 2.1 AA) basados en Radix UI, copiados al proyecto (sin lock-in de versión de librería). Touch targets configurables directamente en componente.

**Build Tooling:**
Next.js build con Turbopack (dev). Code splitting automático por ruta. Optimización de imágenes. Favorece LCP ≤ 2s (NFR1) y TTI ≤ 3s (NFR4).

**Database:**
Supabase (PostgreSQL). RLS habilitado por defecto en el proyecto — aislamiento en capa de BD.

**Code Organization (estructura inicial acordada):**

```
app/
  (auth)/           # rutas públicas — login, signup
  (app)/            # rutas protegidas — layout con household guard
    [householdId]/
      inventory/
      consumables/
      shopping/
      search/
lib/
  supabase/         # server.ts + client.ts (del starter)
  db/               # queries de dominio por área
    inventory.ts
    shopping.ts
  types/
    supabase.ts     # generado: supabase gen types typescript --local
    domain.ts       # tipos derivados del schema
supabase/
  migrations/       # schema versionado
```

### Gaps Críticos — Responsabilidad Explícita del Equipo

El starter NO cubre los siguientes puntos. Deben implementarse antes de desarrollar features:

**1. RLS Policies (bloqueante de seguridad)**
El starter habilita auth, no Row Level Security. Cada tabla del dominio requiere:
- `ALTER TABLE ... ENABLE ROW LEVEL SECURITY`
- Políticas explícitas que filtran por `household_id` vía `auth.uid()`
- Test SQL que verifica: usuario de hogar A obtiene 0 filas de hogar B

**2. Schema de dominio + optimistic locking**
Migración `001_core_schema.sql`: tablas `households`, `household_members`, `inventory_items`, `consumables`, `shopping_list_items`. Campo `updated_at` con trigger en cada tabla mutable (requerido para HTTP 409 en conflictos). Índices compuestos `(household_id, nombre)`, `(household_id, categoría)` para búsqueda ≤ 1s.

**3. Capa de data access**
Las queries de Supabase no deben dispersarse por la aplicación. Patrón acordado:
- Server Actions como único punto de escritura
- Cliente Supabase instanciado exclusivamente desde `server-only`
- Funciones de dominio en `lib/db/` — una por área funcional

**4. Middleware de household (segunda capa)**
El `middleware.ts` del starter valida sesión. Añadir validación de membresía de household: sesión válida + pertenencia al household del request. Nunca confiar en `household_id` del cliente.

**5. Error boundaries + manejo HTTP 409**
- `app/error.tsx` global
- `loading.tsx` por segmento de ruta principal
- Patrón de retry en Server Actions para conflictos de escritura concurrente

**6. Estrategia de caché y revalidación entre usuarios**
Decidir explícitamente: `revalidatePath` en cada Server Action que muta datos compartidos. Los cambios de un usuario deben ser visibles al otro en el siguiente page load (FR25).

### Secuencia de Implementación Inicial (pre-features)

1. `supabase init` + `supabase start` → Postgres + Auth + Studio local
2. `001_core_schema.sql` (tablas + `updated_at` triggers + seed: 1 hogar, 2 usuarios)
3. `002_rls_policies.sql` + test de aislamiento cross-household
4. `supabase gen types typescript --local > lib/types/supabase.ts`
5. Estructura de carpetas de dominio + `tsc --noEmit` sin errores

**Criterio de inicio de features:** Schema → RLS → Tipos → Features. En ese orden.

### UX Architecture Constraints (non-negotiable)

- Rutas principales: datos disponibles en Server Component, sin waterfalls cliente
- Touch targets de acciones frecuentes: mínimo 48×48px, en componente (no CSS global)
- Navegación principal: bottom navigation (accesible con pulgar), no hamburger menu
- Contraste mínimo 4.5:1 definido en design token de Tailwind
- Flujos críticos (añadir ítem, marcar completado, resolver conflicto): 100% navegables sin ratón
- Experiencia con conexión débil: sin PWA/SW en MVP — decisión explícita diferida a Fase 2

**Note:** Project initialization using the starter command should be the first implementation story.

## Core Architectural Decisions

### Decision Priority Analysis

**Critical Decisions (Block Implementation):**
1. Stack base: Next.js App Router + TypeScript + Supabase + Vercel.
2. Data model relacional con FKs y joins explicitos (sin denormalizacion prematura).
3. Prisma como capa ORM y migraciones (source of truth del modelo de aplicacion).
4. Seguridad RLS-first + middleware de sesion + validacion de household.
5. API interna basada en Server Actions (Route Handlers solo para integraciones externas).
6. Error envelope unificado con soporte explicito para conflictos `409`.
7. Frontend server-first: Server Components por defecto, Client Components minimos.
8. Entornos separados dev/preview/prod con secretos independientes.

**Important Decisions (Shape Architecture):**
1. Validacion server-side con Zod en Server Actions.
2. Documentacion OpenAPI acotada a endpoints externos/integraciones.
3. Rate limiting aplicado a endpoints publicos/sensibles, no global MVP.
4. Arquitectura de componentes por dominio (inventory, consumables, shopping, search).
5. Routing con route groups + segmento protegido por `householdId`.
6. Observabilidad con Vercel Analytics/Logs + Sentry.
7. Escalado gestionado por plataforma y optimizacion por indices/cache.

**Deferred Decisions (Post-MVP):**
1. Event-driven interno/microservicios.
2. API publica extensa.
3. Realtime/WebSockets.
4. Estrategias avanzadas de cache distribuida y colas complejas.
5. Hardening enterprise (WAF avanzado, SIEM, etc.) segun crecimiento.

### Data Architecture

- Modelado: relacional normalizado con foreign keys explicitas.
- Validacion: Zod en Server Actions como capa de entrada obligatoria.
- ORM/migraciones: Prisma (version validada: `7.8.0`) + Prisma Migrate.
- Regla de consistencia: Prisma define modelo y migraciones; RLS se mantiene explicitamente en SQL versionado.
- Caching inicial: revalidacion dirigida por mutacion (`revalidatePath`) y cache server-first.
- Indices criticos: compuestos por `household_id` + campos de busqueda/filtrado.

### Authentication & Security

- Auth provider: Supabase Auth (cookies SSR) con `@supabase/ssr` (version validada: `0.10.2`).
- SDK base: `@supabase/supabase-js` (version validada: `2.105.1`).
- Autorizacion: patron RLS-first + middleware de sesion en App Router.
- Aislamiento multi-hogar: enforcement dual.
  - Capa app: sesion y pertenencia household.
  - Capa DB: RLS por `auth.uid()` y `household_id`.
- API Security: escrituras via Server Actions + validacion fuerte + control de errores.
- Conflictos concurrentes: optimistic locking (`updated_at`/version) + `HTTP 409`.

### API & Communication Patterns

- Patron principal: Server Actions para operaciones internas de UI.
- Excepciones: Route Handlers para webhooks e integraciones externas.
- Documentacion: OpenAPI minima para endpoints externos.
- Errores: contrato unificado `{ code, message, details, retryable }`.
- Rate limiting: solo en endpoints publicos/sensibles en MVP.
- Comunicacion interna: monolito modular (sin bus/eventos en MVP).

### Frontend Architecture

- State management: server-first + estado local por pantalla (sin store global MVP).
- Component architecture: feature/domain-based + libreria UI compartida.
- Routing: route groups + layouts anidados + rutas protegidas por `householdId`.
- Rendimiento: Server Components por defecto, Client Components minimos.
- Bundle: lazy loading en componentes pesados, control de dependencias.
- Framework/runtime: Next.js `16.2.4`, Node >= `20.9` (alineado a docs actuales).

### Infrastructure & Deployment

- Hosting: Vercel (app/runtime) + Supabase gestionado (DB/Auth/Storage).
- CI/CD: GitHub + Preview Deployments por PR + produccion en `main`.
- Entornos: dev/preview/prod con secretos aislados y politicas separadas.
- Observabilidad: Vercel Analytics + Logs + Sentry.
- Escalado: managed-first; optimizacion por indices, cache y limites por fase.

### Decision Impact Analysis

**Implementation Sequence:**
1. Inicializar starter y estructura base.
2. Definir schema Prisma + migraciones iniciales.
3. Implementar RLS SQL y pruebas de aislamiento.
4. Configurar Server Actions + validacion Zod + error envelope.
5. Construir modulos MVP por dominio.
6. Integrar observabilidad y politicas de despliegue.
7. Ajustar performance sobre metricas reales (LCP, TTI, latencia acciones).

**Cross-Component Dependencies:**
- RLS depende del diseno de modelo de datos y claves de household.
- Server Actions dependen de contratos de validacion y error envelope.
- UX de conflictos depende de optimistic locking y estrategia de revalidacion.
- Performance percibida depende de decisiones conjuntas de routing, carga de datos y bundle.
- Seguridad operacional depende de separacion de entornos + CI/CD + monitoreo.

## Implementation Patterns & Consistency Rules

### Pattern Categories Defined

**Critical Conflict Points Identified:**
12 areas where AI agents could make different choices

### Naming Patterns

**Database Naming Conventions:**
- Tablas en snake_case plural: `households`, `inventory_items`, `shopping_list_items`.
- Columnas en snake_case: `household_id`, `created_at`, `updated_at`.
- Primary key: `id`.
- Foreign key: `<entity>_id`.
- Indices: `idx_<table>_<column_list>`.
- Constraints: `chk_<table>_<rule>`, `uq_<table>_<column_list>`, `fk_<table>_<ref_table>`.

**API Naming Conventions:**
- Route Handlers externos en kebab-case/plural: `/api/webhooks/supabase`, `/api/integrations`.
- Query params en snake_case: `household_id`, `updated_after`.
- Headers custom con prefijo `X-HomeApp-`.
- Versionado en path solo cuando exista API publica: `/api/v1/`.

**Code Naming Conventions:**
- Componentes React en PascalCase: `InventoryList.tsx`.
- Archivos de utilidades en kebab-case: `format-date.ts`.
- Funciones y variables en camelCase: `getInventoryItems`, `householdId`.
- Tipos e interfaces en PascalCase.
- Constantes en UPPER_SNAKE_CASE.

### Structure Patterns

**Project Organization:**
- Organizacion por dominio primero, por tipo despues.
- Rutas protegidas bajo `app/(app)/[householdId]/...`.
- Cada dominio mantiene trio: actions, queries, ui.
- Tests co-localizados para unidad y `tests/integration` para flujos cross-domain.

**File Structure Patterns:**
- `lib/db` para acceso a datos.
- `lib/validation` para esquemas Zod.
- `lib/errors` para contratos de error.
- `lib/logging` para logger y niveles.
- `supabase/migrations` para SQL versionado.
- `prisma/schema.prisma` como fuente del modelo de aplicacion.

### Format Patterns

**API Response Formats:**
- Route Handlers usan envelope:
  - success: `{ data, meta?, error: null }`
  - failure: `{ data: null, error: { code, message, details?, retryable }, meta? }`
- Server Actions devuelven objeto tipado de accion:
  - `{ ok: true, data, message? }`
  - `{ ok: false, error: { code, message, details?, retryable } }`

**Data Exchange Formats:**
- Fechas siempre ISO-8601 UTC.
- Booleanos reales `true/false`.
- IDs UUID string.
- Payload persistido en snake_case; mapping a camelCase solo en capa UI si aplica.
- Null explicito; no usar undefined en payload persistido.

### Communication Patterns

**Event System Patterns:**
- Sin bus de eventos en MVP.
- Eventos internos (si aparecen) en pasado y snake_case: `inventory_item_updated`.
- Payload minimo comun:
  - `{ event_name, occurred_at, household_id, actor_user_id, entity_id, version }`

**State Management Patterns:**
- Server-first: estado fuente en servidor.
- Cliente solo para estado efimero de UI.
- Mutaciones exclusivamente via Server Actions.
- Toda mutacion compartida debe ejecutar `revalidatePath` del segmento afectado.

### Process Patterns

**Error Handling Patterns:**
- Taxonomia minima:
  - `VALIDATION_ERROR` (400)
  - `UNAUTHORIZED` (401)
  - `FORBIDDEN` (403)
  - `NOT_FOUND` (404)
  - `CONFLICT` (409)
  - `RATE_LIMITED` (429)
  - `INTERNAL_ERROR` (500)
- Conflictos concurrentes siempre `CONFLICT` + `retryable` explicito.
- Mensajes al usuario sin detalles internos; detalles tecnicos solo en logs.

**Loading State Patterns:**
- `loading.tsx` por segmento principal.
- Skeletons consistentes por dominio.
- Botones de accion con estado `pending` bloqueante por submit.
- Mostrar estado de retry al superar el umbral UX definido.

### Enforcement Guidelines

**All AI Agents MUST:**
- Respetar naming conventions exactas.
- Implementar mutaciones solo via Server Actions.
- Mantener contrato de error unificado.
- Aplicar `revalidatePath` en mutaciones compartidas.
- No crear patrones nuevos fuera del documento sin ADR.

**Pattern Enforcement:**
- PR checklist obligatorio con seccion de patrones.
- Lint + typecheck + tests como gate minimo.
- Violaciones se registran como debt items con owner.
- Cambios de patron requieren ADR breve en `docs/architecture/adr`.

### Pattern Examples

**Good Examples:**
- DB: `inventory_items.household_id` con indice `idx_inventory_items_household_id`.
- Action error:
  - `{ ok: false, error: { code: "CONFLICT", message: "El item fue actualizado por otro usuario", retryable: true } }`
- Route Handler success:
  - `{ data: {...}, error: null, meta: { request_id } }`

**Anti-Patterns:**
- Mezclar camelCase y snake_case en columnas SQL.
- Escribir directo a DB desde Client Components.
- Retornar errores string sin `code`.
- Crear rutas fuera de `[householdId]` para datos compartidos.
- Revalidar todo el sitio en lugar de path/tag puntual.

## Project Structure & Boundaries

### Complete Project Directory Structure

```text
home-app-new/
├── README.md
├── package.json
├── pnpm-lock.yaml
├── next.config.ts
├── tsconfig.json
├── eslint.config.mjs
├── postcss.config.mjs
├── .env.example
├── .env.local            # local only, no commit
├── .gitignore
├── middleware.ts
├── components.json       # shadcn/ui
├── sentry.client.config.ts
├── sentry.server.config.ts
├── sentry.edge.config.ts
├── instrumentation.ts
│
├── .github/
│   └── workflows/
│       ├── ci.yml
│       └── quality-gates.yml
│
├── docs/
│   └── architecture/
│       ├── adr/
│       │   └── 0001-pattern-governance.md
│       ├── api/
│       │   └── external-openapi.yaml
│       └── decisions/
│           └── project-structure-boundaries.md
│
├── prisma/
│   ├── schema.prisma
│   ├── seed.ts
│   └── migrations/
│       ├── 0001_init/
│       │   └── migration.sql
│       └── 0002_household_rls/
│           └── migration.sql
│
├── supabase/
│   ├── config.toml
│   ├── seed.sql
│   ├── migrations/
│   │   ├── 0001_extensions.sql
│   │   ├── 0002_rls_policies.sql
│   │   └── 0003_indexes.sql
│   └── tests/
│       └── rls/
│           ├── household_isolation.sql
│           └── policy_regression.sql
│
├── public/
│   ├── icons/
│   ├── images/
│   └── fonts/
│
├── src/
│   ├── app/
│   │   ├── layout.tsx
│   │   ├── globals.css
│   │   ├── error.tsx
│   │   ├── not-found.tsx
│   │   ├── (auth)/
│   │   │   ├── login/
│   │   │   │   └── page.tsx
│   │   │   ├── signup/
│   │   │   │   └── page.tsx
│   │   │   └── callback/
│   │   │       └── route.ts
│   │   ├── (app)/
│   │   │   ├── [householdId]/
│   │   │   │   ├── layout.tsx
│   │   │   │   ├── loading.tsx
│   │   │   │   ├── page.tsx
│   │   │   │   ├── inventory/
│   │   │   │   │   ├── page.tsx
│   │   │   │   │   ├── loading.tsx
│   │   │   │   │   └── error.tsx
│   │   │   │   ├── consumables/
│   │   │   │   │   ├── page.tsx
│   │   │   │   │   ├── loading.tsx
│   │   │   │   │   └── error.tsx
│   │   │   │   ├── shopping/
│   │   │   │   │   ├── page.tsx
│   │   │   │   │   ├── loading.tsx
│   │   │   │   │   └── error.tsx
│   │   │   │   └── search/
│   │   │   │       ├── page.tsx
│   │   │   │       ├── loading.tsx
│   │   │   │       └── error.tsx
│   │   └── api/
│   │       ├── webhooks/
│   │       │   └── supabase/
│   │       │       └── route.ts
│   │       └── integrations/
│   │           └── health/
│   │               └── route.ts
│   │
│   ├── components/
│   │   ├── ui/                  # shadcn generated
│   │   ├── layout/
│   │   │   ├── bottom-nav.tsx
│   │   │   ├── app-header.tsx
│   │   │   └── sync-status-chip.tsx
│   │   ├── shared/
│   │   │   ├── error-banner.tsx
│   │   │   ├── loading-skeleton.tsx
│   │   │   └── empty-state.tsx
│   │   └── domains/
│   │       ├── inventory/
│   │       ├── consumables/
│   │       ├── shopping/
│   │       └── search/
│   │
│   ├── modules/
│   │   ├── inventory/
│   │   │   ├── actions/
│   │   │   │   ├── create-item.action.ts
│   │   │   │   ├── update-item.action.ts
│   │   │   │   └── delete-item.action.ts
│   │   │   ├── queries/
│   │   │   │   ├── list-items.query.ts
│   │   │   │   └── get-item.query.ts
│   │   │   ├── validation/
│   │   │   │   └── inventory.schema.ts
│   │   │   └── ui/
│   │   │       ├── inventory-list.tsx
│   │   │       └── inventory-form.tsx
│   │   │
│   │   ├── consumables/
│   │   │   ├── actions/
│   │   │   ├── queries/
│   │   │   ├── validation/
│   │   │   └── ui/
│   │   │
│   │   ├── shopping/
│   │   │   ├── actions/
│   │   │   ├── queries/
│   │   │   ├── validation/
│   │   │   └── ui/
│   │   │
│   │   └── search/
│   │       ├── actions/
│   │       ├── queries/
│   │       ├── validation/
│   │       └── ui/
│   │
│   ├── lib/
│   │   ├── supabase/
│   │   │   ├── server.ts
│   │   │   ├── client.ts
│   │   │   └── middleware.ts
│   │   ├── prisma/
│   │   │   └── client.ts
│   │   ├── db/
│   │   │   ├── household.repository.ts
│   │   │   ├── inventory.repository.ts
│   │   │   ├── consumables.repository.ts
│   │   │   ├── shopping.repository.ts
│   │   │   └── search.repository.ts
│   │   ├── auth/
│   │   │   ├── require-session.ts
│   │   │   └── require-household-membership.ts
│   │   ├── errors/
│   │   │   ├── app-error.ts
│   │   │   ├── error-codes.ts
│   │   │   └── to-error-envelope.ts
│   │   ├── logging/
│   │   │   ├── logger.ts
│   │   │   └── request-context.ts
│   │   ├── validation/
│   │   │   └── common.schema.ts
│   │   ├── revalidation/
│   │   │   └── paths.ts
│   │   └── utils/
│   │       ├── datetime.ts
│   │       └── ids.ts
│   │
│   ├── types/
│   │   ├── prisma.ts
│   │   ├── api.ts
│   │   ├── domain.ts
│   │   └── events.ts
│   │
│   └── styles/
│       ├── tokens.css
│       └── utilities.css
│
└── tests/
  ├── unit/
  │   ├── modules/
  │   ├── lib/
  │   └── components/
  ├── integration/
  │   ├── actions/
  │   ├── api/
  │   └── db/
  ├── e2e/
  │   ├── auth.spec.ts
  │   ├── inventory.spec.ts
  │   ├── shopping.spec.ts
  │   └── conflict-resolution.spec.ts
  ├── fixtures/
  │   ├── households.ts
  │   └── users.ts
  └── setup/
    ├── env.ts
    ├── db.ts
    └── test-server.ts
```

### Architectural Boundaries

**API Boundaries:**
- Server Actions: mutaciones internas UI.
- Route Handlers: solo webhooks/integraciones externas.
- Auth boundary en middleware + guards de household.

**Component Boundaries:**
- UI por dominio dentro de `modules/<domain>/ui`.
- Shared UI en `components/shared`.
- `components/ui` reservado para primitives de shadcn.

**Service Boundaries:**
- Acceso a datos solo via `lib/db` repositories.
- Regla: ningun componente UI consulta Prisma/Supabase directo.

**Data Boundaries:**
- Prisma como fuente del modelo.
- RLS SQL como enforcement de aislamiento.
- Revalidation centralizada en `lib/revalidation/paths.ts`.

### Requirements to Structure Mapping

**Feature/FR Mapping:**
- Autenticacion y acceso -> `src/app/(auth)`, `src/lib/auth`
- Inventario -> `src/modules/inventory`
- Consumibles -> `src/modules/consumables`
- Lista de compra -> `src/modules/shopping`
- Busqueda -> `src/modules/search`, `src/lib/db/search.repository.ts`
- Sincronizacion/colaboracion -> `src/lib/revalidation`, `src/lib/errors`, `tests/e2e/conflict-resolution.spec.ts`
- Estado del sistema/navegacion -> `src/components/layout`, `src/app/(app)/[householdId]/layout.tsx`

**Cross-Cutting Concerns:**
- Seguridad: `src/lib/auth` + `middleware.ts` + `supabase/migrations/0002_rls_policies.sql`
- Error handling: `src/lib/errors` + `src/app/error.tsx`
- Observabilidad: `src/lib/logging` + `sentry.*.config.ts`
- Accesibilidad y mobile-first: `src/components/layout` + `src/styles/tokens.css`

### Integration Points

**Internal Communication:**
- UI -> Server Actions -> `lib/db` repositories -> Prisma/Supabase.
- Errores normalizados por `to-error-envelope`.
- `revalidatePath` llamado en acciones de mutacion compartida.

**External Integrations:**
- Supabase Auth/DB/Storage.
- Webhooks externos en `src/app/api/webhooks`.
- Vercel runtime y observabilidad.

**Data Flow:**
- Request autenticado entra por middleware.
- Guard de household valida contexto.
- Query o action ejecuta acceso a datos.
- Resultado se serializa con formato estandar.
- UI renderiza estado/skeleton/error segun convencion.

### File Organization Patterns

**Configuration Files:**
- Root: package, tsconfig, eslint, next, sentry.
- Entorno: `.env.example` versionado, `.env.local` local.
- Infra: `.github/workflows` para CI y quality gates.

**Source Organization:**
- Dominio primero (`modules`).
- Shared concerns en `lib`.
- App Router como orquestador de rutas y layouts.

**Test Organization:**
- Unit por modulo.
- Integration para boundaries entre capas.
- E2E para journeys del PRD.

**Asset Organization:**
- `public` para estaticos.
- `styles` para tokens y utilidades globales.

### Development Workflow Integration

**Development Server Structure:**
- Next.js dev server con route groups y boundaries separados.
- Supabase local opcional para pruebas de politicas RLS.

**Build Process Structure:**
- Typecheck + lint + tests como gate de CI.
- Build de Next consume `src` y configuraciones root.

**Deployment Structure:**
- Preview por PR en Vercel.
- Produccion desde `main` con secretos por entorno.

## Architecture Validation Results

### Coherence Validation ✅

**Decision Compatibility:**
- El stack elegido es compatible extremo a extremo.
  - Next.js 16.2.4 + Node >= 20.9.
  - Supabase Auth/DB con `@supabase/supabase-js` 2.105.1 y `@supabase/ssr` 0.10.2.
  - Prisma 7.8.0 para modelo y migraciones de aplicacion.
- No hay contradiccion entre MPA objetivo y App Router: se mantiene server-first con navegacion client-side aceptada explicitamente.
- Seguridad y datos no colisionan: middleware + guards de household + RLS SQL.

**Pattern Consistency:**
- Naming, formatos, estructura y procesos son coherentes entre si.
- El contrato de error unificado soporta Server Actions y Route Handlers.
- La regla de `revalidatePath` en mutaciones compartidas alinea estado UI con colaboracion multiusuario.

**Structure Alignment:**
- La estructura fisica definida soporta todas las decisiones.
  - Modulos por dominio.
  - `lib` para concerns compartidos.
  - Boundaries explicitos entre UI, actions, repositorios y persistencia.

### Requirements Coverage Validation ✅

**Functional Requirements Coverage:**
- Todas las categorias FR del PRD tienen ubicacion arquitectonica explicita.
  - Autenticacion/acceso.
  - Inventario.
  - Consumibles.
  - Lista de compra.
  - Busqueda.
  - Sincronizacion y conflictos.
  - Navegacion/estado sistema.

**Non-Functional Requirements Coverage:**
- Performance: server-first, segment loading states, bundle control, indices de busqueda.
- Security: RLS-first, middleware, household guards, error taxonomy.
- Accessibility: constraints mobile-first y objetivos de interaccion incluidos.
- Reliability: patron de manejo de conflicto 409, reintentos y observabilidad.

### Implementation Readiness Validation ✅

**Decision Completeness:**
- Decisiones criticas documentadas con versiones y rationale operativo.
- Limites de API interna/externa definidos.
- Estrategia de deployment, entornos y observabilidad especificada.

**Structure Completeness:**
- Arbol completo de proyecto incluido.
- Boundaries API/component/service/data definidos.
- Mapeo requirements->estructura completado.

**Pattern Completeness:**
- Puntos de conflicto entre agentes identificados y cubiertos.
- Reglas concretas de enforcement y anti-patterns documentadas.
- Criterios de consistencia listos para PR checklist y quality gates.

### Gap Analysis Results

**Critical Gaps:**
- Ninguno bloqueante detectado.

**Important Gaps:**
- Agregar ADR especifico para politica de cache por modulo (inventory, shopping, search) antes de implementacion masiva.
- Explicitar politica de versionado OpenAPI cuando se habiliten integraciones externas en Fase 2.

**Nice-to-Have Gaps:**
- Plantillas base de test por dominio para acelerar onboarding de nuevos agentes.
- Guia breve de convenciones de logs por evento de negocio.

### Validation Issues Addressed

- Se resolvio la tension Prisma vs RLS: Prisma como source of truth del modelo de app, RLS mantenido explicitamente en SQL versionado y testeado.
- Se resolvio la tension MPA vs App Router: navegacion client-side aceptada como optimizacion compatible con objetivo server-first.
- Se consolido formato unico de error para evitar divergencias entre agentes.

### Architecture Completeness Checklist

**✅ Requirements Analysis**
- [x] Project context thoroughly analyzed
- [x] Scale and complexity assessed
- [x] Technical constraints identified
- [x] Cross-cutting concerns mapped

**✅ Architectural Decisions**
- [x] Critical decisions documented with versions
- [x] Technology stack fully specified
- [x] Integration patterns defined
- [x] Performance considerations addressed

**✅ Implementation Patterns**
- [x] Naming conventions established
- [x] Structure patterns defined
- [x] Communication patterns specified
- [x] Process patterns documented

**✅ Project Structure**
- [x] Complete directory structure defined
- [x] Component boundaries established
- [x] Integration points mapped
- [x] Requirements to structure mapping complete

### Architecture Readiness Assessment

**Overall Status:** READY FOR IMPLEMENTATION

**Confidence Level:** HIGH

**Key Strengths:**
- Coherencia fuerte entre decisiones, estructura y patrones.
- Seguridad multi-hogar cerrada en dos capas (app + DB).
- Guia concreta para implementacion consistente por multiples agentes.
- Riesgo de drift reducido por reglas de enforcement y anti-patterns.

**Areas for Future Enhancement:**
- Profundizar ADRs de cache/revalidacion por modulo en fase de hardening.
- Ampliar contratos de integracion externa cuando se active Fase 2.
- Incorporar benchmarks automaticos de NFR en pipeline.

### Implementation Handoff

**AI Agent Guidelines:**
- Follow all architectural decisions exactly as documented.
- Use implementation patterns consistently across all components.
- Respect project structure and boundaries.
- Refer to this document for all architectural questions.

**First Implementation Priority:**
- Ejecutar inicializacion del starter y baseline tecnico acordado:
  - `npx create-next-app -e with-supabase home-app-new`
