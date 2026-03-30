# Project Instructions

This project uses the **mqtech-labs/agent-base** software development base.

## Agent Base

- Base: `software-development`
- Manifest: See `project-manifest.yaml` for activated agents and project settings.
- Agents are defined in the agent base at `bases/software-development/agents/`.

## Project Overview

<!-- Replace with a brief description of this project -->
TODO: Describe the project purpose, target users, and key goals.

## Tech Stack

<!-- Update to match your actual stack -->
- **Language:** TypeScript
- **Runtime:** Node.js
- **Framework:** TODO
- **Database:** TODO
- **Hosting:** TODO

## Code Conventions

- Use the project's configured linter and formatter before committing.
- Write meaningful commit messages following Conventional Commits.
- All public APIs must have JSDoc or equivalent documentation.
- Keep functions short and single-purpose.
- Prefer composition over inheritance.

## Architecture Guidelines

<!-- Describe your project's architecture here -->
TODO: Document folder structure, module boundaries, and data flow.

## Testing Requirements

- All new features require unit tests.
- Integration tests for API endpoints and service boundaries.
- E2E tests for critical user flows.
- Maintain test coverage above the project threshold.

## Workflow

The default workflow is `feature-lifecycle`:

1. **Discovery** -- Product owner and analyst clarify requirements.
2. **Specification** -- Analyst produces technical spec; security reviews constraints.
3. **Design** -- Designer creates UI/UX specs; db-admin designs schema.
4. **Implementation** -- Developers build; team-lead reviews code.
5. **Testing** -- Tester runs automated and exploratory tests.
6. **Deployment** -- DevOps manages CI/CD and rollout.
7. **Monitoring** -- SRE validates health; marketing prepares release comms.

## Agent Coordination

- The **director** assigns tasks and resolves cross-agent blockers.
- The **team-lead** is the quality gate before code is merged.
- The **tester** validates acceptance criteria defined by the **product-owner**.
- The **security** agent reviews all changes touching auth, data access, or third-party integrations.

## Environment Setup

```bash
# Install dependencies
npm install

# Run development server
npm run dev

# Run tests
npm test

# Build for production
npm run build
```

<!-- Update commands above to match your project's tooling -->
