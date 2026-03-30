# Agent Base Framework

This repository contains reusable AI agent team definitions organized by domain.

## Project Structure

- `shared/` — Cross-base shared resources (quality gates, communication protocols, MCP configs)
- `bases/` — Domain-specific agent bases (each with agents/, workflows/, templates/)
- `docs/` — Framework documentation

## Conventions

- Agent definitions use dual format: `AGENT.md` (Claude Code native) + `agent.yaml` (portable)
- Orchestration follows the Orchestrator-Worker pattern — Director mediates all agent communication
- Agents are spawned on-demand, never all at once (3-5 concurrent max)
- Every agent output must pass its quality gates before integration
- Workflows define the sequence of agent activations for common scenarios

## Working With This Repo

- When creating a new agent, always create both `AGENT.md` and `agent.yaml`
- Keep agent definitions self-contained within their directory
- Reference `shared/` resources rather than duplicating them
- Update the base `manifest.yaml` when adding or removing agents
- Follow existing naming conventions: kebab-case for directories, clear role names
