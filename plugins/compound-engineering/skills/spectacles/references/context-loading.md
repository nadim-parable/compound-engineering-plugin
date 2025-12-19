# Context Loading with Spectacles

How to load and interpret domain context from Spectacles specs.

## The Context Command

```bash
spectacles context <WORK-ID>
```

This is the primary command for loading context. It returns:
1. All relevant spec files to read
2. Code paths where work will happen

## Interpreting Output

**Example output:**
```
Work Item: AUTH-001 - Login form validation

Specs:
  - .spectacles/global/technical-standards.md
  - .spectacles/domains/auth.md
  - .spectacles/work/AUTH-001-login-validation.md

Code paths:
  - src/auth/login/*
  - tests/auth/login/*

Upstream domains: (none)
Downstream domains: (none)
```

### Reading Order

1. **Global specs first** - Understand the non-negotiable constraints
2. **Domain specs second** - Understand the context and conventions
3. **Work spec last** - Understand the specific task requirements

This order ensures you understand the constraints before the task details.

## What to Extract from Each Spec Type

### From Global Specs

Extract and remember:
- [ ] Required tech stack and versions
- [ ] Mandatory coding patterns (DOs)
- [ ] Forbidden patterns (DON'Ts)
- [ ] Testing requirements
- [ ] AI-specific rules

### From Domain Specs

Extract and remember:
- [ ] What this domain owns (tables, APIs, models)
- [ ] What this domain depends on
- [ ] Key entry point files to read
- [ ] Local conventions specific to this domain
- [ ] Known pitfalls and invariants
- [ ] Testing expectations

### From Work Specs

Extract and remember:
- [ ] Goal (what problem we're solving)
- [ ] Requirements (mandatory checkboxes)
- [ ] Non-goals (explicit scope boundaries)
- [ ] Inputs and outputs
- [ ] Acceptance criteria (sign-off checklist)

## Handling Missing Context

### If work item doesn't exist

```bash
spectacles find --file path/to/file.py
```

This finds specs related to a file path.

### If no specs found

Ask the user to create one:
```bash
spectacles new work --domain <domain> --type <type> --title "Description"
spectacles index
```

### If domain doesn't exist

Ask the user to create the domain first:
```bash
spectacles new domain <domain-name> --title "Domain Title" --code-path "src/<domain>/*"
spectacles index
```

## After Work is Complete

### Update status

Edit the work spec frontmatter:
```yaml
status: "done"  # was "todo" or "in_progress"
```

### Rebuild index if you changed specs

```bash
spectacles index
```

Always run this if you:
- Modified any spec's frontmatter
- Added new information to domain specs
- Created new work items

## Pattern Promotion

When you solve a non-trivial problem, evaluate if it should become a domain convention:

**Promote if:**
- The mistake could easily happen again
- Future work would benefit from knowing this upfront
- It represents a non-obvious invariant

**Add to domain spec** in section "5. Local Conventions & Pitfalls":
```markdown
- **[Pattern name]**: [Terse rule - one sentence]
  - Learned from: `docs/solutions/[category]/[filename].md`
```

This closes the feedback loop: solutions become constraints that prevent future issues.

