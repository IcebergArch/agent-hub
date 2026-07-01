---
name: agent-team-router
description: 当任务涉及 AgentOS、Agent infra、AI 技术/协议/架构研究、Product UI/功能/架构、音画同步、互动影游，或用户提到 Agent Team / IT Agent Team 时使用。
---

# Agent Team Router

为复杂产品、架构和技术任务选择最小必要角色视角。它是路由器，不是角色百科。

## Trigger

- AgentOS、Agent infra、AI 技术/协议/组件/架构研究。
- Product UI、功能、产品架构、管理台、工作流。
- 音画同步、互动影游、媒体 pipeline、Agent Studio、tool/MCP 连接。
- 用户提到 Agent Team / IT Agent Team。

路径明确的机械改动、单点文案、小范围已知 bug 不额外路由。

## Router

| Domain | Typical Roles |
| --- | --- |
| Agent infra / AgentOS | IT_Platform_Architect、Tool_Integration、Security_Governance、SRE_Ops |
| AI 技术/协议/架构研究 | Data_AI_Architect、IT_Platform_Architect、Security_Governance |
| Product UI / feature / architecture | Product_Workflow、UX_Product_Design、Application_Architect |
| 音画同步 / 视频 / timeline | Media_Sync_Product、Application_Architect、SRE_Ops |
| 互动影游 / game-film | Game_Interactive_Product、UX_Product_Design、Application_Architect |

默认 3-6 个角色。用户点名角色时，以用户指定为准。没有多 agent runtime 时，由当前 Agent 按角色视角综合判断。

## Workflow

1. 识别领域、生命周期阶段、风险等级和用户真正需要的判断。
2. 选择最小角色集合；必要时加入安全、成本、运维、数据或工具连接视角。
3. 每个角色只给对当前任务有用的风险、建议和验收信号。
4. 输出综合结论优先于逐角色罗列；有分歧时说明取舍。

## References

- `references/role-catalog.md`：完整角色职责和维护规则。
- `references/product-ui-quality-gates.md`：操作型 UI、管理台、布局和视觉质量门。
- `references/tool-connectivity-patterns.md`：MCP/tool/API/worker 连接模式。

## Output

默认包含：命中领域、选用角色、综合判断、关键风险、建议路径、验证信号。

## Quality Bar

- 不把所有角色拉进同一轮。
- 角色视角服务决策，不做表演式圆桌。
- 涉及 UI 或 MCP/tool 连通细节时再读对应 reference。
