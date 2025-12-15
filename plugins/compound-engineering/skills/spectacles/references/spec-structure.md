# Spectacles Spec Structure

How specs are organized and the hierarchy of constraints.

## Directory Layout

```
.spectacles/
├── config.yaml           # CLI and schema configuration
├── global/               # Cross-cutting standards
│   └── technical-standards.md
├── domains/              # Bounded context specs
│   ├── auth.md
│   └── billing.md
├── work/                 # Task specifications
│   ├── AUTH-001-login-validation.md
│   └── BILLING-002-invoice-pdf.md
├── templates/            # Templates for new specs
├── index.json            # Machine-readable index (auto-generated)
└── agents.md             # Instructions for AI agents
```

## Spec Types

### Global Specs

**Location:** `.spectacles/global/*.md`

**Purpose:** Define hard constraints that apply to ALL code in the repository.

**Contains:**
- Tech stack requirements (Python 3.11+, specific frameworks)
- Coding patterns (DO use dependency injection, DON'T use global mutable state)
- Repository layout conventions
- Testing and quality gates
- AI usage rules

**Example frontmatter:**
```yaml
---
id: "GLOBAL-001"
kind: global
title: "Technical Standards"
description: "Core technical requirements for all code"
tags: []
version: "0.1.0"
---
```

**Override rule:** Global specs ALWAYS win. If any other spec conflicts with a global spec, the global spec takes precedence.

### Domain Specs

**Location:** `.spectacles/domains/*.md`

**Purpose:** Capture tribal knowledge about a bounded context.

**Contains:**
- Responsibilities and boundaries
- Dependencies on other domains
- Key entry points (APIs, services, models)
- Data model overview
- Local conventions and pitfalls
- Testing expectations

**Example frontmatter:**
```yaml
---
id: "auth"
kind: domain
domain: "auth"
title: "Authentication Domain"
description: "User authentication and session management"
code_paths:
  - "src/auth/*"
  - "tests/auth/*"
---
```

**Key section:** "5. Local Conventions & Pitfalls" - This is where learned patterns and invariants live. Critical for preventing repeat mistakes.

### Work Specs

**Location:** `.spectacles/work/*.md`

**Purpose:** Define specific tasks with requirements, scope, and acceptance criteria.

**Contains:**
- Context and goal
- Requirements (mandatory checkboxes)
- Non-goals (explicit scope boundaries)
- Inputs and outputs
- Implementation notes (optional guidance)
- Test plan
- Acceptance criteria
- References (similar code, docs, PRs)

**Example frontmatter:**
```yaml
---
id: "AUTH-001"
kind: work
type: "feature"
domain: "auth"
parent_ids: []
code_paths: ["src/auth/login/*"]
upstream_domains: []
downstream_domains: []
status: "todo"
title: "Login form validation"
ai_context:
  include_global: true
  include_domains: ["auth"]
  include_parents: true
  include_upstream: true
  include_downstream: true
---
```

## Hierarchy and Override Rules

```
┌─────────────────────────────────────┐
│         GLOBAL SPECS                │  ← Highest priority
│   (Always apply, non-negotiable)    │
└─────────────────────────────────────┘
                 │
                 ▼ overrides
┌─────────────────────────────────────┐
│         DOMAIN SPECS                │  ← Medium priority
│   (Context-specific conventions)    │
└─────────────────────────────────────┘
                 │
                 ▼ overrides
┌─────────────────────────────────────┐
│          WORK SPECS                 │  ← Lowest priority
│   (Task-specific requirements)      │
└─────────────────────────────────────┘
```

**Rule:** If there's a conflict, higher-level specs win.

**Example:**
- Global: "All APIs must validate input with Pydantic"
- Domain: (no mention of validation)
- Work: "Add a quick endpoint" (no mention of validation)
- Result: You MUST use Pydantic validation, even though the work spec didn't mention it.

## Domain Relationships

Work items can specify upstream and downstream domain dependencies:

- **upstream_domains:** Domains this work reads from or depends on
- **downstream_domains:** Domains this work writes to or impacts

When `spectacles context` is run, it automatically includes:
- The work item's primary domain spec
- All upstream domain specs (if `include_upstream: true`)
- All downstream domain specs (if `include_downstream: true`)

This ensures you have full context about data flow and dependencies.

## The ai_context Section

Controls what specs are automatically loaded:

```yaml
ai_context:
  include_global: true       # Load global specs
  include_domains: ["auth"]  # Load specific domains
  include_parents: true      # Load parent work items
  include_siblings: []       # Load sibling work items
  include_upstream: true     # Load upstream domain specs
  include_downstream: true   # Load downstream domain specs
```

This is how Spectacles ensures deterministic context loading - you don't search for relevant specs, the system tells you exactly what to load.
