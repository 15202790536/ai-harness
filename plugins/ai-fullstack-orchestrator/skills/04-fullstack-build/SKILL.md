---
name: 04-fullstack-build
description: >
  Language-adaptive code generation. Reads change plans / design docs, performs dependency topology
  sort, batches by file ownership, dispatches parallel sub-agents per language template, and runs
  multi-round gap-filling. Auto-routes between CREATE (new files) and ALTER (in-place edits).
version: 1.0.0
author: AI Orchestrator
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
  - Agent
  - Edit
forbiddenTools:
  - delete
---

# 04-fullstack-build — Language-Adaptive Code Build

<objective>
Execute all [create] and [alter] features from the change plan. Perform topology sort,
file-level aggregation, batched parallel sub-agent dispatch, and multi-round gap-filling.
Auto-select code templates based on the project's declared language and framework.
</objective>

<context>
This is the execution engine of the orchestrator. It reads the change plan, routes code
generation through language-specific templates, and enforces evidence-driven tracking.
Every feature completion requires verifiable evidence (compile output, lint pass, etc.).
</context>

<process>

## Pre-requisites
- Change plan: `设计输入/03-详细设计/变更方案.md`
- Feature checklist: `设计输入/02-需求分析/功能清单.md`
- Dependency topology: `设计输入/02-需求分析/依赖拓扑图.md`
- Page descriptions: `设计输入/01-需求输入/{module}/page-description.md`
- DDL: `设计输入/04-契约与脚本/DDL.sql`
- API contracts: `设计输入/04-契约与脚本/{module}-API契约.md` (if exists)
- Development rules: `.claude/rules/backend.md`, `.claude/rules/frontend.md`
- Project config: `.claude/config.md`

## Core Principles

### Coverage Requirements
- Business logic layer (Service): >= 80% code coverage
- All public methods must be tested
- Boundary conditions must be covered (null, empty, invalid input)
- Exception paths must be tested

### Prohibited
- Do NOT skip implementation steps or leave `// TODO` without justification
- Exception: External dependencies not ready → mark with `TODO(external): {reason}` and track as blocked
- Do NOT modify code outside the current module
- Do NOT merge multiple APIs into one — one API per function
- API methods: POST and GET only (RESTful convention)

---

## Step 1: Pre-Execution Status Assessment

1. Read the change plan to get all features.
2. Read the tracking checklist (if exists from previous run) to identify completed/incomplete items.
3. Read page descriptions for frontend change details.
4. Read the DDL for database schema info.

## Step 2: Dependency Topology Analysis

Analyze dependencies between features to determine execution order:

```
F0002 (User list API) ← F0003 (User list page)
F0004 (Order query API) ← F0005 (Order list page) ← F0006 (Order export)
F0007 (Shared component) ← F0008, F0009 (pages using component)
```

Identify blocking chains — features that cannot start because their dependencies are not ready.

## Step 3: File-Level Change Aggregation

Group all changes by target file to avoid multi-round conflicts on the same file:

- `UserController.java` → F0001 + F0002 + F0005 (3 changes, 1 execution)
- `OrderService.java` → F0003 + F0004 (2 changes, 1 execution)
- `OrderList.vue` → F0005 + F0006 + F0007 (3 changes, 1 execution)

Output tracking checklist: `设计输入/05-执行跟踪/开发任务跟踪清单.md`

## Step 4: Batched Parallel Execution

### Batch Strategy:

**Batch 1 (API Foundation Layer)**: Backend APIs first
- All backend Controller/Router + Service + Repository
- Reason: Frontend depends on backend APIs

**Batch 2 (Business Logic Layer)**: Service implementation
- Full business logic with TDD
- Reason: Core logic must be complete before UI

**Batch 3 (Frontend Presentation Layer)**: Pages + Routes + Mock data
- Vue/React pages, routing config, mock data
- Reason: Depends on Batch 1 API contracts

**Batch 4 (Fill Layer)**: Gap detection + multi-round fix (max 5 rounds)
- Completeness check, missing features, edge cases

### Sub-Agent Execution Protocol:

Each sub-agent receives:
1. Feature ID + type ([create] or [alter])
2. Target file path
3. Page description (for frontend)
4. API contract (for backend)
5. Reference module path (for code style)

Each sub-agent executes:
1. **Code Generation**: Generate code following the language template
2. **Routing**: Configure routes for [create] features
3. **Mock Data**: Generate mock data matching API contract
4. **Completeness Check**: Verify against page description

## Step 5: Language Template Routing

Based on `.claude/config.md` backend language/framework:

### Java / Spring Boot:
```
Layers: Controller → Service (interface) → ServiceImpl → Mapper → Entity → DTO/VO
Tests: JUnit 5 + Mockito + SpringBootTest
Build: mvn clean compile, mvn test
Lint: checkstyle / spotbugs (if configured)
```

- [create]: Generate all layers from templates
- [alter]: Modify existing files, preserve commented old code, annotate with feature ID

### Python / FastAPI:
```
Layers: Router → Service → Repository → Model (SQLAlchemy) → Schema (Pydantic)
Tests: pytest + httpx (async test client)
Build: python -m compileall ., python -m pytest
Lint: ruff check
```

- [create]: Generate all layers from templates
- [alter]: Modify existing files, preserve commented old code, annotate with feature ID

### Frontend (Vue 3):

- [create]: `views/{module}/Index.vue` + `api/{module}.js` + `router/{module}.js` + `mock/{module}.js`
- [alter]: Modify existing .vue files, preserve old code, annotate changes

### Frontend (React):

- [create]: `pages/{Module}/index.tsx` + `services/{module}.ts` + route config + mock
- [alter]: Modify existing .tsx files, preserve old code, annotate changes

## Step 6: Change Annotation Convention

For [alter] features, use the following annotation format:

```
// F0001: {change description} — {specific change content}
// OLD: {old code (commented out)}
```

This enables traceability from code back to the change plan.

## Step 7: Evidence-Driven Tracking

After completing each feature, immediately update the tracking checklist:

| Field | Value |
|-------|-------|
| Feature ID | F0001 |
| Type | [create] / [alter] |
| Status | done / in_progress / blocked |
| Evidence | compile: pass / test: 5/5 pass / lint: pass |
| Files Changed | {list of modified files} |
| Notes | {any issues or TODOs} |

### Status States:
- **todo**: Not started
- **in_progress**: Currently being implemented
- **done**: Completed with evidence
- **blocked**: External dependency not ready — marked with reason

### Status Transition Rules:
- todo → in_progress: Free to start
- in_progress → done: MUST have evidence (compile + test + lint output)
- in_progress → blocked: MUST record specific blocking dependency + status
- blocked → in_progress: Blocking dependency must be resolved first

</process>

<constraints>
- Do NOT modify code outside the current module
- [alter] features: preserve old code as comments, annotate with feature ID
- External dependencies not ready: mark `TODO(external): {reason}` and set status to blocked
- One API per function — do not merge APIs
- API methods: POST and GET only
- All done transitions require evidence (compile, test, or lint output)
- No evidence = not done — plain text claims are invalid
</constraints>
