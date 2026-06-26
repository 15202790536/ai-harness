---
name: 01-fullstack-init
description: >
  Unified project initialization. Auto-detects project phase (v1.0 greenfield or iteration),
  clones repositories, detects tech stack, creates workspace directory structure, and generates
  development rules. Supports Java/Spring Boot and Python/FastAPI backends, Vue3/React frontends.
argument-hint: <config-path.md>
version: 1.0.0
author: AI Orchestrator
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
---

# 01-fullstack-init — Unified Project Initialization

<objective>
Initialize a full-stack project workspace. Auto-detect if this is a new project (v1.0) or
an iteration on an existing project. Clone repos, detect tech stack, generate rules.
</objective>

<context>
Receives one optional argument: path to a project config file (default: `.claude/config.md`).
If the config file does not exist, interactively generate it.
</context>

<process>

## Step 1: Load or Generate Configuration

1. Check if `.claude/config.md` exists in the current working directory.
2. **If it exists**: read and parse the configuration.
3. **If it does not exist**: interactively generate it by asking the user:

   ```
   Q1: Project name?
   Q2: Backend language? (java | python)
   Q3: Backend framework? (spring-boot | fastapi)
   Q4: Backend repository URL + branch?
   Q5: Frontend framework? (vue3 | react)
   Q6: UI component library? (element-plus | ant-design | custom)
   Q7: Frontend repository URL + branch?
   Q8: Database type? (mysql | postgresql)
   Q9: Reference modules? (existing modules to use as code style blueprint)
   Q10: Modules to develop? (list of new modules for this iteration)
   ```

4. Write the configuration to `.claude/config.md` using the following template:

```markdown
# Project Configuration

## Basic Info
- Project Name: {project-name}
- Mode: auto  (auto = auto-detect | v1.0 = new project | iteration = iterative change)

## Backend Tech Stack
- Language: {java | python}
- Framework: {spring-boot | fastapi}
- Package/Module Path: {com.example.myproject | app}
- Test Framework: {junit5 | pytest}
- Repository URL: {url}
- Branch: {branch}

## Frontend Tech Stack
- Framework: {vue3 | react}
- UI Library: {element-plus | ant-design | custom}
- Repository URL: {url}
- Branch: {branch}

## Database
- Type: {mysql | postgresql}
- Charset: utf8mb4

## Reference Modules
- Backend Reference: {module-name}
- Frontend Reference: {module-name}

## Modules to Develop
- {module-1} ({module-1-en})
- {module-2} ({module-2-en})
```

## Step 2: Clone Repositories

1. Parse `.claude/config.md` to extract repository URLs and branches.
2. Clone the backend repository:
   ```bash
   git clone -b {branch} {backend-url} {backend-project-name}
   ```
3. Clone the frontend repository:
   ```bash
   git clone -b {branch} {frontend-url} {frontend-project-name}
   ```
4. If repos already exist locally, run `git pull` instead.

## Step 3: Auto-Detect Tech Stack

Scan the cloned repositories to auto-detect the actual tech stack (may override config defaults):

### Backend Detection:
- **Java/Spring Boot**: Look for `pom.xml` with `spring-boot-starter`, or `build.gradle` with Spring Boot plugin, or `@SpringBootApplication` annotation
- **Python/FastAPI**: Look for `requirements.txt`/`pyproject.toml` with `fastapi`, or `main.py` with FastAPI app instance

### Frontend Detection:
- **Vue 3**: Look for `package.json` with `vue@3`, or `createApp` in `main.js`/`main.ts`
- **React**: Look for `package.json` with `react`, or JSX/TSX files in `src/`

### Database Detection:
- Check existing DDL files for MySQL/PostgreSQL syntax
- Check ORM config (`application.yml`, `settings.py`, etc.)

Update `.claude/config.md` if auto-detection differs from user-provided values.

## Step 4: Determine Project Phase

1. Scan the cloned backend and frontend projects for existing business code:
   - **Empty / scaffold only**: only framework boilerplate, no business modules → **v1.0 Mode**
   - **Has business code**: controllers/services with business logic → **Iteration Mode**

2. Record the detected mode in the generated CLAUDE.md.

## Step 5: Create Workspace Directory Structure

Create the `设计输入/` directory and its subdirectories:

```
设计输入/
├── 01-需求输入/           # Input: prototypes, requirements docs
│   └── {module}/
│       ├── prototypes/
│       └── page-description.md
├── 02-需求分析/           # Output: feature checklist, dependency topology
├── 03-详细设计/           # Output: design docs OR change plans
├── 04-契约与脚本/         # Output: DDL + API contracts
├── 05-执行跟踪/           # Output: execution tracking + reports
├── 06-验证测试/           # Output: test reports + issue list
└── 07-代码审查/           # Output: code review reports
```

## Step 6: Generate Development Rules

Analyze the cloned projects and generate:

### `.claude/rules/frontend.md`
- Directory structure (extracted from actual project)
- Common commands (dev server, build, lint)
- Coding conventions (naming, component structure, routing pattern)
- UI component library usage patterns
- Environment configuration notes

### `.claude/rules/backend.md`
- Module structure and dependencies
- Common commands (compile, test, run)
- Coding conventions (package structure, naming, layering)
- Framework-specific patterns:
  - **Java/Spring Boot**: Controller→Service→Mapper layering, MyBatis/MyBatis-Plus, validation
  - **Python/FastAPI**: Router→Service→Repository layering, Pydantic models, dependency injection
- Database access patterns
- Application configuration notes

## Step 7: Initialize CLAUDE.md

Run `/init` equivalent to generate `CLAUDE.md`:

```markdown
# {Project Name}

## Project Overview
- Mode: {v1.0 | iteration}
- {brief description from config}

## Tech Stack

### Backend ({backend-project-name})
- Language: {java | python}
- Framework: {spring-boot | fastapi}
- Package: {package-path}
- Test: {test-framework}
- Project Root: `./{backend-project-name}/`

#### Directory Structure
{auto-extracted from actual project}

#### Common Commands
{auto-extracted from pom.xml/package.json/Makefile}

### Frontend ({frontend-project-name})
- Framework: {vue3 | react}
- UI Library: {element-plus | ant-design | custom}
- Project Root: `./{frontend-project-name}/`

#### Directory Structure
{auto-extracted from actual project}

#### Common Commands
{auto-extracted from package.json}

## Knowledge Base
- Backend Rules: `.claude/rules/backend.md`
- Frontend Rules: `.claude/rules/frontend.md`
- Workspace: `设计输入/`

## Reference Modules
- Backend: {reference-module}
- Frontend: {reference-module}

## Modules to Develop
{list from config}

## Notes
{additional notes from config}
```

## Step 8: Validation

1. Verify CLAUDE.md exists and is properly formatted
2. Verify `.claude/rules/frontend.md` and `.claude/rules/backend.md` exist
3. Verify `设计输入/` directory and all 7 subdirectories exist
4. Verify repositories are cloned and on the correct branch
5. Run a quick compile check if possible:
   - Java: `mvn -v` to verify Maven is available
   - Python: `python --version` to verify Python is available
6. Verify both repositories compile successfully:
   - Backend: `mvn clean compile -DskipTests` or `python -m compileall .`
   - Frontend: `npm install && npm run build` or equivalent

</process>

<constraints>
- Do NOT modify any existing business code in the cloned repos
- All configuration goes into `.claude/config.md` (Markdown format)
- The `设计输入/` directory name is fixed — do not translate or change it
- Reference modules MUST exist in the cloned repos; if not, ask user to specify
- Auto-detection results that differ from config MUST be confirmed with user
</constraints>
