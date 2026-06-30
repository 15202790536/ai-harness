# AI Fullstack Orchestrator

AI 驱动的全栈开发编排插件，覆盖从项目初始化到迭代变更的完整开发生命周期。

## 核心理念

一个插件，两个模式，全生命周期覆盖：

| 模式 | 场景 | 核心能力 |
|------|------|---------|
| **v1.0 模式** | 全新项目初始化 | DDD 详细设计 + 数据库完整建模 + API 契约 + 6维评审 |
| **迭代模式** | 已有项目迭代 | 功能分类 + 拓扑排序 + 变更方案 + 增量DDL + 验证闭环 |

## 技能列表

### 核心流水线（按序执行）

| 序号 | 命令 | 角色 | 说明 |
|------|------|------|------|
| 01 | `/01-fullstack-init` | 项目初始化 | 克隆仓库、检测技术栈、创建目录、生成规范 |
| 02 | `/02-fullstack-analyze` | 需求分析 | 功能分类（create/alter/unchanged）、依赖拓扑 |
| 03 | `/03-fullstack-design` | 详细设计 | DDD设计 or 变更方案 + 6维评审 |
| 04 | `/04-fullstack-build` | 代码构建 | 拓扑排序→文件聚合→并行生成→多轮补齐 |
| 05 | `/05-fullstack-verify` | 验证修复 | 编译→测试→诊断→修复 循环（最多5轮）|
| 06 | `/06-fullstack-review` | 代码审查 | 架构/安全/规范 + 设计-代码一致性 |

### 辅助技能（按需调用）

| 命令 | 说明 |
|------|------|
| `/fullstack-autopilot` | 全自动4阶段编排 + 断点续传 |
| `/fullstack-quick` | 单文件快速变更 |
| `/fullstack-fpa` | 功能点分析（FPA） |
| `/fullstack-debug` | 全栈 Bug 修复 |

## 快速开始

### 安装

```
/plugin marketplace add https://github.com/15202790536/ai-harness.git
/plugin install ai-fullstack-orchestrator@nexai-harness-market
```

### 新项目使用

```
# 1. 初始化（首次运行会交互生成配置）
/01-fullstack-init

# 2. 将原型/需求放入 设计输入/01-需求输入/

# 3. 分析需求
/02-fullstack-analyze

# 4. 详细设计
/03-fullstack-design

# 5. 代码生成
/04-fullstack-build

# 6. 验证
/05-fullstack-verify

# 7. 审查
/06-fullstack-review
```

### 一键全自动

```
/fullstack-autopilot
```

## 支持的技术栈

### 后端（一期）
- Java / Spring Boot (JUnit 5)
- Python / FastAPI (pytest)

### 前端
- Vue 3 / Element Plus
- React / Ant Design

### 数据库
- MySQL
- PostgreSQL

## 配置

首次运行 `/01-fullstack-init` 会交互式生成 `.claude/config.md`，也可手动编辑：

```markdown
# 项目配置
## 基本信息
- 项目名称: my-project
- 运行模式: auto

## 后端技术栈
- 语言: java
- 框架: spring-boot
- 包路径: com.example.myproject
- 测试框架: junit5
- 仓库地址: https://gitlab.com/team/my-backend.git
- 分支: develop
...
```

## 许可证

MIT
