---
name: octie-install
description: 'Install and configure Octie CLI on the system. Verifies Node.js, installs octie-cli globally via npm, validates installation, initializes a project, and guides the user through basic Octie usage. Use when: "install octie", "setup octie", "get started with octie", "octie install", "how to use octie", "configure octie".'
allowed-tools: Bash, Read, Write
---

# Octie Install - Setup & Onboarding

**Purpose**: Install Octie CLI and guide the user through initial setup and basic usage.

**When to use**: First-time Octie setup, installing on a new machine, onboarding new users.
**When NOT to use**: Octie already installed (use `/octie-plan` or `/octie-dev` instead).

---

## Workflow Overview

```
A. Verify Prerequisites → B. Install Octie → C. Validate Installation → D. Initialize Project → E. Usage Guide
```

---

## Phase A: Verify Prerequisites

### Step A1: Check Node.js Version

```bash
node --version
```

**Requirement**: Node.js >= 20.0.0

**If missing or outdated**: Guide user to install/upgrade Node.js from https://nodejs.org

### Step A2: Check npm Availability

```bash
npm --version
```

**If missing**: Guide user to install npm (usually comes with Node.js).

### Step A3: Check Git (Optional but Recommended)

```bash
git --version
```

**Purpose**: Version control for project tracking. Not required for Octie itself.

---

## Phase B: Install Octie

### Step B1: Install octie-cli Globally

```bash
npm install -g octie-cli
```

**If permission error on Linux/macOS**:
```bash
sudo npm install -g octie-cli
```

**If Windows PowerShell execution policy error**:
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
npm install -g octie-cli
```

### Step B2: Verify Installation

```bash
octie --version
octie --help
```

**Expected**: Version number and help text with available commands.

**If command not found**: Check npm global bin path:
```bash
npm config get prefix
# Add the bin directory to PATH if needed
```

---

## Phase C: Validate Installation

### Step C1: Run Octie Help

```bash
octie --help
```

**Confirm these commands are available**:
- `octie init` — Initialize a project
- `octie create` — Create tasks
- `octie list` — List tasks
- `octie get` — Get task details
- `octie update` — Update tasks
- `octie approve` — Approve completed tasks
- `octie graph` — Graph operations
- `octie serve` — Start web UI

### Step C2: Check Global Registry

```bash
octie projects
```

**Purpose**: Shows registered Octie projects. Should be empty on fresh install.

---

## Phase D: Initialize a Project (Optional Demo)

### Step D1: Create Demo Directory

```bash
mkdir octie-demo && cd octie-demo
```

### Step D2: Initialize Octie Project

```bash
octie init
```

**Expected**: Creates `.octie/` directory with `project.json`.

### Step D3: Create a Sample Task

```bash
octie create \
  --title "Implement hello world endpoint" \
  --description "Create a simple GET /hello endpoint that returns { message: 'Hello, World!' }" \
  --success-criterion "Returns 200 status code" \
  --success-criterion "Response body contains 'Hello, World!'" \
  --deliverable "src/routes/hello.ts" \
  --deliverable "tests/routes/hello.test.ts" \
  --priority top
```

### Step D4: View the Task

```bash
octie list --format md
octie list --graph
```

### Step D5: Clean Up Demo (Optional)

```bash
cd .. && rm -rf octie-demo
```

---

## Phase E: Usage Guide

### Core Concepts

**Tasks are atomic**: Each task should be specific, executable, and verifiable in 2-8 hours.

**Status is auto-derived**: You don't set status manually. It's computed from:
- Success criteria completion
- Deliverable completion
- Blocker resolution

**Graph-based dependencies**: Tasks form a directed acyclic graph (DAG). Dependencies are tracked via blockers.

### Essential Commands

| Command | Purpose |
|---------|---------|
| `octie init` | Initialize project in current directory |
| `octie create --title "..." --description "..." --success-criterion "..." --deliverable "..."` | Create a task |
| `octie list --format md` | List all tasks (markdown format) |
| `octie list --graph` | View task dependency graph |
| `octie list --status ready` | List tasks ready to work on |
| `octie get <task-id>` | View task details |
| `octie update <task-id> --complete-criterion <id>` | Mark a success criterion complete |
| `octie update <task-id> --complete-deliverable <id>` | Mark a deliverable complete |
| `octie approve <task-id>` | Approve task (in_review → completed) |
| `octie serve` | Start web UI server |

### Task Lifecycle

```
ready → in_progress → in_review → completed
                         ↓
                      blocked (if dependencies exist)
```

**Key rule**: Status transitions are automatic. When all criteria + deliverables are complete, status moves to `in_review`. Use `octie approve` to finalize.

### Web UI

```bash
octie serve
```

Opens a React-based web interface with:
- Kanban board view
- Graph visualization
- Task detail panel
- Real-time updates via SSE

### Tips for AI Agents

1. **Always use `--format md`** for token-efficient output
2. **Use short IDs** (first 7-8 characters of UUID)
3. **Run `octie list --status ready`** to find available work
4. **Run `octie get <id>`** before updating to see current state
5. **Use `octie approve`** to unblock dependent tasks — never use `--unblock`

---

## Next Steps

| Goal | Command |
|------|---------|
| Plan tasks for a project | `/octie-plan` |
| Start development loop | `/octie-dev` |
| Fix a bug or add a feature | `/octie-fix` |
| Deep research for project scope | `/octie-research` |

---

## Error Handling

| Error | Cause | Resolution |
|-------|-------|------------|
| `command not found: octie` | npm global bin not in PATH | Add npm bin to PATH or reinstall |
| `EACCES` permission denied | npm global install needs sudo | Use `sudo npm install -g octie-cli` |
| `Node.js version too old` | Octie requires Node >= 20 | Upgrade Node.js from nodejs.org |
| `octie init` fails | Directory permissions | Check write permissions in current directory |

---

## ⚠️ REMINDER: Tasks System Required

**CRITICAL**: Use Claude Code Tasks System throughout. Break down EVERY step into granular, trackable sub-tasks.
