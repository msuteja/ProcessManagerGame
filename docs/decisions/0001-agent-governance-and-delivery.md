# ADR 0001: Agent governance and delivery

Status: Accepted
Date: 2026-09-21

## Context

Future development will use AI agents. The project needs cost-aware delegation, reviewable changes, and documentation that remains current.

## Decision

Terra and Sol act as planning/orchestration agents and hand bounded implementation work to Luna where doing so saves context and cost. The orchestrator reviews and verifies the result. Agents may create commits and pull requests using Conventional Commits 1.0.0. Every PR includes a changelog, rationale, material decisions, verification evidence, and matching documentation updates. The user reviews PRs and makes every final approval/merge decision.

## Consequences

Plans and acceptance criteria must be explicit before delegation. Documentation is a deliverable within each PR. An ADR is required for material commitments, while ordinary implementation detail stays in code and task/PR descriptions.
