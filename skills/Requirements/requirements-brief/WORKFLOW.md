---
name: requirements-brief
description: 当用户提出产品、功能、工具、自动化、workflow 或改造想法，需要整理成需求简报和验收边界时使用。
---

# Purpose

把模糊想法变成组织可执行的需求简报：目标、边界、工作流和验收。

# When to Use

- “整理需求”“写 PRD”“功能简报”“把想法落成需求”。
- 目标、用户、非目标或验收标准还不清楚。
- 要做工具、页面、自动化或 workflow，但实现边界需要先收敛。

# When NOT to Use

- 用户已经给出明确实现任务，可以直接进入执行。
- 只是代码实现、测试或文档刷新。

# Inputs

- 用户意图、目标用户、使用场景、约束、已有方案、非目标、成功信号和开放问题。

# Decision Principles

- 区分底层目标和用户提出的第一版方案。
- 验收标准必须能被页面 smoke、API smoke、测试或人工验收验证。
- 非目标要明确，防止实现时扩 scope。

# Workflow

1. Capture Intent：提炼真正要解决的问题和当前方案假设。
2. Users And Jobs：说明谁用、在哪用、完成前后状态如何变化。
3. Scope：列 in-scope、out-of-scope 和后续可选项。
4. Workflow：写关键路径、输入、系统反馈、失败态和恢复路径。
5. Acceptance：定义成功、失败、边界、权限和数据验收。
6. Readiness：判断是否足够进入执行，或还需研究/导航/contract。

# Checklist

- 是否没有把第一版方案当唯一目标。
- 是否有可验证验收标准。
- 是否明确非目标和需要用户确认的问题。

# Escalation

- 需要实现闭环：`skills/Core/task-execution-lifecycle/WORKFLOW.md`
- 需要外部资料：`skills/Research/source-grounded-research/WORKFLOW.md`
- 涉及接口/tool：Engineering 相关 Skill。

# References

- `references/idea-shaping-patterns.md`
