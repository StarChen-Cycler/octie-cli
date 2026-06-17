---
name: octie-research
description: 'Deep research phase for Octie projects. MUST use web search tools, MCPs, and investigation tools to research technology stack, architecture patterns, edge cases, and best practices before creating tasks. Searches C7 MCP for library docs, Tavily/web search for current practices, and codebase for existing patterns. Use when: "research project", "investigate technology", "deep research", "search for best practices", "octie research", "explore options", "find patterns".'
allowed-tools: Bash, Read, Write, WebSearch, WebFetch, mcp__tavily__tavily_search, mcp__metaso__metaso_web_search, mcp__metaso__metaso_web_reader, mcp__context7__resolve-library-id, mcp__context7__query-docs
---

# Octie Research - Deep Investigation & Technology Research

**Purpose**: Conduct thorough research on technologies, patterns, and approaches before planning Octie tasks.

**When to use**: Starting a new project, exploring unfamiliar technology, investigating architecture options, researching best practices.
**When NOT to use**: Technology already well-understood (skip to `/octie-plan`), implementation phase (use `/octie-dev`).

---

## Workflow Overview

```
A. Context Loading → B. Technology Research → C. Architecture Investigation → D. Edge Case Discovery → E. Document Findings
```

**Core principle**: By all means, use every available search and investigation tool. Never guess when you can research.

---

## Critical Requirements

**⚠️ MANDATORY RESEARCH**: Do NOT skip research for unfamiliar technologies. Always verify assumptions with search tools.

**⚠️ USE ALL TOOLS**: Leverage C7 MCP, Tavily, web search, codebase search, and documentation. Cross-validate findings.

**⚠️ DOCUMENT EVERYTHING**: Save research findings to `.memo/memodocs/` for future reference.

---

## Phase A: Context Loading

### Step A1: Understand Project Scope

**Read existing project files** (if any):
```bash
ls -la
cat CLAUDE.md 2>/dev/null
ls .memo/memodocs/ 2>/dev/null
```

**Extract**:
- Project goals and requirements
- Tech stack mentioned
- Existing architecture decisions
- Open questions

### Step A2: Identify Research Targets

**Create a research checklist** based on project scope:
- Technologies to investigate
- Architecture patterns to evaluate
- Integration points to understand
- Edge cases to discover

---

## Phase B: Technology Research

### Step B1: Query C7 MCP for Library Documentation

**For each major technology in the project**, query C7 MCP:

```
1. Resolve library ID:
   mcp__context7__resolve-library-id for "<library-name>"

2. Query documentation:
   mcp__context7__query-docs for "<specific question>"
```

**Example queries**:
- "Best practices for Express.js middleware error handling"
- "React 18+ hooks patterns for data fetching"
- "PostgreSQL connection pooling with Node.js"
- "WebSocket authentication patterns"

**Document findings**: Note recommended patterns, deprecated approaches, and version-specific considerations.

### Step B2: Web Search for Current Practices

**Use Tavily for deep search**:
```
mcp__tavily__tavily_search for "<technology> best practices <year>"
mcp__tavily__tavily_search for "<technology> common pitfalls"
mcp__tavily__tavily_search for "<pattern> vs <alternative> comparison"
```

**Use WebSearch for quick lookups**:
```
WebSearch for "<specific question>"
```

**Cross-validate**: Compare findings from multiple sources. Note discrepancies.

### Step B3: Search for Real-World Examples

**Search GitHub for patterns**:
```
WebSearch for "site:github.com <technology> <pattern> example"
```

**Search for production usage**:
```
mcp__tavily__tavily_search for "<technology> production deployment lessons"
mcp__tavily__tavily_search for "<framework> scale issues"
```

---

## Phase C: Architecture Investigation

### Step C1: Evaluate Architecture Options

**For each major architectural decision**, research:

| Decision | Research Questions |
|----------|-------------------|
| Database choice | Scaling patterns, query performance, migration complexity |
| API design | REST vs GraphQL, versioning strategy, error handling |
| Authentication | Session vs JWT, OAuth flows, security considerations |
| State management | Client vs server state, caching strategies |
| Deployment | Container vs serverless, CI/CD patterns |

**Document pros/cons** for each option.

### Step C2: Investigate Integration Patterns

**For each integration point**:
```
mcp__context7__query-docs for "<service> integration best practices"
mcp__tavily__tavily_search for "<service> API common issues"
```

**Key areas**:
- Error handling and retry logic
- Rate limiting and backoff
- Data transformation patterns
- Security considerations

### Step C3: Research Testing Strategies

```
mcp__context7__query-docs for "<framework> testing best practices"
mcp__tavily__tavily_search for "<technology> testing patterns"
```

**Document**:
- Unit testing approach
- Integration testing strategy
- E2E testing tools
- Mocking patterns

---

## Phase D: Edge Case Discovery

### Step D1: Search for Known Issues

```
mcp__tavily__tavily_search for "<technology> known issues"
mcp__tavily__tavily_search for "<framework> common bugs"
WebSearch for "<library> GitHub issues <topic>"
```

### Step D2: Investigate Failure Modes

**For each critical component**, research:
- What happens when external service is down?
- How does the system handle malformed input?
- What are the memory/CPU limits?
- How does it behave under load?

### Step D3: Security Research

```
mcp__tavily__tavily_search for "<technology> security vulnerabilities"
mcp__tavily__tavily_search for "<framework> OWASP top 10"
```

**Document**:
- Authentication pitfalls
- Input validation requirements
- SQL injection / XSS prevention
- CORS and CSP configuration

---

## Phase E: Document Findings

### Step E1: Create Research Document

**Save to**: `.memo/memodocs/research_<topic>.md`

**Structure**:
```markdown
# Research: <Topic>

## Technologies Investigated
- Technology 1: Version, key findings
- Technology 2: Version, key findings

## Architecture Decisions
| Decision | Option A | Option B | Recommendation |
|----------|----------|----------|----------------|
| ... | ... | ... | ... |

## Best Practices Discovered
1. Practice 1 — Source, rationale
2. Practice 2 — Source, rationale

## Edge Cases & Pitfalls
1. Edge case 1 — How to handle
2. Edge case 2 — How to handle

## Security Considerations
1. Consideration 1 — Mitigation
2. Consideration 2 — Mitigation

## Testing Strategy
- Unit: ...
- Integration: ...
- E2E: ...

## Open Questions
- Question 1
- Question 2
```

### Step E2: Update Project Context

**If CLAUDE.md exists**, add research findings to relevant sections.

**If .memo/ exists**, save research for future reference.

### Step E3: Report Research Summary

```markdown
## Research Complete

**Technologies Investigated**: X
**Architecture Decisions**: Y documented
**Edge Cases Found**: Z
**Security Concerns**: W

**Key Findings**:
1. Finding 1
2. Finding 2
3. Finding 3

**Recommendation**: [Summary of recommended approach]

**Next Step**: Use `/octie-plan` to create tasks based on research findings.
```

---

## Research Tools Quick Reference

| Tool | Best For | Speed |
|------|----------|-------|
| C7 MCP (`mcp__context7__query-docs`) | Library documentation, code examples | Fast |
| Tavily (`mcp__tavily__tavily_search`) | Deep web search, multiple sources | Medium |
| WebSearch | Quick factual lookups | Fast |
| WebFetch | Reading specific documentation pages | Medium |
| Metaso (`mcp__metaso__metaso_web_search`) | Chinese-language sources, academic papers | Medium |
| Codebase search (Grep/Glob) | Existing patterns in current project | Fast |

---

## Edge Cases

| Scenario | Handling |
|----------|----------|
| C7 MCP unavailable | Use Tavily and WebSearch instead |
| No existing project files | Research from scratch, document everything |
| Conflicting information | Document both perspectives, note source reliability |
| Technology not in C7 | Use Tavily for web research |
| Research scope too broad | Focus on immediate project needs first |

---

## Best Practices

1. **Never assume** — Always verify with search tools
2. **Cross-validate** — Use multiple sources for important decisions
3. **Document findings** — Save research for future reference
4. **Focus on edges** — Edge cases and failure modes matter most
5. **Version-specific** — Note library versions for findings
6. **Security first** — Always research security implications
7. **Test strategy** — Research testing patterns early

---

## ⚠️ REMINDER: Tasks System Required

**CRITICAL**: Use Claude Code Tasks System throughout. Break down EVERY step into granular, trackable sub-tasks.
