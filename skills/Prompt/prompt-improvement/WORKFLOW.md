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

- 当前 prompt、失败样例、目标输出类型、受众、禁止事项、验收标准和需要对齐的组织风格。

# Decision Principles

- Prompt 资产回答“团队希望如何输出”，不是复制通用提示词技巧。
- 稳定规则在模板中，项目事实留给输入上下文。
- 输出规范必须可检查，避免靠长篇解释弥补目标不清。

# Workflow

1. Identify Format：确认目标是 RFC、ADR、Issue、Design Doc、Commit Message、PR、Checklist 或 agent instructions。
2. Extract Contract：写清输入、输出、边界、必须/禁止内容和完成标准。
3. Normalize Shape：统一标题、字段、顺序、语气、长度和证据要求。
4. Separate Context：把动态项目事实从长期模板中剥离。
5. Add Checks：加入自检项、失败态和升级条件。
6. Validate Samples：用 2-4 个代表输入检查格式稳定性。

# Checklist

- 模板是否只包含长期组织规范。
- 是否去掉通用 prompt 教程内容。
- 是否能通过样例验证输出形状。

# Escalation

- Prompt 规则应进入 Hub 或 agent 配置：`skills/Knowledge/knowledge-evolution/WORKFLOW.md`
- 需求格式不清：`skills/Requirements/requirements-brief/WORKFLOW.md`

# References

- None.
