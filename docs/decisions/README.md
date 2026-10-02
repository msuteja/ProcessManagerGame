# Architecture decision records

Purpose: hold short, durable records of material decisions so agents do not rediscover or silently reverse them.

Create an ADR for decisions affecting product commitments, architecture, persistence compatibility, platform scope, online/multiplayer design, security, deployment, or repository structure. The user has final approval: use `Proposed` until they explicitly accept it.

Accepted repository-structure decisions: [ADR 0004 — Project-root repository](0004-project-root-repository.md) records the historical move to one root repository; [ADR 0005 — Android project directory](0005-android-project-directory.md) records the current Android project directory name.

## Format

Name files `NNNN-short-title.md`, starting at `0001`. Keep each record brief:

```markdown
# ADR NNNN: Title

Status: Proposed | Accepted | Superseded by ADR NNNN
Date: YYYY-MM-DD

## Context

## Decision

## Consequences
```

An ADR states the chosen direction and consequences; it is not a task plan or a change log. Link to it from the relevant product/architecture/persistence document when accepted.
