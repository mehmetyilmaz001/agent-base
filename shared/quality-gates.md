# Shared Quality Gates

Quality gates are verification checkpoints that every agent output must pass before the Director integrates it into the project.

## Universal Gates (All Agents)

### Completeness
- Output addresses all requirements in the task assignment
- No placeholder content, TODOs, or incomplete sections
- All referenced files, functions, or resources actually exist

### Consistency
- Output is consistent with existing project conventions
- Naming conventions followed (files, variables, functions, classes)
- No contradictions with other agent outputs or existing code

### Documentation
- Changes are self-explanatory or include necessary comments
- Public APIs have clear documentation
- Breaking changes are explicitly noted

## Code Quality Gates (Development Agents)

### Tests
- All existing tests pass after changes
- New functionality has corresponding tests
- Test coverage meets project threshold (configurable per project)

### Linting & Formatting
- No linting errors or warnings
- Code formatted according to project standards
- No TypeScript/type errors (if applicable)

### Security
- No hardcoded secrets, keys, or credentials
- Dependencies scanned for known vulnerabilities
- Input validation at system boundaries
- No OWASP Top 10 violations

### Performance
- No obvious performance regressions
- Database queries are optimized (no N+1, proper indexing)
- Assets are appropriately sized and optimized

## Design Quality Gates

### Accessibility
- WCAG AA compliance minimum
- Keyboard navigation works
- Screen reader compatible
- Sufficient color contrast

### Responsiveness
- Works across defined breakpoints
- No horizontal scrolling on mobile
- Touch targets appropriately sized

## Infrastructure Quality Gates

### Deployability
- Changes are deployable without manual intervention
- Rollback plan exists
- Environment variables documented

### Observability
- Critical paths have monitoring
- Alerts configured for failure scenarios
- Logs are structured and searchable

## How to Use

1. Each agent has specific quality gates in their `agent.yaml`
2. The Director checks these gates after receiving agent output
3. If a gate fails, the Director returns the output to the agent with feedback
4. The agent must address all failures before the output is accepted
