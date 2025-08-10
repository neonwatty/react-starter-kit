# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview
This is an Inertia Rails Starter Kit - a full-stack application with Rails backend and React frontend using Inertia.js. The project combines Ruby on Rails for the backend API with React/TypeScript and shadcn/ui components for the frontend.

## Key Technologies
- **Backend**: Rails 8.0.2, Ruby
- **Frontend**: React 19, TypeScript, Inertia.js
- **UI Components**: shadcn/ui with Radix UI primitives
- **Styling**: Tailwind CSS 4.0
- **Build Tool**: Vite with vite-plugin-ruby
- **Database**: SQLite
- **Authentication**: Custom authentication based on Authentication Zero
- **Testing**: RSpec for Rails, Factory Bot for fixtures
- **Deployment**: Kamal (optional SSR support)

## Development Commands

### Setup and Running
```bash
# Initial setup (installs dependencies, creates database)
bin/setup

# Start development server (Rails + Vite)
bin/dev

# Or run servers separately
bin/rails s -p 3000  # Rails server
bin/vite dev         # Vite server
```

### Testing
```bash
# Run all RSpec tests
bin/rspec

# Run specific test file
bin/rspec spec/requests/sessions_spec.rb

# Run tests with specific pattern
bin/rspec spec/requests/settings/
```

### Linting and Formatting
```bash
# TypeScript type checking
npm run check

# ESLint for JavaScript/TypeScript
npm run lint
npm run lint:fix

# Prettier formatting
npm run format
npm run format:fix

# Ruby linting with Rubocop
bin/rubocop
bin/rubocop -a  # Auto-fix

# Security scanning
bin/brakeman
```

### Database
```bash
# Run migrations
bin/rails db:migrate

# Rollback migration
bin/rails db:rollback

# Reset database
bin/rails db:reset

# Seed database
bin/rails db:seed
```

## Architecture

### Rails Backend Structure
- **Controllers**: Inherit from `InertiaController` (which inherits from `ApplicationController`)
  - `InertiaController` provides Inertia.js integration and shared props (flash, auth)
  - Authentication handled via `Current.session` and `Current.user`
- **Models**: User model with has_secure_password, Session model for auth
- **Routes**: RESTful routes with Inertia-specific routes for settings pages

### React Frontend Structure
- **Pages** (`app/frontend/pages/`): Inertia page components organized by feature
  - Each page receives props from Rails controller
  - Pages use layouts for consistent structure
- **Layouts** (`app/frontend/layouts/`): 
  - `PersistentLayout`: Default layout that persists across page transitions
  - `AppLayout`: Main application layout with sidebar
  - `AuthLayout`: Authentication pages layout
- **Components** (`app/frontend/components/`):
  - Custom components and shadcn/ui components in `ui/` subdirectory
  - Components use TypeScript and follow React best practices
- **Hooks** (`app/frontend/hooks/`): Custom React hooks for shared logic
- **Routes** (`app/frontend/routes/`): JS-Routes integration for Rails routes in TypeScript

### Inertia.js Integration
- Rails controllers render Inertia responses instead of HTML/JSON
- Frontend pages are loaded dynamically based on controller response
- Shared data (auth, flash) automatically passed to all pages
- Form submissions use Inertia's `useForm` hook

### Authentication Flow
1. Session-based authentication with secure cookies
2. `ApplicationController#authenticate` protects routes
3. `Current` model stores request-specific data (user, session)
4. Identity namespace handles email verification and password resets

## Important Patterns

### Creating New Pages
1. Create Rails controller action that renders Inertia response
2. Create React component in `app/frontend/pages/` matching controller path
3. Page automatically receives props from controller

### Form Handling
Use Inertia's `useForm` hook for forms:
```tsx
const { data, setData, post, errors } = useForm({
  email: '',
  password: ''
})
```

### Adding UI Components
shadcn/ui components are in `app/frontend/components/ui/`. Import and use them directly - they're already configured with Tailwind CSS.

### Route Helpers
Use JS-Routes for type-safe Rails routes in TypeScript:
```tsx
import { dashboardPath, settingsProfilePath } from "@/routes"
```

## SSR Support
Optional SSR is available but disabled by default. To enable:
1. Uncomment hydration code in `app/frontend/entrypoints/inertia.ts`
2. Uncomment SSR configuration in `config/deploy.yml`
3. Use `Dockerfile-ssr` for deployment

## MCP Server Tools

This project has access to several MCP (Model Context Protocol) servers that provide powerful capabilities:

### shadcn/ui Components (mcp__shadcn-ui)
Use these tools to work with shadcn/ui v4 components:

```bash
# Get a specific component's source code
mcp__shadcn-ui__get_component componentName="button"

# Get demo/example code for a component
mcp__shadcn-ui__get_component_demo componentName="dialog"

# List all available components
mcp__shadcn-ui__list_components

# Get pre-built UI blocks (dashboards, forms, etc.)
mcp__shadcn-ui__get_block blockName="dashboard-01"

# List all blocks (optionally filtered by category)
mcp__shadcn-ui__list_blocks category="dashboard"
```

When adding new UI components:
1. First check available components with `mcp__shadcn-ui__list_components`
2. Get the component source with `mcp__shadcn-ui__get_component`
3. Review demo usage with `mcp__shadcn-ui__get_component_demo`
4. Place component in `app/frontend/components/ui/`
5. Components are already configured for Tailwind CSS and TypeScript

### Context7 Documentation Lookup (mcp__context7)
Use for fetching up-to-date documentation for any library:

```bash
# First resolve library name to ID
mcp__context7__resolve-library-id libraryName="react"

# Then get documentation
mcp__context7__get-library-docs context7CompatibleLibraryID="/facebook/react" topic="hooks"
```

Useful for:
- Getting latest React, Rails, or Inertia.js documentation
- Understanding library APIs and best practices
- Finding code examples and patterns

### Playwright Browser Testing (mcp__playwright)
Use for browser automation and testing:

```bash
# Navigate to a page
mcp__playwright__browser_navigate url="http://localhost:3000"

# Take screenshots
mcp__playwright__browser_take_screenshot filename="dashboard.png"

# Get page snapshot for testing
mcp__playwright__browser_snapshot

# Interact with elements
mcp__playwright__browser_click element="Sign In button" ref="button[type='submit']"

# Fill forms
mcp__playwright__browser_type element="Email input" ref="input[name='email']" text="user@example.com"
```

Perfect for:
- End-to-end testing of Inertia pages
- Visual regression testing
- Automated form testing
- Debugging UI interactions

### Best Practices for MCP Servers

1. **For UI Development**: Always check shadcn/ui first before creating custom components
2. **For Documentation**: Use Context7 when you need current documentation for React, Rails, or any dependency
3. **For Testing**: Use Playwright to verify UI changes work correctly across pages
4. **Component Integration**: When adding shadcn components, ensure they match the existing Tailwind configuration