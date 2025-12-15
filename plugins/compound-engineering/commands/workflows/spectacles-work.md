---
name: workflows:spectacles-work
description: Work on a Spectacles item using the full Compound Engineering workflow with domain context
argument-hint: "<WORK-ID> [optional focus instructions]"
---

# Spectacles + Compound Engineering Work

Execute a Spectacles work item using Compound Engineering's complete workflow with proper domain context loading.

## Work Item

<work_id> #$ARGUMENTS </work_id>

**If no work ID provided, ask:** "What is the Spectacles work item ID? (e.g., AUTH-001)"

---

## Phase 1: Load Domain Context

**Use the spectacles skill to load context.**

```bash
skill: spectacles
```

Run the context command:
```bash
spectacles context <work_id>
```

**Read ALL files listed under "Specs:"** before proceeding:
- **Global specs** = hard constraints that apply everywhere
- **Domain specs** = conventions and invariants for this bounded context
- **Work spec** = the specific requirements for this task

<context_checklist>
Before proceeding, confirm you have:
- [ ] Read all global specs and noted the constraints
- [ ] Read all domain specs and noted the conventions/pitfalls
- [ ] Read the work spec and understood requirements AND non-goals
- [ ] Inspected key files in the code paths
</context_checklist>

---

## Phase 2: Plan (Research Implementation)

**Always run the plan command** to research HOW to implement:

```
/workflows:plan "<work item title and key requirements from the spec>"
```

This will:
- Research similar patterns in the codebase
- Check framework documentation
- Identify best practices
- Create a plan file at `plans/<title>.md`

**Review the plan output.** If it conflicts with Spectacles specs, the specs win.

Optionally add useful research findings to the work spec's "Implementation Notes" or "References" sections.

---

## Phase 3: Execute

**Run the work command** with the Spectacles work spec:

```
/workflows:work .spectacles/work/<work_id>-*.md
```

This handles:
- Git worktree setup (if preferred)
- Todo list creation
- Systematic task execution
- Continuous testing
- PR creation

<execution_constraints>
During execution, you MUST:
- Follow ALL constraints from global specs (non-negotiable)
- Follow ALL invariants from domain specs (context-specific)
- Follow ALL requirements from the work spec
- NOT exceed scope defined by non-goals

If Compound's execution would violate a Spectacles spec, **the spec wins**.
</execution_constraints>

---

## Phase 4: Review

```
/workflows:review
```

Run the multi-agent code review. Address any critical findings before merging.

---

## Phase 5: Compound & Feedback Loop

### 5.1 Capture Solution

If you solved a non-trivial problem during implementation:

```
/workflows:compound
```

This creates a detailed problem-solution document in `docs/solutions/`.

### 5.2 Evaluate for Domain Promotion

After `/workflows:compound` completes, **evaluate whether the solution should be promoted to Spectacles.**

<promotion_criteria>
**Promote to Spectacles if ANY of these are true:**
- The same mistake could easily happen again in this domain
- The solution represents a non-obvious convention or invariant
- Future work in this domain would benefit from knowing this upfront
- The pattern applies to multiple files or components in the domain

**Do NOT promote if:**
- It was a simple typo or one-off bug
- The fix is obvious and self-explanatory
- It only applies to this specific work item
</promotion_criteria>

### 5.3 Migrate Pattern to Spectacles (If Applicable)

If you determined the pattern should be promoted:

**For project-wide patterns**, add to `.spectacles/global/*.md`:
```markdown
## Constraints

- **[Pattern name]**: [Terse rule - one sentence max]
  - See: `docs/solutions/[category]/[filename].md` for details
```

**For domain-specific patterns**, add to `.spectacles/domains/<domain>.md` under section "5. Local Conventions & Pitfalls":
```markdown
## 5. Local Conventions & Pitfalls

- Local conventions:
  - **[Pattern name]**: [Terse rule - one sentence max]
    - Learned from: `docs/solutions/[category]/[filename].md`
```

<pattern_guidelines>
- Keep the Spectacles entry TERSE (one sentence rule + link to detailed doc)
- The detailed explanation lives in `docs/solutions/`, not in the spec
- The spec is for PREVENTING the issue; the doc is for DEBUGGING it
</pattern_guidelines>

### 5.4 Rebuild Index

If you updated any Spectacles specs:

```bash
spectacles index
```

---

## Phase 6: Wrap Up

1. **Update work spec status**
   ```yaml
   status: "done"  # Change from "todo" or "in_progress"
   ```

2. **Commit all changes**
   Include: code, tests, spec updates, docs/solutions if created

3. **Notify user**
   Summarize what was completed and any patterns promoted to specs

---

## Summary

| Phase | Tool | Purpose |
|-------|------|---------|
| 1. Context | Spectacles | Load WHAT to build + domain constraints |
| 2. Plan | Compound | Research HOW to implement |
| 3. Execute | Compound | Systematic implementation |
| 4. Review | Compound | Multi-agent quality check |
| 5. Compound | Both | Capture solution + auto-promote patterns |
| 6. Wrap Up | Spectacles | Update status, commit |

---

## Troubleshooting

**If `spectacles context` fails:**
- Check that `.spectacles/` directory exists
- Run `spectacles index` to rebuild
- Verify the work item ID is correct with `spectacles list`

**If no work item exists:**
- Ask user to create one: `spectacles new work --domain <domain> --type <type> --title "..."`
- Or use `spectacles find --file <path>` to find related specs

**If domain doesn't exist:**
- Ask user to create it: `spectacles new domain <name> --title "..." --code-path "src/<name>/*"`
