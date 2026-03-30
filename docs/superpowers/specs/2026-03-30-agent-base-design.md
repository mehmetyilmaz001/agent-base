# Agent Base: Multi-Domain AI Agent Team Framework

**Date:** 2026-03-30
**Status:** Draft
**Author:** Brainstorming session

---

## Problem Statement

Managing a software project from scratch to production requires coordination across many disciplines: product management, analysis, design, development, testing, security, DevOps, and monitoring. Currently, these roles are handled ad-hoc without a structured agent framework, leading to inconsistent quality and missed steps.

## Goal

Create a comprehensive, reusable agent base framework that defines AI agent teams capable of handling software projects end-to-end following industry best practices. The framework must support multiple domain-specific bases (software development, e-commerce, content creation, etc.) within a single repository.

## Architecture: Modular Agent Registry

### Key Decisions

- **Orchestration Pattern:** Orchestrator-Worker. The Director agent decomposes tasks, spawns specialized agents on-demand, and verifies output through quality gates. Agents do not communicate peer-to-peer; the Director mediates all coordination.
- **Agent Activation:** All 14 agents defined as templates. Only spawned when needed (3-5 concurrent agents is the sweet spot per Anthropic's research).
- **Dual Format:** Each agent has a Claude Code native `AGENT.md` plus a portable `agent.yaml` for framework adaptability.
- **PM Tooling:** GitHub-native (Issues, Projects, PRs, Actions).
- **Project Scope:** General-purpose, not tied to a specific tech stack.

### Project Structure

```
agent-base/
├── README.md
├── CLAUDE.md
├── shared/
│   ├── quality-gates.md
│   ├── communication-protocol.md
│   └── mcp-configs/
│       ├── github.yaml
│       ├── slack.yaml
│       └── database.yaml
├── bases/
│   ├── software-development/
│   │   ├── README.md
│   │   ├── manifest.yaml
│   │   ├── agents/
│   │   │   ├── director/
│   │   │   │   ├── AGENT.md
│   │   │   │   ├── agent.yaml
│   │   │   │   └── skills/
│   │   │   ├── product-owner/
│   │   │   │   ├── AGENT.md
│   │   │   │   ├── agent.yaml
│   │   │   │   └── skills/
│   │   │   ├── analyst/
│   │   │   ├── designer/
│   │   │   ├── backend-developer/
│   │   │   ├── frontend-developer/
│   │   │   ├── tester/
│   │   │   ├── team-lead/
│   │   │   ├── devops/
│   │   │   ├── marketing/
│   │   │   ├── db-admin/
│   │   │   ├── sre-monitoring/
│   │   │   ├── mobile-developer/
│   │   │   └── security/
│   │   ├── workflows/
│   │   │   ├── feature-lifecycle.md
│   │   │   ├── bug-fix.md
│   │   │   └── release.md
│   │   └── templates/
│   │       └── new-project/
│   │           ├── .claude/
│   │           │   ├── settings.json
│   │           │   └── agents/
│   │           ├── CLAUDE.md
│   │           └── project-manifest.yaml
│   ├── e-commerce/          # Future
│   └── content-creator/     # Future
└── docs/
    ├── architecture.md
    └── creating-a-new-base.md
```

---

## Agent Definitions (14 Agents)

### 1. Director

| Field | Value |
|-------|-------|
| **Role** | Orchestrates the entire team |
| **Goal** | Decompose project goals into tasks, spawn the right agents, monitor progress, verify quality |
| **Skills** | Task decomposition, agent routing, progress tracking, risk assessment, conflict resolution |
| **Tools** | GitHub Projects MCP, all other agents (spawns them) |
| **Receives From** | User/stakeholder requests |
| **Delivers To** | All agents (task assignments) |
| **Quality Gate** | All sub-agent outputs pass their respective gates |

### 2. Product Owner

| Field | Value |
|-------|-------|
| **Role** | Defines product vision, writes user stories, prioritizes backlog |
| **Goal** | Ensure the team builds the right thing with clear acceptance criteria |
| **Skills** | User story writing, acceptance criteria, backlog prioritization, stakeholder communication |
| **Tools** | GitHub Issues MCP, Notion/Confluence MCP |
| **Receives From** | Director, stakeholders |
| **Delivers To** | Analyst, Team Lead, Designer |
| **Quality Gate** | Every story has acceptance criteria and definition of done |

### 3. Analyst

| Field | Value |
|-------|-------|
| **Role** | Gathers requirements, creates technical specs, analyzes feasibility |
| **Goal** | Produce complete, unambiguous, implementable specifications |
| **Skills** | Requirements analysis, data modeling, process mapping, feasibility assessment |
| **Tools** | GitHub MCP, Context7, web search |
| **Receives From** | Product Owner, Director |
| **Delivers To** | Backend Dev, Frontend Dev, Designer, DB Admin |
| **Quality Gate** | Specs complete, unambiguous, traceable to user stories |

### 4. Designer

| Field | Value |
|-------|-------|
| **Role** | Creates UI/UX designs, design systems, wireframes |
| **Goal** | Deliver accessible, responsive, beautiful interfaces |
| **Skills** | UI design, accessibility (WCAG), responsive design, design system management |
| **Tools** | Figma MCP, browser preview, Lighthouse accessibility |
| **Receives From** | Product Owner, Analyst |
| **Delivers To** | Frontend Dev, Mobile Dev |
| **Quality Gate** | WCAG AA compliance, responsive across breakpoints |

### 5. Backend Developer

| Field | Value |
|-------|-------|
| **Role** | Implements APIs, business logic, integrations, data access |
| **Goal** | Build robust, secure, well-tested backend systems |
| **Skills** | API design, database queries, authentication, error handling, TDD |
| **Tools** | GitHub MCP, database MCPs, Context7, terminal |
| **Receives From** | Analyst, Team Lead, Designer |
| **Delivers To** | Tester, Team Lead, DevOps |
| **Quality Gate** | Tests pass, API contracts match spec, no security vulnerabilities |

### 6. Frontend Developer

| Field | Value |
|-------|-------|
| **Role** | Implements UI components, state management, client-side logic |
| **Goal** | Build performant, accessible, pixel-perfect user interfaces |
| **Skills** | Component architecture, state management, performance optimization, accessibility |
| **Tools** | GitHub MCP, browser preview, Figma MCP, Context7 |
| **Receives From** | Designer, Analyst, Team Lead |
| **Delivers To** | Tester, Team Lead |
| **Quality Gate** | Matches design, accessible, Lighthouse >90 |

### 7. Tester (QA)

| Field | Value |
|-------|-------|
| **Role** | Creates test plans, writes automated tests, E2E testing, bug reporting |
| **Goal** | Ensure software quality through comprehensive testing |
| **Skills** | Test strategy, E2E testing, API testing, regression testing, performance testing |
| **Tools** | GitHub MCP, browser automation, Playwright, terminal |
| **Receives From** | Backend Dev, Frontend Dev, Team Lead |
| **Delivers To** | Team Lead, Director |
| **Quality Gate** | Coverage meets threshold, no critical/high bugs open |

### 8. Team Lead

| Field | Value |
|-------|-------|
| **Role** | Coordinates dev work, reviews code, resolves technical decisions |
| **Goal** | Maintain code quality and architectural consistency |
| **Skills** | Code review, architecture decisions, conflict resolution, sprint management |
| **Tools** | GitHub MCP (PRs, reviews), terminal |
| **Receives From** | Director, all developers |
| **Delivers To** | DevOps, Director |
| **Quality Gate** | All PRs reviewed, architecture decisions documented |

### 9. DevOps

| Field | Value |
|-------|-------|
| **Role** | CI/CD pipelines, infrastructure, deployments, environment management |
| **Goal** | Reliable, automated, zero-downtime deployments |
| **Skills** | Docker, Kubernetes, Terraform, GitHub Actions, monitoring setup |
| **Tools** | GitHub Actions MCP, Terraform MCP, Kubernetes MCP, AWS/GCP MCP |
| **Receives From** | Team Lead, Director |
| **Delivers To** | SRE, Director |
| **Quality Gate** | Pipeline green, IaC, zero-downtime deploys |

### 10. Marketing

| Field | Value |
|-------|-------|
| **Role** | Landing pages, SEO, content strategy, analytics, launch plans |
| **Goal** | Drive product visibility and user acquisition |
| **Skills** | Copywriting, SEO optimization, analytics tracking, A/B testing |
| **Tools** | Browser tools, SEO audit skills, analytics MCPs, web search |
| **Receives From** | Product Owner, Director |
| **Delivers To** | Frontend Dev, Director |
| **Quality Gate** | SEO score >90, analytics verified, copy reviewed |

### 11. DB Admin

| Field | Value |
|-------|-------|
| **Role** | Schema design, migrations, query optimization, backup strategies |
| **Goal** | Maintain data integrity, performance, and reliability |
| **Skills** | Schema design, indexing, query tuning, migration management, security |
| **Tools** | Database MCPs (PostgreSQL, MySQL, MongoDB), terminal |
| **Receives From** | Analyst, Backend Dev |
| **Delivers To** | Backend Dev, DevOps |
| **Quality Gate** | Migrations reversible, queries optimized, backups configured |

### 12. SRE / Monitoring

| Field | Value |
|-------|-------|
| **Role** | Observability, alerting, incident response, SLO/SLA management |
| **Goal** | Ensure system reliability and rapid incident resolution |
| **Skills** | Monitoring dashboards, log aggregation, alerting rules, incident playbooks |
| **Tools** | Datadog MCP, Sentry MCP, PagerDuty MCP, Kubernetes MCP |
| **Receives From** | DevOps, Director |
| **Delivers To** | Director, Team Lead |
| **Quality Gate** | Critical paths monitored, alerts configured, runbooks documented |

### 13. Mobile Developer

| Field | Value |
|-------|-------|
| **Role** | Mobile app development, platform-specific features, app store deployment |
| **Goal** | Build performant cross-platform mobile applications |
| **Skills** | React Native/Expo/Flutter, native modules, app store guidelines, push notifications |
| **Tools** | GitHub MCP, browser preview, Context7, terminal |
| **Receives From** | Designer, Analyst, Team Lead |
| **Delivers To** | Tester, Team Lead |
| **Quality Gate** | Runs on both platforms, follows platform guidelines, performance targets met |

### 14. Security

| Field | Value |
|-------|-------|
| **Role** | Application security, vulnerability scanning, OWASP compliance, security code reviews |
| **Goal** | Identify and eliminate security vulnerabilities before production |
| **Skills** | SAST/DAST, OWASP Top 10, dependency scanning, secrets detection, security headers, CSP |
| **Tools** | GitHub Security MCP, terminal (npm audit, snyk, trivy), browser tools |
| **Receives From** | Backend Dev, Frontend Dev, Team Lead |
| **Delivers To** | Team Lead, DevOps |
| **Quality Gate** | No critical/high vulns, OWASP addressed, secrets scanning clean |

---

## Orchestration

### Director Workflow

```
Project Request → Director
  1. Analyze request type (feature, bug, release, etc.)
  2. Select appropriate workflow template
  3. Identify required agents for this task
  4. Create task breakdown in GitHub Projects
  5. Spawn agents sequentially or in parallel as needed
  6. Collect and verify outputs via quality gates
  7. Integrate results and report to user
```

### Core Workflows

#### Feature Lifecycle
1. Product Owner writes user story with acceptance criteria
2. Analyst creates technical specification
3. Designer creates UI designs (if UI involved)
4. Team Lead breaks spec into dev tasks
5. DB Admin designs schema changes (if needed)
6. Backend Dev + Frontend Dev + Mobile Dev implement (parallel)
7. Security reviews implementation
8. Tester writes and executes tests
9. Team Lead reviews code (PR review)
10. DevOps deploys to staging
11. SRE verifies monitoring coverage
12. Product Owner performs acceptance testing
13. DevOps deploys to production
14. Marketing updates public-facing content (if needed)

#### Bug Fix
1. Tester reproduces and documents bug
2. Team Lead assigns to appropriate developer
3. Developer fixes with tests
4. Security reviews (if security-related)
5. Tester verifies fix
6. DevOps deploys

#### Release
1. Team Lead coordinates release scope
2. Tester runs full regression
3. Security performs release scan
4. DevOps deploys to staging
5. Product Owner signs off
6. DevOps deploys to production
7. SRE monitors rollout
8. Marketing publishes release notes

---

## MCP & Tool Stack

| Category | MCP Server | Used By |
|----------|-----------|---------|
| Code & PM | GitHub (official) | All agents |
| Database | anydb-mcp / PostgreSQL | DB Admin, Backend Dev |
| Design | Figma (official) | Designer, Frontend Dev |
| Monitoring | Datadog, Sentry | SRE, DevOps |
| Incidents | PagerDuty | SRE |
| Docs | Confluence/Notion | Analyst, Product Owner |
| Communication | Slack | Director, all agents |
| Infrastructure | Terraform, Kubernetes | DevOps |
| Library Docs | Context7 | All developers |
| Security | GitHub Security, Snyk | Security agent |
| CI/CD | GitHub Actions | DevOps, Team Lead |
| Cloud | AWS/GCP MCP | DevOps |

### CLI Tools
- `git` - version control
- `npm`/`yarn`/`pnpm` - package management
- `docker` - containerization
- `terraform` - infrastructure as code
- `kubectl` - Kubernetes management
- `gh` - GitHub CLI
- `snyk`/`trivy` - security scanning
- `playwright` - E2E testing

---

## Agent YAML Spec Format (Portable)

```yaml
agent:
  name: <kebab-case-name>
  version: 1.0.0
  role: "<one-line role description>"
  goal: "<what this agent optimizes for>"

  skills:
    - <skill-1>
    - <skill-2>

  tools:
    mcp_servers:
      - <mcp-name>
    cli:
      - <cli-tool>
    superpowers_skills:
      - <skill-reference>

  quality_gates:
    - name: <gate-name>
      command: "<verification command>"
      required: true|false

  context:
    receives_from: [<agent-names>]
    delivers_to: [<agent-names>]

  constraints:
    - "<rule this agent must follow>"
```

---

## Verification Plan

1. **Structure validation:** All 14 agent directories exist with AGENT.md and agent.yaml
2. **AGENT.md validation:** Each file is valid Claude Code agent format and can be loaded
3. **agent.yaml validation:** Each YAML file parses correctly and follows the schema
4. **Workflow validation:** Feature lifecycle can be traced through all required agents
5. **MCP config validation:** Referenced MCP servers have valid configuration templates
6. **Template validation:** The new-project template bootstraps a working Claude Code setup
7. **Cross-reference check:** Agent `receives_from`/`delivers_to` fields are consistent (no dangling references)
