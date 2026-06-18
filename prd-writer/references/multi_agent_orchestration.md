# Multi-Agent Orchestration for Claude Code

Patterns for splitting PRD implementation across specialized agents with test-driven validation.

## Core Principles

### 1. Test-First Development

The TEST AGENT writes tests BEFORE implementation agents build features:

```
1. TEST AGENT reads acceptance criteria
2. TEST AGENT writes failing tests
3. IMPLEMENTATION AGENT builds feature
4. Tests must pass before merge
```

### 2. Clear Boundaries

Each agent has:
- **Single responsibility** — One domain (DB, API, UI, Tests)
- **Defined inputs** — What sections of PRD to read
- **Explicit outputs** — What files to create
- **Verification command** — How to validate completion

### 3. Dependency Management

Tasks declare dependencies explicitly:
- Sequential tasks wait for dependencies
- Parallel tasks can run simultaneously
- Orchestrator validates before advancing phases

---

## Agent Roles

### ORCHESTRATOR AGENT

The main coordinating agent that:
- Reads and understands full PRD
- Spawns specialized agents with scoped tasks
- Tracks task completion and dependencies
- Runs integration validation between phases
- Reports final status and any failures

**Orchestrator never writes implementation code** — only coordinates.

### DATABASE AGENT

Specializes in:
- Schema design and migrations
- Row Level Security (RLS) policies
- Indexes and performance optimization
- Seed data for development/testing
- Database functions and triggers

**Inputs:**
- Section 9: Database Schema
- Section 5: Features (for data requirements)

**Outputs:**
```
/supabase/migrations/
  ├── 001_initial_schema.sql
  ├── 002_rls_policies.sql
  └── 003_indexes.sql
/supabase/seed.sql
```

**Verification:**
```bash
supabase db reset
supabase db lint
npm run test:db
```

### BACKEND AGENT

Specializes in:
- API route handlers
- Request validation (Zod schemas)
- Business logic implementation
- Error handling and responses
- Authentication/authorization checks

**Inputs:**
- Section 9: API Endpoints
- Section 5: Acceptance Criteria
- Section 6: Out of Scope (what NOT to build)

**Outputs:**
```
/app/api/
  ├── [resource]/
  │   ├── route.ts
  │   └── [id]/route.ts
/lib/
  ├── validations/[resource].ts
  └── services/[resource].ts
```

**Verification:**
```bash
npm run lint
npm run test:api
```

### FRONTEND AGENT

Specializes in:
- React/Next.js components
- Forms and validation UI
- State management
- Loading, error, and empty states
- Responsive design
- Accessibility

**Inputs:**
- Section 5: Features (UI requirements)
- Section 7: User Flows
- Section 3: Target User (for UX decisions)

**Outputs:**
```
/components/
  ├── [Feature]/
  │   ├── [Feature]Form.tsx
  │   ├── [Feature]List.tsx
  │   └── [Feature]Card.tsx
/app/
  ├── [route]/
  │   └── page.tsx
```

**Verification:**
```bash
npm run lint
npm run test:components
npm run build  # Catches type errors
```

### TEST AGENT

Specializes in:
- Unit tests (Vitest)
- Integration tests (API + DB)
- End-to-end tests (Playwright)
- Test coverage analysis
- Mapping tests to acceptance criteria

**Inputs:**
- Section 5: Acceptance Criteria (primary source)
- Section 7: User Flows (for E2E tests)
- Section 10: Test Requirements table

**Outputs:**
```
/tests/
  ├── unit/
  │   ├── components/*.test.tsx
  │   └── lib/*.test.ts
  ├── integration/
  │   ├── api/*.test.ts
  │   └── db/*.test.ts
  └── e2e/
      └── flows/*.spec.ts
```

**Verification:**
```bash
npm run test:all
npm run test:coverage  # Must meet threshold
```

---

## Orchestration Patterns

### Pattern 1: Sequential Foundation

Use when later work depends on earlier work.

```
DATABASE → BACKEND → FRONTEND → E2E TESTS
```

**When to use:**
- Small features
- Tight coupling between layers
- Learning/exploration phase

### Pattern 2: Parallel Core (Recommended)

Database first, then parallelize backend/frontend with mocks.

```
        ┌→ BACKEND  ─┐
DATABASE│            ├→ INTEGRATION → E2E
        └→ FRONTEND ─┘
```

**When to use:**
- Medium to large features
- Clear API contracts
- Multiple developers/agents

### Pattern 3: Test-First Parallel

Tests written first, then implementation in parallel.

```
                    ┌→ DATABASE
TEST AGENT (specs) ─┼→ BACKEND
                    └→ FRONTEND
                         ↓
                   ALL TESTS PASS
```

**When to use:**
- High-quality requirements
- TDD practices
- Critical features

---

## Task Spawning Templates

### Spawn Database Agent

```markdown
You are the DATABASE AGENT. Your task:

## Context
[Paste relevant PRD sections]

## Your Responsibilities
1. Create database migrations for the schema defined above
2. Implement Row Level Security policies
3. Create seed data for testing
4. Add necessary indexes

## Constraints
- Use Supabase migrations format
- All tables need created_at, updated_at
- RLS must be enabled on all tables
- Seed data should cover all test scenarios

## Output Files
- /supabase/migrations/[timestamp]_[name].sql
- /supabase/seed.sql

## Verification
Run these commands and ensure no errors:
```bash
supabase db reset
supabase db lint
```

## When Complete
Reply with:
- List of created files
- Any assumptions made
- Any questions or blockers
```

### Spawn Backend Agent

```markdown
You are the BACKEND AGENT. Your task:

## Context
[Paste API endpoints and acceptance criteria]

## Your Responsibilities
1. Implement API route handlers
2. Add request validation with Zod
3. Handle all error cases from acceptance criteria
4. Connect to Supabase for data operations

## Constraints
- Use Next.js App Router API routes
- Return consistent error format: { error: { code, message } }
- All endpoints need authentication check
- Follow RESTful conventions

## Output Files
- /app/api/[resource]/route.ts
- /app/api/[resource]/[id]/route.ts
- /lib/validations/[resource].ts

## Acceptance Criteria to Implement
[List specific ACs]

## When Complete
Reply with:
- List of created files
- Mapping of ACs to implementation
- Any edge cases discovered
```

### Spawn Frontend Agent

```markdown
You are the FRONTEND AGENT. Your task:

## Context
[Paste features and user flows]

## Your Responsibilities
1. Build React components for each feature
2. Implement forms with client-side validation
3. Handle loading, error, and empty states
4. Follow the user flows exactly

## Constraints
- Use Tailwind CSS for styling
- Components must be accessible (ARIA)
- Handle all states: loading, error, empty, success
- Mobile-responsive design

## Output Files
- /components/[Feature]/*.tsx
- /app/[route]/page.tsx

## User Flows to Implement
[List specific flows]

## When Complete
Reply with:
- List of created files
- Screenshots or descriptions of each state
- Any UX decisions made
```

### Spawn Test Agent

```markdown
You are the TEST AGENT. Your task:

## Context
[Paste acceptance criteria and user flows]

## Your Responsibilities
1. Write tests for EVERY acceptance criterion
2. Create E2E tests for each user flow
3. Ensure edge cases are covered
4. Achieve 80% code coverage minimum

## Test Mapping Required
For each AC, create a test:

| AC ID | Criterion | Test File | Test Name |
|-------|-----------|-----------|-----------|
| AC-1 | Given... | /tests/integration/feature.test.ts | should [behavior] |

## Output Files
- /tests/unit/*.test.ts
- /tests/integration/*.test.ts  
- /tests/e2e/*.spec.ts

## Constraints
- Use Vitest for unit/integration tests
- Use Playwright for E2E tests
- Tests must be deterministic (no flaky tests)
- Mock external services

## When Complete
Reply with:
- Test coverage report
- AC-to-test mapping table
- Any untestable requirements (flag for review)
```

---

## Validation Checkpoints

### After Phase 1 (Database)

```bash
# All must pass before Phase 2
supabase db reset          # Migrations apply cleanly
supabase db lint           # No schema issues
npm run test:db            # DB tests pass
```

### After Phase 2 (Core Implementation)

```bash
# All must pass before Phase 3
npm run lint               # No lint errors
npm run test:unit          # Unit tests pass
npm run test:integration   # API tests pass
npm run build              # TypeScript compiles
```

### After Phase 3 (Integration)

```bash
# All must pass before deployment
npm run test:e2e           # E2E flows work
npm run test:coverage      # Coverage threshold met
```

### Final Validation

```bash
# Complete test suite
npm run test:all

# Coverage report
npm run test:coverage -- --reporter=html

# Verify AC mapping
# (Manual: Check every AC has a passing test)
```

---

## Handling Failures

### Test Failure Protocol

When tests fail during orchestration:

1. **Identify failing AC** — Map test to acceptance criterion
2. **Determine responsible agent** — DB, Backend, or Frontend
3. **Spawn fix task** — Give agent specific failure context
4. **Re-run validation** — Ensure fix doesn't break other tests

### Integration Failure Protocol

When components don't work together:

1. **Isolate the boundary** — API contract mismatch? Data format issue?
2. **Review shared contracts** — Check Zod schemas, types
3. **Spawn coordination task** — Both agents align on interface
4. **Add integration test** — Prevent regression

### Blocked Task Protocol

When an agent can't proceed:

1. **Document blocker** — What's missing or unclear?
2. **Flag to orchestrator** — Don't guess, escalate
3. **Update PRD if needed** — Add clarification to Open Questions
4. **Resume when unblocked** — Re-spawn with additional context

---

## Example: Invoice Reminder Feature

### Task Breakdown

```
PHASE 1: Database
├── DB AGENT: Create invoices, reminders tables
└── TEST AGENT: Write schema constraint tests

PHASE 2: Core (Parallel)
├── BACKEND AGENT: 
│   ├── POST /api/invoices
│   ├── GET /api/invoices
│   ├── POST /api/invoices/:id/remind
│   └── Cron job for auto-reminders
├── FRONTEND AGENT:
│   ├── InvoiceForm component
│   ├── InvoiceList component
│   └── Dashboard page
└── TEST AGENT:
    ├── API tests for each endpoint
    └── Component tests for forms

PHASE 3: Integration
├── FRONTEND: Connect to real API
└── TEST AGENT: E2E test for full invoice flow

PHASE 4: Validation
└── ORCHESTRATOR: Run all tests, verify coverage
```

### AC-to-Test Mapping

| AC ID | Acceptance Criterion | Test |
|-------|---------------------|------|
| AC-1.1 | Given logged-in user, when creating invoice with valid data, then invoice saved | `invoices.test.ts: creates invoice` |
| AC-1.2 | Given invoice overdue 7 days, when cron runs, then reminder email sent | `reminders.test.ts: sends reminder` |
| AC-1.3 | Given invalid email, when creating invoice, then validation error | `invoices.test.ts: rejects invalid email` |
| AC-2.1 | Full create-to-remind flow | `e2e/invoice-flow.spec.ts` |
