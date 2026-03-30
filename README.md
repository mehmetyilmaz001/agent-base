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

---

## Sample Prompts

Real-world prompt examples to get the most out of each agent. Copy-paste these into Claude Code when working on any project.

### Director — Full Orchestration

The Director is your entry point for any multi-step task. It decomposes the work and spawns the right agents.

**Build a complete feature from scratch:**
```
Use the director agent to implement a user notification system.

Requirements:
- Users can receive in-app and email notifications
- Notification preferences (opt-in/opt-out per channel)
- Mark as read/unread, bulk actions
- Real-time updates via WebSocket

Please follow the feature-lifecycle workflow.
```

**Start a new project sprint:**
```
Use the director agent to plan and execute Sprint 3.

Here are the user stories for this sprint:
1. As a user, I want to filter products by price range
2. As an admin, I want to export reports as CSV
3. As a user, I want to receive email when my order ships

Break these into tasks, assign to appropriate agents, and coordinate delivery.
```

**Handle a production incident:**
```
Use the director agent to run the bug-fix workflow.

Bug: Users report 500 errors when submitting the checkout form.
Error from logs: "TypeError: Cannot read property 'address' of undefined"
Endpoint: POST /api/orders
Severity: P0 — checkout is completely broken in production.
```

**Coordinate a release:**
```
Use the director agent to run the release workflow for v2.1.0.

Changes since last release:
- New notification system (#142)
- Fixed cart calculation bug (#138)
- Updated supplier API integration (#145)

Ensure regression tests pass, security scan is clean, and release notes are ready.
```

---

### Product Owner — Stories & Backlog

**Write user stories from a feature idea:**
```
Use the product-owner agent to write user stories for a "Saved Searches" feature.

Context: Users want to save their search filters and get notified when new
products match. This is for an e-commerce product discovery platform.

Write stories with acceptance criteria in Given/When/Then format.
Prioritize using WSJF framework.
```

**Prioritize the backlog:**
```
Use the product-owner agent to review and prioritize our current backlog.

We have 12 open issues in GitHub. Please analyze them by:
- Business value and user impact
- Dependencies between stories
- Effort estimation (S/M/L/XL)

Recommend what goes into the next sprint (2-week capacity, 2 developers).
```

**Acceptance testing:**
```
Use the product-owner agent to perform acceptance testing on the
notification feature deployed to staging.

Check each acceptance criterion from story #42:
- [ ] User receives in-app notification within 5 seconds
- [ ] Email notification arrives within 1 minute
- [ ] User can mute specific notification types
- [ ] Notification badge shows unread count
```

---

### Analyst — Specs & Research

**Create a technical specification:**
```
Use the analyst agent to create a technical specification for adding
multi-tenant support to our SaaS platform.

Requirements:
- Each tenant has isolated data
- Shared database with row-level isolation
- Tenant-specific settings and branding
- Admin can manage tenants

Include data models, API contracts, migration strategy, and edge cases.
```

**Research and recommend a technology:**
```
Use the analyst agent to research and recommend a real-time communication
solution for our app.

Options to evaluate:
- WebSocket (Socket.io)
- Server-Sent Events (SSE)
- Pusher/Ably (managed service)

Compare: latency, scalability, cost, complexity, and browser support.
Recommend the best fit for our Next.js + PostgreSQL stack.
```

---

### Backend Developer — APIs & Logic

**Implement an API endpoint:**
```
Use the backend-developer agent to implement the REST API for user notifications.

Spec:
- GET /api/notifications — list user's notifications (paginated)
- POST /api/notifications/:id/read — mark as read
- PUT /api/notifications/preferences — update notification settings
- WebSocket channel for real-time delivery

Follow TDD. Write tests first, then implement.
Use the existing repository pattern in lib/db/queries/.
```

**Build an integration:**
```
Use the backend-developer agent to integrate the Stripe webhook handler.

Requirements:
- Listen for: checkout.session.completed, invoice.paid, subscription.updated
- Verify webhook signatures
- Update our database subscription status accordingly
- Handle idempotency (don't process the same event twice)
- Add comprehensive error handling and logging

Write tests with mocked Stripe events first.
```

**Optimize a slow query:**
```
Use the backend-developer agent to optimize the dashboard analytics query.

The GET /api/dashboard/stats endpoint takes 4.2 seconds.
It aggregates: total revenue, order count, top products, and daily trends.

Profile the query, suggest indexes, and consider caching strategies.
Current implementation is in lib/db/queries/dashboard.ts.
```

---

### Frontend Developer — UI & Components

**Build a UI component:**
```
Use the frontend-developer agent to build a notification center component.

Requirements:
- Bell icon with unread badge in the header
- Dropdown panel showing recent notifications
- Each notification: icon, title, message, timestamp, read/unread state
- "Mark all as read" action
- Click to navigate to the relevant page
- Empty state when no notifications

Use shadcn/ui components. Follow existing component patterns.
Ensure accessibility (keyboard navigation, screen reader support).
```

**Implement a complex page:**
```
Use the frontend-developer agent to build the analytics dashboard page.

Layout:
- Top: date range picker + comparison toggle
- Row 1: 4 KPI cards (revenue, orders, customers, conversion rate)
- Row 2: line chart (daily trends) + bar chart (top products)
- Row 3: data table with sorting, filtering, and CSV export

Use Recharts for charts. Server Components for data fetching.
Client Components only for interactive elements.
```

**Fix a performance issue:**
```
Use the frontend-developer agent to fix the product listing page performance.

Symptoms:
- Page takes 3+ seconds to become interactive
- Scrolling is janky with 500+ products
- Lighthouse performance score is 42

Investigate: bundle size, unnecessary re-renders, image optimization,
virtualization for long lists. Target: Lighthouse >90.
```

---

### Tester (QA) — Tests & Quality

**Write a comprehensive test suite:**
```
Use the tester agent to write tests for the notification system.

Cover:
1. Unit tests for notification service (create, read, update, delete)
2. API tests for all notification endpoints
3. Edge cases: empty state, pagination boundaries, invalid IDs
4. WebSocket tests: connection, real-time delivery, reconnection

Use the existing test patterns. Co-locate tests as *.test.ts files.
Target: 90% coverage for the notification module.
```

**Create an E2E test flow:**
```
Use the tester agent to write Playwright E2E tests for the checkout flow.

Test scenario:
1. User logs in
2. Adds 2 products to cart
3. Proceeds to checkout
4. Fills shipping address
5. Enters payment details
6. Submits order
7. Sees confirmation page with order number

Test both happy path and error cases (invalid card, out of stock).
```

**Run regression and report:**
```
Use the tester agent to run the full test suite and create a quality report.

Check:
- All unit tests pass
- All integration tests pass
- Coverage meets 80% threshold
- No flaky tests (run twice to confirm)
- List any newly failing tests with root cause analysis
```

---

### Team Lead — Code Review & Architecture

**Review a pull request:**
```
Use the team-lead agent to review the changes in the current branch.

Focus on:
- Architecture compliance (follows existing patterns?)
- Code quality (DRY, SOLID, readability)
- Test coverage (sufficient? edge cases?)
- Security concerns
- Performance implications
- Breaking changes

Provide actionable feedback with specific file/line references.
```

**Make an architecture decision:**
```
Use the team-lead agent to evaluate and document an architecture decision.

Decision: Should we use a message queue for async notifications?

Options:
A) Direct processing in the API handler
B) Redis-based queue (BullMQ)
C) Database-backed queue (our existing pattern)

Consider: reliability, complexity, scalability, operational cost.
Document as an ADR (Architecture Decision Record).
```

**Conduct a code health review:**
```
Use the team-lead agent to review the overall codebase health.

Check for:
- Files that are too large (>300 lines) and should be split
- Duplicated logic across modules
- Inconsistent patterns or naming
- Missing or outdated documentation
- Dependency versions that need updating
- TODO/FIXME comments that should be addressed

Prioritize findings by impact and create issues for the top 5.
```

---

### DevOps — CI/CD & Infrastructure

**Set up a CI/CD pipeline:**
```
Use the devops agent to create a GitHub Actions CI/CD pipeline.

Requirements:
- On PR: lint, type-check, test, build
- On merge to main: deploy to staging automatically
- On release tag: deploy to production
- Slack notification on failure
- Cache node_modules and .next/cache for speed

Target: CI should complete in under 5 minutes.
```

**Dockerize a service:**
```
Use the devops agent to create a production Dockerfile for the Next.js app.

Requirements:
- Multi-stage build (deps → build → production)
- Minimal final image (alpine or distroless)
- Non-root user
- Health check endpoint
- Environment variable injection at runtime
- Output: standalone Next.js server

Also update docker-compose.yml to include the new service.
```

**Set up monitoring infrastructure:**
```
Use the devops agent to configure monitoring for our staging environment.

Set up:
- Health check endpoints for all services
- Docker container resource monitoring
- Log aggregation (structured JSON logs)
- Alerting rules: service down, high error rate, disk usage >80%
- Dashboard showing service status at a glance
```

---

### DB Admin — Schema & Performance

**Design a database schema:**
```
Use the db-admin agent to design the schema for the notification system.

Requirements:
- notifications table: id, user_id, type, title, message, data (JSON),
  read_at, created_at
- notification_preferences: user_id, channel (in_app/email/push), enabled
- Support for bulk operations (mark all read, delete old)
- Efficient queries: unread count, paginated list, filter by type

Create the Drizzle schema and migration. Add appropriate indexes.
```

**Optimize database performance:**
```
Use the db-admin agent to audit and optimize our database performance.

Symptoms:
- Dashboard query takes 4+ seconds
- Product search with filters is slow (1.5s)
- Database CPU usage is consistently >70%

Investigate:
- Missing indexes
- N+1 query patterns
- Expensive joins that could be denormalized
- Queries that need EXPLAIN ANALYZE
- Connection pool sizing
```

**Plan a data migration:**
```
Use the db-admin agent to plan the migration from single-tenant to
multi-tenant data model.

Current: All data in shared tables without tenant isolation.
Target: Add tenant_id to all tables, row-level security policies.

Plan:
- Migration steps (reversible!)
- Data backfill strategy
- Zero-downtime approach
- Rollback procedure
- Verification queries to confirm data integrity
```

---

### Security — Audits & Hardening

**Security audit before launch:**
```
Use the security agent to perform a pre-launch security audit.

Check:
- OWASP Top 10 compliance
- Authentication flow (session handling, token expiry, refresh)
- API endpoint authorization (who can access what?)
- Input validation at all boundaries
- Dependency vulnerabilities (npm audit)
- Security headers (CSP, HSTS, X-Frame-Options)
- Secrets management (no hardcoded keys)
- SQL/NoSQL injection vectors
- XSS prevention in user-generated content

Provide a prioritized report with P0/P1/P2 severity levels.
```

**Review authentication implementation:**
```
Use the security agent to review our NextAuth v5 implementation.

Check:
- OAuth callback handling (CSRF protection)
- Session strategy (JWT vs database sessions)
- Token rotation and expiry
- Account linking security
- Rate limiting on auth endpoints
- Password reset flow (if applicable)
- RBAC implementation for admin/user roles
```

**Dependency audit:**
```
Use the security agent to audit all project dependencies.

Run:
- npm audit for known vulnerabilities
- Check for outdated packages with known CVEs
- Review packages that have broad filesystem/network access
- Flag any packages with fewer than 100 weekly downloads
- Identify packages that haven't been updated in 12+ months

Create a remediation plan for any findings.
```

---

### Marketing — SEO & Content

**SEO audit and optimization:**
```
Use the marketing agent to audit and improve our landing page SEO.

Check:
- Meta tags (title, description, OG tags)
- Heading hierarchy (H1-H6)
- Image alt texts
- Page load speed impact on SEO
- Mobile-friendliness
- Schema.org structured data
- Internal linking strategy

Target: SEO score >90 on all key pages.
Provide specific code changes for each improvement.
```

**Write launch content:**
```
Use the marketing agent to create launch content for our new
"AI Product Recommendations" feature.

Create:
- Landing page headline and subheadline (3 variants to A/B test)
- Feature description (150 words, benefit-focused)
- 3 customer use cases with before/after scenarios
- CTA copy for free trial signup
- Meta description for SEO

Tone: Professional but approachable. Target: e-commerce entrepreneurs.
```

---

### SRE / Monitoring — Observability

**Set up monitoring for a new service:**
```
Use the sre-monitoring agent to set up observability for our notification service.

Requirements:
- Health check endpoint (/health with dependency status)
- Metrics: delivery rate, latency p50/p95/p99, error rate, queue depth
- Alerts: delivery failure rate >5%, latency p99 >2s, queue backlog >1000
- Dashboard: real-time notification flow, delivery success/failure pie chart
- Runbook: what to do when delivery failures spike

Define SLOs:
- 99.9% delivery success rate
- p95 latency <500ms for in-app notifications
```

---

### Mobile Developer — Apps

**Build a mobile feature:**
```
Use the mobile-developer agent to implement push notifications
in our React Native app.

Requirements:
- Register for push notifications on app launch
- Handle notification taps (navigate to relevant screen)
- Display in-app notification banner
- Badge count on app icon
- Notification preferences screen

Use Expo Notifications. Test on both iOS and Android.
Follow existing navigation patterns with Expo Router.
```

---

### Designer — UI/UX

**Create a design system component:**
```
Use the designer agent to design the notification center component.

Requirements:
- Fits within existing design system
- Bell icon states: default, unread (with count badge), active
- Notification panel: floating dropdown, max 5 visible, scroll for more
- Notification item: icon (by type), title, preview text, time ago, dot for unread
- Empty state illustration and message
- Dark mode variant
- Mobile responsive (full-screen sheet on mobile)

Ensure WCAG AA accessibility compliance.
Provide specs for all states and breakpoints.
```

---

### Combining Agents — Power Patterns

**Full feature with Director orchestration (recommended for complex work):**
```
Use the director agent to implement a complete "Team Collaboration" feature.

Vision: Allow multiple users to work on the same project with real-time updates.

I need:
- User stories with acceptance criteria
- Technical spec covering real-time sync strategy
- Database schema for teams, roles, and permissions
- Backend APIs for team CRUD + invitation flow
- Frontend components for team management UI
- Real-time presence indicators
- Comprehensive test coverage
- Security review for multi-tenant data isolation

Follow the feature-lifecycle workflow. Deploy to staging when ready.
```

**Quick single-agent tasks (for focused, scoped work):**
```
# Fix a specific bug
Use the backend-developer agent to fix the date timezone bug in
GET /api/reports?startDate=2026-01-01. Dates are off by 1 day
for users in UTC+ timezones.

# Add a single test
Use the tester agent to add tests for the edge case where a user
has no notification preferences set (should default to all enabled).

# Quick code review
Use the team-lead agent to review just the files changed in the
last commit and flag any issues.

# Schema change
Use the db-admin agent to add a "last_login_at" timestamp column
to the users table with a reversible migration.
```

---

## Documentation

- [Architecture Overview](docs/architecture.md)
- [Creating a New Base](docs/creating-a-new-base.md)
- [Design Spec](docs/superpowers/specs/2026-03-30-agent-base-design.md)
