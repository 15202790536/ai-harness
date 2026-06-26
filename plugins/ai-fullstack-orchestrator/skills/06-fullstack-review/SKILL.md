---
name: 06-fullstack-review
description: >
  Language-agnostic code and design review. Reviews architecture, security, coding standards,
  framework best practices, design-code alignment, and API contract consistency.
version: 1.0.0
author: AI Orchestrator
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
---

# 06-fullstack-review — Code & Design Review

<objective>
Perform a comprehensive review of implemented code against the design specification.
Cover architecture, security, coding standards, best practices, design-code alignment,
and API contract consistency.
</objective>

<process>

## Pre-requisites
- Design docs: 设计输入/03-详细设计/
- API contracts: 设计输入/04-契约与脚本/
- Verification report: 设计输入/06-验证测试/测试报告.md
- Dev rules: .claude/rules/

## Review Dimensions

### 1. Architecture
Check layering, dependency directions, module boundaries, circular deps.

### 2. Security
SQL injection, XSS, auth, authorization, sensitive data handling.

### 3. Coding Standards
Language-specific checks per .claude/rules/.

### 4. Framework Best Practices
Transactional boundaries, validation, DI, error handling.

### 5. Design-Code Alignment
Compare design spec vs implementation. Flag deviations.

### 6. API Contract Consistency
Endpoint paths, request/response, error codes, POST/GET only.

## Issue Grading

| Grade | Action |
|-------|--------|
| Blocker | Must fix immediately — security or data risk |
| Major | Must fix before release |
| Minor | Should fix, not blocking |
| Suggestion | Optional improvement |

## Output
Write to 设计输入/07-代码审查/代码审查报告.md with:
- Summary (total findings by grade)
- Findings per dimension (file, line, issue, recommendation)
- Design-code diff per feature (MATCH / PARTIAL / MISMATCH)
- Overall assessment and recommendation

</process>

<constraints>
- Each finding MUST include: file path, line reference, description, recommendation
- Security findings default to Blocker
- Design-code deviations MUST be reconciled
- Reviews are advisory — identify issues, do not auto-fix
</constraints>