# Product Doc Skills

Two [Claude Code](https://docs.claude.com/en/docs/claude-code) Agent Skills for turning rough product ideas into structured, implementation-ready specs that Claude Code can build from directly.

| Skill | Use it for | Output |
|-------|-----------|--------|
| **`prd-writer`** | A brand-new product, MVP, or feature **from scratch** (greenfield) | A full Product Requirements Document (`.md` + `.docx`) |
| **`feature-dev`** | Adding a feature **to an existing codebase** | A Feature Development Document (`.md`) |

Both skills work conversationally: you share a rough idea, the skill asks only the questions it genuinely needs answered, and then generates a complete, parseable document optimized for Claude Code (and structured for multi-agent / Agent Team execution).

---

## Which skill do I use?

| Scenario | `prd-writer` | `feature-dev` |
|----------|:---:|:---:|
| Building a new product from scratch | ✅ | ❌ |
| MVP / POC with no existing code | ✅ | ❌ |
| Adding a feature to an existing app | ❌ | ✅ |
| Extending a current codebase | ❌ | ✅ |

Rule of thumb: **no code yet → `prd-writer`. Code already exists → `feature-dev`.**

---

## Installation

These are standard Claude Code Agent Skills — each is a folder containing a `SKILL.md` and supporting `references/`. Install them by placing the folders where Claude Code looks for skills.

### Option 1 — Personal skills (available in every project)

```bash
# Clone the repo
git clone https://github.com/wswarren12/product-doc-skills.git

# Copy both skills into your personal skills directory
mkdir -p ~/.claude/skills
cp -R "product-doc-skills/prd-writer" ~/.claude/skills/
cp -R "product-doc-skills/feature-dev" ~/.claude/skills/
```

### Option 2 — Project skills (shared with a repo / team)

```bash
# From the root of the project you want the skills available in:
mkdir -p .claude/skills
cp -R /path/to/product-doc-skills/prd-writer .claude/skills/
cp -R /path/to/product-doc-skills/feature-dev .claude/skills/
```

Commit `.claude/skills/` so everyone working in the repo gets them.

### Verify the install

Start (or restart) Claude Code and run:

```
/skills
```

You should see `prd-writer` and `feature-dev` listed. Claude Code auto-discovers any folder containing a `SKILL.md` under a `skills/` directory.

> **Note:** The `.docx` output from `prd-writer` relies on Claude Code's `docx` skill. The Markdown output works with no extra dependencies.

---

## Usage

You don't have to invoke the skills by name — describe what you want and Claude Code will trigger the right one. To be explicit, just name it.

**Greenfield product / MVP:**
```
I want to build an app for tracking unpaid invoices and sending reminders. Write a PRD.
```
→ triggers **`prd-writer`**

**Feature on an existing app:**
```
Add a real-time notifications feature to my app. Here's the PRD and the README.
```
→ triggers **`feature-dev`**

Each skill will ask a few targeted clarifying questions (2–3 at a time), then generate the document. Save it in your project and hand it to Claude Code with something like:

```
Read PRD-InvoiceReminder-v1.md and implement it.
```
```
Read FEATURE-Notifications.md and implement this feature.
```

---

## Skill overviews

### 📄 `prd-writer` — PRD Writer

**Purpose:** Transform a rough product idea into a complete, testable Product Requirements Document optimized for AI coding assistants. PRDs follow Marty Cagan's "what & why, not how" principle, are scoped explicitly (including what *not* to build), and are structured so a Claude Code orchestrator can split the work across database, backend, frontend, and test agents.

**Inputs:** A rough concept — a one-liner, a comparison ("like X but for Y"), a problem statement, or a feature request.

**Output:** Dual-format document — `PRD-[ProductName]-v1.md` (for Claude Code + version control) and `PRD-[ProductName]-v1.docx` (for stakeholders).

**Bundled references:**
- `acceptance_criteria.md` — Given/When/Then patterns
- `user_flow_examples.md` — flow examples
- `tech_stack_options.md` — alternative stack recommendations
- `multi_agent_orchestration.md` — agent-spawning templates and validation patterns

#### PRD output sections

A generated PRD contains these sections:

1. **Overview** — One-paragraph summary + a one-sentence "A [product] that [does what] for [whom] so they can [benefit]" description
2. **Problem Statement** — The problem, quantitative + qualitative evidence, and current alternatives
3. **Target User** — Primary persona table (role, goal, pain point, technical level) and user context
4. **Solution Overview** — Value proposition and key differentiators
5. **Features (In Scope)** — Each feature with description, user flow, Trigger→Action→Result behavior spec, Given/When/Then acceptance criteria, and error states
6. **Out of Scope** — Explicit "NOT building" list to prevent scope creep
7. **User Flows** — Numbered, step-by-step primary and secondary flows
8. **Success Metrics** — Metric / target / measurement-method table
9. **Implementation Notes** — Recommended tech stack, database schema, API endpoints, and key technical considerations
10. **Multi-Agent Task Breakdown** — Orchestration strategy, per-agent task definitions (Database / Backend / Frontend / Testing), feature-to-agent mapping, AC-to-test mapping, and ready-to-use orchestrator prompts
11. **Open Questions** — Tracked question / status / answer table
12. **Timeline & Phases** — Phased checklist (MVP, enhancements)
- **Appendix** — Glossary and references

---

### 🛠️ `feature-dev` — Feature Development Document Generator

**Purpose:** Generate an implementation-ready spec for adding a feature to an **existing** codebase. The defining principle is *architecture-first*: the output document instructs Claude Code to study the current code, conventions, and patterns **before** writing anything, so the new feature extends existing systems instead of bolting on parallel ones.

**Inputs (three things):**
1. The original PRD or product documentation
2. The app's README / technical docs (so the skill understands the current stack and patterns)
3. A feature description framed as **Why / What / How**

**Output:** `FEATURE-[FeatureName].md` — drop it in your project root or `/docs` and tell Claude Code to read and implement it.

**Bundled references:**
- `feature_dev_template.md` — the full output template
- `codebase_discovery.md` — codebase-review patterns embedded into the output

#### Feature Development Document output sections

A generated Feature Development Document contains these sections:

1. **Feature Overview** — Why (problem & motivation), What (desired outcome), How (approach), and explicit scope boundaries
2. **Codebase Discovery Protocol** — Step-by-step instructions for Claude Code to review project structure, architecture patterns, conventions, and dependencies *before* coding
3. **Implementation Tasks** — Ordered, agent-assignable tasks (DB → backend → frontend → integration), each with files to touch, acceptance criteria, and test requirements
4. **Test Coverage Plan** — Coverage requirements, acceptance-criteria → test mapping, test patterns to follow, and edge cases
5. **Integration Checklist** — Functional verification, architectural compliance, regression safety, and code quality checks
6. **Claude Code Execution Guide** — Ready-to-use prompts for Agent Teams (3+ tasks) or single-agent execution
- **Appendix** — New dependencies, environment variables, migration/rollback notes, and references

---

## How the two fit together

```
Idea ──► prd-writer ──► PRD ──► Claude Code builds the product
                          │
                          └──► (later) feature-dev ──► Feature Doc ──► Claude Code extends it
```

Use `prd-writer` to launch the product, then `feature-dev` for every feature you add once the codebase exists. `feature-dev` even takes the original PRD as one of its inputs.

---

## Repository structure

```
product-doc-skills/
├── README.md
├── prd-writer/
│   ├── SKILL.md
│   ├── LICENSE.txt
│   └── references/
│       ├── acceptance_criteria.md
│       ├── user_flow_examples.md
│       ├── tech_stack_options.md
│       └── multi_agent_orchestration.md
└── feature-dev/
    ├── SKILL.md
    └── references/
        ├── feature_dev_template.md
        └── codebase_discovery.md
```

## License

The `prd-writer` skill includes its license in `prd-writer/LICENSE.txt`.
