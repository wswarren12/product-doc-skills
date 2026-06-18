---
name: feature-dev
description: Generate Feature Development Documents optimized for Claude Code to implement new features within existing projects. Use when users want to add a feature to an existing codebase, extend an application, or create implementation specs for Claude Code to execute against a live project. Triggers include "add a feature", "build this feature", "feature spec", "feature development doc", "extend the app", or any request to plan a new capability for an existing application. NOT for greenfield PRDs (use prd-writer instead) — this skill is specifically for features being added to existing codebases where understanding current architecture matters.
---

# Feature Development Document Generator

Generate implementation-ready Feature Development Documents (.md) that Claude Code reads to build new features into existing projects.

## When to Use This vs PRD Writer

| Scenario | Use This Skill | Use PRD Writer |
|----------|---------------|----------------|
| Adding feature to existing app | ✅ | ❌ |
| Extending current codebase | ✅ | ❌ |
| Building new product from scratch | ❌ | ✅ |
| MVP with no existing code | ❌ | ✅ |

## Quick Start

1. **Gather inputs** — Collect PRD, README/docs, and feature description
2. **Clarify gaps** — Ask targeted questions about the feature and codebase
3. **Generate document** — Create the Feature Development Document (.md)
4. **Deliver output** — Save .md file for user to place in their project

## Conversation Workflow

### Step 1: Collect Required Inputs

Ask the user to provide three things:

**Required:**
1. **Original PRD or product documentation** — The PRD, product spec, or any doc describing the overall product
2. **Application README or technical docs** — README.md, architecture docs, or any documentation that describes the current codebase structure, tech stack, and patterns
3. **Feature description** — What they want to build, structured around:
   - **Why** — The problem this feature solves, who benefits, business motivation
   - **What** — The desired outcome, user-facing behavior, what "done" looks like
   - **How** — High-level approach, any technical preferences or constraints

If the user provides all three upfront, proceed to Step 2. If not, ask for what's missing. Group requests conversationally (don't ask for all three in separate messages if you can help it).

### Step 2: Clarify Gaps

Analyze provided inputs against what's needed for a complete feature spec. Ask clarifying questions ONLY for genuinely missing context. Max 2-3 questions per message.

**Key gaps to probe:**

| Category | Questions to Consider |
|----------|----------------------|
| Scope | Is this a single feature or a feature set? What's explicitly NOT included? |
| Dependencies | Does this touch existing features? Database changes needed? New API endpoints? |
| User flow | What's the step-by-step user experience? Entry points? |
| Edge cases | What happens on error? Empty states? Permissions? |
| Testing | Any specific test requirements? Existing test patterns to follow? |
| Rollout | Feature flag needed? Migration strategy? Backwards compatibility? |

**Smart question selection:**
- If the PRD is detailed, skip high-level "why" questions
- If README shows the tech stack and patterns, skip architecture questions
- If the user describes specific UI behavior, skip user flow questions
- Always ask about scope boundaries — what this feature does NOT do

### Step 3: Generate the Feature Development Document

Once context is sufficient, generate the complete document using the template in `references/feature_dev_template.md`.

**Critical principles for the output document:**

1. **Architecture-first** — The document MUST instruct Claude Code to review the existing codebase before writing any code. This prevents architectural drift.
2. **Pattern-matching** — Tasks should reference existing patterns in the codebase (e.g., "Follow the pattern in `src/routes/users.ts` for the new endpoint")
3. **Incremental extension** — Frame everything as extending existing systems, not building parallel ones
4. **Test coverage is mandatory** — Every task needs test requirements mapped to acceptance criteria
5. **Agent-assignable tasks** — Each task is self-contained enough for a single Claude Code agent/teammate to own

### Step 4: Deliver Output

Save as `FEATURE-[FeatureName].md` in outputs.

This file is designed to be placed in the project root (or a `/docs` directory) where Claude Code can read it with: `Read FEATURE-[FeatureName].md and implement this feature.`

---

## Output Document Structure

The generated document follows the template in `references/feature_dev_template.md`. Key sections:

1. **Feature Overview** — Why/What/How summary, scope boundaries
2. **Codebase Discovery Protocol** — Instructions for Claude Code to review architecture BEFORE coding
3. **Implementation Tasks** — Ordered task list with agent assignments, acceptance criteria, and test requirements
4. **Test Coverage Plan** — Comprehensive testing strategy mapped to acceptance criteria
5. **Integration Checklist** — How to verify the feature works within the existing system
6. **Claude Code Execution Guide** — Ready-to-use prompts for Agent Teams or single-agent execution

See `references/feature_dev_template.md` for the complete template with all sections and placeholders.

See `references/codebase_discovery.md` for detailed codebase review patterns that get embedded in the output document.

---

## Writing Guidelines

### Task Granularity

Each task should be:
- **Completable in one agent session** — If a task would take multiple rounds, split it
- **Independently testable** — Has its own acceptance criteria and test requirements
- **Clearly scoped** — Explicit about which files to create/modify and which to leave alone

### Acceptance Criteria Format

Use Given/When/Then (Gherkin) for testable criteria:
```
Given [precondition/context]
When [action taken]
Then [expected result]
```

### Referencing Existing Code

When specifying tasks, reference existing patterns:
```
Create a new API route at `src/routes/billing.ts` following the pattern
established in `src/routes/users.ts` — same middleware chain, error
handling, and response format.
```

### Scope Language

Be surgical about scope:
- ✅ "Add a `status` column to the `projects` table. Do NOT modify existing columns."
- ✅ "Create new component at `src/components/BillingCard.tsx`. Do NOT modify `DashboardLayout`."
- ❌ "Update the database as needed"
- ❌ "Make whatever UI changes are necessary"

---

## Example Conversation Flow

**User**: "I want to add a notifications feature to my app. Here's the PRD and README."

**Claude**: Thanks! I've reviewed both. The PRD describes a project management tool built with Next.js, Supabase, and Tailwind. Let me ask a few things to scope the notification feature:

1. **What triggers notifications?** Task assignments? Due dates? Comments? All of the above?
2. **Delivery channels** — In-app only for now, or also email/push?
3. **What's NOT in scope?** For example, should we skip notification preferences/settings for v1?

**User**: "Task assignments and comments trigger them. In-app only. Skip preferences for now — everyone gets everything."

**Claude**: Got it. One more:

- **Real-time or polling?** Supabase supports real-time subscriptions. Should notifications appear instantly, or is polling on page load fine for v1?

**User**: "Real-time would be great since we're already on Supabase."

**Claude**: Perfect, I have everything needed. Generating the Feature Development Document now.

[Generates FEATURE-Notifications.md following template]
