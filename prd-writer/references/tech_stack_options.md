# Tech Stack Options

Alternative stack recommendations based on project requirements.

## Quick Selection Guide

| If building... | Recommended Stack |
|----------------|-------------------|
| Simple MVP / POC | Supabase + Next.js + Vercel |
| Data-heavy dashboard | Supabase + Next.js + Recharts |
| Real-time features | Supabase (real-time) or Firebase |
| Static site + forms | Astro + Netlify Forms |
| Full-stack with API | Next.js API routes + Prisma + PostgreSQL |
| Mobile-first web | React + Capacitor |
| Serverless functions | Vercel/Netlify Functions + DynamoDB |

---

## Default Stack (POC-Friendly)

Best for: MVPs, prototypes, solo developers

### Database & Auth
**Supabase** (PostgreSQL + Auth + Storage)
- Free tier: 500MB database, 1GB file storage
- Built-in authentication (email, OAuth providers)
- Real-time subscriptions
- Row Level Security for permissions
- Dashboard for data management

### Frontend
**Next.js 14+ (App Router)**
- Server and client components
- API routes built-in
- File-based routing
- Excellent TypeScript support

**Tailwind CSS**
- Utility-first styling
- Consistent design system
- Responsive by default

### Deployment
**Vercel**
- Zero-config Next.js deployment
- Preview deployments for PRs
- Free tier: 100GB bandwidth

---

## Alternative Stacks

### Firebase Stack
Best for: Real-time apps, mobile-web, Google ecosystem

```
- Database: Firestore (NoSQL)
- Auth: Firebase Auth
- Functions: Cloud Functions
- Hosting: Firebase Hosting
- Storage: Cloud Storage
```

**Pros:**
- Excellent real-time sync
- Strong mobile SDKs
- Generous free tier
- Easy to start

**Cons:**
- NoSQL can be limiting for complex queries
- Vendor lock-in
- Costs scale quickly

### Railway/Render Stack
Best for: Traditional server apps, custom backends

```
- Database: PostgreSQL (managed)
- Backend: Node.js/Express or Python/FastAPI
- ORM: Prisma (Node) or SQLAlchemy (Python)
- Frontend: React/Vite
- Hosting: Railway or Render
```

**Pros:**
- Full control over backend
- Traditional architecture
- Easy local development

**Cons:**
- More configuration
- Cold starts on free tier

### Serverless Stack
Best for: Event-driven, pay-per-use

```
- Database: PlanetScale (MySQL) or DynamoDB
- Functions: Vercel Functions / AWS Lambda
- Auth: Clerk or Auth0
- Frontend: Next.js or Remix
```

**Pros:**
- Pay only for usage
- Auto-scaling
- No server management

**Cons:**
- Cold start latency
- Vendor lock-in
- Complex local testing

---

## Component-Specific Options

### Databases

| Database | Type | Best For | Free Tier |
|----------|------|----------|-----------|
| Supabase | PostgreSQL | General purpose | 500MB |
| PlanetScale | MySQL | Serverless apps | 5GB |
| MongoDB Atlas | NoSQL | Flexible schemas | 512MB |
| Firebase | NoSQL | Real-time | 1GB |
| Neon | PostgreSQL | Serverless SQL | 3GB |

### Authentication

| Service | Best For | Free Tier |
|---------|----------|-----------|
| Supabase Auth | Supabase projects | Unlimited users |
| Clerk | Modern auth UI | 10K MAUs |
| Auth0 | Enterprise features | 7K MAUs |
| NextAuth.js | Self-hosted | Unlimited |
| Firebase Auth | Firebase projects | Unlimited |

### File Storage

| Service | Best For | Free Tier |
|---------|----------|-----------|
| Supabase Storage | Supabase projects | 1GB |
| Cloudflare R2 | Cost efficiency | 10GB |
| AWS S3 | Scale | 5GB (12mo) |
| Uploadthing | Simple uploads | 2GB |

### Testing Stack

| Tool | Type | Best For |
|------|------|----------|
| Vitest | Unit/Integration | Fast, Jest-compatible, native ESM |
| Playwright | E2E | Cross-browser, reliable, auto-wait |
| Testing Library | Component | React/Vue testing utilities |
| MSW | Mocking | API mocking for tests |
| Faker | Data | Generating test data |

**Recommended Test Setup:**

```bash
# Install testing dependencies
npm install -D vitest @testing-library/react @testing-library/jest-dom
npm install -D playwright @playwright/test
npm install -D msw @faker-js/faker
```

**package.json scripts:**
```json
{
  "scripts": {
    "test": "vitest",
    "test:ui": "vitest --ui",
    "test:coverage": "vitest --coverage",
    "test:e2e": "playwright test",
    "test:e2e:ui": "playwright test --ui",
    "test:all": "vitest run && playwright test",
    "test:db": "vitest run tests/integration/db",
    "test:api": "vitest run tests/integration/api",
    "test:components": "vitest run tests/unit/components"
  }
}
```

**vitest.config.ts:**
```typescript
import { defineConfig } from 'vitest/config'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',
    setupFiles: ['./tests/setup.ts'],
    coverage: {
      provider: 'v8',
      reporter: ['text', 'html'],
      thresholds: {
        lines: 80,
        functions: 80,
        branches: 80,
        statements: 80
      }
    }
  }
})
```

**playwright.config.ts:**
```typescript
import { defineConfig } from '@playwright/test'

export default defineConfig({
  testDir: './tests/e2e',
  fullyParallel: true,
  retries: process.env.CI ? 2 : 0,
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
  },
  webServer: {
    command: 'npm run dev',
    port: 3000,
    reuseExistingServer: !process.env.CI,
  },
})
```

### Email

| Service | Best For | Free Tier |
|---------|----------|-----------|
| Resend | Developer-friendly | 3K/mo |
| SendGrid | High volume | 100/day |
| Postmark | Transactional | 100/mo |
| AWS SES | Cost efficiency | 62K/mo (EC2) |

### Payments

| Service | Best For | Fees |
|---------|----------|------|
| Stripe | Most cases | 2.9% + 30¢ |
| LemonSqueezy | Digital goods | 5% + 50¢ |
| Paddle | SaaS | 5% + 50¢ |

---

## Stack Selection Questions

Ask users these questions to determine the best stack:

### Existing Constraints
- "Do you have an existing codebase or tech stack to integrate with?"
- "Are there team members with specific framework expertise?"
- "Any compliance requirements (HIPAA, SOC2, etc.)?"

### Feature Requirements
- "Do you need real-time updates (live collaboration, chat)?"
- "How much data do you expect to store?"
- "Do you need file uploads? What types and sizes?"

### Scale Expectations
- "Expected users for MVP? For 6 months out?"
- "Any geographic requirements (EU data residency)?"

### Budget
- "Are you willing to pay for hosting, or must it stay free tier?"
- "Timeline expectations (speed to market vs. polish)?"

---

## Database Schema Conventions

### Standard Fields (include in all tables)

```sql
CREATE TABLE example (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  -- ... other fields ...
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Auto-update updated_at
CREATE OR REPLACE FUNCTION update_updated_at()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = NOW();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trigger_updated_at
BEFORE UPDATE ON example
FOR EACH ROW EXECUTE FUNCTION update_updated_at();
```

### Common Patterns

**Soft Delete:**
```sql
deleted_at TIMESTAMPTZ DEFAULT NULL
-- Query: WHERE deleted_at IS NULL
```

**User Ownership:**
```sql
user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE
```

**Enum Status:**
```sql
status TEXT CHECK (status IN ('draft', 'published', 'archived')) DEFAULT 'draft'
```

**JSON Metadata:**
```sql
metadata JSONB DEFAULT '{}'::JSONB
```

---

## API Design Patterns

### RESTful Conventions

```
GET    /api/items           # List all items
GET    /api/items/:id       # Get single item
POST   /api/items           # Create item
PUT    /api/items/:id       # Update item
DELETE /api/items/:id       # Delete item
```

### Response Formats

**Success (single item):**
```json
{
  "data": { "id": "...", "name": "..." },
  "meta": { "timestamp": "..." }
}
```

**Success (list):**
```json
{
  "data": [...],
  "meta": {
    "total": 100,
    "page": 1,
    "per_page": 10
  }
}
```

**Error:**
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Email is required",
    "details": { "field": "email" }
  }
}
```

### Common HTTP Status Codes

| Code | Meaning | Use When |
|------|---------|----------|
| 200 | OK | Successful GET, PUT |
| 201 | Created | Successful POST |
| 204 | No Content | Successful DELETE |
| 400 | Bad Request | Validation error |
| 401 | Unauthorized | Not authenticated |
| 403 | Forbidden | Not authorized |
| 404 | Not Found | Resource doesn't exist |
| 409 | Conflict | Duplicate entry |
| 500 | Server Error | Unexpected failure |
