# Octie CLI

**State-oriented task graph kernel for the agentic era.**

Loop engineering substrate. Context engineering infrastructure. State is derived, not set. The task layer that every agent framework can call.

---

## Installation

```bash
npm install -g octie-cli
```

**Requirements**: Node.js >= 20.0.0

---

## Quick Start

```bash
# Initialize a project
mkdir my-project && cd my-project
octie init

# Create a task
octie create \
  --title "Implement login endpoint" \
  --description "Create POST /auth/login with JWT authentication" \
  --success-criterion "Returns 200 with valid JWT" \
  --success-criterion "Returns 401 for invalid credentials" \
  --deliverable "src/auth/login.ts" \
  --deliverable "tests/auth/login.test.ts" \
  --priority top

# View tasks
octie list --graph
octie list --format md

# Start web UI
octie serve
```

---

## What is Octie?

Octie is a **state-oriented task graph kernel** — infrastructure for AI agent workflows. Not another todo list. A directed acyclic graph (DAG) where state is derived, not set.

### Loop Engineering Substrate

Design the system that prompts your agents — not the prompts themselves. Octie provides discovery, verification, memory, and guardrails for agent loops.

- **Discovery** — `octie list --status ready` finds work for agents
- **Verification** — Success criteria define when work is done
- **Memory** — Immutable snapshots persist state across sessions
- **Guardrails** — Atomic validation prevents vague, unexecutable tasks

### Context Engineering Infrastructure

Structured, typed, dependency-aware context for agents. Not just text — tasks, criteria, deliverables, blockers. The context that lives between steps.

### Agent-Native Task Layer

CLI-first, file-based, local-first. Designed for any shell agent — Claude Code, Codex, Gemini CLI, or any terminal-based AI.

### State-Oriented Architecture

Status is derived, not set. Five states compute from item completion and blockers. The system recalculates itself — agents cannot drift out of sync.

---

## CLI Commands

| Command | Purpose |
|---------|---------|
| `octie init` | Initialize project |
| `octie create` | Create atomic task |
| `octie list` | List tasks (with filters) |
| `octie get` | Get task details |
| `octie update` | Update task (criteria, deliverables, blockers) |
| `octie approve` | Approve task (in_review → completed) |
| `octie delete` | Delete task |
| `octie merge` | Merge two tasks |
| `octie wire` | Insert task in dependency chain |
| `octie graph` | Graph operations (validate, cycles, topology) |
| `octie export` | Export to MD/JSON |
| `octie import` | Import from MD/JSON |
| `octie history` | View/restore snapshots |
| `octie serve` | Start web UI |
| `octie find` | Search tasks (by status, priority, blockers) |
| `octie batch` | Batch operations on multiple tasks |

---

## For AI Agents

```bash
# Get ready tasks (markdown format for AI)
octie list --status ready --format md

# Get task details
octie get <task-id> --format md

# Mark progress
octie update <id> --complete-criterion <criterion-id>
octie update <id> --complete-deliverable <deliverable-id>

# Status auto-derives: ready → in_progress → in_review
# Approve to unblock dependents
octie approve <id>
```

---

## Agent Skills

This repository includes skills for any shell agent that provide structured workflows for working with Octie:

| Skill | Purpose |
|-------|---------|
| [octie-install](skills/octie-install/) | Install and configure Octie CLI |
| [octie-research](skills/octie-research/) | Deep research using search tools |
| [octie-plan](skills/octie-plan/) | Create atomic tasks with dependencies |
| [octie-dev](skills/octie-dev/) | Development loop with audit trails |
| [octie-fix](skills/octie-fix/) | Bug investigation and fix preparation |
| [octie-refine](skills/octie-refine/) | Graph refinement and optimization |

### Installing Skills

Copy the skills to your agent's skills directory:

```bash
# For Claude Code
cp -r skills/* ~/.claude/skills/

# For other agents, copy to their respective skill directories
# or use the skills directly from this repository
```

---

## Task States

Octie uses 5 auto-derived states:

| State | Description |
|-------|-------------|
| **ready** | No blockers, prerequisites met, ready to start |
| **in_progress** | Work has started, some items completed |
| **in_review** | All items complete, awaiting approval |
| **completed** | Approved and done |
| **blocked** | Has unresolved blockers or prerequisites |

Status is **never set manually** — it derives from item completion and blockers.

---

## Links

- **Website**: [GitHub Pages](https://starchen-cycler.github.io/octie-cli/)

---

## License

MIT
