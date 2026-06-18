# Codebase Discovery Patterns

Patterns for instructing Claude Code to understand an existing codebase before implementing new features. Use these patterns when populating Section 2 of the Feature Development Document.

## Why Discovery Matters

Without codebase discovery, Claude Code will:
- Create files in the wrong locations
- Use different naming conventions than the existing codebase
- Implement patterns that conflict with established architecture
- Miss existing utilities and duplicate functionality
- Break existing tests by violating assumptions

With discovery, Claude Code will:
- Extend the application naturally, as if the original author built the feature
- Reuse existing utilities, types, and patterns
- Place files where they belong
- Write tests that match the existing test style

## Discovery Commands by Stack

### Next.js / React Projects

```bash
# App Router structure
find src/app -type f -name "*.ts" -o -name "*.tsx" | head -30

# Component library
ls src/components/

# Shared utilities
ls src/lib/ src/utils/ 2>/dev/null

# API route patterns (read one representative route)
cat src/app/api/[first-route]/route.ts

# Middleware
cat src/middleware.ts 2>/dev/null

# Types/interfaces
ls src/types/ 2>/dev/null
```

### Express / Node Projects

```bash
# Route structure
find src/routes -type f | head -20

# Middleware chain
cat src/middleware/ 2>/dev/null
cat src/app.ts | head -40

# Models/schemas
ls src/models/ 2>/dev/null

# Service layer
ls src/services/ 2>/dev/null
```

### Python / Django / FastAPI

```bash
# App structure
find . -name "*.py" -not -path "*/venv/*" -not -path "*/__pycache__/*" | head -30

# Models
cat */models.py | head -50

# URL patterns
cat */urls.py 2>/dev/null
```

### Database Discovery

```bash
# Supabase migrations
ls -la supabase/migrations/ 2>/dev/null

# Prisma schema
cat prisma/schema.prisma 2>/dev/null

# Drizzle schema
cat src/db/schema.ts 2>/dev/null

# Raw SQL migrations
ls -la migrations/ db/migrations/ 2>/dev/null
```

### Test Discovery

```bash
# Test file locations and naming
find . -name "*.test.*" -o -name "*.spec.*" | head -20

# Test config
cat vitest.config.* jest.config.* playwright.config.* 2>/dev/null

# Read one representative test to understand style
cat [first test file found]
```

## Pattern Extraction Questions

When reviewing the codebase, Claude Code should answer these questions and record the answers:

### File Organization
- Where do new routes/endpoints go?
- Where do new components go?
- Where do new utility functions go?
- Is there a barrel export pattern (index.ts files)?

### Code Style
- Are files named in camelCase, PascalCase, kebab-case, or snake_case?
- Do components use default or named exports?
- Are there shared type definition files?
- Is there a consistent error handling pattern?

### Data Flow
- How does data get from the database to the UI?
- Is there a service/repository layer between API and database?
- How is authentication state managed?
- Are there shared API client utilities?

### Testing
- What test runner is used?
- Where do test files live (co-located or separate directory)?
- What assertion library is used?
- Are there shared test utilities or fixtures?
- How are mocks handled?

## Customizing Discovery for the Feature

When writing Section 2 of the Feature Development Document, tailor the discovery commands to the specific feature being built. Examples:

**If the feature adds a new database table:**
- Include commands to review existing migration files
- Point to specific migration files as pattern references
- Include RLS policy review if using Supabase

**If the feature adds new API endpoints:**
- Include commands to review existing route files
- Point to a specific route file that's most similar to what's being built
- Include middleware chain review

**If the feature adds new UI components:**
- Include commands to review existing component files
- Point to the most similar existing component
- Include state management pattern review

**If the feature modifies existing features:**
- Include commands to review the specific files being modified
- Include test review for the existing feature
- Flag any existing tests that might break
