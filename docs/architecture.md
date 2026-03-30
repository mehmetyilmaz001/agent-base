# Architecture Overview

This document describes the architecture of the Agent Base framework -- a multi-domain AI agent team system for managing projects from scratch to production.

## Design Philosophy

Agent Base is built on four core principles:

1. **Orchestrator-Worker Pattern** -- A single Director agent mediates all coordination. Agents never communicate peer-to-peer. The Director decomposes goals, routes tasks, passes context between agents, and verifies every output. This eliminates coordination chaos and gives the Director a complete picture of project state.

2. **On-Demand Activation** -- All agents are defined as templates, not running processes. The Director spawns only the agents needed for a given task, keeping 3-5 agents active concurrently (the practical sweet spot per Anthropic's research on multi-agent systems). Agents are terminated after their task completes and their output passes quality gates.

3. **Quality Gates** -- Every agent output must pass defined verification checkpoints before the Director accepts it and passes it downstream. If a gate fails, the output returns to the agent with specific feedback. Quality is never traded for speed; scope is reduced instead.

4. **Dual Agent Definition** -- Each agent is defined in two formats: `AGENT.md` for native Claude Code consumption, and `agent.yaml` for portability across frameworks and tooling. Both files live side-by-side in the agent's directory.

## Repository Structure

```
agent-base/
├── shared/                          # Cross-base shared resources
│   ├── quality-gates.md             # Universal quality gate definitions
│   ├── communication-protocol.md    # Message flow and handoff rules
│   └── mcp-configs/                 # MCP server configuration templates
│       ├── github.yaml
│       ├── slack.yaml
│       └── database.yaml
├── bases/                           # Domain-specific agent teams
│   └── software-development/        # Example: 14-agent SDLC team
│       ├── manifest.yaml            # Base metadata and agent registry
│       ├── agents/                  # One directory per agent
│       │   ├── director/
│       │   │   ├── AGENT.md         # Claude Code native definition
│       │   │   └── agent.yaml       # Portable definition
│       │   ├── backend-developer/
│       │   │   ├── AGENT.md
│       │   │   └── agent.yaml
│       │   └── ...                  # 12 more agents
│       ├── workflows/               # Common workflow sequences
│       └── templates/               # Project bootstrapping templates
│           └── new-project/
└── docs/                            # Framework documentation
```

### shared/ -- Cross-Base Resources

Resources in `shared/` apply to all bases. They define the universal rules that every agent team follows:

- **quality-gates.md** -- Defines quality checkpoints by category: universal gates (completeness, consistency, documentation), code quality gates (tests, linting, security, performance), design gates (accessibility, responsiveness), and infrastructure gates (deployability, observability).
- **communication-protocol.md** -- Defines the message flow pattern (all communication through Director), task assignment format, output format, escalation rules, and handoff protocol.
- **mcp-configs/** -- Template configurations for MCP servers that agents can connect to. Each config specifies the server package, capabilities, required environment variables, and which agents use which capabilities.

### bases/ -- Domain-Specific Teams

Each directory under `bases/` is a complete agent team for a specific domain. A base contains:

- **manifest.yaml** -- Declares the base metadata, lists all agents, and defines available workflows.
- **agents/** -- One subdirectory per agent, each containing `AGENT.md` and `agent.yaml`.
- **workflows/** -- Step-by-step sequences defining which agents activate for common scenarios (feature development, bug fix, release, etc.).
- **templates/** -- Starter files for bootstrapping a new project that uses this base.

## Agent Definition Format

Every agent has two definition files that serve complementary purposes.

### AGENT.md (Claude Code Native)

This is the file Claude Code reads directly. It uses the Claude Code agent format with YAML frontmatter and detailed markdown instructions.

Structure:

```markdown
---
name: agent-name
description: One-line description of the agent's purpose.
---

# Agent Name

System prompt that defines the agent's identity, responsibilities,
detailed workflow instructions, interaction patterns with other agents,
constraints, quality gate criteria, and output format.
```

The `AGENT.md` is the authoritative, detailed definition. It contains the full system prompt with:
- Role identity and core responsibilities
- Step-by-step workflow instructions
- Code architecture guidance and standards (for development agents)
- Interaction patterns with other agents
- Constraints and rules
- Quality gate criteria with specifics
- Expected output format

### agent.yaml (Portable)

A structured, machine-readable definition for tooling, validation, and portability to other frameworks.

```yaml
agent:
  name: agent-name
  version: 1.0.0
  role: "One-line role description"
  goal: "What this agent optimizes for"

  skills:
    - skill-name-1
    - skill-name-2

  tools:
    mcp_servers:
      - github
      - database
    cli:
      - git
      - npm
    superpowers_skills:
      - test-driven-development

  quality_gates:
    - name: gate-name
      command: "verification command or description"
      required: true

  context:
    receives_from:
      - agent-name-1
    delivers_to:
      - agent-name-2

  constraints:
    - "Rule this agent must follow"
```

Key fields:
- **skills** -- Capabilities this agent brings to the team.
- **tools** -- MCP servers, CLI tools, and superpowers skills the agent uses.
- **quality_gates** -- Named checkpoints with verification commands. The Director runs these after receiving the agent's output.
- **context.receives_from / delivers_to** -- Declares the agent's position in the workflow graph. Used for validation (no dangling references) and for the Director to plan handoffs.
- **constraints** -- Hard rules the agent must follow, enforced by the Director.

## Orchestration Flow

The Director follows a four-phase orchestration cycle for every incoming request:

```
                    ┌──────────────┐
                    │     User     │
                    │   Request    │
                    └──────┬───────┘
                           │
                ┌──────────▼───────────┐
                │   Phase 1: PLAN      │
                │                      │
                │  - Parse request     │
                │  - Select workflow   │
                │  - Decompose tasks   │
                │  - Map dependencies  │
                └──────────┬───────────┘
                           │
                ┌──────────▼───────────┐
                │  Phase 2: ASSIGN     │
                │                      │
                │  - Spawn agents      │
                │  - Provide context   │
                │  - Parallel where    │
                │    possible          │
                └──────────┬───────────┘
                           │
          ┌────────────────┼────────────────┐
          │                │                │
     ┌────▼────┐     ┌────▼────┐     ┌────▼────┐
     │ Agent A │     │ Agent B │     │ Agent C │
     │  (task) │     │  (task) │     │  (task) │
     └────┬────┘     └────┬────┘     └────┬────┘
          │                │                │
          └────────────────┼────────────────┘
                           │
                ┌──────────▼───────────┐
                │  Phase 3: VERIFY     │
                │                      │
                │  - Check quality     │
                │    gates             │
                │  - Return failures   │
                │    for rework        │
                │  - Pass outputs to   │
                │    downstream agents │
                └──────────┬───────────┘
                           │
                           │ (loop back if gates fail)
                           │
                ┌──────────▼───────────┐
                │  Phase 4: DELIVER    │
                │                      │
                │  - Verify integrated │
                │    result            │
                │  - Compile output    │
                │  - Report to user    │
                └──────────────────────┘
```

### Workflow Selection

The Director selects the appropriate workflow based on the request type:

| Request Type | Key Agents (in order) |
|---|---|
| New feature (full stack) | Product Owner, Analyst, Designer, Backend+Frontend Dev (parallel), Tester, DevOps |
| Bug fix | Analyst, relevant Developer(s), Tester |
| API-only change | Analyst, Backend Dev, Tester |
| UI-only change | Designer, Frontend Dev, Tester |
| Database migration | Analyst, DB Admin, Backend Dev, Tester |
| Infrastructure change | DevOps, SRE, Tester |
| Security audit | Security, relevant Developer(s), Tester |
| Performance optimization | Analyst, relevant Developer(s), Tester, SRE |

When a request spans multiple workflow types, the Director composes them and identifies the critical path.

### Handoff Protocol

When work flows from one agent to the next:

1. The sending agent completes its task and reports output to the Director.
2. The Director verifies the output against the agent's quality gates.
3. If gates pass, the Director enriches the next agent's task with relevant context from the previous output.
4. The Director spawns the next agent with the enriched task.
5. If the receiving agent needs clarification, it requests it through the Director (never directly from the previous agent).

## Communication Protocol

All communication follows the hub-and-spoke model with the Director at the center:

```
User / Stakeholder
       │
       ▼
   Director ──────────────────────────┐
       │         │         │          │
       ▼         ▼         ▼          ▼
   Agent A   Agent B   Agent C   Agent N
       │         │         │          │
       └─────────┴─────────┴──────────┘
                         │
                         ▼
                     Director
                    (verifies)
```

### Task Assignment Format

The Director assigns tasks using a structured format:

```yaml
task:
  id: "TASK-001"
  type: "implementation"
  title: "Implement user authentication API"
  description: "Clear description of what needs to be done."
  context:
    - Previous agent outputs relevant to this task
  acceptance_criteria:
    - Specific, measurable criteria
  dependencies:
    - Tasks that must be completed first
  delivers_to: "team-lead"
```

### Agent Output Format

Agents report back with structured output:

```yaml
output:
  task_id: "TASK-001"
  status: "completed"
  summary: "Brief description of what was done"
  artifacts:
    - files_changed: ["path/to/file"]
    - tests_added: ["path/to/test"]
  quality_gate_results:
    - gate: "tests-pass"
      status: "pass"
  notes: "Context for the next agent"
  blockers: []
```

### Escalation Rules

1. **Agent blocked** -- Reports to Director. Director resolves or asks the user.
2. **Quality gate fails** -- Director returns output with feedback. Agent must fix and resubmit.
3. **Conflicting outputs** -- Director consults Team Lead to resolve.
4. **Scope creep** -- Director flags to Product Owner for prioritization.
5. **User input needed** -- Director escalates to user with clear options.

## MCP Integration Layer

Agents connect to external tools through MCP (Model Context Protocol) servers. MCP configurations are defined in `shared/mcp-configs/` and referenced by agents in their `agent.yaml` definitions.

### Configured MCP Servers

| MCP Server | Purpose | Key Agents |
|---|---|---|
| GitHub (`@modelcontextprotocol/server-github`) | Repos, issues, PRs, projects, actions, security | All agents |
| Slack (`@anthropic/mcp-server-slack`) | Team communication and notifications | Director, Team Lead, DevOps, SRE |
| Database (`anydb-mcp`) | Schema management, queries, optimization | DB Admin, Backend Dev |
| Figma | Design files and components | Designer, Frontend Dev |
| Datadog / Sentry | Monitoring and error tracking | SRE, DevOps |
| Context7 | Library documentation lookup | All developers |

### How MCP Configs Work

Each MCP config file in `shared/mcp-configs/` defines:

1. **Server identity** -- Package name and description.
2. **Capabilities** -- What the server can do (e.g., issues, pull_requests, query_execution).
3. **Environment variables** -- Credentials and configuration needed (referenced as `${VAR_NAME}`).
4. **Usage by agent** -- Which agents use which capabilities, enabling least-privilege access.

Example from `shared/mcp-configs/github.yaml`:

```yaml
mcp_server:
  name: github
  package: "@modelcontextprotocol/server-github"
  capabilities:
    - repository_management
    - issues
    - pull_requests
    - actions
    - security
  environment:
    GITHUB_PERSONAL_ACCESS_TOKEN: "${GITHUB_TOKEN}"
  usage_by_agent:
    director: [issues, projects, pull_requests]
    backend-developer: [repository_management, pull_requests]
    security: [security, pull_requests]
```

Agents only receive access to the capabilities listed under their name, enforcing the principle of least privilege.

## Quality Gate System

Quality gates are verification checkpoints enforced by the Director after every agent output.

### Gate Categories

**Universal Gates (all agents)**
- Completeness -- Output addresses all task requirements; no placeholders or TODOs.
- Consistency -- Output follows project conventions; no contradictions.
- Documentation -- Changes are self-explanatory; public APIs documented.

**Code Quality Gates (development agents)**
- Tests -- All existing tests pass; new functionality has tests; coverage meets threshold.
- Linting and Formatting -- No errors or warnings; code formatted to project standards.
- Security -- No hardcoded secrets; dependencies scanned; input validated; OWASP Top 10 addressed.
- Performance -- No regressions; queries optimized; assets appropriately sized.

**Design Quality Gates**
- Accessibility -- WCAG AA compliance; keyboard navigation; screen reader compatibility.
- Responsiveness -- Works across breakpoints; no horizontal scroll on mobile.

**Infrastructure Quality Gates**
- Deployability -- Automated deployment; rollback plan exists; env vars documented.
- Observability -- Critical paths monitored; alerts configured; logs structured.

### Gate Enforcement Flow

```
Agent completes task
        │
        ▼
Director checks quality gates
        │
   ┌────┴────┐
   │         │
 Pass      Fail
   │         │
   ▼         ▼
Accept    Return to agent
output    with specific
   │      feedback
   │         │
   ▼         ▼
Pass to    Agent fixes
next       and resubmits
agent         │
              └──> Director re-checks
```

Each agent's `agent.yaml` declares its specific gates:

```yaml
quality_gates:
  - name: tests-pass
    command: "npm test"
    required: true
  - name: coverage-threshold
    command: "verify test coverage meets or exceeds 80%"
    required: true
```

The Director checks all `required: true` gates. If any fail, the output is rejected with actionable feedback, and the agent must fix and resubmit.

## Context Engineering Principles

The framework applies context engineering principles from Anthropic's research on building effective AI agents:

### Minimal Viable Context

Each agent receives only the context it needs to complete its task. The Director curates context for each spawn, including:
- The task description and acceptance criteria
- Relevant outputs from upstream agents
- Applicable project constraints and conventions

Agents do not receive the full project history or other agents' unrelated work. This keeps context windows focused and effective.

### Sub-Agent Isolation

Each spawned agent operates in isolation with its own context window. Benefits:
- Agents cannot interfere with each other's state.
- Failures are contained -- one agent's failure does not corrupt another's context.
- The Director maintains the single source of truth for project state.

### Conversation Compaction

The Director compacts information as it flows between agents. Instead of passing raw, verbose outputs, the Director extracts the relevant artifacts and context needed by the downstream agent. This prevents context window overflow in long-running projects.

### On-Demand Activation Over Persistent Agents

Agents are templates, not persistent processes. They are spawned when needed and terminated after delivery. This avoids:
- Stale context from long-running conversations
- Resource waste from idle agents
- Context window degradation over time

The practical limit is 3-5 concurrent agents, which balances parallelism against coordination overhead.

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                        AGENT BASE FRAMEWORK                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌───────────┐                                                      │
│  │   User    │                                                      │
│  └─────┬─────┘                                                      │
│        │                                                            │
│        ▼                                                            │
│  ┌───────────────────────────────────────────┐                      │
│  │              DIRECTOR                     │                      │
│  │  - Decomposes goals into tasks            │                      │
│  │  - Selects workflow                       │                      │
│  │  - Spawns agents on demand                │                      │
│  │  - Verifies quality gates                 │                      │
│  │  - Integrates results                     │                      │
│  └──┬──────┬──────┬──────┬──────┬──────┬─────┘                      │
│     │      │      │      │      │      │                            │
│     ▼      ▼      ▼      ▼      ▼      ▼                           │
│  ┌─────┐┌─────┐┌─────┐┌─────┐┌─────┐┌─────┐                       │
│  │ PO  ││Anlst││Dsgn ││BkDev││FrDev││Tstr │  ... (14 agents)      │
│  └──┬──┘└──┬──┘└──┬──┘└──┬──┘└──┬──┘└──┬──┘                       │
│     │      │      │      │      │      │                            │
│     └──────┴──────┴──────┴──────┴──────┘                            │
│                        │                                            │
│            ┌───────────┴───────────┐                                │
│            │    QUALITY GATES      │                                │
│            │  (verify before       │                                │
│            │   integration)        │                                │
│            └───────────────────────┘                                │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐    │
│  │                    SHARED RESOURCES                          │    │
│  │  quality-gates.md │ communication-protocol.md │ mcp-configs │    │
│  └─────────────────────────────────────────────────────────────┘    │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐    │
│  │                    MCP INTEGRATION LAYER                     │    │
│  │  GitHub │ Slack │ Database │ Figma │ Datadog │ Context7      │    │
│  └─────────────────────────────────────────────────────────────┘    │
│                                                                     │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐              │
│  │    bases/     │  │    bases/     │  │    bases/     │             │
│  │  software-    │  │  e-commerce  │  │  content-    │              │
│  │  development  │  │  (future)    │  │  creator     │              │
│  │  (14 agents)  │  │              │  │  (future)    │              │
│  └──────────────┘  └──────────────┘  └──────────────┘              │
└─────────────────────────────────────────────────────────────────────┘
```

### Data Flow Through a Feature Lifecycle

```
User: "Build a user authentication system"
  │
  ▼
Director: Selects feature-development workflow
  │
  ├──> Product Owner: Writes user stories with acceptance criteria
  │         │
  │    Director: Verifies stories (quality gate: AC defined)
  │         │
  ├──> Analyst: Creates technical spec (API contracts, data model)
  │         │
  │    Director: Verifies spec (quality gate: complete, unambiguous)
  │         │
  ├──> Designer: Creates login/signup UI designs
  │         │
  │    Director: Verifies designs (quality gate: WCAG AA, responsive)
  │         │
  ├──> DB Admin: Designs user schema and migrations
  │    Director: Verifies (quality gate: reversible migrations)
  │         │
  ├──┬──> Backend Dev: Implements auth API (parallel)
  │  │        │
  │  └──> Frontend Dev: Implements auth UI (parallel)
  │              │
  │    Director: Verifies both (quality gates: tests, security, spec match)
  │         │
  ├──> Security: Reviews implementation
  │    Director: Verifies (quality gate: no critical vulnerabilities)
  │         │
  ├──> Tester: Writes and runs E2E tests
  │    Director: Verifies (quality gate: coverage, no open bugs)
  │         │
  ├──> Team Lead: Code review
  │    Director: Verifies (quality gate: review approved)
  │         │
  ├──> DevOps: Deploys to staging, then production
  │    Director: Verifies (quality gate: pipeline green, rollback ready)
  │         │
  └──> Director: Compiles final report, delivers to user
```
