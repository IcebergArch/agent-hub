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

常用指令摘要见 `reports/personal/agent/runbooks/2026-06-08-关于AgentHub-projectRefactor.md`；它用于人工翻阅和 LLM 快速查询，完整规则仍以 `AGENTS.md`、`README.md` 和对应 Skill 为准。

## Work Directions

用户长期工作方向包括：

1. 建设 AgentOS / Agent infra 产品。
2. 探索和研究各类 AI 技术、协议、组件和架构。
3. 设计、创建、维护和迭代 Product UI、功能和架构。

当前重点产品域包括：

- 音画同步产品。
- 互动影游产品。
- Agent infra / AgentOS 产品。

当对话涉及以上方向且需要产品、架构、技术或角色判断时，默认读取 `skills/agent-team-router/SKILL.md`，按其中的 Agent Team Router 自动选择参与视角；路径明确的机械改动不额外路由。

## Collaboration Efficiency

后续用户可能直接携带本 README 与 Agent 沟通。新任务开始前，Agent 应先基于本文件判断是否缺少必要信息；只有缺口会显著造成返工时才提问，且一次只问最关键的 1-3 个问题。

默认目标是把长任务从 2-4 小时压缩到更短闭环：

- 30 分钟：边界清楚的小修、小 UI、单接口问题。
- 1 小时：一条完整 CRUD 或前后端窄链路。
- 2 小时：跨 DB、API、前端、runtime 的功能闭环。

新任务进入执行前，Agent 应优先冻结 3-7 条不变量：目标仓库/模块、必须修改与禁止修改范围、核心数据源/owner、ID 与权限语义、优先级顺序、非目标、验收信号。若信息已足够，直接执行；若用户连续纠偏、提速或中断，立即切换到快速收敛模式：停止旧计划，只确认当前状态、最新约束和下一步最小动作。

推荐节奏：5 分钟冻结约束，30 分钟做最小闭环，10 分钟审查 diff 边界，20 分钟补测试和验证。轻量任务按实际风险缩短这些步骤；每 20-30 分钟给一次短状态更新，重点说明是否越界、是否阻塞、下一步是什么。

用户在一个任务中引用本 README 一次后，后续沟通默认持续适用 hub 规则；Agent 应在必要时主动读取、引用 hub 内容。更新或重构 hub 只在用户明确要求、触发 `receive`、存在定时/自动化触发，或出现反复且可复用的高风险流程缺口时执行；不得让这些后台质量动作阻塞主任务推进。

实现类任务按 `skills/task-execution-lifecycle/SKILL.md` 分级收口：轻量任务只做定向读取、最小修改、最窄验证和 diff 边界；标准任务冻结关键不变量后完成可验证闭环；高风险任务才升级到完整 review、外部参考、main 同步、commit/PR 准备或报告沉淀。

Agent Hub 的常驻升级任务之一是持续吸收 Superpowers 等优秀 Agent 方法论。“常驻”指后续在用户明确要求升级、触发 `receive`、或设置了定时提醒/自动化后执行；不代表当前对话持续自动续跑或等待。后续遇到外部技能库、插件、workflow 或工程实践时，默认按 `skills/methodology-harvest/SKILL.md` 先做来源核对、机制抽取和 Hub 适配，再落位到既有 Skill、Rule、Reference 或 Report。

Agent Hub 的另一项长期能力是 repo 内自升级：当任务内出现稳定、可复用、跨设备也应成立的流程缺口或设计经验时，优先通过 `skills/self-evolution-engine/SKILL.md` 判断是否需要沉淀。这个能力必须由仓库内的 Rule、Skill、Reference 和 Report 触发与恢复，而不是依赖某台设备上的 memory、automation 或插件缓存状态。

## Required Curation Stage

当用户要求整理项目内容、规则、Skills、报告或删除内容时，必须先执行内容整理阶段：分析存量内容，将可复用流程抽成 Skill，将稳定约束保留为规则，将项目结论放入 reports，将本地噪声、重复旧入口和未引用废弃内容验证后删除。

当用户说 `project refactor`、`refactor hub` 或要求从当前对话抽取可复用规则、技能、注意点、工作方法时，触发 `skills/refactor-hub/SKILL.md`：先回看当前对话消息和现有 hub 内容，再分析并落位可优化部分。

## Directory Map

```text
.
├── AGENTS.md
├── agents/
│   ├── codex.md
│   └── cursor.md
├── skills/
│   ├── README.md
│   └── <skill-slug>/
│       ├── SKILL.md
│       └── references/
└── reports/
    ├── index.md
    ├── company/
    │   └── projects/
    │       ├── aion/
    │       └── maxwell-ai/
    ├── personal/
    │   ├── agent/
    │   │   ├── analysis/
    │   │   ├── chat/
    │   │   └── runbooks/
    │   └── projects/
    │       ├── auto-info/
    │       └── maxwell-ai/
    └── README.md
```

`.agents/skills` 是兼容入口，指向 `skills/`，用于让支持该约定的工具自动发现技能。
