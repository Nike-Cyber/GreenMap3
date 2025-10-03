# GreenMap - Environmental Impact Tracking Platform

## Overview

GreenMap is a full-stack environmental tracking application that enables users to report and visualize tree plantations and pollution hotspots on an interactive map. The platform combines geolocation data with community-driven reporting to create a comprehensive view of environmental activities. Users can submit reports with location data, view statistics on environmental impact, and explore a map showing all reported activities.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend Architecture

**Framework & Build Tool**
- React 18+ with TypeScript for type-safe component development
- Vite as the build tool and development server, providing fast HMR and optimized production builds
- Wouter for lightweight client-side routing (chosen over React Router for minimal bundle size)

**UI Component System**
- shadcn/ui components built on Radix UI primitives for accessible, customizable UI elements
- Tailwind CSS for utility-first styling with custom design tokens
- CSS variables-based theming system for consistent visual design across the application
- "New York" style variant from shadcn/ui configuration

**State Management**
- TanStack Query (React Query) for server state management, caching, and data synchronization
- Custom hooks pattern for encapsulating business logic (e.g., `useAuth`, `useIsMobile`)
- Local component state with React hooks for UI-specific state

**Map Integration**
- Leaflet library for interactive mapping functionality
- Custom marker system to differentiate between tree plantations and pollution hotspots
- Geolocation support for capturing user location coordinates

**Form Handling**
- React Hook Form for performant form management with minimal re-renders
- Zod integration via @hookform/resolvers for runtime validation
- Schema validation aligned with backend database schemas

### Backend Architecture

**Server Framework**
- Express.js as the HTTP server framework
- TypeScript throughout for type safety across client-server boundary
- Custom middleware for request logging and error handling

**Development & Production Modes**
- Development: Vite dev server integrated as Express middleware for HMR
- Production: Static file serving from compiled dist/public directory
- Separate build processes for client (Vite) and server (esbuild)

**API Design**
- RESTful endpoints following /api/* pattern
- Authentication middleware protecting sensitive routes
- Standardized JSON responses with error handling

**Authentication System**
- Replit Auth via OpenID Connect (OIDC) protocol
- Passport.js strategy implementation for OIDC flow
- Session-based authentication with PostgreSQL session store
- Mandatory user operations (getUser, upsertUser) for Replit Auth compatibility

**Session Management**
- connect-pg-simple for PostgreSQL-backed session storage
- 7-day session TTL with secure, httpOnly cookies
- Session data stored in dedicated `sessions` table (required by Replit Auth)

### Data Storage

**Database**
- PostgreSQL via Neon serverless driver for connection pooling and edge compatibility
- Drizzle ORM for type-safe database queries and schema management
- WebSocket-based connections for Neon serverless compatibility

**Schema Design**
- `users` table: Stores user profile information (mandatory for Replit Auth)
- `sessions` table: Stores session data (mandatory for Replit Auth)
- `reports` table: Environmental reports with type enum (tree_plantation | pollution_hotspot)
- Cascading deletes configured to maintain referential integrity
- UUID primary keys with PostgreSQL gen_random_uuid()

**Data Access Layer**
- Storage interface (IStorage) abstracting database operations
- DatabaseStorage implementation using Drizzle ORM
- Separation of concerns between routes, storage, and database layers

**Migrations**
- Drizzle Kit for schema migrations
- Migration files stored in ./migrations directory
- Schema definition in shared/schema.ts for type sharing between client and server

### External Dependencies

**Third-Party Services**
- Replit Auth (OIDC): User authentication and identity management
- Neon Database: Serverless PostgreSQL hosting
- Leaflet CDN: Map marker icons and styles

**NPM Packages**
- **UI Components**: @radix-ui/* packages for accessible primitives
- **Forms & Validation**: react-hook-form, zod, @hookform/resolvers
- **Data Fetching**: @tanstack/react-query
- **Map Library**: leaflet with TypeScript definitions
- **Database**: drizzle-orm, @neondatabase/serverless
- **Authentication**: openid-client, passport, express-session
- **Build Tools**: vite, esbuild, tsx
- **Styling**: tailwindcss, class-variance-authority, clsx

**Development Tooling**
- Replit-specific Vite plugins for development environment integration
- TypeScript compiler for type checking across the entire codebase
- Path aliases configured for clean imports (@/, @shared/, @assets/)

### Code Organization

**Monorepo Structure**
- `/client`: Frontend React application
- `/server`: Backend Express API
- `/shared`: Shared TypeScript types and schemas (Drizzle schemas, Zod validators)
- `/migrations`: Database migration files

**Type Sharing Strategy**
- Shared schema definitions using Drizzle and Zod
- Common types exported from @shared/* for use in both client and server
- Compile-time type safety across the full stack

**Environment Configuration**
- DATABASE_URL: PostgreSQL connection string (required)
- REPL_ID: Replit-specific identifier for OIDC
- SESSION_SECRET: Encryption key for session cookies
- ISSUER_URL: OIDC issuer endpoint
- REPLIT_DOMAINS: Allowed domains for OIDC redirect