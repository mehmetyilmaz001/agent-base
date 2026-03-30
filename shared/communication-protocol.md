# Communication Protocol

How agents communicate within the Orchestrator-Worker framework.

## Core Principle

**All communication flows through the Director.** Agents do not communicate directly with each other. The Director mediates all handoffs, resolves conflicts, and maintains project coherence.

## Message Flow

```
User/Stakeholder
       │
       ▼
   Director ──────────────────────────────┐
       │         │         │              │
       ▼         ▼         ▼              ▼
   Agent A   Agent B   Agent C    ... Agent N
       │         │         │              │
       └─────────┴─────────┴──────────────┘
                         │
                         ▼
                     Director
                    (verifies)
```

## Task Assignment Format

When the Director assigns a task to an agent, it provides:

```yaml
task:
  id: "TASK-001"
  type: "implementation"  # implementation, review, design, test, deploy, etc.
  title: "Implement user authentication API"
  description: |
    Clear description of what needs to be done.
  context:
    - Previous agent outputs relevant to this task
    - Links to specs, designs, or other references
  acceptance_criteria:
    - Specific, measurable criteria for completion
  dependencies:
    - Tasks that must be completed first
  delivers_to: "team-lead"  # Who receives the output next
```

## Output Format

When an agent completes a task, it reports back to the Director:

```yaml
output:
  task_id: "TASK-001"
  status: "completed"  # completed, blocked, needs-clarification
  summary: "Brief description of what was done"
  artifacts:
    - files_changed: ["path/to/file1", "path/to/file2"]
    - tests_added: ["path/to/test"]
  quality_gate_results:
    - gate: "tests-pass"
      status: "pass"
    - gate: "lint-clean"
      status: "pass"
  notes: "Any additional context for the next agent"
  blockers: []  # If status is "blocked", describe what's blocking
```

## Escalation Rules

1. **Agent is blocked**: Reports to Director with blockers. Director resolves by spawning another agent or asking the user.
2. **Quality gate fails**: Director returns output to agent with specific feedback. Agent must fix and resubmit.
3. **Conflicting outputs**: Director resolves by consulting the Team Lead agent or making a judgment call.
4. **Scope creep detected**: Director flags to Product Owner for prioritization decision.
5. **User input needed**: Director escalates to user with clear options.

## Handoff Protocol

When work passes from one agent to another (mediated by Director):

1. Director collects output from Agent A
2. Director verifies quality gates
3. Director adds relevant context from Agent A's output to Agent B's task
4. Director spawns Agent B with the enriched task
5. Agent B can request clarification through Director if needed
