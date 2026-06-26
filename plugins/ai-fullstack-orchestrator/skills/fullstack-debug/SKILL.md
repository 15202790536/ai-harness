---
name: fullstack-debug
description: >
  Language-agnostic full-stack bug fix workflow. Root cause analysis -> propose fix ->
  user confirmation -> execute -> verify. Adapts to configured language and framework.
version: 1.0.0
author: AI Orchestrator
argument-hint: <bug-description>
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
  - Edit
---

# fullstack-debug — Full-Stack Bug Fix

<objective>
Diagnose and fix bugs across the full stack. Follow a structured workflow:
root cause analysis, solution proposal (with user confirmation), execution, and verification.
</objective>

<process>

## Step 1: Bug Understanding
Parse the bug description. Identify:
- Symptoms (what the user sees)
- Where it occurs (frontend page, API call, data issue)
- When it occurs (repro conditions)

## Step 2: Root Cause Analysis
Trace the bug through the full stack:
1. Frontend: Check UI code, API call, state management
2. Network: Check request/response payload
3. Backend: Check controller, service, repository
4. Database: Check data integrity, query logic

Identify the exact root cause — not just the symptom.

## Step 3: Solution Proposal
Propose fix with:
- Which file(s) to change
- What exactly to change and why
- Impact on other features
- Risk assessment

**Wait for user confirmation before proceeding.**

## Step 4: Execute Fix
Apply the fix. Follow [alter] conventions:
- Preserve old code as comments
- Annotate with bug ID / description

## Step 5: Verify
- Compile check
- Test the specific scenario
- Regression check (ensure no breakage)
- Lint check

## Language Adaptation
- Java: Use mvn compile + mvn test
- Python: Use compileall + pytest
- Frontend: Use npm run build + eslint
</process>

<constraints>
- Do NOT fix symptoms without finding root cause
- Do NOT modify files unrelated to the bug
- Wait for user confirmation before executing
- Verify the fix actually resolves the reported issue
</constraints>