---
name: prompt-improvement
description: 当用户要求优化 prompt、agent instructions 或统一输出模板时使用。
---

# Purpose

维护组织统一输出规范和可复用 prompt 资产，不讲通用 prompt 教程。

# When to Use

- 用户要求优化 prompt、system/developer instructions、agent 指令或输出格式。
- 需要形成 RFC、ADR、Issue、Design Doc、Commit Message、PR、Checklist 等统一模板。
- 模型输出格式不稳、边界反复跑偏或工具使用规则需要固化。

# When NOT to Use

- 讲解什么是 prompt、few-shot、CoT、role prompting 等通用知识。
- 一次性文案，不需要成为长期模板。

# Inputs

- 当前 prompt、失败样例、目标输出类型、受众、用户希望保留的判断责任、希望 Agent 补足的能力缺口、禁止事项、验收标准和需要对齐的组织风格。

# Decision Principles

- Prompt 资产回答“团队希望如何输出”，不是复制通用提示词技巧。
- 需要跨轮、跨任务或跨 Agent 稳定复用的协作行为，必须写成明确的 prompt/instruction contract；不能依赖模型“已经理解用户”或临时上下文自行延续。
- 可复用 Prompt 的作用是校准协作责任：保留用户擅长且希望亲自判断的部分，补足用户明确要求 Agent 承担的分析、结构化、执行与验证；不按“能力越强介入越多”默认接管全部决策。
- 用户能力缺口只能来自明确表达、反复纠偏或可观察失败，不从单次简写、口吻或一次选择推断；Prompt 写清 Agent 可自主推进、必须展示 demo/evidence、需要请求决策和收到纠偏后立即停止的边界。
- 稳定规则在模板中，项目事实留给输入上下文。
- 输出规范必须可检查，避免靠长篇解释弥补目标不清。
- 先保住问题闭环和必要门禁，再压缩长度；用户认为内容过多时，优先合并重复表达和删除无效解释，不得删掉能阻止已知失败的关键判定、失败分类或恢复边界。

# Workflow

1. Identify Format：确认目标是 RFC、ADR、Issue、Design Doc、Commit Message、PR、Checklist 或 agent instructions。
2. Calibrate Responsibility：写清用户保留的目标、品味、方向与风险判断，以及 Agent 应补足的分析、方案、执行和验证责任；定义自主推进、展示、暂停和升级边界。
3. Extract Contract：写清输入、输出、边界、必须/禁止内容和完成标准。
4. Preserve Minimum Contract：从失败样例反推最小有效规则集，确认每个关键门禁都直接保护目标或已知失败边界。
5. Normalize Shape：统一标题、字段、顺序、语气、长度和证据要求，并在不损失最小有效规则集的前提下压缩重复。
6. Separate Context：把动态项目事实、临时方案和已废弃偏好从长期模板中剥离。
7. Add Checks：加入自检项、失败态、用户纠偏后的 stop/reset 行为和升级条件。
8. Validate Samples：用 2-4 个能力缺口和自主性不同的代表输入检查格式、责任边界与输出稳定性。

# Checklist

- 模板是否只包含长期组织规范。
- 是否说明用户与 Agent 各自负责什么，且没有把一次行为推断成永久能力缺口。
- 是否去掉通用 prompt 教程内容。
- 是否能通过样例验证输出形状。

# Escalation

- Prompt 规则应进入 Hub 或 agent 配置：`skills/Knowledge/knowledge-evolution/WORKFLOW.md`
- 需求格式不清：`skills/Requirements/requirements-brief/WORKFLOW.md`

# References

- None.
