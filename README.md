# Agent Base

A multi-domain AI agent team framework for managing projects from scratch to production.

## Overview

Agent Base provides reusable, composable AI agent team definitions organized by domain. Each **base** contains a complete team of specialized agents that can be spawned on-demand to handle different aspects of a project.

## Architecture

- **Orchestrator-Worker Pattern**: A Director agent decomposes tasks, spawns specialized agents, and verifies output through quality gates
- **On-Demand Activation**: All agents are defined as templates — only 3-5 are active concurrently (per Anthropic's best practices)
- **Dual Format**: Each agent has a Claude Code native `AGENT.md` plus a portable `agent.yaml` for framework adaptability
- **Quality Gates**: Every agent output is verified against defined standards before integration

## Structure

```
agent-base/
├── shared/              # Cross-base shared resources
│   ├── quality-gates.md
│   ├── communication-protocol.md
│   └── mcp-configs/
├── bases/
│   ├── software-development/   # 14 agents for full SDLC
│   ├── e-commerce/             # Future
│   └── content-creator/        # Future
└── docs/
```

## Available Bases

### Software Development (14 Agents)

Full software development lifecycle coverage:

| Agent | Role |
|-------|------|
| Director | Orchestrates team, decomposes tasks, verifies quality |
| Product Owner | Defines vision, writes stories, prioritizes backlog |
| Analyst | Requirements gathering, technical specifications |
| Designer | UI/UX design, design systems, accessibility |
| Backend Developer | APIs, business logic, data access |
| Frontend Developer | UI components, state management, performance |
| Tester (QA) | Test plans, automated testing, E2E testing |
| Team Lead | Code review, architecture decisions, sprint management |
| DevOps | CI/CD, infrastructure, deployments |
| Marketing | Landing pages, SEO, content strategy, analytics |
| DB Admin | Schema design, migrations, query optimization |
| SRE / Monitoring | Observability, alerting, incident response |
| Mobile Developer | Cross-platform mobile apps |
| Security | Vulnerability scanning, OWASP compliance, code review |

## Quick Start

1. Copy the `templates/new-project/` directory into your project
2. Configure `project-manifest.yaml` with the agents you need
3. Start working — the Director will orchestrate the team

## Documentation

- [Architecture Overview](docs/architecture.md)
- [Creating a New Base](docs/creating-a-new-base.md)
- [Design Spec](docs/superpowers/specs/2026-03-30-agent-base-design.md)
