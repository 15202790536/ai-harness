---
name: fullstack-autopilot
description: >
  Fully automated 4-phase orchestration. Chains init+analyze, design, build, verify+review
  across isolated Agent sessions to prevent context compression. Supports checkpoint resume.
version: 1.0.0
author: AI Orchestrator
argument-hint: [--from=N]
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
  - Agent
---

# fullstack-autopilot — Fully Automated Orchestrator

<objective>
Orchestrate the entire development workflow across 4 phases, each running in an isolated
Agent session. Supports checkpoint resume via --from=N and state persistence.
</objective>

<process>

## State Management
State file: .claude/state.json maintains phase completion status.
Each phase writes outputs to disk; downstream phases read from disk.

## Phases

### Phase 0: Project Manager
- Skills: 01-fullstack-init + 02-fullstack-analyze
- Outputs: CLAUDE.md, config, feature checklist, dependency topology

### Phase 1: Systems Architect
- Skills: 03-fullstack-design
- Outputs: Design/change plans, DDL, API contracts, review report

### Phase 2: Developer
- Skills: 04-fullstack-build
- Mode: Parallel sub-agents for concurrent module development
- Outputs: Implemented code, tracking checklist, execution report

### Phase 3: QA Engineer
- Skills: 05-fullstack-verify + 06-fullstack-review
- Outputs: Test report, legacy issues, code review report

## Checkpoint Resume
Usage: /fullstack-autopilot --from=2
Resumes from phase 2, reading all prior phase outputs from disk.
Failed phases preserve state for retry.

## Constraints
- Each phase runs in its own Agent session
- Phase outputs are persisted to disk between sessions
- State file is the source of truth for progress
</process>