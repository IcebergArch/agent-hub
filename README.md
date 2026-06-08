# Agent Hub

个人 Agent 使用经验仓库。

这个仓库维护三类本地 Agent 工作资产：

- `agents/`：不同 AI 工具的适配说明。
- `skills/`：可复用技能，每个技能一个目录。
- `reports/`：分析、审查、运行记录等沉淀报告；个人单独与 Agent 探讨、设计或研究放在 `reports/personal/agent/`，个人项目放在 `reports/personal/projects/`，公司项目放在 `reports/company/projects/`。

根目录的 `AGENTS.md` 是 Codex、Cursor 共用的主入口。工具专属差异放在 `agents/` 里，不把同一套规则复制多份。

## Living Context

本 `README.md` 是用户与 AI 协作过程中的长期上下文入口。Codex、Cursor 等工具进入本仓库时，应先通过 `AGENTS.md` 或工具规则读取本文件，而不是要求用户每次手动附带背景。

本文件只沉淀稳定、可复用、会影响后续协作判断的信息；一次性任务细节、临时草稿、密钥、私密凭据和未验证过程日志不要写入这里。

## Work Directions

用户长期工作方向包括：

1. 建设 AgentOS / Agent infra 产品。
2. 探索和研究各类 AI 技术、协议、组件和架构。
3. 设计、创建、维护和迭代 Product UI、功能和架构。

当前重点产品域包括：

- 音画同步产品。
- 互动影游产品。
- Agent infra / AgentOS 产品。

当对话涉及以上方向时，默认读取 `skills/agent-team-router/SKILL.md`，按其中的 Agent Team Router 自动选择参与视角；用户不需要手动指定角色。

## Required Curation Stage

当用户要求整理项目内容、规则、Skills、报告或删除内容时，必须先执行内容整理阶段：分析存量内容，将可复用流程抽成 Skill，将稳定约束保留为规则，将项目结论放入 reports，将本地噪声、重复旧入口和未引用废弃内容验证后删除。

当用户说 `refactor hub` 或要求从当前对话抽取可复用规则、技能、注意点、工作方法时，触发 `skills/refactor-hub/SKILL.md`：先回看当前对话消息和现有 hub 内容，再分析并落位可优化部分。

## Directory Map

```text
.
├── AGENTS.md
├── agents/
│   ├── codex.md
│   └── cursor.md
├── skills/
│   ├── README.md
│   ├── agent-team-router/
│   ├── paper-record/
│   ├── project-content-curation/
│   └── refactor-hub/
└── reports/
    ├── index.md
    ├── company/
    │   └── projects/
    │       ├── aion/
    │       └── maxwell-ai/
    ├── personal/
    │   ├── agent/
    │   │   ├── analysis/
    │   │   └── chat/
    │   └── projects/
    │       └── auto-info/
    └── README.md
```

`.agents/skills` 是兼容入口，指向 `skills/`，用于让支持该约定的工具自动发现技能。
