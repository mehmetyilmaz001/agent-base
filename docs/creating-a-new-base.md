# Creating a New Base

This guide walks through creating a new agent base -- a domain-specific team of AI agents within the Agent Base framework.

## When to Create a New Base vs. Extend an Existing One

**Create a new base when:**
- The domain requires a fundamentally different set of agent roles (e.g., e-commerce needs a Catalog Agent, Payment Agent, and Fulfillment Agent that have no equivalent in software development).
- The workflows are domain-specific and cannot be expressed as variations of an existing base's workflows.
- The quality gates require domain-specific criteria (e.g., PCI compliance for payment systems, HIPAA for healthcare).

**Extend an existing base when:**
- You need the same roles but with additional specialization (e.g., adding a "Data Engineer" agent to the software development base).
- You need a subset of an existing base's agents for a narrower use case.
- The workflows are minor variations of existing ones.

## Directory Structure

Every base follows this structure:

```
bases/
└── your-base-name/
    ├── manifest.yaml              # Base metadata and agent registry
    ├── agents/
    │   ├── director/              # Required: every base needs a Director
    │   │   ├── AGENT.md
    │   │   └── agent.yaml
    │   ├── agent-two/
    │   │   ├── AGENT.md
    │   │   └── agent.yaml
    │   └── agent-three/
    │       ├── AGENT.md
    │       └── agent.yaml
    ├── workflows/
    │   ├── primary-workflow.md
    │   └── secondary-workflow.md
    └── templates/
        └── new-project/
            ├── .claude/
            │   ├── settings.json
            │   └── agents/        # Symlinks or copies of agent AGENT.md files
            ├── CLAUDE.md
            └── project-manifest.yaml
```

### Naming Conventions

- **Base directory**: kebab-case, descriptive of the domain (e.g., `e-commerce`, `content-creator`, `data-pipeline`).
- **Agent directories**: kebab-case, matching the agent's role name (e.g., `catalog-agent`, `payment-agent`).
- **Workflow files**: kebab-case, named after the scenario they cover (e.g., `product-launch.md`, `order-fulfillment.md`).

## Step 1: Create the manifest.yaml

The manifest is the entry point for the base. It declares metadata, lists all agents, and references workflows.

```yaml
base:
  name: your-base-name
  version: 1.0.0
  description: "One-line description of what this base covers."
  domain: "your-domain"

agents:
  - name: director
    role: "Team Orchestrator"
    path: agents/director/
  - name: agent-two
    role: "Role description"
    path: agents/agent-two/
  - name: agent-three
    role: "Role description"
    path: agents/agent-three/

workflows:
  - name: primary-workflow
    description: "Description of when this workflow is used"
    path: workflows/primary-workflow.md
  - name: secondary-workflow
    description: "Description of when this workflow is used"
    path: workflows/secondary-workflow.md

shared_resources:
  quality_gates: ../../shared/quality-gates.md
  communication_protocol: ../../shared/communication-protocol.md
  mcp_configs: ../../shared/mcp-configs/
```

## Step 2: Define Your Agents

Every agent needs two files: `AGENT.md` and `agent.yaml`. Start with the Director (every base must have one), then define domain-specific agents.

### AGENT.md Format

The `AGENT.md` is a Claude Code agent file with YAML frontmatter and a detailed system prompt in markdown.

```markdown
---
name: your-agent-name
description: One-line summary of what this agent does.
---

# Agent Display Name

You are the [Agent Name] agent -- [brief identity statement]. [What you are
responsible for and what you optimize for].

## Core Responsibilities

1. **Responsibility One** -- Description.
2. **Responsibility Two** -- Description.
3. **Responsibility Three** -- Description.

## Workflow

Step-by-step instructions for how this agent approaches its work.

### Step 1: Understand the Task
- Read the task assignment and acceptance criteria.
- Review any input artifacts from upstream agents.
- Ask clarifying questions through the Director if anything is ambiguous.

### Step 2: Execute
- Detailed instructions for the agent's work process.
- Domain-specific guidance and standards.

### Step 3: Verify
- Self-check against quality gate criteria before submitting.

## Working with Other Agents

| Agent | How You Interact |
|---|---|
| Director | Receive tasks, report output, escalate blockers. |
| Upstream Agent | Receive [specific artifacts]. |
| Downstream Agent | Deliver [specific artifacts]. |

## Constraints

- Hard rules this agent must follow.
- "Never do X without Y."
- "Always verify Z before submitting."

## Quality Gate

Your output passes its quality gate when:
- Criterion 1.
- Criterion 2.
- Criterion 3.

## Output Format

When delivering completed work, provide:

[Structured template for the agent's output]
```

### agent.yaml Format

The `agent.yaml` is the machine-readable, portable companion.

```yaml
agent:
  name: your-agent-name
  version: 1.0.0
  role: "One-line role title"
  goal: "What this agent optimizes for -- its north star."

  skills:
    - skill-one
    - skill-two
    - skill-three

  tools:
    mcp_servers:
      - github                    # Reference to shared/mcp-configs/github.yaml
    cli:
      - git
      - domain-specific-cli
    superpowers_skills:
      - relevant-skill

  quality_gates:
    - name: gate-name
      command: "Verification command or description"
      required: true              # true = must pass; false = advisory
    - name: another-gate
      command: "npm test"
      required: true

  context:
    receives_from:
      - director                  # Must match agent names in the base
      - upstream-agent
    delivers_to:
      - downstream-agent
      - director

  constraints:
    - "Rule one"
    - "Rule two"
```

### Key Rules for Agent Definitions

1. **The `name` field must match** between `AGENT.md` frontmatter, `agent.yaml`, and the directory name.
2. **`receives_from` and `delivers_to` must reference valid agent names** within the same base. The Director can deliver to all agents and receive from all agents.
3. **Quality gates in `agent.yaml` must align** with the quality gate section in `AGENT.md`. The YAML is used for automated checking; the markdown provides human context.
4. **MCP servers referenced in `tools.mcp_servers`** should have corresponding configs in `shared/mcp-configs/` or be documented in the base.
5. **Keep `AGENT.md` self-contained.** An agent should be able to operate with only its `AGENT.md` and the shared resources. Do not rely on implicit knowledge.

## Step 3: Create Workflows

Workflows define the standard sequences of agent activations for common scenarios in your domain.

```markdown
# Workflow Name

Description of when this workflow is triggered and what it produces.

## Trigger

What kind of request activates this workflow.

## Agent Sequence

1. **Agent A** -- What they do in this workflow.
   - Input: What they receive.
   - Output: What they produce.
   - Quality gate: Key verification criteria.

2. **Agent B** -- What they do.
   - Input: Output from Agent A.
   - Output: What they produce.

3. **Agent C + Agent D** (parallel) -- What they do.
   - Input: Outputs from Agent A and Agent B.
   - Output: What they produce.

4. **Agent E** -- Final verification.
   - Input: Outputs from Agent C and Agent D.
   - Output: Final deliverable.

## Diagram

[ASCII diagram showing the flow]

## Variations

- If [condition], skip Agent B and go directly to Agent C.
- If [condition], add Agent F after Agent E.
```

## Step 4: Reference Shared Resources

Bases should reference `shared/` resources rather than duplicating them.

### Quality Gates

Your agents inherit the universal quality gates from `shared/quality-gates.md`. You can add domain-specific gates in your agent definitions, but the universal gates (completeness, consistency, documentation) always apply.

### Communication Protocol

All bases follow the communication protocol defined in `shared/communication-protocol.md`. The hub-and-spoke pattern with the Director at center is mandatory.

### MCP Configs

Reference shared MCP configs by name in your agent's `agent.yaml`:

```yaml
tools:
  mcp_servers:
    - github      # Uses shared/mcp-configs/github.yaml
    - database    # Uses shared/mcp-configs/database.yaml
```

If your domain needs MCP servers not in `shared/`, you have two options:
1. Add the config to `shared/mcp-configs/` if it could be useful to other bases.
2. Create a base-local `mcp-configs/` directory for domain-specific servers.

## Step 5: Create the Project Template

The `templates/new-project/` directory provides starter files for bootstrapping a project that uses your base.

### project-manifest.yaml

```yaml
project:
  name: "my-project"
  base: "your-base-name"
  version: 1.0.0

active_agents:
  - director          # Always active
  - agent-two         # Include agents relevant to this project
  - agent-three

settings:
  max_concurrent_agents: 5
  default_workflow: "primary-workflow"

mcp_servers:
  github:
    token_env: "GITHUB_TOKEN"
    repo: "org/repo-name"
  # Add other MCP server configs as needed

quality_overrides:
  # Override default thresholds per project
  test_coverage_threshold: 80
```

### CLAUDE.md (Project-Level)

Create a `CLAUDE.md` that references the base and provides project-specific context:

```markdown
# Project Name

This project uses the [base-name] agent base.

## Agent Base

- Base: bases/your-base-name/
- Active agents: [list]

## Project Conventions

- [Project-specific conventions]
- [Technology choices]
- [Coding standards]

## How to Work

- The Director agent orchestrates all work.
- Use the project-manifest.yaml to see which agents are active.
- Follow shared/communication-protocol.md for all agent interactions.
```

## Testing and Validation Checklist

Before considering a new base complete, verify:

### Structure Validation
- [ ] All agent directories contain both `AGENT.md` and `agent.yaml`.
- [ ] Directory names match agent `name` fields.
- [ ] `manifest.yaml` lists all agents and workflows.

### AGENT.md Validation
- [ ] Each `AGENT.md` has valid YAML frontmatter with `name` and `description`.
- [ ] Each file defines: responsibilities, workflow, constraints, quality gate, and output format.
- [ ] Instructions are detailed enough for the agent to operate independently.

### agent.yaml Validation
- [ ] Each `agent.yaml` parses as valid YAML.
- [ ] Required fields present: `name`, `version`, `role`, `goal`, `skills`, `tools`, `quality_gates`, `context`, `constraints`.
- [ ] `quality_gates` entries have `name`, `command`, and `required` fields.

### Cross-Reference Validation
- [ ] Every agent listed in `receives_from` exists in the base.
- [ ] Every agent listed in `delivers_to` exists in the base.
- [ ] No dangling references (agent A delivers to agent B, but agent B does not list agent A in `receives_from` or vice versa -- unless mediated by the Director).
- [ ] MCP servers referenced in agent YAMLs have configs in `shared/mcp-configs/` or are documented.

### Workflow Validation
- [ ] Each workflow references only agents that exist in the base.
- [ ] Workflows cover the primary use cases for the domain.
- [ ] The Director's `AGENT.md` includes workflow selection logic for all defined workflows.

### Template Validation
- [ ] `templates/new-project/` contains `CLAUDE.md` and `project-manifest.yaml`.
- [ ] The template can bootstrap a working Claude Code setup when copied into a new project.

## Example: E-Commerce Base

To make the process concrete, here is a sketch of what an e-commerce base might look like.

### Agents (8 agents)

| Agent | Role | Key Responsibilities |
|---|---|---|
| **Director** | Team Orchestrator | Decomposes e-commerce goals, spawns agents, verifies quality |
| **Store Manager** | Store Strategy | Product strategy, pricing, promotions, inventory policies |
| **Catalog Agent** | Product Catalog | Product listings, categories, attributes, search indexing, media |
| **Payment Agent** | Payment Processing | Payment gateway integration, checkout flows, PCI compliance |
| **Order Agent** | Order Management | Order lifecycle, fulfillment, returns, refunds, notifications |
| **Customer Agent** | Customer Experience | Accounts, support flows, reviews, loyalty programs |
| **Analytics Agent** | Data and Insights | Sales analytics, funnel tracking, A/B testing, reporting |
| **Storefront Agent** | Frontend Implementation | UI components, cart, checkout UX, responsive design |

### Directory Structure

```
bases/
└── e-commerce/
    ├── manifest.yaml
    ├── agents/
    │   ├── director/
    │   │   ├── AGENT.md
    │   │   └── agent.yaml
    │   ├── store-manager/
    │   │   ├── AGENT.md
    │   │   └── agent.yaml
    │   ├── catalog-agent/
    │   │   ├── AGENT.md
    │   │   └── agent.yaml
    │   ├── payment-agent/
    │   │   ├── AGENT.md
    │   │   └── agent.yaml
    │   ├── order-agent/
    │   │   ├── AGENT.md
    │   │   └── agent.yaml
    │   ├── customer-agent/
    │   │   ├── AGENT.md
    │   │   └── agent.yaml
    │   ├── analytics-agent/
    │   │   ├── AGENT.md
    │   │   └── agent.yaml
    │   └── storefront-agent/
    │       ├── AGENT.md
    │       └── agent.yaml
    ├── workflows/
    │   ├── product-launch.md
    │   ├── order-fulfillment.md
    │   ├── payment-integration.md
    │   └── storefront-redesign.md
    └── templates/
        └── new-project/
            ├── CLAUDE.md
            └── project-manifest.yaml
```

### Example: Catalog Agent (agent.yaml)

```yaml
agent:
  name: catalog-agent
  version: 1.0.0
  role: "Product Catalog Manager"
  goal: "Maintain a well-organized, searchable, and complete product catalog with accurate listings, media, and categorization."

  skills:
    - product-data-modeling
    - category-taxonomy
    - search-optimization
    - media-management
    - bulk-import-export
    - inventory-sync

  tools:
    mcp_servers:
      - github
      - database
    cli:
      - git
      - npm
    superpowers_skills:
      - systematic-debugging
      - verification-before-completion

  quality_gates:
    - name: data-integrity
      command: "verify all products have required fields (name, SKU, price, description, category)"
      required: true
    - name: search-indexing
      command: "verify products are discoverable via search with relevant keywords"
      required: true
    - name: media-validation
      command: "verify all product images meet size/format requirements and have alt text"
      required: true
    - name: category-consistency
      command: "verify category taxonomy has no orphans and all products are categorized"
      required: true

  context:
    receives_from:
      - store-manager
      - director
    delivers_to:
      - storefront-agent
      - analytics-agent
      - order-agent

  constraints:
    - "Every product must have a unique SKU"
    - "Category changes must maintain backward-compatible URLs"
    - "Product descriptions must be SEO-optimized"
    - "Bulk operations must be idempotent and resumable"
    - "Media assets must be optimized for web delivery"
```

### Example Workflow: Product Launch

```
Store Manager: Define product strategy and pricing
       │
Director: Verifies strategy (quality gate: pricing validated, margins checked)
       │
Catalog Agent: Create product listings, categories, media
       │
Director: Verifies catalog (quality gate: data integrity, search indexing)
       │
Payment Agent: Configure pricing rules and tax calculations
       │
Director: Verifies payment config (quality gate: PCI compliance)
       │
┌──────┴──────┐
│             │
Storefront    Analytics
Agent:        Agent:
Build PDP,    Set up
PLP, search   tracking
│             │
Director: Verifies both (quality gates: responsive, accessible, tracking live)
       │
Order Agent: Configure inventory and fulfillment rules
       │
Director: Verifies (quality gate: order flow tested end-to-end)
       │
Director: Compiles launch checklist, reports to user
```

This example demonstrates how the same Orchestrator-Worker pattern, quality gate system, and communication protocol from the `shared/` resources apply to a completely different domain. The structure is consistent; only the agent roles, skills, and domain-specific gates change.
