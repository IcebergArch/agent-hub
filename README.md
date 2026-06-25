# Agent Hub

个人 Agent 工作资产中心，用于让 Agent 随协作持续升级：更适配用户的习惯、风格、审美和质量标准，同时支持多项目差异化控制、项目风格展示和上下文隔离。

## Core Laws

1. **实事求是**：先尊重真实需求、真实上下文、真实证据、真实能力边界和真实产物状态，再抽象规则或执行方案。
2. **精简有效**：通用执行策略、逻辑、纲要和 workflow 必须短、准、可验证、可持续迭代；不把 Hub 养成百科或词表。
3. **分层承载**：通用约束放入口和 Skill，工具差异放 `agents/`，项目事实、风格和证据放对应 `reports/`。
4. **按需加载**：默认只读必要入口；Skill、reference 和 report 由任务类型、风险信号或用户要求触发。
5. **主动升级**：当任务暴露跨场景不变量、规则失效、owner 混淆、验证缺口或用户不应反复重述的稳定要求时，按 `skills/self-evolution-engine/SKILL.md` 判断是否沉淀。

## Routing

- `AGENTS.md`：Codex、Cursor 共用的主入口、加载顺序和硬约束。
- `agents/`：不同 Agent 工具的适配说明，例如 `agents/codex.md`、`agents/cursor.md`。
- `skills/`：可复用 workflow，每个技能一个 `skills/<skill-slug>/SKILL.md`，长检查表放同技能 `references/`。
- `reports/`：项目、个人、公司维度的结论、证据、设计取舍和运行记录；不作为默认全量加载内容。
- Hub 架构蓝图：`skills/project-content-curation/references/hub-architecture.md`。

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
├── skills/
└── reports/
```

`.agents/skills` 是兼容入口，指向 `skills/`，用于让支持该约定的工具自动发现技能。
