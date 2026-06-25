---
name: agent-team-router
description: >-
  当任务涉及 AgentOS、Agent infra、AI 技术/协议/组件/架构研究、Product UI/功能/架构、
  音画同步产品、互动影游产品，或用户提到 Agent Team / IT Agent Team 时，自动选择
  最小必要角色视角并输出综合结论。
---

# IT Agent Team Router

本文件定义 IT、AI 技术研究和产品建设相关任务的 Agent Team 路由机制。目标是用最小上下文先选出必要角色视角，再按命中的领域读取对应 reference；不要把全部角色目录、UI 检查表或工具模式一次性塞进任务上下文。

当任务涉及 AgentOS、Agent infra、AI 技术/协议/组件/架构研究、Product UI/功能/架构、音画同步产品、互动影游产品时，默认使用本文件进行角色路由。用户临时指定角色时，以用户指定为准。

路径明确的机械改动、单点文案、小范围已知 bug 或只需要执行现有计划的任务，不额外路由；按 `task-execution-lifecycle` 的轻量路径完成即可。

## Routing Principles

- 先路由，再讨论：先判断任务属于哪个工作方向、产品域、生命周期阶段和风险等级，再选择最小必要 Agent Team。
- 用户不需要手动指定角色；除非用户明确点名，否则由本文件自动选择。
- 默认使用 3 到 6 个角色；不要把所有角色都拉入同一轮讨论。
- 角色路由不等于必须启动真实子 Agent；当工具环境没有多 Agent runtime 时，由当前工具按这些角色视角完成分析。
- 角色只提供职责视角和质量门槛，不替代最终综合判断。
- 首次触发只读本文件；只有命中具体角色目录、操作型 UI 或 MCP/tool 连通细节时，才读取 `references/` 下对应文件。

## Routing Inputs

每次路由先识别以下字段：

```yaml
work_direction:
product_domain:
lifecycle_phase:
risk_flags:
artifact_type:
primary_agents:
supporting_agents:
```

## Compact Router

| Work Direction | 触发信号 | 最小默认角色 |
| --- | --- | --- |
| `agent_os_build` | AgentOS、agent runtime、tool gateway、MCP、memory、store、eval、policy、control plane、agent loop | `IT_Platform_Architect_AGENT`、`Tool_Integration_AGENT`、`Security_Governance_AGENT`、`SRE_Ops_AGENT` |
| `ai_tech_research` | 探索、研究、对比、论文、协议、模型、RAG、agent framework、组件选型、架构演进 | `AI_Research_AGENT`、`Data_AI_Architect_AGENT`、`IT_Platform_Architect_AGENT` |
| `product_ui_feature_architecture` | Product UI、功能设计、前端交互、用户流程、产品架构、管理台、原型、迭代 | `Product_Workflow_AGENT`、`UX_Product_Design_AGENT`、`Application_Architect_AGENT` |
| `audio_visual_sync_product` | 音画同步、音频、视频、字幕、timeline、media pipeline、latency、render/export | `Media_Sync_Product_AGENT`、`Product_Workflow_AGENT`、`Application_Architect_AGENT`、`SRE_Ops_AGENT` |
| `interactive_game_film_product` | 互动影游、互动叙事、game loop、scene、state、branching、engine、实时交互 | `Interactive_Entertainment_Product_AGENT`、`UX_Product_Design_AGENT`、`Application_Architect_AGENT`、`SRE_Ops_AGENT` |
| `agent_infra_product` | Agent infra 产品、Agent Studio、工具管理、Agent 配置、调试、trace、评估、发布 | `IT_Platform_Architect_AGENT`、`Product_Workflow_AGENT`、`Tool_Integration_AGENT`、`Security_Governance_AGENT` |
| `information_architecture` | 目录结构、包结构、模块拆分、命名、入口层、surface、entrypoint、可维护性、认知成本 | `Information_Architecture_AGENT`、`Application_Architect_AGENT`、`IT_Platform_Architect_AGENT` |

## Risk Add-ons

命中以下风险信号时，在默认角色外补一个最相关角色即可；需要完整角色职责时再读 `references/role-catalog.md`。

| Risk Signal | 加入角色 |
| --- | --- |
| 生产变更、部署、回滚、长任务、CI/CD | `SRE_Ops_AGENT` |
| 权限、密钥、租户、数据访问、外部 API、CLI 执行 | `Security_Governance_AGENT` |
| 成本、资源配额、供应商、模型调用成本 | `FinOps_Governance_AGENT` |
| 数据流、知识库、记忆、向量库、模型上下文 | `Data_AI_Architect_AGENT` |
| MCP、HTTP、terminal、worker、第三方系统 | `Tool_Integration_AGENT` |

## Discussion Protocol

- 默认先执行 Work Direction Router 和 Risk Router，形成最小必要 Team。
- 涉及 AgentOS 或平台边界时，由 `IT_Platform_Architect_AGENT` 先界定问题归属和系统边界。
- 涉及 AI research 时，`AI_Research_AGENT` 必须区分事实、推断、假设和待验证问题。
- 涉及 Product UI、功能和架构时，`Product_Workflow_AGENT` 与 `UX_Product_Design_AGENT` 必须检查用户任务流和产品闭环。
- 涉及操作型 UI、配置台、管理台、列表/审核/监控页时，读取 `references/product-ui-quality-gates.md` 后再给布局或实现建议。
- `Tool_Integration_AGENT` 负责把 MCP、HTTP、terminal 和其他工具能力落到可调用 schema、回执和状态流。
- 需要讨论 MCP、HTTP API、terminal/CLI、MCP server 或 worker/job 的接入模式时，读取 `references/tool-connectivity-patterns.md`。
- 早期联调、mock 或本地验证中，如果用户已经明确数量、范围和验收目标，优先做直接、低成本、可跑通的实现；不要为了“未来可能泛化”提前抽 registry、hub、插件框架或复杂生命周期。后续确有扩展需求时再抽象。
- `Security_Governance_AGENT` 必须检查权限、审批、密钥和审计风险。
- `Data_AI_Architect_AGENT`、`FinOps_Governance_AGENT`、`Product_Workflow_AGENT` 等角色按议题加入，不默认扩大讨论面。
- 当设计或实现被用户连续纠偏，或完成高风险设计闭环后，自动执行 `skills/design-principle-library/SKILL.md` 的自我提升检查；把可复用原则更新到对应 Skill/Rule，不能只靠用户后续手动触发 `receive`。
- 最终输出必须包含综合结论，不只罗列角色观点；存在分歧时说明取舍。

## References

- `references/role-catalog.md`：角色职责、完整风险检查、角色定义 schema，以及新增/合并角色协议；只有默认路由不足、需要解释角色职责或维护 Agent 定义时读取。
- `references/product-ui-quality-gates.md`：操作型 Product UI、配置台、管理台、列表/审核/监控页的布局与质量门槛；只有 UI/产品体验任务命中时读取。
- `references/tool-connectivity-patterns.md`：MCP、HTTP API、terminal/CLI、MCP server、worker/job 的接入模式；只有工具连通架构或协议设计任务命中时读取。

## Output Shape

需要 Agent Team 讨论时，优先使用以下结构：

1. `结论`：一句话说明推荐方向。
2. `路由结果`：说明命中的工作方向、产品域、风险信号和参与角色。
3. `角色意见`：每个相关 Agent 给出风险和建议。
4. `架构取舍`：说明为什么这样分层或这样设计。
5. `落地清单`：列出需要补齐的接口、权限、状态、测试、体验和观测项。
6. `后续问题`：只保留真正阻塞决策的问题。
