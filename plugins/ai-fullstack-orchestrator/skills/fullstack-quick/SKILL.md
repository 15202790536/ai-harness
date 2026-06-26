---
name: fullstack-quick
description: >
  Fast single-feature change path. For simple changes (< 80 lines, single file).
  Skips the full pipeline: analyze -> propose -> confirm -> execute -> verify.
version: 1.0.0
author: AI Orchestrator
argument-hint: --description
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
  - Edit
---

# fullstack-quick — Quick Single-Feature Change

<objective>
Handle simple, single-file changes quickly without the full pipeline overhead.
Ideal for small bug fixes, field additions, or minor UI tweaks.
</objective>

<process>

## When to Use
- Single file change (< 80 lines)
- No database schema changes
- No new API endpoints
- No dependency on other features

## Steps

### Step 1: Requirement Identification
Parse the user description, identify the target file and change scope.

### Step 2: Root Cause / Gap Analysis
Read the target file(s), understand current implementation, identify exact change points.

### Step 3: Solution Proposal
Propose the change with:
- Which file(s) will be modified
- What exactly will change
- Impact assessment

Wait for user confirmation before proceeding.

### Step 4: Execute
Apply the change. For alter-type changes, preserve old code as comments.

### Step 5: Verify
Run language-appropriate compile/lint/test checks.

## Constraints
- DO NOT modify files outside the change scope
- If the change grows beyond single file, suggest using the full pipeline
- Wait for user confirmation before executing
</process>