# Agent Team Role Catalog

日期：2026-06-24
用途：作为 `agent-team-router` 的按需参考；用于默认路由不足、需要解释角色职责、调整 Agent Team 或新增/合并角色时。

首次路由只读 `../SKILL.md`。只有需要完整角色职责、风险检查重点或维护角色定义时，才读取本文件。

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

## Agent Creation And Merge Protocol

- Agent Team 的目标是更好完成任务，而不是固定角色表演；当现有角色不足以覆盖任务质量、效果或规模时，可以创建临时角色视角，必要时沉淀为长期 Agent。
- 新建或调整 Agent 时必须说明：触发场景、mission、职责边界、out_of_scope、与现有 Agent 的差异、合并时需要检查的质量门槛。
- Team 规模按任务复杂度控制：默认 3 到 6 个角色；扩大团队必须能降低风险或提升结果质量，不能只增加讨论噪声。
- 多 Agent 结果必须经过最终合并层：综合结论优先于逐条罗列；存在分歧时说明取舍、采用哪条路径、弃用哪条路径以及原因。
- 对结构设计类任务，若目录/命名/导航成为主要风险，自动加入 `Information_Architecture_AGENT`；它负责降低认知成本，并检查是否过度收敛或过度扩散。工具入口层目录名统一收敛为 `surfaces`，其他同义词只作为语义触发或讨论概念。
- 用户允许干预 agent-hub 设计时，可以更新 README、Skills 或 Agent 定义；更新前先判断是否是稳定、可复用的协作规则，避免把一次性任务细节写入长期上下文。
