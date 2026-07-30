# Agent Hub

个人 Agent 工作资产中心，用于让 Agent 随协作持续升级：更适配用户的习惯、风格、审美和质量标准，同时支持多项目差异化控制、项目风格展示和上下文隔离。

Codex 插件入口为 `agent-hub`，唯一用户入口为 `/hub`。用户用 `/hub <指令>` 向 agent-hub 插件发送指令；不用 `/hub` 时不应自动触发该插件，也不做隐藏读取。触发后先读本文件，再按任务需要从 Hub 真实路径读取最新文件，不把 Hub 内容复制到插件目录或 Codex 静态副本里。

## Core Laws

1. **实事求是**：先尊重真实需求、真实上下文、真实证据、真实能力边界和真实产物状态，再抽象规则或执行方案。
2. **精简有效**：通用执行策略、逻辑、纲要和 workflow 必须短、准、可验证、可持续迭代；Skill 是可组合 SOP，不把 Hub 养成百科、词表或场景树。
3. **分层承载**：通用约束放入口和 Skill，工具差异放 `agents/`，项目 helper 与报告放文档工作区，具体路径由工作区 `README.md` 说明。
4. **按需加载**：默认只读必要入口；Skill、reference 和项目 helper 由任务类型、风险信号或用户要求触发。
5. **主动升级**：当任务暴露跨场景不变量、规则失效、owner 混淆、验证缺口或用户不应反复重述的稳定要求时，按 `skills/Knowledge/knowledge-evolution/WORKFLOW.md` 判断是否沉淀。
6. **项目隔离**：Hub 只保留跨项目可复用的泛化规则、workflow 和工具适配；具体项目路径、仓库约束、业务事实、SPEC/STDD、helper、报告和路由映射只写入文档工作区或项目自身。

## Routing

- `AGENTS.md`：Codex、Cursor 共用的主入口、加载顺序和硬约束。
- `CLAUDE.md`：Claude 专属入口；默认只做项目 SPEC 编写，不执行代码，除非用户明确切换 Claude 执行。
- `agents/`：不同 Agent 工具的适配说明，例如 `agents/codex.md`、`agents/cursor.md`。
- `skills/`：Hub 内部 workflow 库，唯一目录定义为 `skills/<Category>/<skill-slug>/WORKFLOW.md`，长检查表放同目录 `references/`。
- 文档工作区入口：`/Users/shatang/Project/nexus-os/doc-hub/README.md`。任务查找、SPEC/STDD、helper、报告和项目文档规则以该文件为准。
- `/Users/shatang/Documents/temp/`：临时材料区；过度产物、迁移账本、一次性对照和缓存清单放这里，不作为 Hub 或正式 workspace 的长期内容。
- Hub 架构蓝图：`skills/Context/project-content-curation/references/hub-architecture.md`。

## Reading Guide

- 人看：先看本文件，再看 `AGENTS.md` 的标题和触发表；只在需要执行某类任务时进入对应 Skill。
- 常规仓库协作：读 `AGENTS.md`、本文件和当前工具 adapter；workflow 与 reference 按触发和风险渐进加载。
- `/hub` 插件启动：先读本文件；只有任务需要执行规则、工具差异、具体 workflow 或文档工作区内容时，再读取对应文件。
- `/hub` 只表示显式启用 Agent Hub 路由；不要因此自动读取文档工作区项目文件，除非用户任务涉及具体项目或正式文档。
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
```

`skills/` 是 Hub 内部 workflow 库，由 `/hub` 按需读取；内部文件使用 `WORKFLOW.md`，不暴露为一组独立命令。
