---
name: spectacles
description: Load domain context from Spectacles specs before working on tasks. Use when working on a Spectacles work item or when you need to understand domain constraints, conventions, and requirements for a bounded context.
---

<objective>
Spectacles provides spec-as-code domain context for AI agents. This skill teaches how to load and interpret Spectacles specs to understand WHAT to build and the constraints to follow.

Spectacles organizes knowledge into three layers:
- **Global specs**: Hard constraints that apply everywhere (tech stack, coding standards)
- **Domain specs**: Bounded context knowledge (auth, billing conventions and invariants)
- **Work specs**: Task requirements, acceptance criteria, and scope boundaries
</objective>

<when_to_use>
## When to Use This Skill

Use this skill when:
- Working on a Spectacles work item (e.g., AUTH-001, BILLING-003)
- Need to understand domain constraints before starting implementation
- Want to load all relevant context (global specs, domain specs, work requirements)
- Working in a repository that has a `.spectacles/` directory

Do NOT use this skill when:
- The repository has no `.spectacles/` directory
- Working on ad-hoc tasks without a spec
- Just exploring code without a specific work item
</when_to_use>

<quick_start>
To load context for a work item:

```bash
spectacles context <WORK-ID>
```

This outputs all relevant specs and code paths. **Read ALL files listed under "Specs:"** before starting work.
</quick_start>

<essential_principles>
## Spec Hierarchy (Override Rules)

**Global specs override domain specs, which override work specs.**

If a work spec suggests something that conflicts with a global spec, the global spec wins. For example:
- Global: "Use Python 3.11+"
- Work: Doesn't mention Python version
- Result: You MUST use Python 3.11+

## Specs Define WHAT, Not HOW

Specs tell you:
- What requirements must be satisfied
- What constraints cannot be violated
- What scope boundaries exist (non-goals)

Specs do NOT dictate implementation details unless explicitly stated. Figure out HOW to implement within the constraints.

## Context is Deterministic

Unlike searching through docs, `spectacles context` gives you exactly what you need. No guessing which files matter - the spec system has already linked work items to their relevant domains.
</essential_principles>

<process>
## Loading Context

1. **Run the context command**
   ```bash
   spectacles context <WORK-ID>
   ```

2. **Read the output structure**
   The command outputs:
   ```
   Specs:
     - .spectacles/global/technical-standards.md
     - .spectacles/domains/auth.md
     - .spectacles/work/AUTH-001-login-validation.md
   
   Code paths:
     - src/auth/*
     - tests/auth/*
   ```

3. **Load ALL spec files**
   Read every file listed under "Specs:" - these define the constraints.

4. **Inspect code paths**
   The code paths show where work will likely occur. Familiarize with existing patterns.

## Interpreting Specs

### Global Specs
Location: `.spectacles/global/*.md`

Contains:
- Tech stack requirements (languages, frameworks, versions)
- Coding patterns (DO and DON'T)
- Repository layout conventions
- Testing requirements
- AI usage rules

**These are non-negotiable.** If about to do something that violates a global spec, STOP and warn the user.

### Domain Specs
Location: `.spectacles/domains/*.md`

Contains:
- Domain responsibilities and boundaries
- Dependencies on other domains
- Key entry points and files
- Data model overview
- Local conventions and pitfalls (tribal knowledge)
- Testing expectations

**Section 5 (Local Conventions & Pitfalls) is critical** - this is where learned patterns and invariants live.

### Work Specs
Location: `.spectacles/work/*.md`

Contains:
- Context and goal
- Requirements (checkboxes to satisfy)
- Non-goals (explicit scope boundaries)
- Inputs and outputs
- Implementation notes (optional guidance)
- Test plan
- Acceptance criteria

**Requirements are mandatory. Non-goals are boundaries that must not be crossed.**

## When Done

1. **Update work spec status**
   Change `status: "todo"` to `status: "done"` in the work spec frontmatter.

2. **Rebuild index if specs changed**
   ```bash
   spectacles index
   ```
</process>

<other_commands>
## Additional Spectacles Commands

**Find specs for a file:**
```bash
spectacles find --file path/to/file.py
```

**List all specs:**
```bash
spectacles list
```

**Show a specific spec:**
```bash
spectacles show AUTH-001
```

**View domain dependency graph:**
```bash
spectacles graph
spectacles graph billing  # For specific domain
```

**Create new work item:**
```bash
spectacles new work --domain auth --type feature --title "Add login validation"
```
</other_commands>

<success_criteria>
Context is properly loaded when:
- ALL files listed under "Specs:" from `spectacles context` have been read
- The global constraints that apply are understood
- The domain conventions and invariants are understood
- The work item requirements and non-goals are understood
- Can articulate what must be done and what must NOT be done
</success_criteria>

