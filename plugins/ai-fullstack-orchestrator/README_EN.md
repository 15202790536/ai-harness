# AI Fullstack Orchestrator

An AI-driven full-stack development orchestration plugin for Claude Code, covering the complete development lifecycle from project initialization to iterative changes.

## Core Philosophy

One plugin, two modes, full lifecycle coverage:

| Mode | Scenario | Key Capabilities |
|------|----------|-----------------|
| **v1.0 Mode** | New project | DDD detailed design + DB modeling + API contracts + 6-dim review |
| **Iteration Mode** | Existing project | Feature classification + topology sort + change plan + incremental DDL + verification loop |

## Skills

### Core Pipeline (sequential)

| # | Command | Role | Description |
|---|---------|------|-------------|
| 01 | `/01-fullstack-init` | Init | Clone repos, detect tech stack, create workspace, generate rules |
| 02 | `/02-fullstack-analyze` | Analyze | Feature classification (create/alter/unchanged), dependency topology |
| 03 | `/03-fullstack-design` | Design | DDD design or change plan + 6-dim review |
| 04 | `/04-fullstack-build` | Build | Topology sort, file aggregation, parallel agents, multi-round fill |
| 05 | `/05-fullstack-verify` | Verify | Compile, test, diagnose, fix loop (max 5 rounds) |
| 06 | `/06-fullstack-review` | Review | Architecture/security/standards + design-code alignment |

### Auxiliary (on-demand)

| Command | Description |
|---------|-------------|
| `/fullstack-autopilot` | Full auto 4-phase orchestration + checkpoint resume |
| `/fullstack-quick` | Single-file quick change |
| `/fullstack-fpa` | Function point analysis (FPA) |
| `/fullstack-debug` | Full-stack bug fix |

## Quick Start

### Installation

```
/plugin marketplace add <repo-url>
/plugin install ai-fullstack-orchestrator@nexai-harness-market
```

### New Project

```
/01-fullstack-init
/02-fullstack-analyze
/03-fullstack-design
/04-fullstack-build
/05-fullstack-verify
/06-fullstack-review
```

### One-Click

```
/fullstack-autopilot
```

## Supported Tech Stacks

### Backend (Phase 1)
- Java / Spring Boot (JUnit 5)
- Python / FastAPI (pytest)

### Frontend
- Vue 3 / Element Plus
- React / Ant Design

### Database
- MySQL
- PostgreSQL

## License

MIT
