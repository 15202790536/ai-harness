---
name: 02-fullstack-analyze
description: >
  Unified requirements analysis. Reads prototypes/requirements, explores the codebase,
  classifies each feature as [create] (new), [alter] (modify existing), or [unchanged] (skip),
  draws dependency topology, and produces a feature checklist with change chain traces.
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

# 02-fullstack-analyze — Unified Requirements Analysis

<objective>
Analyze all features in the current iteration. For each feature, determine whether it is a
brand-new module ([create]), a modification to existing code ([alter]), or already implemented
([unchanged]). Generate a dependency topology graph and a structured feature checklist.
</objective>

<context>
Reads prototypes and requirements from `设计输入/01-需求输入/`, explores both frontend and
backend codebases, and produces structured analysis artifacts in `设计输入/02-需求分析/`.

The feature classification is the linchpin of the entire workflow — downstream skills
(design, build, verify) depend on it to decide CREATE vs ALTER, full DDL vs incremental,
new files vs in-place edits.
</context>

<process>

## Prerequisites
- Workspace initialized by `01-fullstack-init`
- CLAUDE.md present with tech stack, reference modules, and modules-to-develop list
- Prototypes/requirements placed in `设计输入/01-需求输入/{module}/`
- For each module: `page-description.md` (required), `prototypes/` (images/HTML, optional)

## Step 1: Read All Requirements

1. Read `CLAUDE.md` — extract the modules-to-develop list and reference modules.
2. For each module under `设计输入/01-需求输入/` (excluding system directories):
   - Read `page-description.md` — extract:
     - Page layout structure (list/detail/form/dialog/tabs)
     - Interactive elements (buttons, actions, events)
     - Data elements (tables, forms, fields, constraints, pagination)
     - Field names, types, required/optional, validations
   - If `prototypes/` exists: read prototype screenshots or HTML files for visual reference

## Step 2: Explore Codebase & Classify Features

For each feature identified in the requirements, explore the codebase to determine its type.

**Decision logic (per feature):**

```
Explore: does this feature exist in the codebase?

  NO → [create]
    - No corresponding page/API/table exists
    - Brand new module or standalone new feature within existing module
    - Example: "Add coupon management module" when no coupon code exists

  YES → Does it match the requirement?

    PARTIALLY (has differences) → [alter]
      - Feature exists but needs modification
      - Differences may be: new fields, changed logic, new APIs, UI changes
      - Classify change chain type:
        - Frontend only (UI changes, no API/DB changes)
        - Backend only (API/logic changes, no UI changes)
        - Full-stack (changes across frontend + backend + possibly DB)

    FULLY MATCHES → [unchanged]
      - Implementation already meets the requirement
      - Skip in this iteration — no action needed
```

**Exploration strategy:**

1. **Frontend exploration:**
   - Search for existing pages matching the module name in `views/` or `pages/`
   - Search for route definitions matching the module path
   - Read existing `.vue`/`.jsx`/`.tsx` files for the module

2. **Backend exploration:**
   - Search for existing Controller/Router classes matching the module
   - Search for existing Service classes
   - Search for existing Mapper/Repository classes
   - Search for existing Entity/Model classes

3. **Database exploration:**
   - Read existing DDL files (if provided)
   - Search for existing table definitions related to the module
   - Note: actual DB schema may come from Entity annotations or migration files

## Step 3: Dependency Topology Analysis

Analyze dependencies between features to determine execution order:

1. **Identify dependencies:**
   - Feature A's API is consumed by Feature B's page → B depends on A
   - Feature C's data table is referenced by Feature D → D depends on C
   - Feature E is a shared component used by F, G → F, G depend on E

2. **Draw dependency graph:**

```
F0001 (User query API) ← F0002 (User list page)
F0001 (User query API) ← F0003 (User detail page)
F0004 (Order query API) ← F0005 (Order list page)
F0004 (Order query API) ← F0006 (Order export)
```

3. **Identify blocking chains:**
   - Which features are blocked because their dependencies are not yet ready
   - External dependencies (third-party APIs, data sources not yet available)

## Step 4: Generate Feature Checklist

Output to `设计输入/02-需求分析/功能清单.md`:

```markdown
# Feature Checklist — {iteration/version}

## Summary
- Total Features: {N}
- [create]: {X} | [alter]: {Y} | [unchanged]: {Z}
- Full-stack: {A} | Frontend-only: {B} | Backend-only: {C}

## Feature List

### Module: {module-name} ({feature-count} features)

| # | ID | Feature | Type | Chain | Dependencies | Files Involved (est.) |
|---|-----|---------|------|-------|-------------|----------------------|
| 1 | F0001 | User list page with search | [create] | Full-stack | None | 6 files |
| 2 | F0002 | Add export field to order | [alter] | Full-stack | F0001 | 3 files |
| 3 | F0003 | Customer status display | [alter] | Frontend-only | None | 1 file |

### Feature Details

#### F0001: User list page with search
- Type: [create]
- Chain: Full-stack
- Frontend: Create `views/user/Index.vue` with search form + paginated table
- Backend: Create `UserController`, `UserService`, `UserMapper`, `User` entity
- Database: CREATE TABLE `t_user` (...)
- Dependencies: None (root feature)

#### F0002: Add export field to order
- Type: [alter]
- Chain: Full-stack
- Frontend: Modify `views/order/Index.vue` — add export button, add export API call
- Backend: Modify `OrderController.export()` — add `couponStatus` field to export
- Database: ALTER TABLE `t_order` ADD COLUMN `coupon_status` TINYINT
- Dependencies: F0001 (user must exist to associate)

...
```

## Step 5: Generate Dependency Topology Map

Output to `设计输入/02-需求分析/依赖拓扑图.md`:

```markdown
# Dependency Topology — {iteration/version}

## Dependency Graph

F0001 (User query API) ────→ F0002 (User list page)
     │
     └──→ F0003 (User detail page)

F0004 (Order query API) ────→ F0005 (Order list page)
     │
     └──→ F0006 (Order export)

## Execution Batches

### Batch 1 (No dependencies — execute first):
- F0001: User query API
- F0004: Order query API

### Batch 2 (Depends on Batch 1):
- F0002: User list page (needs F0001)
- F0003: User detail page (needs F0001)
- F0005: Order list page (needs F0004)

### Batch 3 (Depends on Batch 2):
- F0006: Order export (needs F0005)

## Blocked Features
- None currently blocked by external dependencies
```

</process>

<constraints>
- Do NOT modify any code during analysis — this is read-only exploration
- Each feature MUST be classified as [create], [alter], or [unchanged]
- [unchanged] features still appear in the checklist for traceability
- Dependency analysis MUST be complete — missing a dependency causes build-order errors
- API methods MUST follow RESTful conventions (POST, GET only)
- If a feature's classification is ambiguous, default to [alter] (safer to modify than overwrite)
</constraints>
