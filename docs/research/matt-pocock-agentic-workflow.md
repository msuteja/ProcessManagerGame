# Matt Pocock's agentic development workflow

Purpose: capture primary-source findings that can inform Cooking Spree's agent workflow. This is a research note, not a binding project process; project-specific rules in `AGENTS.md` take precedence.

## Sources

- [AI Engineer: AI Coding Workflow — From Product Idea to Tested Implementation](https://ai.engineer/talks/-QFHIoCo-Ko-ai-coding-workflow) — Matt Pocock's talk and accompanying chapter notes.
- [Matt Pocock's `skills` repository](https://github.com/mattpocock/skills) — his published, composable agent skills and repository guidance.
- [Matt Pocock's `grill-with-docs` skill](https://github.com/mattpocock/skills/blob/main/skills/engineering/grill-with-docs/SKILL.md) — published description of the alignment/documentation step.
- [Matt Pocock's `to-prd` skill](https://github.com/mattpocock/skills/blob/main/skills/engineering/to-prd/SKILL.md) — published PRD synthesis step (path may move as the skills repository evolves).

## Direct evidence from Matt's published material

### 1. Align with the human before implementation

Matt's workflow begins with a Grill Me interview that explores the repository and resolves product and technical ambiguity one decision at a time. The human remains responsible for answering unresolved product questions; the result is then captured in a PRD containing the problem, solution, user stories, implementation/testing decisions, and exclusions. The talk explicitly presents this as human-in-the-loop planning, not an unattended step.

The repository's `grill-with-docs` skill adds a durable paper trail: it records clarified domain vocabulary in `CONTEXT.md` and important one-way decisions as ADRs while the interview proceeds.

### 2. Convert the destination into small vertical slices

Matt separates a PRD (the destination) from execution. He recommends a dependency-aware Kanban board of independently actionable issues, with explicit blocking relationships and an indication of which issues are safe for unattended execution (AFK).

He prefers vertical “tracer bullet” slices that cross the needed layers and produce a testable user-visible behavior. He warns against database-first, API-second, frontend-last decomposition because integration problems are discovered too late.

### 3. Let agents implement with TDD and feedback loops

After alignment and issue shaping, an AFK loop can select eligible issues. His described implementation prompt tells the agent to explore the repository, use test-driven development, and run feedback loops. The demonstrated red-green-refactor sequence is: write a failing test, implement the behavior, then run tests/type checks and refactor.

He recommends rehearsing a single implementation iteration repeatedly before trusting a longer unattended loop, and isolating longer runs in a sandbox.

### 4. Review in a fresh context; retain human QA and ownership

Matt recommends fresh-context automated review rather than asking the exhausted implementation session to approve its own work. He describes giving reviewers explicit applicable standards, while implementers can pull guidance as needed. Tests and type checks are necessary but not sufficient: his talk gives an example where they passed while real use exposed a missing database table.

The human still owns product alignment, architecture, manual QA, code review, and final quality. He also warns that faster agent implementation can create more code than humans can comfortably review, and does not claim to have completely solved the small-PR problem.

### 5. Branch/worktree isolation supports parallel implementation

For parallel work, Matt describes Sandcastle: a system that creates isolated git worktrees and Docker runs, coordinates a planner, per-issue implementers, reviewers, and a merger agent. The planner selects compatible issues; implementers work on separate branches; reviewers inspect commits; and the merger resolves integration problems involving tests and types.

This is direct evidence for an isolated branch → review → merge pipeline. It is not evidence that every personal workflow uses a particular hosting provider or exact PR CLI command.

### 6. Keep architecture and documentation agent-friendly

Matt argues that tangled dependencies and weak feedback loops reduce agent effectiveness. He favors “deep modules”: substantial behavior behind small, understandable interfaces, tested at meaningful boundaries. Humans should retain ownership of interfaces and architectural boundaries while delegating internal implementation.

He also warns about documentation rot: completed PRDs can mislead future agents after requirements or code change. Planning documents should therefore be treated as working artifacts, closed/removed when stale, or clearly superseded by current documentation.

## What this implies for Cooking Spree (project-specific inference)

These are adaptations, not claims about Matt's exact repository or tooling:

1. Terra/Sol should own the Grill Me-style discussion, codebase exploration, plan/PRD, issue slicing, and final review; Luna should receive a bounded issue/plan for implementation.
2. Each Luna task should be a vertical slice with acceptance criteria, test expectations, dependencies, and the exact documentation surfaces it may change.
3. The worker should use a feature branch, make a Conventional Commit, run the strongest available tests, update docs in the same change, and open a PR for the human owner to review.
4. A PR should include a changelog, why the change is needed, decisions/trade-offs, tests/evidence, and documentation changes. This extends Matt's review/quality principles with the project's explicit preference.
5. Keep Android work isolated from future iPhone work until the Android roadmap is complete; record cross-platform decisions as ADRs when they become load-bearing.
6. Do not let an old PRD override current `AGENTS.md`, project docs, or user decisions. Update or archive stale planning material.

## Limits of the evidence

The primary talk documents an AFK implementation loop and a branch/worktree/reviewer/merger architecture, but it does not specify Matt's exact personal GitHub PR template, commit naming convention, or whether a human or agent presses the final merge button. For Cooking Spree, those should remain explicit project policy rather than being attributed to Matt.
