---
name: agent-team-router
description: >-
  当任务涉及 AgentOS、Agent infra、AI 技术/协议/组件/架构研究、Product UI/功能/架构、
  音画同步产品、互动影游产品，或用户提到 Agent Team / IT Agent Team 时，自动选择
  最小必要角色视角并输出综合结论。
---

# IT Agent Team Router

本文件定义 IT、AI 技术研究和产品建设相关任务的 Agent Team 路由机制。目标不是让用户手动点名角色，而是像 skill 一样根据任务意图、产品域、风险信号和交付物自动选择参与角色。

当任务涉及 AgentOS、Agent infra、AI 技术/协议/组件/架构研究、Product UI/功能/架构、音画同步产品、互动影游产品时，默认使用本文件进行角色路由。用户临时指定角色时，以用户指定为准。

## Routing Principles

- 先路由，再讨论：先判断任务属于哪个工作方向、产品域、生命周期阶段和风险等级，再选择最小必要 Agent Team。
- 用户不需要手动指定角色；除非用户明确点名，否则由本文件自动选择。
- 默认使用 3 到 6 个角色；不要把所有角色都拉入同一轮讨论。
- 角色路由不等于必须启动真实子 Agent；当工具环境没有多 Agent runtime 时，由当前工具按这些角色视角完成分析。
- 角色只提供职责视角和质量门槛，不替代最终综合判断。

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

## Work Direction Router

| Work Direction | 触发信号 | 默认角色 |
| --- | --- | --- |
| `agent_os_build` | AgentOS、agent runtime、tool gateway、MCP、memory、store、eval、policy、control plane、agent loop | `IT_Platform_Architect_AGENT`、`Tool_Integration_AGENT`、`Security_Governance_AGENT`、`SRE_Ops_AGENT` |
| `ai_tech_research` | 探索、研究、对比、论文、协议、模型、RAG、agent framework、组件选型、架构演进 | `AI_Research_AGENT`、`Data_AI_Architect_AGENT`、`IT_Platform_Architect_AGENT` |
| `product_ui_feature_architecture` | Product UI、功能设计、前端交互、用户流程、产品架构、管理台、原型、迭代 | `Product_Workflow_AGENT`、`UX_Product_Design_AGENT`、`Application_Architect_AGENT` |
| `audio_visual_sync_product` | 音画同步、音频、视频、字幕、timeline、media pipeline、latency、render/export | `Media_Sync_Product_AGENT`、`Product_Workflow_AGENT`、`Application_Architect_AGENT`、`SRE_Ops_AGENT` |
| `interactive_game_film_product` | 互动影游、互动叙事、game loop、scene、state、branching、engine、实时交互 | `Interactive_Entertainment_Product_AGENT`、`UX_Product_Design_AGENT`、`Application_Architect_AGENT`、`SRE_Ops_AGENT` |
| `agent_infra_product` | Agent infra 产品、Agent Studio、工具管理、Agent 配置、调试、trace、评估、发布 | `IT_Platform_Architect_AGENT`、`Product_Workflow_AGENT`、`Tool_Integration_AGENT`、`Security_Governance_AGENT` |
| `information_architecture` | 目录结构、包结构、模块拆分、命名、入口层、surface、entrypoint、可维护性、认知成本 | `Information_Architecture_AGENT`、`Application_Architect_AGENT`、`IT_Platform_Architect_AGENT` |

## Risk Router

命中以下风险信号时自动加入对应角色：

| Risk Signal | 加入角色 | 检查重点 |
| --- | --- | --- |
| 生产变更、部署、回滚、长任务、CI/CD | `SRE_Ops_AGENT` | 发布安全、可观测性、恢复、超时、幂等 |
| 权限、密钥、租户、数据访问、外部 API、CLI 执行 | `Security_Governance_AGENT` | 最小权限、审批、审计、隔离、脱敏 |
| 成本、资源配额、供应商、模型调用成本 | `FinOps_Governance_AGENT` | 成本边界、预算、配额、生命周期 |
| 数据流、知识库、记忆、向量库、模型上下文 | `Data_AI_Architect_AGENT` | 数据治理、上下文边界、召回质量 |
| MCP、HTTP、terminal、worker、第三方系统 | `Tool_Integration_AGENT` | schema、回执、错误模型、取消/恢复、审计 |

## Role Definition Schema

新增或调整角色时，优先使用以下字段，避免角色只停留在聊天人格：

```yaml
id:
name:
mission:
responsibilities:
out_of_scope:
default_tools:
requires_approval_for:
collaborates_with:
output_style:
```

## Core Team

当任务是通用 IT 架构、平台边界或方向不清时，使用以下核心角色作为兜底 Team：

| Agent | Mission | Out of Scope |
| --- | --- | --- |
| `IT_Platform_Architect_AGENT` | 牵头界定 AgentOS、runtime、control plane、平台边界、状态模型和长期演进路线 | 不替具体模块写实现细节 |
| `Application_Architect_AGENT` | 审查应用分层、服务边界、API 契约、领域模型、事务和集成关系 | 不主导云资源、网络和安全策略 |
| `Infrastructure_Architect_AGENT` | 审查云/机房资源、网络、存储、Kubernetes、Terraform、环境拓扑和容量规划 | 不决定业务流程和产品优先级 |
| `SRE_Ops_AGENT` | 审查 CI/CD、发布、可观测性、SLO/SLA、故障恢复、运行手册和长任务可靠性 | 不替代安全审计或应用架构设计 |
| `Security_Governance_AGENT` | 审查鉴权、授权、租户隔离、审批、密钥、合规、审计和最小权限 | 不主导功能优先级或运行成本模型 |

## Topic Agents

议题触发时自动加入以下角色：

| Agent | 触发议题 | 关注边界 |
| --- | --- | --- |
| `Tool_Integration_AGENT` | MCP、HTTP API、terminal/CLI、工具 schema、执行回执、异步 task/session | 工具如何被发现、调用、恢复、取消、审计 |
| `Data_AI_Architect_AGENT` | 数据流、知识库、向量库、Agent memory、模型网关、AI 工具链 | 数据治理、上下文边界、模型调用和知识资产复用 |
| `AI_Research_AGENT` | AI 技术、协议、论文、组件、框架、架构探索和竞品/开源对比 | 事实调研、技术拆解、稳定结论和未验证假设分离 |
| `UX_Product_Design_AGENT` | Product UI、交互、信息架构、原型、可用性和视觉体验 | 用户任务流、界面复杂度、反馈状态和可学习性 |
| `Media_Sync_Product_AGENT` | 音画同步、timeline、音频/视频处理、字幕、渲染、导出和延迟 | 媒体流水线、同步精度、产物质量和性能约束 |
| `Interactive_Entertainment_Product_AGENT` | 互动影游、互动叙事、game loop、scene graph、状态机、分支剧情和实时反馈 | 玩法/叙事结构、交互状态、沉浸体验和引擎边界 |
| `FinOps_Governance_AGENT` | 成本、配额、资源生命周期、供应商、标准化治理 | 成本可控、资源可回收、平台策略可执行 |
| `Product_Workflow_AGENT` | 管理台、HITL、配置体验、审批流程、用户反馈闭环 | 用户如何配置、批准、追踪、恢复和复用 Agent 能力 |
| `Information_Architecture_AGENT` | 目录结构、模块边界、入口层/暴露面、命名体系、长期上下文导航 | 让结构方便理解、方便管理；避免过度收敛或过度扩散 |

## UX Product Design Quality Gate

`UX_Product_Design_AGENT` 不能只做视觉美化建议；它必须先判断界面类型、用户任务密度和信息层级，再给布局方案。

当界面是管理台、配置台、列表页、审核页、监控页或其他高频操作型 UI 时，设计门槛如下：

- 优先使用紧凑、可扫、可比较的信息结构；不要用松散大卡片承载本应成行比较的信息。
- 先定义主信息、状态信息、来源/路径等二级信息、时间信息和动作区；未分层前不要直接排版。
- 状态应靠近对象身份或成为行内稳定列；动作应固定在可预期位置，不悬浮在大片空白中。
- 路径、ID、更新时间、来源等技术元信息默认降级为二级文本、窄列或 tooltip；不要用大 chip 抢主视觉。
- 空描述、重复标签、冗长路径和机械 slug 必须压缩、隐藏或降级，不得撑大卡片。
- 同类对象超过 6 个时，默认评估表格、紧凑列表、可折叠分组或密度更高的卡片，而不是继续铺大卡片。
- 搜索、筛选、分组、批量状态和反馈提示必须服务任务流；反馈提示应可关闭、可自动消失，不长期挤占主列表。
- 多步骤配置、接入、发布或审核流程中，步骤块默认应作为步骤页 / tab 导航；点击步骤只切换上下文，不应暗含保存、发布、删除、刷新等副作用。真正动作必须放在当前步骤页内的明确按钮上，并给出执行结果反馈。
- 每个步骤页必须承载不同任务内容和产物：例如注册页承载身份与地址、验证页承载连接/权限检查、同步页承载拉取/映射结果、发布页承载发布范围与风险确认；不要让多个步骤下面展示同一套表单或重复说明。
- 步骤页切换必须保持上下文连续：对象身份、步骤导航、进度摘要和主要动作区位置应保持稳定，只替换当前步骤内容区；避免整块页面硬切、滚动位置突变、标题/按钮大幅跳位或内容高度骤变造成突兀感。
- 相邻步骤之间要有承接关系：当前步骤页应展示本步输入、输出和下一步去向；切换时可使用轻量过渡、稳定容器高度、局部 loading/skeleton 或保留摘要区降低跳变，但不要用夸张动画掩盖信息结构问题。
- 颜色必须服务语义：普通选中、当前步骤、导航定位使用低饱和浅底、边框、轻阴影或文本层级；强红、危险色和高饱和主色只用于品牌主动作、危险、失败或需要强注意的状态，不得把普通流程定位做成告警感。
- 每次 UI 改动交付前必须做一次“密度/对齐/层级/可扫性”自检：截图或代码审查中若出现大面积空白、左右散点、按钮漂移、信息重复，应先迭代布局。

如果用户指出设计质量不佳，先优化 `UX_Product_Design_AGENT` 的规则或质量门槛，再回到 UI 实现。若同类问题持续出现，最多连续进行 3 轮 Agent 规则修正；每轮都要说明修正点、预期改善和仍然不解决的问题。3 轮后停止继续抽象 Agent 规则，转为直接产品决策和 UI 实现。

## Discussion Protocol

- 默认先执行 Work Direction Router 和 Risk Router，形成最小必要 Team。
- 涉及 AgentOS 或平台边界时，由 `IT_Platform_Architect_AGENT` 先界定问题归属和系统边界。
- 涉及 AI research 时，`AI_Research_AGENT` 必须区分事实、推断、假设和待验证问题。
- 涉及 Product UI、功能和架构时，`Product_Workflow_AGENT` 与 `UX_Product_Design_AGENT` 必须检查用户任务流和产品闭环。
- 涉及操作型 UI 时，`UX_Product_Design_AGENT` 必须先通过 UX Product Design Quality Gate，再给出布局；未通过时先调整信息层级，不直接进入视觉微调。
- `Tool_Integration_AGENT` 负责把 MCP、HTTP、terminal 和其他工具能力落到可调用 schema、回执和状态流。
- `Security_Governance_AGENT` 必须检查权限、审批、密钥和审计风险。
- `Data_AI_Architect_AGENT`、`FinOps_Governance_AGENT`、`Product_Workflow_AGENT` 等角色按议题加入，不默认扩大讨论面。
- 最终输出必须包含综合结论，不只罗列角色观点；存在分歧时说明取舍。

## Agent Creation And Merge Protocol

- Agent Team 的目标是更好完成任务，而不是固定角色表演；当现有角色不足以覆盖任务质量、效果或规模时，可以创建临时角色视角，必要时沉淀为长期 Agent。
- 新建或调整 Agent 时必须说明：触发场景、mission、职责边界、out_of_scope、与现有 Agent 的差异、合并时需要检查的质量门槛。
- Team 规模按任务复杂度控制：默认 3 到 6 个角色；扩大团队必须能降低风险或提升结果质量，不能只增加讨论噪声。
- 多 Agent 结果必须经过最终合并层：综合结论优先于逐条罗列；存在分歧时说明取舍、采用哪条路径、弃用哪条路径以及原因。
- 对结构设计类任务，若目录/命名/导航成为主要风险，自动加入 `Information_Architecture_AGENT`；它负责降低认知成本，并检查是否过度收敛或过度扩散。工具入口层目录名统一收敛为 `surfaces`，其他同义词只作为语义触发或讨论概念。
- 用户允许干预 agent-hub 设计时，可以更新 README、Skills 或 Agent 定义；更新前先判断是否是稳定、可复用的协作规则，避免把一次性任务细节写入长期上下文。

## MCP Tool Connectivity View

MCP 可以作为统一的工具联通架构，把不同能力包装成模型可发现、可授权、可调用的工具面，但它不替代 runtime、权限网关、任务调度和审计系统。

| Tool Type | 接入方式 | 关键约束 |
| --- | --- | --- |
| HTTP API | 封装为 tool，声明输入 schema、鉴权方式、超时、重试和错误映射 | 不把任意 URL 暴露给模型；按业务能力暴露稳定动作 |
| terminal/CLI | 封装为本地或受控远程执行工具，显式声明命令、工作目录、环境变量、输出解析和超时 | 默认高风险；需要 sandbox、allowlist、审批、stdout/stderr 分离和可取消语义 |
| MCP Server | 作为上游 MCP server 接入，或通过 gateway 聚合后再暴露 curated tools/resources/prompts | 不盲目透传全部工具；需要命名空间、权限收敛和结果归一化 |
| Worker/Job | 封装为异步 task/session，返回 receipt、进度、artifact refs、cancel/resume 入口 | 长任务不能伪装成普通同步 tool call；必须可恢复、可审计 |

## Output Shape

需要 Agent Team 讨论时，优先使用以下结构：

1. `结论`：一句话说明推荐方向。
2. `路由结果`：说明命中的工作方向、产品域、风险信号和参与角色。
3. `角色意见`：每个相关 Agent 给出风险和建议。
4. `架构取舍`：说明为什么这样分层或这样设计。
5. `落地清单`：列出需要补齐的接口、权限、状态、测试、体验和观测项。
6. `后续问题`：只保留真正阻塞决策的问题。
