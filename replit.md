# Replit Agent Guide – La Pecora Nera

## Overview

**La Pecora Nera** is a full-stack web application for an Italian rustic picnic area / countryside farm business. It serves as a complete digital presence including:

- **Homepage** with hero section and feature cards
- **Video gallery** (YouTube links and uploads)
- **Events & bookings** system with reservation forms
- **Photo gallery** with image management
- **Online shop** (product catalog with cart and checkout)
- **Contact page** with messaging form

The visual identity is rustic Italian countryside — wood textures, checkered tablecloth patterns, handwritten-style fonts (Amatic SC for headings, Quicksand for body text), and a warm cream/brown/red/green color palette.

The app is in Italian (UI labels, routes like `/eventi`, `/galleria`, `/carrello`, `/assistenza`).

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend
- **Framework**: React 18 with TypeScript
- **Routing**: Wouter (lightweight alternative to React Router)
- **State Management**: TanStack React Query for server state; local state via React hooks
- **Cart**: Client-side cart stored in `localStorage` via custom `useCart` hook
- **Styling**: Tailwind CSS with CSS custom properties for theming, plus custom rustic UI components in `client/src/components/RusticUI.tsx`
- **UI Components**: shadcn/ui (new-york style) installed in `client/src/components/ui/` with Radix UI primitives
- **Forms**: react-hook-form with zod resolvers (schema validation shared with backend)
- **Animations**: framer-motion for page transitions and entrance effects
- **Icons**: lucide-react
- **Build Tool**: Vite with React plugin

### Backend
- **Runtime**: Node.js with Express 5
- **Language**: TypeScript (run via `tsx` in development)
- **API Pattern**: RESTful JSON API under `/api/` prefix
- **Route definitions**: Shared route contracts in `shared/routes.ts` using Zod schemas for both input validation and response typing
- **Storage layer**: `server/storage.ts` implements `IStorage` interface with `DatabaseStorage` class using Drizzle ORM

### Data Storage
- **Database**: PostgreSQL (required via `DATABASE_URL` environment variable)
- **ORM**: Drizzle ORM with `drizzle-zod` for automatic Zod schema generation from table definitions
- **Schema location**: `shared/schema.ts` — single source of truth for both database tables and TypeScript types
- **Migrations**: Drizzle Kit with `db:push` command for schema synchronization
- **Tables**: `videos`, `events`, `bookings`, `products`, `orders`, `gallery`, `contactMessages`

### Shared Layer
- `shared/schema.ts` — Drizzle table definitions, insert schemas (via `createInsertSchema`), and TypeScript types
- `shared/routes.ts` — API route contract definitions with paths, methods, input schemas, and response schemas
- Both client and server import from `@shared/*` path alias

### Build & Deploy
- **Development**: `npm run dev` runs `tsx server/index.ts` which sets up Vite dev server middleware for HMR
- **Production build**: `npm run build` runs a custom build script (`script/build.ts`) that builds the Vite client to `dist/public` and bundles the server with esbuild to `dist/index.cjs`
- **Production start**: `npm start` runs `node dist/index.cjs` which serves the static frontend and API

### Key Design Decisions

1. **Shared schema between client and server**: The Drizzle schema in `shared/schema.ts` generates both database types and Zod validation schemas, ensuring type safety across the full stack. This eliminates drift between frontend forms and backend validation.

2. **No authentication**: The current implementation has no auth system — all API endpoints are public. This is appropriate for a small business site but should be considered if admin features are added.

3. **Client-side cart**: Shopping cart uses `localStorage` rather than server-side sessions, keeping the backend stateless for cart operations. Orders are only persisted server-side at checkout.

4. **Custom rustic UI layer on top of shadcn**: The app uses both shadcn/ui primitives and custom `RusticUI` components (`RusticButton`, `RusticInput`, `RusticCard`, etc.) that apply the rustic theme styling.

5. **Italian-language routes**: Frontend routes use Italian paths (`/eventi`, `/galleria`, `/carrello`, `/assistenza`) while API routes use English (`/api/events`, `/api/gallery`, `/api/orders`, `/api/contact`).

## External Dependencies

### Database
- **PostgreSQL** — Required. Connection via `DATABASE_URL` environment variable. Used with Drizzle ORM and `connect-pg-simple` for session storage.

### Frontend Libraries
- `@tanstack/react-query` — Server state management and caching
- `wouter` — Client-side routing
- `framer-motion` — Animations
- `react-hook-form` + `@hookform/resolvers` — Form handling with Zod validation
- `embla-carousel-react` — Carousel component
- `recharts` — Charts (via shadcn chart component)
- `react-day-picker` + `date-fns` — Calendar/date picker

### Backend Libraries
- `express` v5 — HTTP server
- `drizzle-orm` + `drizzle-kit` — Database ORM and migration tooling
- `drizzle-zod` — Generate Zod schemas from Drizzle tables
- `zod` — Schema validation
- `multer` — File upload handling (referenced in build allowlist)
- `connect-pg-simple` — PostgreSQL session store

### External Assets
- **Google Fonts**: Amatic SC (display/heading) and Quicksand (body)
- **Unsplash images**: Used for placeholder/background images (wood texture, food, sheep)
- **Transparent Textures**: Paper texture overlay pattern

### Replit-specific
- `@replit/vite-plugin-runtime-error-modal` — Runtime error overlay in development
- `@replit/vite-plugin-cartographer` — Dev tooling (development only)
- `@replit/vite-plugin-dev-banner` — Dev banner (development only)