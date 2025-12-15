# Spectacles CLI Commands

Complete reference for all `spectacles` CLI commands.

## Core Commands

### spectacles context

Get all relevant context for a work item.

```bash
spectacles context <WORK-ID>
```

**Output:**
```
Specs:
  - .spectacles/global/technical-standards.md
  - .spectacles/domains/auth.md
  - .spectacles/work/AUTH-001-login-validation.md

Code paths:
  - src/auth/*
  - tests/auth/*
```

**Options:**
- `--project-root PATH` - Explicit project root (useful in scripts)

### spectacles find

Find specs related to a file path.

```bash
spectacles find --file path/to/file.py
```

Use when you don't know the work item ID but know which file you're working on.

### spectacles list

List all specs in the project.

```bash
spectacles list
```

**Options:**
- `--project-root PATH` - Explicit project root

### spectacles show

Display a specific spec's content.

```bash
spectacles show <SPEC-ID>
```

### spectacles graph

View domain dependency relationships.

```bash
# Show all domains
spectacles graph

# Show specific domain's neighbors
spectacles graph billing
```

**Output:**
```
billing
├── Upstream (Depends On)
│   └── auth (via BILLING-001, BILLING-003)
└── Downstream (Impacts)
    └── analytics (via BILLING-002)
```

### spectacles index

Rebuild the index after spec changes.

```bash
spectacles index
```

**Always run after:**
- Creating new specs
- Modifying spec frontmatter
- Changing domain relationships

**Options:**
- `--project-root PATH` - Explicit project root

## Creation Commands

### spectacles init

Initialize a new `.spectacles/` directory.

```bash
spectacles init
spectacles init --with-samples  # Include starter specs
```

### spectacles new global

Create a new global spec.

```bash
spectacles new global "coding-standards"
```

### spectacles new domain

Create a new domain spec.

```bash
spectacles new domain auth
spectacles new domain auth --title "Authentication Service" --code-path "src/auth/*"
```

### spectacles new work

Create a new work item.

```bash
# Basic
spectacles new work --domain auth --type feature --title "Login validation"

# With relationships
spectacles new work \
  --domain billing \
  --type task \
  --title "Invoice generation" \
  --upstream auth \
  --downstream analytics \
  --code-path "src/billing/invoices/*"

# Interactive mode
spectacles new work --interactive
```

**Work types:** `epic`, `feature`, `task`, `bug`, `refactor`

## Project Root Detection

Spectacles automatically finds the project root by searching upward for a `.spectacles/` directory. This means:

- Commands work from any subdirectory
- The nearest `.spectacles/` is used
- Use `--project-root` in scripts for explicit control

```bash
# In a script
spectacles index --project-root /path/to/project
```
