---
name: agent-team-router
description: 当任务涉及 AgentOS、Agent infra、AI 技术/协议/架构、Product UI/功能/架构、音画同步、互动影游或 Agent Team 时使用。
---

# Purpose

为复杂产品、架构和技术任务选择最小必要角色视角。

# When to Use

- AgentOS、Agent infra、AI 技术/协议/组件/架构研究。
- Product UI、功能、产品架构、管理台、工作流。
- 音画同步、互动影游、媒体 pipeline、Agent Studio、tool/MCP 连接。
- 用户提到 Agent Team / IT Agent Team。

# When NOT to Use

- 路径明确的机械改动、单点文案、小范围已知 bug。
- 不需要产品、架构、技术或角色判断的任务。

# Inputs

- 任务领域、生命周期阶段、风险等级、用户点名角色、目标决策和验收信号。

# Decision Principles

- 默认 3-6 个角色；角色视角服务决策，不做表演式圆桌。
- 用户点名角色时，以用户指定为准。
- 没有多 agent runtime 时，由当前 Agent 按角色视角综合判断。

# Workflow

1. Domain：识别 Agent infra、AI 技术、Product UI、音画同步、互动影游等领域。
2. Stage：判断是需求、架构、实现、验证、收口还是复盘。
3. Roles：选择最小角色集合，必要时加入安全、成本、运维、数据或工具连接视角。
4. Risks：每个角色只给当前任务有用的风险、建议和验收信号。
5. Synthesis：输出综合结论；有分歧时说明取舍。

# Checklist

- 是否没有拉入无关角色。
- 是否输出综合判断，而不是逐角色堆观点。
- UI 或 MCP/tool 细节是否只在需要时读取 reference。

# Escalation

- 操作型 UI / 管理台质量门：`references/product-ui-quality-gates.md`
- MCP/tool/API/worker 连接模式：`references/tool-connectivity-patterns.md`
- 视频或 timeline：`skills/Domain/video-creation/WORKFLOW.md`

# References

- `references/role-catalog.md`
- `references/product-ui-quality-gates.md`
- `references/tool-connectivity-patterns.md`
