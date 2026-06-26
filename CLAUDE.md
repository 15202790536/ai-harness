# CLAUDE.md

This file provides guidance to Claude Code when working with code in this repository.

## Project Overview

This is a **Claude Code Plugin Marketplace** repository. The root is the marketplace layer, registered via `.claude-plugin/marketplace.json`. It currently ships one plugin: `ai-fullstack-orchestrator` located at `plugins/ai-fullstack-orchestrator/`.

## Repository Structure

```
.
├── .claude-plugin/marketplace.json
├── plugins/
│   └── ai-fullstack-orchestrator/
│       ├── .claude-plugin/plugin.json
│       ├── hooks/          # Telemetry system
│       └── skills/         # 10 skills
│           ├── 01-fullstack-init/
│           ├── 02-fullstack-analyze/
│           ├── 03-fullstack-design/
│           ├── 04-fullstack-build/
│           ├── 05-fullstack-verify/
│           ├── 06-fullstack-review/
│           ├── fullstack-autopilot/
│           ├── fullstack-quick/
│           ├── fullstack-fpa/
│           └── fullstack-debug/
├── README.md
├── README_EN.md
└── CLAUDE.md
```

## Validation

```bash
claude plugin validate .
```

## Key Conventions

- Skills communicate through the `设计输入/` filesystem directory
- Each skill reads outputs from its predecessor
- Configuration lives in `.claude/config.md` (Markdown format, AI-readable)
- Code generation follows the project's existing code style via reference modules
- Feature classification: [create] = new, [alter] = modify existing, [unchanged] = skip
- All status changes require evidence (compilation output, test results, etc.)
- Telemetry uses OpenTelemetry format via `send-trace.js`

## Skill Design Pattern

Each SKILL.md:
- Contains YAML frontmatter (name, version, allowed-tools, etc.)
- Defines role, process steps, inputs, outputs
- Is self-contained — can be loaded independently by Claude Code
- References supporting templates in the same skill directory
