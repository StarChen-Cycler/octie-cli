---
name: octie-refine
description: 'Refine and restructure Octie task graph. Rearrange dependencies, merge small tasks, split oversized tasks, remove unnecessary blockers, validate graph integrity. Use when: "rearrange tasks", "modify dependencies", "restructure octie graph", "change task flow", "refine octie tasks", "update blockers", "reorganize octie", "clean up tasks", "optimize task graph".'
allowed-tools: Bash, Read, Write
---

# Octie Refine - Task Graph Refinement

**Purpose**: Modify and optimize Octie task dependencies after initial planning.

**When to use**: Restructuring task flow, fixing dependency issues, reorganizing priorities, splitting/merging tasks, cleaning up graph.
**When NOT to use**: Initial task creation (use `/octie-plan`), implementation coding (use `/octie-dev`), creating new tasks (use `/octie-plan`).

---

## Workflow Overview

```
A. Read Context → B. Analyze Graph → C. Refine (1.Remove Blockers → 2.Merge/Split → 3.Rearrange) → D. Validate → E. Report
```

**⚠️ CRITICAL SEQUENCE**: Refinement MUST follow this order:
1. **Remove unnecessary blockers** — Clean graph first, parallelize where possible
2. **Merge or split tasks** — Align atomicity second
3. **Rearrange dependencies** — Wire/reorder third
4. **Validate graph** — Final step, after all changes

---

## Critical Requirements

**⚠️ MUST**: Read existing tasks before any modifications. Understand current state first.

**⚠️ MUST**: Follow refinement sequence: Remove blockers → Merge/Split → Rearrange → Validate.

**⚠️ MUST**: Validate graph AFTER all refinement operations complete.

**⚠️ NEVER**: Modify task status directly. Status is auto-calculated.

**⚠️ NEVER**: Create new tasks here. Use `/octie-plan` for new task creation.

---

## Phase A: Read Context

### Step A1: Verify Octie Availability

```bash
octie --version
octie --help
```

**If unavailable**: Abort — use `/octie-install` first.

### Step A2: Read Project Context

```bash
ls -la
cat CLAUDE.md 2>/dev/null
ls .memo/memodocs/ 2>/dev/null
```

**If spec files exist**, read them:
```bash
cat .memo/memodocs/*.md 2>/dev/null
```

**Extract**: Core features, tech stack, architecture patterns, integration points.

### Step A3: Query All Tasks

```bash
octie list --format md
octie list --graph
```

**Purpose**: Understand complete project state before refinement.

### Step A4: Check Command Options

```bash
octie update -h
octie wire -h
octie merge -h
```

---

## Phase B: Analyze Current Graph

### Step B1: View Current Structure

**Full task details**:
```bash
octie list --format md
```

**Dependency graph**:
```bash
octie list --graph
```

**Ready tasks**:
```bash
octie find --without-blockers --format md
```

**Orphan tasks**:
```bash
octie find --orphans --format md
```

### Step B2: Validate Blocker Logic

**For each blocker relationship**, check:

| Question | If No → Action |
|----------|----------------|
| Does blocked task NEED a deliverable from blocker? | Remove blocker |
| Is there NO workaround (cannot stub/mock)? | Remove blocker |
| Is the dependency documented in `--dependencies`? | Add documentation |

**Red flags** (remove these blockers):
- Blocker added for "coordination" only → Use priority instead
- Same person assigned to both tasks → Use priority instead
- No concrete deliverable dependency → Make parallel
- Stub/mock workaround exists → Not a true blocker

### Step B3: Assess Task Atomicity

**For each task**, check:

| Aspect | Good | Bad | Action |
|--------|------|-----|--------|
| Title | Single objective | Multiple "and"s | Split |
| Scope | 2-8 hours | > 1 day | Split |
| Deliverables | 1-5 files | > 8 files | Split |
| Criteria | 2-5 criteria | > 8 criteria | Split |
| Trivial | 2+ deliverables | 0-1 deliverable | Merge |

**Identify candidates**:
- **Split candidates**: Tasks with many unrelated objectives, large scope
- **Merge candidates**: Small tasks with 1-2 deliverables at same level

### Step B4: Check Priority Alignment

**For tasks at the same dependency level**:
- Similar scope should have similar priority
- `top` = urgent, do first
- `second` = normal priority
- `later` = can wait

**Misalignment**: Task A is `top` but has 8 hours scope, Task B is `later` but has 2 hours scope → Consider swapping priorities.

---

## Phase C: Refine Graph

**⚠️ CRITICAL SEQUENCE**: Follow C1 → C2 → C3 in order. Do NOT skip or reorder.

### Step C1: Remove Unnecessary Blockers (FIRST)

**Purpose**: Maximize parallelization by removing unjustified blockers.

**Get current task state**:
```bash
octie get <task-id> --format md
```

**Remove a specific blocker**:
```bash
octie update <task-id> --unblock <blocker-id>
```

**⚠️ NOTE**: When removing the last blocker, `--dependencies` explanation is cleared automatically. Do NOT provide `--dependency-explanation` with `--unblock`.

**Remove blocker when**:
- No concrete deliverable dependency exists
- Stub/mock workaround is possible
- Blocker was added for coordination only
- Same person assigned to both (use priority instead)

**After removal**, verify:
```bash
octie get <task-id> --format md
octie list --graph
```

### Step C2: Merge or Split Tasks (SECOND)

**Purpose**: Align task atomicity — merge trivial tasks, split oversized ones.

#### Merge Small Tasks

```bash
octie merge <source-id> <target-id> --force
```

**Merge when**:
- Task has only 1-2 deliverables
- Tasks are at same dependency level
- Tasks have strong coupling (same component)
- Combined scope is still 2-8 hours

**⚠️ NEVER merge when**:
- Results in multiple objectives (violates atomicity)
- Tasks are at different dependency levels
- Blocker relationships would be lost

**⚠️ CAUTION**: Cannot undo. Verify with `octie get <id>` first.

#### Split Large Tasks

**Use `octie wire` to split oversized tasks**:

1. Create the new sub-task:
```bash
octie create \
  --title "<Sub-task title>" \
  --description "<Focused scope>" \
  --success-criterion "..." \
  --deliverable "..." \
  --priority <same-as-original>
```

2. Wire the new task into the chain:
```bash
octie wire <new-task-id> \
  --after <original-task-id> \
  --before <next-task-id> \
  --dep-on-after "Needs output from original task" \
  --dep-on-before "Next task needs this output"
```

3. Update original task to remove moved deliverables/criteria:
```bash
octie update <original-task-id> --remove-deliverable <deliverable-id>
```

**Split when**:
- Task has many unrelated objectives
- Scope exceeds 8 hours
- Multiple "and" in title
- More than 8 deliverables

### Step C3: Rearrange Dependencies (THIRD)

**Purpose**: Insert/reorder tasks in dependency chains.

**Insert task between existing tasks**:
```bash
octie wire <task-id> \
  --after <predecessor-id> \
  --before <successor-id> \
  --dep-on-after "Why this task needs predecessor" \
  --dep-on-before "Why successor needs this task"
```

**Example**:
```bash
# Before: Task A → Task C
# After:  Task A → Task B → Task C

octie wire <B-id> \
  --after <A-id> \
  --before <C-id> \
  --dep-on-after "Needs API spec from Task A" \
  --dep-on-before "Task C needs models from Task B"
```

**⚠️ PREREQUISITE**: Edge A→C must already exist. Wire only works on connected tasks.

**Twin Validation**: Both `--dep-on-after` AND `--dep-on-before` are required.

### Step C4: Update Priority (Optional)

**Adjust task urgency without changing dependencies**:
```bash
octie update <task-id> --priority <top|second|later>
```

---

## Phase D: Validate Graph (FINAL STEP)

**⚠️ CRITICAL**: Run validation AFTER completing ALL refinement operations (C1-C3). Do NOT validate after each individual change.

### Step D1: Run All Validation Commands

```bash
octie graph validate
octie graph cycles
octie find --orphans
octie list --graph
```

### Step D2: Check for Issues

| Check | Command | If Failed |
|-------|---------|-----------|
| No cycles | `octie graph cycles` | Remove one blocker in the cycle |
| No orphans | `octie find --orphans` | Wire orphans to main graph |
| Valid structure | `octie graph validate` | Fix reported issues |
| Ready tasks exist | `octie find --without-blockers` | Check blocker logic |

### Step D3: Verify Ready Tasks

```bash
octie list --status ready --format md
octie find --without-blockers --format md
```

**Confirm**: At least one task is ready to start.

---

## Phase E: Report

### Step E1: Summarize Changes

```markdown
## Octie Refine: Graph Updated

**Operations Performed**:
- Blockers removed: X
- Tasks merged: X
- Tasks split: X
- Dependencies rearranged: X

**Current State**:
- Tasks: X total (Top: Y, Second: Z, Later: W)
- Graph: Valid / Invalid
- Ready tasks: X without blockers

**Task List**:
| ID | Title | Priority | Status |
|----|-------|----------|--------|
| xxx | Task 1 | top | ready |
| xxx | Task 2 | second | blocked |

**Next Steps**:
- If valid: Use `/octie-dev` for implementation
- If issues: Additional refinement needed
```

---

## Quick Reference

| Order | Command | Purpose |
|-------|---------|---------|
| 1 | `octie update <id> --unblock <blocker-id>` | Remove blocker |
| 2a | `octie merge <src> <tgt> --force` | Combine small tasks |
| 2b | `octie wire <id> --after --before` | Split large tasks |
| 3 | `octie wire <id> --after --before` | Rearrange dependencies |
| — | `octie update <id> --priority <level>` | Change urgency |
| FINAL | `octie graph validate` | Check integrity |
| FINAL | `octie graph cycles` | Detect circular deps |
| FINAL | `octie find --orphans` | Find disconnected |

---

## Dependency Logic & Rationale

### Horizontal Decomposition
Parallel tasks at same level with no deliverable dependencies between them.
```
Task A ──┐
         ├──→ Task D
Task B ──┘
```

### Vertical Decomposition
Sequential tasks where output of Task N is required input for Task N+1.
```
Task A → Task B → Task C
```

### Blocker Rationale
A task should only block another if:
- It produces a deliverable the blocked task needs (file, API, data structure)
- The dependency is unavoidable (no stub/mock workaround)
- The relationship is explicitly documented in `--dependencies`

**⚠️ NEVER** add blockers for coordination, ordering preferences, or same-person assignment.

---

## Edge Cases

| Scenario | Handling |
|----------|----------|
| Octie unavailable | Abort — use `/octie-install` |
| No tasks exist | Use `/octie-plan` to create tasks first |
| All tasks completed | Report completion, suggest `/octie-plan` for new tasks |
| Merge would create cycle | Don't merge, restructure dependencies first |
| Wire target not connected | Wire only works on existing edges, create edge first |
| Graph validation fails | Fix issues before reporting completion |

---

## Error Handling

| Error | Cause | Resolution |
|-------|-------|------------|
| "Cycle detected" | Circular blocker | Use `--unblock` to remove one blocker |
| "twin required" | Missing dependency explanation | Add `--dependency-explanation` with `--blockers` |
| "Edge doesn't exist" | Wire on unconnected tasks | Wire only works on existing A→C edges |
| "Task not found" | Invalid ID | Use 7+ char UUID prefix |
| "violates atomic" | Post-merge too large | Split with wire instead |
| "Cannot merge" | Different dependency levels | Keep separate, use blockers |

---

## Best Practices

1. **Read before modifying** — Understand current state first
2. **Follow the sequence** — Remove → Merge/Split → Rearrange → Validate
3. **Validate at the end** — Don't validate after each change
4. **Maximize parallelization** — Remove unnecessary blockers
5. **Maintain atomicity** — 2-8 hour tasks, single objective
6. **Document dependencies** — Always explain why blockers exist
7. **Never create tasks here** — Use `/octie-plan` for new tasks

---

## ⚠️ REMINDER: Tasks System Required

**CRITICAL**: Use Claude Code Tasks System throughout. Break down EVERY step into granular, trackable sub-tasks.
