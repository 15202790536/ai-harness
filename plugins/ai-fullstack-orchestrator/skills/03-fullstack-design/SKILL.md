---
name: 03-fullstack-design
description: >
  Mode-adaptive detailed design. In v1.0 mode: full DDD design with database modeling and API contracts.
  In iteration mode: change plans with incremental DDL and call-chain traces.
  Both modes run the unified 6-dimension full-link technical review.
version: 1.0.0
author: AI Orchestrator
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
  - Agent
---

# 03-fullstack-design — Mode-Adaptive Design

<objective>
Based on the feature checklist from 02-fullstack-analyze and the project mode (v1.0 or iteration),
produce detailed design artifacts. Route between full DDD design and incremental change planning.
All paths go through the unified 6-dimension technical review.
</objective>

<context>
Reads the feature checklist and project mode from CLAUDE.md and design input docs.
The mode determines the design approach:
- v1.0 mode: DDD (Domain-Driven Design), full database modeling, API contracts
- Iteration mode: Change plans, incremental DDL, call-chain traces
Both modes produce review reports against 6 dimensions.
</context>

<process>

## Pre-requisites
- Feature checklist: `设计输入/02-需求分析/功能清单.md`
- Dependency topology: `设计输入/02-需求分析/依赖拓扑图.md`
- Per-module page descriptions: `设计输入/01-需求输入/{module}/page-description.md`
- Project config: `.claude/config.md`
- DDL: `设计输入/01-需求输入/db_ddl.sql` (if exists)

## Determine Mode

Read `CLAUDE.md` or `.claude/config.md` to determine the current mode:
- `v1.0` → Go to **Path A: DDD Full Design**
- `iteration` → Go to **Path B: Change Plan**

---

## Path A: DDD Full Design (v1.0 Mode)

Execute when the project is new or has no existing business code.

### A1: Domain Modeling

For each [create] feature in the checklist:

1. Identify domain entities: Aggregate roots, entities, value objects
2. Define relationships: One-to-one, one-to-many, many-to-many
3. Define boundaries: Which features belong to which bounded context

### A2: Database Design

Produce complete DDL in `设计输入/04-契约与脚本/DDL.sql`.

Database design follows the 3-level process:
- Conceptual model: Identify entities and relationships
- Logical model: Normalize to 3NF, define fields and types
- Physical model: DDL with indexes, constraints, comments

All tables MUST include:
- `id` BIGINT AUTO_INCREMENT primary key
- Appropriate indexes on query fields
- `create_time` DATETIME DEFAULT CURRENT_TIMESTAMP
- `update_time` DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
- ENGINE=InnoDB, CHARSET=utf8mb4
- COMMENT on table and all columns

### A3: API Contract Specification

For each [create] feature, produce API contract in:
`设计输入/04-契约与脚本/{module}-API契约.md`

Each contract covers:
- List query (GET with pagination and filters)
- Create (POST)
- Update (POST)
- Delete (POST)
- Detail query (GET by ID)
- Request/response field tables
- Error codes

Reference the API-Contract-Spec.md template for detailed format.

---

## Path B: Change Plan (Iteration Mode)

Execute when the project has existing business code.

### B1: Generate Change Plan

For each [create] and [alter] feature, generate a detailed change plan.

Output to `设计输入/03-详细设计/变更方案.md`:

```markdown
# Change Plan — {iteration-name}

## Module: {module-name} ({feature-count} items)

### F0001: {feature-name}
- ID: F0001
- Type: [create] / [alter]
- Change Chain: [Frontend-only | Backend-only | Full-stack]
- Change Description: {detailed description}

#### Frontend Changes
- File: `{path/to/file}`
- Change: {natural language description}
- Details: {specific field/component/behavior changes}

#### Backend Changes
- File: `{path/to/file}`
- Change: {natural language description}
- Details: {specific method/logic changes}

#### Call Chain Trace
```
{page-file}              [{change description}]
  → {api-method}         [{change description}]
    → POST /api/v1/{path} [{change description}]
      → {Controller}.{method}() [{change description}]
        → {Service}.{method}() [{change description}]
          → {Mapper}.{method}() [{change description}]
            └→ DB {table_name} [{change description}]
```
```

### B2: Generate Incremental DDL

For [create] features: CREATE TABLE statements
For [alter] features: ALTER TABLE + CREATE INDEX statements

Output to `设计输入/04-契约与脚本/DDL.sql`.

---

## Unified Review (Both Modes)

After design or change plan is complete, run the 6-dimension full-link technical review.

Output to `设计输入/03-详细设计/设计评审报告.md`.

### Review Dimensions:

1. **Field Lineage**: Can every UI field trace back to a DB column? Are computed fields documented?
2. **Connectivity**: Can every API reach its handler? Are all services wired correctly?
3. **State & Concurrency**: Are state transitions defined? Race conditions identified?
4. **Performance & Volume**: N+1 queries? Missing pagination? Missing indexes?
5. **Data Compliance**: Sensitive data encrypted? Personal data handled per regulations?
6. **Contract Consistency**: API contract fields match DB columns? Response format matches?

### Issue Grading:
- **Blocker**: Must fix before development — blocks downstream
- **Major**: Should fix — significant quality risk
- **Minor**: Nice to fix — improvement opportunity
- **Question**: Needs stakeholder clarification

</process>

<constraints>
- API methods MUST use RESTful conventions: only POST and GET
- All DDL MUST include ENGINE, CHARSET, and COMMENT clauses
- All tables MUST include create_time and update_time timestamp fields
- Design review MUST be completed before build can start
- [create] features get full design + CREATE TABLE
- [alter] features get change description + ALTER TABLE
- [unchanged] features are not included in change plan
- Reference existing code patterns for new module design
</constraints>
