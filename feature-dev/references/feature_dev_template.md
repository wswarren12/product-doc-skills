# Feature Development Document Template

Use this exact template when generating feature development documents. Replace all `[placeholders]` with specific content. Remove any sections not applicable, but always include Sections 1-6.

---

```markdown
# Feature: [Feature Name]

| Field | Value |
|-------|-------|
| Parent PRD | [PRD name or link] |
| Author | [Name] |
| Date | [YYYY-MM-DD] |
| Status | Draft / Approved / In Progress / Complete |
| Version | 1.0 |

---

## 1. Feature Overview

### Why (Problem & Motivation)

[2-3 sentences: What problem does this feature solve? Who is affected? Why does it matter now?]

### What (Desired Outcome)

[2-3 sentences: What does the user experience when this feature is complete? What does "done" look like from the user's perspective?]

### How (Approach)

[2-3 sentences: High-level technical approach. What systems are extended? What's the general strategy?]

### Scope Boundaries

**In scope:**
- [Specific capability 1]
- [Specific capability 2]
- [Specific capability 3]

**Out of scope (do NOT build):**
- [Related thing that's explicitly excluded and why]
- [Future enhancement to defer]
- [Adjacent feature to skip]

---

## 2. Codebase Discovery Protocol

> **CRITICAL**: Claude Code MUST complete this section BEFORE writing any implementation code. Understanding the existing architecture prevents drift and ensures the new feature integrates cleanly.

### Step 1: Project Structure Review

Run these commands to understand the project layout:

```bash
# Get the project tree (excluding noise)
find . -type f \
  -not -path '*/node_modules/*' \
  -not -path '*/.next/*' \
  -not -path '*/dist/*' \
  -not -path '*/.git/*' \
  -not -path '*/coverage/*' \
  | head -200

# Identify the tech stack
cat package.json | head -50
cat tsconfig.json 2>/dev/null
cat next.config.* 2>/dev/null
```

### Step 2: Architecture Pattern Analysis

Read these files to understand existing patterns (adapt paths to actual project):

```bash
# Routing patterns
ls -la src/app/api/ 2>/dev/null || ls -la src/routes/ 2>/dev/null || ls -la pages/api/ 2>/dev/null

# Database patterns
ls -la src/lib/db* 2>/dev/null || ls -la src/utils/supabase* 2>/dev/null || ls -la prisma/ 2>/dev/null

# Component patterns
ls -la src/components/ 2>/dev/null | head -20

# Existing test patterns
ls -la tests/ 2>/dev/null || ls -la __tests__/ 2>/dev/null || ls -la src/**/*.test.* 2>/dev/null
```

**Read at least one representative file from each layer to understand:**
- [Specific file pattern to review, e.g., "Read `src/app/api/projects/route.ts` for API route patterns"]
- [Specific file pattern to review, e.g., "Read `src/components/ProjectCard.tsx` for component patterns"]
- [Specific file pattern to review, e.g., "Read `tests/api/projects.test.ts` for test patterns"]
- [Specific file pattern to review, e.g., "Read `supabase/migrations/` for migration naming and structure"]

### Step 3: Conventions Checklist

Before implementing, confirm you understand:

- [ ] **Naming conventions** — File naming, component naming, variable naming
- [ ] **State management** — How state is managed (Context, Zustand, Redux, server state)
- [ ] **Error handling** — How errors are caught, formatted, and displayed to users
- [ ] **Auth patterns** — How authentication/authorization is handled in routes and components
- [ ] **Database access** — ORM/client used, where queries live, transaction patterns
- [ ] **Testing approach** — Test runner, file naming, assertion style, mock patterns
- [ ] **Import paths** — Alias patterns (e.g., `@/` prefixes), relative vs absolute

### Step 4: Dependency Check

```bash
# Check if required packages are already installed
cat package.json | grep -E "[list key dependencies this feature needs]"

# If new packages are needed, note them here:
# [Package 1] — [why needed]
# [Package 2] — [why needed]
```

---

## 3. Implementation Tasks

> Each task is self-contained and assignable to a single Claude Code agent or teammate. Tasks are ordered by dependency — complete them sequentially unless marked as parallelizable.

### Task 1: [Database/Schema Changes]

**Task ID**: T-1
**Agent**: db-builder (or general agent)
**Priority**: P0 — Foundation
**Depends on**: Codebase Discovery (Section 2)
**Parallel**: No — must complete before T-2, T-3

**Description**: [What database changes are needed]

**Files to create/modify:**
- Create: `[path to new migration file]`
- Modify: `[path to existing file, if any]` — [what to change]

**Detailed instructions:**
1. [Step-by-step implementation instruction]
2. [Reference existing patterns: "Follow the migration naming convention in `supabase/migrations/`"]
3. [Specific SQL or schema changes]

**Do NOT:**
- [Explicitly state what should not be touched]

**Acceptance Criteria:**
- [ ] AC-T1.1: Given [context], when [action], then [result]
- [ ] AC-T1.2: Given [context], when [action], then [result]

**Test Requirements:**
- Unit: [What to test — e.g., "Verify table exists with correct columns and constraints"]
- Integration: [What to test — e.g., "Verify RLS policies allow/deny expected operations"]

---

### Task 2: [Backend/API Changes]

**Task ID**: T-2
**Agent**: backend-builder (or general agent)
**Priority**: P0 — Core
**Depends on**: T-1
**Parallel with**: T-3 (if frontend uses mocks)

**Description**: [What API endpoints or server-side logic is needed]

**Files to create/modify:**
- Create: `[path to new route/handler]`
- Modify: `[path to existing file]` — [what to change]

**Detailed instructions:**
1. [Step-by-step, referencing existing patterns]
2. ["Follow the pattern in `src/app/api/[existing]/route.ts`"]
3. [Request/response shapes]
4. [Validation requirements]
5. [Error handling requirements]

**Do NOT:**
- [Explicitly state what should not be touched]

**Acceptance Criteria:**
- [ ] AC-T2.1: Given [context], when [action], then [result]
- [ ] AC-T2.2: Given [context], when [action], then [result]
- [ ] AC-T2.3: Given [error condition], when [action], then [error response]

**Test Requirements:**
- Unit: [Endpoint unit tests]
- Integration: [Full request/response cycle tests]

---

### Task 3: [Frontend/UI Changes]

**Task ID**: T-3
**Agent**: frontend-builder (or general agent)
**Priority**: P0 — Core
**Depends on**: T-1 (schema), T-2 (API shapes, can mock initially)
**Parallel with**: T-2 (using mocked responses)

**Description**: [What UI components and pages are needed]

**Files to create/modify:**
- Create: `[paths to new components]`
- Modify: `[paths to existing components]` — [what to change]

**Detailed instructions:**
1. [Step-by-step, referencing existing component patterns]
2. ["Follow the component pattern in `src/components/[Existing].tsx`"]
3. [State management approach]
4. [Required UI states: loading, error, empty, success]

**Do NOT:**
- [Explicitly state what should not be touched]

**Acceptance Criteria:**
- [ ] AC-T3.1: Given [context], when [action], then [result]
- [ ] AC-T3.2: Given [empty state], when [page loads], then [empty state UI]
- [ ] AC-T3.3: Given [error], when [action fails], then [error UI]

**Test Requirements:**
- Unit: [Component render tests]
- Integration: [User interaction flow tests]

---

### Task 4: [Integration & Wiring]

**Task ID**: T-4
**Agent**: integrator (or general agent)
**Priority**: P0 — Integration
**Depends on**: T-2, T-3

**Description**: Connect frontend to real backend. Remove all mocks. Verify end-to-end data flow.

**Files to modify:**
- Modify: `[frontend files that need mock removal]`
- Modify: `[any wiring/configuration files]`

**Detailed instructions:**
1. Replace mocked API calls with real endpoint calls
2. Verify data persists through the full user flow
3. Search for remaining mocks: `grep -r "mock\|Mock\|MOCK" --include="*.ts" --include="*.tsx" [relevant dirs]`
4. Test all error states with real API responses

**Do NOT:**
- Modify any API endpoint behavior
- Change database schema

**Acceptance Criteria:**
- [ ] AC-T4.1: Given real data, when [user flow], then [expected result persists in database]
- [ ] AC-T4.2: No mock patterns remain in production code

**Test Requirements:**
- E2E: [Full user flow tests with real backend]

---

### [Task N: Additional tasks as needed — same structure]

---

## 4. Test Coverage Plan

### Coverage Requirements

| Type | Target | Runner | Location |
|------|--------|--------|----------|
| Unit | 80%+ | [Vitest/Jest/etc.] | `tests/unit/` or co-located |
| Integration | Key flows | [Vitest/Jest/etc.] | `tests/integration/` |
| E2E | All user flows | [Playwright/Cypress/etc.] | `tests/e2e/` |

### Acceptance Criteria → Test Mapping

> Every acceptance criterion MUST have a corresponding test. This table tracks coverage.

| AC ID | Description | Test File | Test Name | Status |
|-------|-------------|-----------|-----------|--------|
| AC-T1.1 | [Short description] | [test file path] | [test function name] | ⏳ |
| AC-T1.2 | [Short description] | [test file path] | [test function name] | ⏳ |
| AC-T2.1 | [Short description] | [test file path] | [test function name] | ⏳ |
| AC-T2.2 | [Short description] | [test file path] | [test function name] | ⏳ |
| AC-T3.1 | [Short description] | [test file path] | [test function name] | ⏳ |

### Test Patterns to Follow

Reference existing test files to maintain consistency:
- **Unit test pattern**: Follow `[path to existing unit test]`
- **Integration test pattern**: Follow `[path to existing integration test]`
- **E2E test pattern**: Follow `[path to existing e2e test]`

### Edge Cases to Test

- [Edge case 1 — e.g., "Empty database, no existing records"]
- [Edge case 2 — e.g., "Concurrent modifications to same record"]
- [Edge case 3 — e.g., "Unauthorized user attempts action"]
- [Edge case 4 — e.g., "Network failure mid-operation"]

---

## 5. Integration Checklist

> Use this checklist after all tasks are complete to verify the feature works within the existing system.

### Functional Verification
- [ ] Feature works end-to-end with real data
- [ ] All acceptance criteria pass
- [ ] Error states handled gracefully
- [ ] Empty states render correctly
- [ ] Loading states display during async operations

### Architectural Compliance
- [ ] New files follow existing naming conventions
- [ ] New components follow existing component patterns
- [ ] New API routes follow existing route patterns
- [ ] Database changes include proper migrations (up and down)
- [ ] No new dependencies without justification
- [ ] Auth/permissions enforced on new endpoints and UI

### Regression Safety
- [ ] Existing tests still pass: `[test command]`
- [ ] No modifications to existing files outside of stated scope
- [ ] No breaking changes to existing API contracts
- [ ] Search for unintended changes: `git diff --stat`

### Code Quality
- [ ] Linting passes: `[lint command]`
- [ ] Type checking passes: `[type check command]`
- [ ] No `console.log`, `TODO`, or debug artifacts in committed code
- [ ] New code has appropriate inline comments for complex logic

---

## 6. Claude Code Execution Guide

### Option A: Agent Teams (Recommended for 3+ tasks)

Copy this prompt into Claude Code to begin:

```
Read FEATURE-[FeatureName].md and implement this feature.

BEFORE WRITING ANY CODE:
1. Complete the Codebase Discovery Protocol in Section 2
2. Summarize what you learned about the project's patterns
3. Confirm your implementation plan aligns with existing architecture

Then execute the implementation tasks using an Agent Teams workflow:

TEAM LEAD (you): Coordinate work, verify architectural compliance, run integration checklist.

TEAMMATES:
[Customize based on tasks — example below]
- "db-builder": Owns Task T-1 (database changes)
- "backend-builder": Owns Task T-2 (API endpoints)
- "frontend-builder": Owns Task T-3 (UI components)
- "integrator": Owns Task T-4 (wiring and E2E)
- "test-writer": Writes tests for all tasks, validates acceptance criteria

EXECUTION ORDER:
Phase 1: db-builder executes T-1. test-writer writes DB tests.
  GATE: Run DB tests. All must pass.
Phase 2: backend-builder executes T-2. frontend-builder executes T-3 (with mocks). test-writer writes API and component tests.
  GATE: Run unit and integration tests. All must pass.
Phase 3: integrator executes T-4. test-writer writes E2E tests.
  GATE: Run E2E tests. All must pass.
Phase 4: Run full test suite. Complete Integration Checklist (Section 5). Verify all ACs pass.

CRITICAL RULES:
- Complete Codebase Discovery (Section 2) before any implementation
- Follow existing patterns identified during discovery
- Every AC must have a passing test
- Do not modify files outside of stated scope
```

### Option B: Single Agent (Simpler features)

Copy this prompt into Claude Code:

```
Read FEATURE-[FeatureName].md and implement this feature.

Follow this order:
1. Complete the Codebase Discovery Protocol (Section 2) — read the codebase and understand patterns FIRST
2. Execute Tasks T-1 through T-[N] in order
3. Write tests for each task as you go
4. After all tasks complete, run the Integration Checklist (Section 5)
5. Run the full test suite and verify all acceptance criteria pass

RULES:
- Follow existing code patterns identified in Section 2
- Do not modify files outside of stated scope for each task
- Every acceptance criterion needs a passing test
```

---

## 7. Appendix

### New Dependencies

| Package | Version | Purpose | Required By |
|---------|---------|---------|-------------|
| [package] | [version] | [why needed] | Task T-[N] |

### Environment Variables

| Variable | Description | Required By |
|----------|-------------|-------------|
| [VAR_NAME] | [What it's for] | Task T-[N] |

### Migration/Rollback Notes

[Any notes about data migration, backwards compatibility, or rollback strategy]

### References

- Parent PRD: [link/filename]
- Related features: [links to other feature docs if applicable]
- External docs: [links to relevant API docs, library docs, etc.]
```
