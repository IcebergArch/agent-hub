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

- 任务领域、生命周期阶段、风险等级、用户点名角色、可用专项 Agent/tool、目标决策和验收信号。

# Decision Principles

- 复杂跨域任务默认 3-6 个角色，单一明确评审维度可收敛为 1-2 个；角色视角服务决策，不做表演式圆桌。
- 用户点名角色时，以用户指定为准。
- 角色视角不等于专项能力。设计、工程实现、研究或媒体制作的质量依赖专用模型、Agent 或工具时，优先把对应阶段路由给真正具备该能力的执行者，并保留统一 handoff、集成和验收 owner。
- 没有适配的专项 Agent 或多 agent runtime 时，由当前 Agent 按角色视角综合判断，并使用对应质量门；若能力缺口会改变可交付结果，必须显式报告，不能用角色名称假装已经具备专项能力。
- 专项 Agent 的输出是阶段产物，不自动成为最终决策或完成证据；主 owner 仍需检查目标一致性、跨阶段契约和真实验收。
- 方案评审只选择受影响的产品、UX、应用/平台/基础设施架构、SRE、安全、FinOps、数据或工具连接角色；不因为维度存在就默认全员参与。

# Workflow

1. Domain：识别 Agent infra、AI 技术、Product UI、音画同步、互动影游等领域。
2. Stage：判断是需求、架构、实现、验证、收口还是复盘。
3. Capability Route：区分需要的角色视角与实际执行能力；按阶段选择最小专项 Agent/tool，并写清输入、输出、禁止越界项、handoff 和验收 owner。
4. Roles：选择最小角色集合；按方案影响加入产品/UX、架构、SRE、安全、FinOps、数据或工具连接视角，并为未参与的相邻高风险角色记录不适用理由。
5. Init Input：在 init 阶段，每个角色只给会改变设计的约束、取舍、风险和验收信号，作为 update 正式撰稿的建设输入。
6. Review：update 成稿后优先验收专家意见是否被正确落实；只补充由具体方案或新证据暴露的 blocker，不重复发散提案。
7. Synthesis：输出综合结论；有分歧时说明取舍。

# Checklist

- 是否没有拉入无关角色。
- 是否把专项能力路由给适配执行者，并明确 handoff 与最终验收 owner；不可用时是否如实说明降级。
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
