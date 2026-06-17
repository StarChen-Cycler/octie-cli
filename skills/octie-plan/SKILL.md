---
name: octie-plan
description: 'Create and manage Octie tasks with proper atomic task structure. Initializes octie project, creates tasks with success criteria and deliverables, manages dependencies and blockers, validates graph integrity. Use when: "plan tasks", "create octie tasks", "initialize octie", "task planning", "create task structure", "octie plan", "add tasks", "organize tasks".'
allowed-tools: Bash, Read, Write
---

# Octie Plan - Task Creation & Planning

**Purpose**: Create well-structured, atomic Octie tasks with proper dependencies and success criteria.

**When to use**: Starting a new project, adding new tasks, organizing task structure, planning work.
**When NOT to use**: Implementing tasks (use `/octie-dev`), fixing bugs (use `/octie-fix`), deep research needed (use `/octie-research` first).

---

## Workflow Overview

```
A. Initialize Project → B. Read Context → C. Create Tasks → D. Wire Dependencies → E. Validate Graph → F. Report
```

---

## Critical Rules

| Rule | Description |
|------|-------------|
| **Atomic Tasks** | 2-8 hours scope, single objective, verifiable |
| **Quantitative Criteria** | Success criteria must be measurable (yes/no, pass/fail) |
| **Specific Deliverables** | Concrete outputs (files, APIs, tests, docs) |
| **Twin Rule** | `--blockers` AND `--dependencies` MUST be provided together |
| **Short IDs** | Use first 7-8 characters of UUID |
| **Auto Status** | Never set status manually — it's derived from item completion |

---

## Phase A: Initialize Project

### Step A1: Check if Octie Project Exists

```bash
ls -la .octie/ 2>/dev/null
```

**If .octie/ doesn't exist**:
```bash
octie init
```

**If .octie/ exists**: Skip to Phase B.

### Step A2: Verify Octie Availability

```bash
octie --version
octie --help
```

**If unavailable**: Abort — use `/octie-install` first.

---

## Phase B: Read Context

### Step B1: Read Existing Tasks

```bash
octie list --format md
octie list --graph
```

**Purpose**: Understand current project state before creating new tasks.

### Step B2: Read Project Requirements

**If spec files exist**:
```bash
ls .memo/memodocs/ 2>/dev/null
cat .memo/memodocs/*.md 2>/dev/null
```

**If no specs**: Ask user for project requirements directly.

### Step B3: Understand Dependencies

```bash
octie find --without-blockers --format md
octie find --orphans --format md
```

**Purpose**: Identify which tasks are ready and which are disconnected.

---

## Phase C: Create Tasks

### Step C1: Check Create Command Options

```bash
octie create -h
```

**Key flags**:
- `--title` — Short, action-verb-based title
- `--description` — Detailed explanation (min 50 chars)
- `--success-criterion` — Quantitative criteria (up to 10)
- `--deliverable` — Specific outputs (up to 10)
- `--related-files` — Relevant file paths
- `--priority` — `top`, `second`, or `later`
- `--blockers` — Task IDs that block this task
- `--dependencies` — Why blockers are needed

### Step C2: Design Task Structure

**For each task, define**:

1. **Title**: `<Action Verb> <Object> <Context>`
   - ✅ "Implement JWT login endpoint"
   - ✅ "Add unit tests for user service"
   - ❌ "Auth stuff"
   - ❌ "Fix things"

2. **Description**: What + Why + How (min 50 chars)

3. **Success Criteria**: Quantitative, verifiable
   - ✅ "Returns 200 status code"
   - ✅ "Response time < 200ms"
   - ✅ "Test coverage > 90%"
   - ❌ "Works well"
   - ❌ "Fast and reliable"

4. **Deliverables**: Concrete outputs
   - ✅ "src/api/auth/login.ts handler"
   - ✅ "tests/auth/login.test.ts"
   - ❌ "Code"
   - ❌ "Documentation"

5. **Priority**: `top` > `second` > `later`

### Step C3: Create Each Task

**Basic task**:
```bash
octie create \
  --title "Implement JWT login endpoint" \
  --description "Create POST /api/auth/login endpoint. Validates credentials against database, returns JWT token with 24hr expiration." \
  --success-criterion "Returns 200 with JWT for valid credentials" \
  --success-criterion "Returns 401 for invalid credentials" \
  --success-criterion "Response time < 200ms" \
  --deliverable "src/api/auth/login.ts" \
  --deliverable "tests/api/auth/login.test.ts" \
  --related-files src/auth/,src/routes/ \
  --priority top
```

**Task with blockers** (must include both flags):
```bash
octie create \
  --title "Implement password reset flow" \
  --description "Password reset via email token. User requests reset, receives email with token, resets password." \
  --success-criterion "Reset token expires after 1 hour" \
  --success-criterion "Email sent within 30 seconds" \
  --deliverable "src/api/auth/reset-password.ts" \
  --deliverable "src/services/email.ts" \
  --priority second \
  --blockers <login-task-id> \
  --dependencies "Needs JWT login for token generation patterns"
```

### Step C4: Record Task IDs

**After each creation**, note the task ID for dependency wiring:
```bash
# Save short ID (first 7-8 chars)
TASK_ID="<short-uuid>"
```

---

## Phase D: Wire Dependencies

### Step D1: Check Wire Command Options

```bash
octie wire -h
```

### Step D2: Wire Task Chains

**Insert task between existing tasks**:
```bash
octie wire <new-task-id> \
  --after <predecessor-id> \
  --before <successor-id> \
  --dep-on-after "Needs API spec from predecessor" \
  --dep-on-before "Successor needs models from this task"
```

### Step D3: Add Blockers to Existing Tasks

```bash
octie update <task-id> \
  --blockers <blocker-task-id> \
  --dependency-explanation "Requires blocker completion first"
```

**⚠️ TWIN RULE**: Always provide both `--blockers` AND `--dependency-explanation` together.

---

## Phase E: Validate Graph

### Step E1: Run All Validation Commands

```bash
octie graph validate
octie graph cycles
octie find --orphans
octie list --graph
```

### Step E2: Check for Issues

| Issue | Command | Fix |
|-------|---------|-----|
| Cycles detected | `octie graph cycles` | Remove one blocker in the cycle |
| Orphan tasks | `octie find --orphans` | Wire orphans to main graph |
| No ready tasks | `octie find --without-blockers` | Check blocker logic |

### Step E3: Verify Ready Tasks

```bash
octie list --status ready --format md
octie find --without-blockers --format md
```

**Confirm**: At least one task is ready to start.

---

## Phase F: Report

### Step F1: Generate Summary

```markdown
## Octie Plan: Tasks Created

**Tasks**: X total
- Top Priority: Y
- Second Priority: Z
- Later Priority: W

**Graph Status**: Valid / Invalid
**Ready Tasks**: X tasks without blockers

**Task List**:
| ID | Title | Priority | Status |
|----|-------|----------|--------|
| xxx | Task 1 | top | ready |
| xxx | Task 2 | second | blocked |

**Next Steps**:
- Use `/octie-dev` to begin implementation
- Use `/octie-fix` to prepare specific bug fixes
```

---

## Task Design Patterns

### Pattern 1: Feature Implementation

```bash
octie create \
  --title "Implement <feature> <component>" \
  --description "Create <component> that <does what>. Uses <technology> for <reason>." \
  --success-criterion "<Measurable outcome 1>" \
  --success-criterion "<Measurable outcome 2>" \
  --deliverable "src/<path>/<file>.ts" \
  --deliverable "tests/<path>/<file>.test.ts" \
  --priority top
```

### Pattern 2: Bug Fix

```bash
octie create \
  --title "Fix <issue> in <component>" \
  --description "<What's broken>. Root cause: <cause>. Fix: <approach>." \
  --success-criterion "Bug no longer reproduces" \
  --success-criterion "Regression test passes" \
  --deliverable "src/<path>/<file>.ts (fixed)" \
  --deliverable "tests/<path>/<file>.test.ts (regression)" \
  --priority top
```

### Pattern 3: Testing

```bash
octie create \
  --title "Add tests for <component>" \
  --description "Write unit and integration tests for <component>. Cover <scenarios>." \
  --success-criterion "Test coverage > 90%" \
  --success-criterion "All edge cases covered" \
  --deliverable "tests/<path>/<file>.test.ts" \
  --priority second
```

### Pattern 4: Documentation

```bash
octie create \
  --title "Document <feature> API" \
  --description "Create API documentation for <feature>. Include examples and error codes." \
  --success-criterion "All endpoints documented" \
  --success-criterion "Examples included" \
  --deliverable "docs/api/<feature>.md" \
  --priority later
```

---

## Error Handling

| Error | Cause | Resolution |
|-------|-------|------------|
| "violates atomic task" | Task too large or vague | Split into smaller tasks |
| "twin required" | Missing `--dependencies` | Add `--dependencies` with `--blockers` |
| "Cycle detected" | Circular dependency | Remove one blocker |
| "Task not found" | Invalid ID | Use 7+ char UUID prefix |
| "Duplicate title" | Task with same title exists | Add context to title |

---

## Quick Reference

| Command | Purpose |
|---------|---------|
| `octie init` | Initialize project |
| `octie create --title "..." --description "..." --success-criterion "..." --deliverable "..."` | Create task |
| `octie list --format md` | List all tasks |
| `octie list --graph` | View dependency graph |
| `octie list --status ready` | List ready tasks |
| `octie get <id>` | View task details |
| `octie wire <id> --after <id> --before <id>` | Insert task in chain |
| `octie update <id> --blockers <id> --dependency-explanation "..."` | Add blocker |
| `octie merge <src> <target>` | Combine tasks |
| `octie graph validate` | Check graph integrity |
| `octie graph cycles` | Detect circular deps |
| `octie find --orphans` | Find disconnected tasks |

---

## ⚠️ REMINDER: Tasks System Required

**CRITICAL**: Use Claude Code Tasks System throughout. Break down EVERY step into granular, trackable sub-tasks.
