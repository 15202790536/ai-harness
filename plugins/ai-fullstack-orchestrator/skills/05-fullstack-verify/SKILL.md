---
name: 05-fullstack-verify
description: >
  Language-adaptive verification and fix loop. Compiles, tests, diagnoses, and repairs code
  automatically for up to 5 rounds. Adapts build and test commands based on configured language.
version: 1.0.0
author: AI Orchestrator
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
  - Edit
---

# 05-fullstack-verify — Verification & Fix Loop

<objective>
Verify all implemented changes by compiling, running tests, linting, and performing
completeness checks. Auto-diagnose and fix issues. Loop up to 5 rounds until all checks pass.
Produce a test report and a list of any remaining legacy issues.
</objective>

<context>
This is the quality gate before code review. It reads the change plan and test cases,
runs language-appropriate build and test commands, diagnoses failures, and auto-fixes
where possible. Issues that cannot be fixed are documented for human resolution.
</context>

<process>

## Pre-requisites
- Change plan: `设计输入/03-详细设计/变更方案.md`
- Feature checklist: `设计输入/02-需求分析/功能清单.md`
- Tracking checklist: `设计输入/05-执行跟踪/开发任务跟踪清单.md`
- Project config: `.claude/config.md`
- Development rules: `.claude/rules/backend.md`, `.claude/rules/frontend.md`

## Verification Loop (Max 5 Rounds)

### Round Structure:

For each round:
1. Build & Compile → if fail, diagnose and fix
2. Unit Tests → if fail, diagnose and fix
3. Lint Check → if fail, fix
4. Feature Completeness → if gaps, fill

If all pass → exit loop early.
If 5 rounds completed and issues remain → document in legacy issue list.

### Language-Specific Commands:

#### Java / Spring Boot:
```
# Compile
mvn clean compile -pl {module} -am -DskipTests

# Unit tests
mvn test -pl {module}

# Full backend tests
mvn test -pl {all-changed-modules}
```

#### Python / FastAPI:
```
# Compile check
python -m compileall {module}/

# Unit tests
python -m pytest {module}/tests/ -v --tb=short

# Lint
ruff check {module}/
```

#### Frontend (Vue/React):
```
# Lint
npx eslint --ext .vue,.js,.ts src/views/{module}/ src/api/{module}.js

# Build check
npm run build
```

## Step 1: Compile Check

Run the language-appropriate compile command for all changed modules.

If compilation fails:
1. Read the error output
2. Diagnose root cause (missing import, syntax error, type mismatch, etc.)
3. Fix the issue
4. Re-compile to verify

## Step 2: Unit Test Execution

Run unit tests for all changed modules.

If tests fail:
1. Read the test failure output
2. Diagnose: test expectation wrong or implementation wrong?
3. Fix the implementation (preferred) or adjust the test
4. Re-run tests to verify

## Step 3: Lint Check

Run language-appropriate lint tool.

If lint fails:
1. Read the lint output
2. Auto-fix formatting issues (indentation, quotes, unused imports, etc.)
3. Re-run lint to verify

## Step 4: Feature Completeness Check

For each feature in the tracking checklist:
1. Verify all files listed in the change plan exist and contain the expected changes
2. Verify API endpoints match the contract
3. Verify frontend pages match the page description
4. Verify database DDL matches the change plan

If gaps found:
1. Identify missing implementation
2. Generate the missing code
3. Mark in tracking checklist

## Step 5: Output

After verification completes (or max rounds reached), produce:

### Test Report: `设计输入/06-验证测试/测试报告.md`

```markdown
# Test Report — {iteration/version}

## Summary
- Total Features: {N}
- Passed: {X} | Failed: {Y} | Blocked: {Z}
- Test Rounds Completed: {R}

## Feature Results

| ID | Feature | Compile | Tests | Lint | Completeness | Status |
|----|---------|---------|-------|------|-------------|--------|
| F0001 | User list | PASS | 5/5 | PASS | FULL | done |
| F0002 | Order export | PASS | 3/5 | PASS | PARTIAL | needs_fix |

## Test Execution Details

### F0001: User list
- Compile: mvn clean compile — SUCCESS
- Tests: 5 run, 5 passed, 0 failed
  - testQueryUserList: PASS
  - testQueryUserListPagination: PASS
  - testQueryUserListWithFilter: PASS
  - testQueryUserListEmpty: PASS
  - testQueryUserListInvalidInput: PASS
- Lint: PASS
- Coverage: 85%
```

### Legacy Issues: `设计输入/06-验证测试/遗留问题清单.md`

```markdown
# Legacy Issues — {iteration/version}

## Issues Carried Forward

| # | Feature | Issue | Severity | Root Cause | Resolution Path |
|---|---------|-------|----------|------------|----------------|
| 1 | F0002 | Export CSV missing coupon field | Medium | External: coupon service not yet deployed | Deploy coupon service, then re-verify |

## Blocked Features

| # | Feature | Blocked By | Current Status | Expected Resolution |
|---|---------|------------|---------------|-------------------|
| 1 | F0008 | Third-party payment API | API key pending | Next sprint |
```

</process>

<constraints>
- Maximum 5 repair rounds per feature — after that, document as legacy issue
- Do NOT fix code outside the current iteration's scope
- Diagnoses must reference specific error messages — no generic fixes
- Status changes require evidence (test output, compile log, lint report)
- External dependency failures: document the blocking dependency and expected resolution
</constraints>
