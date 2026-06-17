---
name: octie-dev
description: 'Development loop with Octie task management. Loads project context, queries Octie for ready tasks, implements features with tests, updates task progress, creates audit trail, handles handoffs, and commits changes. Use when: "implement", "dev loop", "continue development", "start working", "begin coding", "octie dev", "development round".'
allowed-tools: Write, Bash, Read, TaskCreate, TaskList, TaskUpdate, TaskGet, TaskDelete
---

# Octie Dev - Development Loop

**Purpose**: Execute a development round — pick up tasks, implement, test, update progress, commit.

**When to use**: Ready to implement features, continuing development, working through task list.
**When NOT to use**: Need to plan tasks first (use `/octie-plan`), need to research (use `/octie-research`), fixing specific bugs (use `/octie-fix`).

---

## Workflow Overview

```
A. Context Loading → B. Task Selection → C. Pre-Modify Analysis → D. Implementation → E. Task Update → F. Audit & Commit → G. Handoff
```

---

## Critical Requirements

**Solve One Task at a Time**: Complete each task fully (all criteria, deliverables) before starting the next.

**Never Use `--unblock`**: This corrupts dependency logic. Always use `octie approve` to unblock dependents.

**Always Use `--format md`**: For token-efficient Octie output.

**Create Audit Trail**: Document what was done, what changed, and why.

---

## Phase A: Context Loading

### Step A1: Verify Octie Availability

```bash
octie --version
octie --help
```

**If unavailable**: Abort — install Octie first (`/octie-install`).

### Step A2: Load Project Context

**Read project structure**:
```bash
ls -la
cat CLAUDE.md 2>/dev/null
ls .memo/memodocs/ 2>/dev/null
```

**Read spec files** (if available):
```bash
cat .memo/memodocs/*.md 2>/dev/null
```

### Step A3: Query All Tasks

```bash
octie list --format md
octie list --graph
```

**Purpose**: Understand full project state before selecting work.

### Step A4: Check Git History

```bash
git log --oneline -10
git diff HEAD~2 HEAD 2>/dev/null
```

**Purpose**: Understand what was done in previous sessions.

---

## Phase B: Task Selection

### Step B1: Find Available Tasks

**Query ready tasks**:
```bash
octie list --status ready --format md
```

**Query in-progress tasks**:
```bash
octie list --status in_progress --format md
```

**Query tasks without blockers**:
```bash
octie find --without-blockers --format md
```

### Step B2: Auto-Select Task

**Selection logic** (follow this exact order):

1. **If `in_progress` tasks exist**: Continue working on those
   ```bash
   octie get <task-id> --format md
   ```
   Check which criteria/deliverables are incomplete.

2. **If no `in_progress` but `ready` tasks exist**: Start highest priority
   ```bash
   # Priority order: top > second > later
   octie list --priority top --status ready --format md
   ```

3. **If all unblocked tasks are `in_review`**: Auto-approve if tests pass
   ```bash
   octie approve <task-id>
   ```

4. **If no tasks available**: Report and ask user for next steps.

### Step B3: Get Task Details

```bash
octie get <task-id> --format md
```

**Extract**:
- Title and description
- Success criteria (what to verify)
- Deliverables (what to produce)
- Related files
- Need-fix items (issues from previous reviews)

---

## Phase C: Pre-Modify Analysis

### Step C1: Present Current System

**Before making any changes**, show the current state:

```markdown
## Pre-Modify Analysis: <Task Title>

### Current System Flow
[Component A] → [Component B] → [Component C]

### Files to Modify
| File | What Changes |
|------|--------------|
| src/file1.ts | Add function X |
| src/file2.ts | Update integration |

### Proposed Approach
1. Change 1 — rationale
2. Change 2 — rationale

### Impact
- Direct: [affected components]
- Tests: [verification needed]
```

### Step C2: Identify Root Cause (for bugs)

**If fixing a bug**:
1. Trace the error path
2. Find the actual root cause (not just symptoms)
3. Determine minimal fix

### Step C3: Plan Implementation

**For each deliverable**, outline:
- What file to create/modify
- What function/component to add
- How it integrates with existing code

---

## Phase D: Implementation

### Step D1: Implement Changes

**Follow the plan from Phase C**:
1. Create/modify files as specified in deliverables
2. Follow existing code patterns
3. Add error handling
4. Write clean, documented code

### Step D2: Write Tests

**For each deliverable**, write corresponding tests:
- Unit tests for functions/classes
- Integration tests for API endpoints
- Edge case coverage

### Step D3: Verify Success Criteria

**For each success criterion**, verify it passes:
```bash
# Run tests
npm test 2>/dev/null || pytest 2>/dev/null || go test ./...

# Check specific criteria
# e.g., "Returns 200 status code" → test the endpoint
# e.g., "Response time < 200ms" → benchmark the function
```

### Step D4: Fix Issues

**If tests fail or criteria not met**:
1. Identify the issue
2. Fix the root cause
3. Re-verify

---

## Phase E: Task Update

### Step E1: Check Update Command Options

```bash
octie update <task-id> -h
```

### Step E2: Mark Criteria Complete

**For each verified success criterion**:
```bash
octie update <task-id> --complete-criterion <criterion-id>
```

**Mark multiple at once**:
```bash
octie update <task-id> --complete-criterion <id1>,<id2>,<id3>
```

### Step E3: Mark Deliverables Complete

**For each produced deliverable**:
```bash
octie update <task-id> --complete-deliverable <deliverable-id>
```

### Step E4: Mark Need-Fix Items Complete

**If there were issues from previous reviews**:
```bash
octie update <task-id> --complete-need-fix <need-fix-id>
```

### Step E5: Add Notes

**Document implementation details**:
```bash
octie update <task-id> --notes "Implemented using X pattern because Y. Tested with Z approach."
```

### Step E6: Add New Issues (if discovered)

**If implementation revealed new issues**:
```bash
# From code review
octie update <task-id> --add-need-fix "<issue>" --need-fix-source review --need-fix-file "path/to/file.ts"

# From runtime error
octie update <task-id> --add-need-fix "<error>" --need-fix-source runtime

# From regression
octie update <task-id> --add-need-fix "<broken feature>" --need-fix-source regression
```

### Step E7: Verify Auto-Status Transition

**When ALL criteria + deliverables are complete**, status automatically moves to `in_review`.

```bash
octie get <task-id> --format md
```

**Confirm**: Status shows `in_review`.

---

## Phase F: Audit & Commit

### Step F1: Generate Audit File

**Create audit documentation**:
```markdown
## Development Audit: <Task Title>

**Date**: <date>
**Task ID**: <task-id>

### Changes Made
- File 1: <what changed>
- File 2: <what changed>

### Success Criteria Verification
- [x] Criterion 1 — verified via <method>
- [x] Criterion 2 — verified via <method>

### Deliverables Produced
- [x] Deliverable 1 — <file path>
- [x] Deliverable 2 — <file path>

### Testing
- Unit tests: X passing
- Integration tests: Y passing
- Edge cases covered: Z

### Notes
<implementation notes>
```

**Save to**: `.memo/memodocs/audit_<date>_<task-id>.md`

### Step F2: Generate Commit Message

**Use conventional commits**:
```
<type>(<scope>): <description>

Types: feat, fix, refactor, test, docs, chore
```

**Examples**:
```
feat(auth): implement JWT login endpoint
fix(api): handle null user in getUserById
test(auth): add unit tests for login validation
```

### Step F3: Execute Git Commands

```bash
git add .
git commit -m "<generated message>"
```

### Step F4: Approve Task (if ready)

**If task is in `in_review` and all tests pass**:
```bash
octie approve <task-id>
```

**This unblocks dependent tasks automatically**.

---

## Phase G: Handoff

### Step G1: Check for Next Tasks

```bash
octie list --status ready --format md
```

**If more tasks available**: Continue with Phase B (Task Selection).

**If no more tasks**: Report completion.

### Step G2: Generate Session Summary

```markdown
## Development Session Complete

**Tasks Completed**: X
**Tasks In Progress**: Y
**Tasks Ready**: Z

**Next Steps**:
- Continue with `/octie-dev` for remaining tasks
- Use `/octie-fix` for any issues discovered
- Use `/octie-plan` to add new tasks

**Audit Files Created**:
- .memo/memodocs/audit_<date>_<task1>.md
- .memo/memodocs/audit_<date>_<task2>.md
```

### Step G3: Push to Remote

```bash
git push 2>/dev/null
```

---

## Octie Command Quick Reference

| Operation | Command |
|-----------|---------|
| List all tasks | `octie list --format md` |
| List ready tasks | `octie list --status ready --format md` |
| List in-progress | `octie list --status in_progress --format md` |
| Get task details | `octie get <id> --format md` |
| Complete criterion | `octie update <id> --complete-criterion <criterion-id>` |
| Complete deliverable | `octie update <id> --complete-deliverable <deliverable-id>` |
| Complete need-fix | `octie update <id> --complete-need-fix <need-fix-id>` |
| Add need-fix (review) | `octie update <id> --add-need-fix "<issue>" --need-fix-source review` |
| Add need-fix (runtime) | `octie update <id> --add-need-fix "<error>" --need-fix-source runtime` |
| Add notes | `octie update <id> --notes "<text>"` |
| Approve task | `octie approve <id>` |
| Validate graph | `octie graph validate` |
| **NEVER use** | `octie update <id> --unblock <id>` (corrupts dependency logic) |

---

## Edge Cases

| Scenario | Handling |
|----------|----------|
| No ready tasks | Check `octie list --status blocked`, may need to unblock |
| All tasks completed | Report completion, suggest `/octie-plan` for new tasks |
| Task fails criteria | Don't commit, fix issues first |
| Git has no commits | Skip git diff, continue |
| Need-fix items exist | Fix issues before approving |
| Multiple tasks same priority | Pick first one — don't ask user |

---

## Best Practices

1. **One task at a time** — Complete fully before starting next
2. **Always use `--format md`** — Token-efficient output
3. **Present pre-modify workflow** — Show current system before changes
4. **Verify all criteria** — Don't mark complete without verification
5. **Create audit trail** — Document what was done
6. **Use `octie approve`** — Never use `--unblock`
7. **Commit per task** — Clean git history
8. **Push to remote** — Don't rely on local-only repos

---

## ⚠️ Git Safety Caution

**CRITICAL**: This skill commits changes to git. Always verify repository location.

### Before ANY Git Operation

```bash
git rev-parse --git-dir
pwd
git log --oneline -5
git remote -v
git status
```

### Mandatory Remote Backup

**ALWAYS push to remote within 1 hour of first commit.**

```bash
git remote add origin <github-url>
git push -u origin main
```

**Never rely on local-only repositories** — catastrophic loss is irreversible.

---

## ⚠️ REMINDER: Tasks System Required

**CRITICAL**: Use Claude Code Tasks System throughout. Break down EVERY step into granular, trackable sub-tasks.
