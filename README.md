# nexai-harness

nexai 的 Claude Code 插件市场，提供 AI 原生开发全流程插件。

## 插件列表

| 插件 | 版本 | 状态 | 说明 |
|------|------|------|------|
| `ai-fullstack-orchestrator` | 1.0.0 | ✅ 已发布 | 全栈开发编排，覆盖 v1.0 初始化到迭代变更 |
| `pm-orchestrator` | — | 🚧 规划中 | 项目管理编排，需求/任务/进度 AI 驱动 |
| 更多插件 | — | 📋 陆续补充 | 持续扩展开发工具链 |

## 快速开始

```text
# 添加市场
/plugin marketplace add <repo-url>

# 安装插件
/plugin install ai-fullstack-orchestrator@nexai-harness-market
```

## 本地调试

```bash
claude plugin validate .
/plugin marketplace add .
/plugin install ai-fullstack-orchestrator@nexai-harness-market
```

## 仓库结构

```
ai-harness/
├── .claude-plugin/
│   └── marketplace.json          # 市场注册，声明所有插件
├── plugins/
│   └── ai-fullstack-orchestrator/ # 全栈开发编排插件
│       ├── .claude-plugin/plugin.json
│       ├── hooks/                 # 遥测系统
│       └── skills/               # 10 个技能
├── README.md
└── CLAUDE.md
```

## 许可证

MIT
