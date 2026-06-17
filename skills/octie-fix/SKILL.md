---
name: octie-fix
description: 'Feature debug and bug fixture preparation. Analyzes bug reports or feature requests, investigates root cause, creates Octie tasks with proper success criteria and deliverables, presents pre-modify workflow, and prepares implementation plan. Use when: "fix bug", "debug issue", "add feature", "new feature", "bug fix", "prepare fix", "investigate error", "octie fix".'
allowed-tools: Write, Bash, Read, TaskCreate, TaskList, TaskUpdate, TaskGet, TaskDelete
---

# Octie Fix - Bug Fix & Feature Preparation

**Purpose**: Investigate bugs/features, create properly structured Octie tasks with fix approach.

**When to use**: Bug reports, feature requests, debugging issues, preparing implementation tasks.
**When NOT to use**: Implementing tasks (use `/octie-dev`), planning entire project (use `/octie-plan`), deep research needed (use `/octie-research`).

---

## Workflow Overview

```
A. Context Loading → B. Issue Investigation → C. Root Cause Analysis → D. Task Creation → E. User Confirmation
```

---

## Critical Requirements

**Verify Octie First**: Always run `octie --version` before proceeding.

**Root Cause Analysis**: Find the actual problem, not just symptoms.

**Quantitative Criteria**: Success criteria must be measurable.

**Pre-Modify Workflow**: Show current system before proposing changes.

---

## Phase A: Context Loading

### Step A1: Verify Octie Availability

```bash
octie --version
octie --help
```

**If unavailable**: Abort — use `/octie-install` first.

### Step A2: Load Project Context

**Read project structure**:
```bash
ls -la
cat CLAUDE.md 2>/dev/null
```

**Read spec files** (if available):
```bash
ls .memo/memodocs/ 2>/dev/null
cat .memo/memodocs/*.md 2>/dev/null
```

### Step A3: Query Existing Tasks

```bash
octie list --format md
octie list --graph
```

**Purpose**: Understand current state, avoid duplicates.

### Step A4: Check Recent Changes

```bash
git log --oneline -10
git diff HEAD~3 HEAD 2>/dev/null
```

**Purpose**: Understand what changed recently that might be related.

---

## Phase B: Issue Investigation

### Step B1: Capture the Issue

**Input**: User provides bug report or feature request.

**For bugs, capture**:
- What's broken (expected vs actual behavior)
- Reproduction steps
- Error messages or logs
- When it started happening

**For features, capture**:
- What functionality to add
- User value / use case
- Acceptance criteria
- Dependencies on existing features

### Step B2: Locate Relevant Files

**Search for affected code**:
```bash
# Search for error messages
grep -r "error message" src/ 2>/dev/null

# Search for related functions
grep -r "functionName" src/ 2>/dev/null

# Search for related files
find . -name "*related*" -type f 2>/dev/null
```

**Use symbols** (if `.memo/memosymbols.txt` exists):
```
Format: line:tag:name (e.g., 4:fun:App)
Tags: fun=function, var=variable, com=command
```

### Step B3: Read Affected Code

**Read the files identified in Step B2**:
1. The file where the bug occurs
2. Upstream dependencies (types, models, utilities)
3. Related test files
4. Configuration files

### Step B4: Trace the Issue

**For bugs**:
1. Start from the error/symptom
2. Trace backwards through the call stack
3. Identify where the actual problem is
4. Determine what's missing or wrong

**For features**:
1. Identify where the feature should be added
2. Check existing patterns in similar features
3. Determine what needs to be created/modified

---

## Phase C: Root Cause Analysis

### Step C1: Identify the Root Cause

**For bugs**:
| Level | Question |
|-------|----------|
| Symptom | What error/symptom is observed? |
| Trigger | What action causes it? |
| Location | What file/function is responsible? |
| Root Cause | What's actually wrong? |
| Fix | What minimal change fixes it? |

**For features**:
| Level | Question |
|-------|----------|
| Need | What user need does this address? |
| Scope | What components are affected? |
| Pattern | What existing pattern should be followed? |
| Approach | What's the implementation strategy? |

### Step C2: Find the Minimal Fix

**Principles**:
- Address root cause, not symptoms
- Follow existing patterns in the codebase
- Minimize changes to reduce regression risk
- Make the smallest change that fully fixes the issue

### Step C3: Assess Impact

**Impact analysis**:
- Direct impact: What components are affected?
- Side effects: What else might change?
- Tests needed: What verification is required?
- Risk level: How likely is regression?

---

## Phase D: Task Creation

### Step D1: Design Success Criteria

**For bugs**:
- Bug no longer reproduces
- Root cause addressed (not just symptom)
- Regression test passes
- No side effects introduced

**For features**:
- Functionality works as specified
- Edge cases handled
- Tests written and passing
- Documentation updated (if applicable)

### Step D2: Design Deliverables

**For bugs**:
- Fixed source file(s)
- Regression test(s)
- Updated documentation (if behavior changes)

**For features**:
- New source file(s)
- Modified source file(s)
- Test file(s)
- Documentation

### Step D3: Check Create Command Options

```bash
octie create -h
```

### Step D4: Create the Task

**Bug fix task**:
```bash
octie create \
  --title "Fix <issue> in <component>" \
  --description "<What's broken>. Root cause: <cause>. Fix: <approach>. Steps to reproduce: <steps>." \
  --success-criterion "Bug no longer reproduces" \
  --success-criterion "Regression test passes" \
  --success-criterion "No side effects on <related component>" \
  --deliverable "src/<path>/<file>.ts (fixed)" \
  --deliverable "tests/<path>/<file>.test.ts (regression)" \
  --related-files src/<path>/,tests/<path>/ \
  --priority top
```

**Feature task**:
```bash
octie create \
  --title "Implement <feature> in <component>" \
  --description "<What to build>. User value: <why>. Approach: <how>." \
  --success-criterion "<Measurable outcome 1>" \
  --success-criterion "<Measurable outcome 2>" \
  --success-criterion "Test coverage > 90%" \
  --deliverable "src/<path>/<file>.ts" \
  --deliverable "tests/<path>/<file>.test.ts" \
  --related-files src/<path>/ \
  --priority top
```

### Step D5: Add Blockers (if needed)

**If task depends on existing tasks**:
```bash
octie create \
  --title "<Task Title>" \
  --description "..." \
  --success-criterion "..." \
  --deliverable "..." \
  --blockers <existing-task-id> \
  --dependency-explanation "Requires <existing-task> completion for <reason>" \
  --priority top
```

**⚠️ TWIN RULE**: Always provide both `--blockers` AND `--dependency-explanation`.

---

## Phase E: User Confirmation

### Step E1: Present the Analysis

```markdown
## Fix Analysis: <Issue Title>

### Root Cause
<What's actually wrong>

### Current Behavior (Bug) / Current State (Feature)
```code
// Relevant code snippet
```

### Proposed Fix / Implementation
1. Change 1 — what and why
2. Change 2 — what and why

### Files to Modify
| File | Changes |
|------|---------|
| src/file1.ts | Fix/add X |
| tests/file1.test.ts | Add regression test |

### Impact
- Direct: [affected components]
- Side effects: [potential issues]
- Tests needed: [verification]
```

### Step E2: Show Created Task

```bash
octie get <task-id> --format md
```

**Present**:
```markdown
## Octie Task Created

**Title**: <title>
**ID**: <task-id>
**Priority**: <priority>

**Success Criteria**:
- [ ] Criterion 1
- [ ] Criterion 2

**Deliverables**:
- Deliverable 1
- Deliverable 2

**Related Files**: file1, file2
```

### Step E3: Confirm with User

**Prompt**:
```
Task created and ready for implementation. Proceed? (y/n)
```

**Behavior**:
- `y` → Report next steps
- `n` → Ask what needs modification, update task, re-confirm

### Step E4: Report Next Steps

```markdown
## Next Steps

**Task**: <title> (ID: <task-id>)

To implement:
1. Use `/octie-dev` to begin development
2. Follow the pre-modify workflow analysis
3. Verify all success criteria
4. Commit and approve

**Audit trail**: Task progress will be tracked in Octie.
```

---

## Investigation Patterns

### Pattern 1: Runtime Error

```bash
# Find error in logs
grep -r "Error" logs/ 2>/dev/null

# Trace error path
# 1. Find where error is thrown
# 2. Find what triggers it
# 3. Find root cause
```

### Pattern 2: Logic Bug

```bash
# Find the function
grep -r "functionName" src/

# Read the function
# 1. Understand expected behavior
# 2. Trace actual behavior
# 3. Find the logic error
```

### Pattern 3: Integration Issue

```bash
# Find integration points
grep -r "import.*from" src/service.ts

# Check each integration
# 1. Verify API contract
# 2. Check error handling
# 3. Verify data transformation
```

### Pattern 4: Performance Issue

```bash
# Find slow operations
# 1. Profile the code
# 2. Identify bottleneck
# 3. Determine optimization approach
```

---

## Edge Cases

| Scenario | Handling |
|----------|----------|
| Octie unavailable | Abort — use `/octie-install` |
| Bug cannot be reproduced | Ask user for more details, add logging |
| Fix spans multiple tasks | Create parent task with subtasks |
| Bug is critical | Set priority to `top` |
| Root cause unclear | Investigate more, ask user for context |
| Related to recent change | Check git history, may need revert |

---

## Error Handling

| Error | Cause | Resolution |
|-------|-------|------------|
| "violates atomic task" | Task too large | Split into smaller tasks |
| "twin required" | Missing dependency explanation | Add `--dependency-explanation` |
| "Task not found" | Invalid ID | Use 7+ char UUID prefix |
| "Duplicate title" | Task exists | Add context to title |

---

## Best Practices

1. **Verify Octie first** — Always run `octie --version`
2. **Find root cause** — Don't just fix symptoms
3. **Minimal fix** — Smallest change that works
4. **Follow patterns** — Match existing code style
5. **Quantitative criteria** — Measurable success
6. **Pre-modify workflow** — Show current system
7. **User confirmation** — Always get approval
8. **Document findings** — Save investigation notes

---

## ⚠️ REMINDER: Tasks System Required

**CRITICAL**: Use Claude Code Tasks System throughout. Break down EVERY step into granular, trackable sub-tasks.
