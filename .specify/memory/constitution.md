<!--
Sync Impact Report
- Version change: template (unversioned) → 1.0.0
- Modified principles:
	- Template principle 1 → I. Maintainable Modular Design
	- Template principle 2 → II. Secure Defensive Engineering
	- Template principle 3 → III. Verification by Default
	- Template principle 4 → IV. Traceability and Documentation
	- Template principle 5 → V. Reviewable Quality Gates
- Added sections:
	- Engineering Standards & Constraints
	- Delivery Workflow & Quality Gates
- Removed sections:
	- None
- Templates requiring updates:
	- ✅ .specify/templates/plan-template.md
	- ✅ .specify/templates/spec-template.md
	- ✅ .specify/templates/tasks-template.md
	- ✅ .github/copilot-instructions.md (reviewed; no changes required)
	- ✅ .specify/templates/commands/*.md (not present; no action required)
- Follow-up TODOs:
	- None
-->

# workingprj Constitution

## Core Principles

### I. Maintainable Modular Design
All production code MUST be organized for readability, reviewability, and long-term
maintenance. Modules, classes, and functions MUST have a single clear responsibility,
explicit interfaces, and bounded side effects. New dependencies, frameworks, and
architectural patterns MUST be introduced only when simpler approved approaches are
insufficient, and that decision MUST be documented in the plan or an architecture note.

Rationale: Siemens-aligned engineering favors predictable, maintainable systems over
clever or fragile implementations.

### II. Secure Defensive Engineering
Code MUST validate all external inputs, sanitize untrusted data, handle errors
explicitly, and prefer fail-safe behavior. Secrets MUST never be hardcoded,
committed, or written to logs. Security-relevant changes MUST follow secure-by-default
practices aligned with OWASP guidance, CERT secure coding principles, and stricter
enterprise or regulatory controls where they apply.

Rationale: defensive engineering reduces operational, security, and safety risk in
enterprise environments.

### III. Verification by Default
Every new behavior and every bug fix MUST include automated verification appropriate
to its risk, typically including unit tests and integration or regression coverage for
critical paths. Static analysis, linting, formatting, and CI validation MUST pass
before merge. A change is not complete until failing tests or checks demonstrate the
intended behavior or defect and then pass with the implementation.

Rationale: verified changes lower regression risk and support reliable delivery.

### IV. Traceability and Documentation
Requirements, design decisions, implementation tasks, tests, and release notes MUST
be traceable across spec, plan, tasks, code, and review artifacts. Non-obvious design
choices, assumptions, safety implications, and operational constraints MUST be
documented close to the affected feature. Breaking changes MUST include explicit
impact analysis, migration guidance, and backward-compatibility notes.

Rationale: traceability enables compliance, efficient reviews, and safer maintenance.

### V. Reviewable Quality Gates
All changes MUST pass peer review and objective quality gates before merge. Pull
requests MUST show compliance with this constitution, summarize security and
reliability impacts, and justify any complexity or standards deviation. If Siemens
internal standards or domain regulations impose stricter requirements than this
constitution, the stricter requirement governs.

Rationale: transparent, enforceable gates are required for enterprise engineering
discipline.

## Engineering Standards & Constraints

- Engineering decisions MUST align with SOLID principles, clean code practices, and a
	simplicity-first mindset.
- Code MUST use clear naming, explicit contracts, deterministic behavior where
	possible, and documented error modes.
- Unsafe operations, reflection, dynamic execution, concurrency primitives, and other
	high-risk constructs MUST be minimized and justified when used.
- Third-party components MUST be reviewed for maintenance health, security posture,
	licensing suitability, and operational fit before adoption.
- Features MUST define relevant performance, privacy, safety, and regulatory
	constraints during planning whenever the domain requires them.

## Delivery Workflow & Quality Gates

- `/speckit.specify` MUST capture functional scope, edge cases, measurable success
	criteria, assumptions, and any applicable security, privacy, safety, or
	compatibility concerns.
- `/speckit.plan` MUST document architecture, data flow, dependency decisions,
	constitution compliance, test strategy, static-analysis approach, and required CI
	gates.
- `/speckit.tasks` MUST include implementation, automated testing, static analysis,
	documentation, and traceability work for each affected story.
- Pull requests MUST reference the governing spec, plan, and task artifacts and MUST
	summarize testing evidence, breaking-change impact, and security considerations.
- CI MUST block merges on failing tests, failed static analysis or linting, unresolved
	critical security findings, or missing required documentation.
- Emergency fixes MAY use an expedited path only if retrospective tests,
	documentation, and root-cause follow-up are completed immediately after
	stabilization.

## Governance

This constitution supersedes conflicting repository-local practices. Amendments MUST
be made by editing this document, documenting rationale, and reviewing downstream
impact on templates, active specifications, and delivery workflow. Governance version
numbers follow semantic versioning: MAJOR for incompatible principle changes or
removals, MINOR for new principles or materially stronger obligations, PATCH for
clarifications that do not change obligations. Compliance MUST be checked during
planning, code review, and release readiness; unresolved exceptions MUST be recorded
in the implementation plan's Complexity Tracking section or an equivalent review log.
Runtime guidance in .github/copilot-instructions.md and Spec Kit templates MUST stay
consistent with this constitution.

**Version**: 1.0.0 | **Ratified**: 2026-04-24 | **Last Amended**: 2026-04-24
