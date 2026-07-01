# Agent Hub

个人 Agent 工作资产中心，用于让 Agent 随协作持续升级：更适配用户的习惯、风格、审美和质量标准，同时支持多项目差异化控制、项目风格展示和上下文隔离。

## Core Laws

1. **实事求是**：先尊重真实需求、真实上下文、真实证据、真实能力边界和真实产物状态，再抽象规则或执行方案。
2. **精简有效**：通用执行策略、逻辑、纲要和 workflow 必须短、准、可验证、可持续迭代；Skill 是可组合 SOP，不把 Hub 养成百科、词表或场景树。
3. **分层承载**：通用约束放入口和 Skill，工具差异放 `agents/`，项目 helper 与报告放文档工作区，具体路径由工作区 `README.md` 说明。
4. **按需加载**：默认只读必要入口；Skill、reference 和项目 helper 由任务类型、风险信号或用户要求触发。
5. **主动升级**：当任务暴露跨场景不变量、规则失效、owner 混淆、验证缺口或用户不应反复重述的稳定要求时，按 `skills/self-evolution-engine/SKILL.md` 判断是否沉淀。

## Routing

- `AGENTS.md`：Codex、Cursor 共用的主入口、加载顺序和硬约束。
- `agents/`：不同 Agent 工具的适配说明，例如 `agents/codex.md`、`agents/cursor.md`。
- `skills/`：可复用 workflow，每个技能一个 `skills/<skill-slug>/SKILL.md`，长检查表放同技能 `references/`。
- `/Users/shatang/Documents/workspace/`：长期文档工作区；Hub 只知道这个入口，helper、报告和项目文档的具体子目录以该目录下的 `README.md` 为准。
- `/Users/shatang/Documents/temp/`：临时材料区；过度产物、迁移账本、一次性对照和缓存清单放这里，不作为 Hub 或正式 workspace 的长期内容。
- Hub 架构蓝图：`skills/project-content-curation/references/hub-architecture.md`。

## Reading Guide

- 人看：先看本文件，再看 `AGENTS.md` 的标题和触发表；只在需要执行某类任务时进入对应 Skill。
- Agent 看：默认读 `AGENTS.md`、本文件和当前工具 adapter；Skill 与 reference 必须按触发和风险渐进加载。
- 入口文件不承载长解释；如果阅读时像“规则堆积”，优先把细节下沉到已有 Skill/reference。

## Work Directions

- AgentOS / Agent infra 产品。
- AI 技术、协议、组件和架构研究。
- Product UI、功能和架构设计与迭代。
- 重点产品域：音画同步、互动影游、Agent infra / AgentOS。

## Directory Map

```text
.
├── AGENTS.md
├── agents/
└── skills/

/Users/shatang/Documents/
├── temp/
└── workspace/
    └── README.md
```

`.agents/skills` 是兼容入口，指向 `skills/`，用于让支持该约定的工具自动发现技能。
