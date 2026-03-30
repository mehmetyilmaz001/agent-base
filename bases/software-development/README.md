# Software Development Agent Base

A structured agent base that provides 14 specialized agents for full-lifecycle software development. Each agent has a defined role, activation triggers, and collaboration patterns that enable teams to move from product discovery through production monitoring.

## Agents

| Agent | Role | Primary Responsibility |
|-------|------|----------------------|
| director | Project Director | Orchestration, milestone tracking, blocker resolution |
| product-owner | Product Owner | Backlog management, user stories, prioritization |
| analyst | Business & Systems Analyst | Requirements, specifications, data flow mapping |
| designer | UX/UI Designer | Wireframes, design tokens, accessibility |
| backend-developer | Backend Developer | APIs, services, data models |
| frontend-developer | Frontend Developer | UI components, client-side logic |
| tester | QA Engineer | Test plans, automated tests, verification |
| team-lead | Technical Team Lead | Code review, architecture, quality gates |
| devops | DevOps Engineer | CI/CD, infrastructure, deployments |
| marketing | Marketing Specialist | Release notes, product copy, launch comms |
| db-admin | Database Administrator | Schema design, migrations, query optimization |
| sre-monitoring | Site Reliability Engineer | Monitoring, alerting, SLOs, incident response |
| mobile-developer | Mobile Developer | Native/cross-platform mobile apps |
| security | Security Engineer | Threat modeling, audits, compliance |

## Workflows

**feature-lifecycle** (default) -- End-to-end feature delivery: discovery, specification, design, implementation, testing, deployment, monitoring.

**bug-fix** -- Rapid turnaround: triage, investigation, fix, verification, deployment.

**infrastructure** -- Platform changes with safety: planning, implementation, review, rollout, monitoring.

## Getting Started

1. Copy the `templates/new-project/` directory into your project root.
2. Edit `project-manifest.yaml` to select the agents you need and specify your tech stack.
3. Configure `.claude/settings.json` with your MCP server credentials.
4. Customize `CLAUDE.md` with project-specific conventions and guidelines.

## MCP Server Requirements

**Required:** GitHub (repository management, PRs, issues).

**Optional:** Linear, Slack, Sentry, Supabase, Figma, Context7, Vercel, Firebase.

## Project Structure

```
bases/software-development/
  manifest.yaml          # Base metadata, agent list, workflows, MCP config
  README.md              # This file
  agents/                # Individual agent definitions (AGENT.md files)
  workflows/             # Workflow definitions and stage configs
  templates/
    new-project/         # Scaffold for new projects using this base
      .claude/
        settings.json    # MCP server configuration template
      CLAUDE.md          # Project-level instructions template
      project-manifest.yaml  # Agent selection and project settings
```
