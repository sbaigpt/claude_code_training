# Rule: Where project specs, plans, and rules live

All technical specifications, implementation plans, and project rules/conventions for this repo live under `.claude/` in the project root:

- `.claude/specs/` — feature spec documents (the WHY/WHAT, written before implementation).
- `.claude/plans/` — technical design / implementation plans (the HOW), one per feature.
- `.claude/rules/` — standing rules and conventions like this one.

Workflow for a new feature: write or receive the spec into `.claude/specs/<feature>.md`, produce an implementation plan into `.claude/plans/<feature>-plan.md`, then implement against that plan. Commit the spec and plan files to the repo alongside the code so future sessions and teammates have the full history of why the code looks the way it does.
