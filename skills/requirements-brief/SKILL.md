---
name: requirements-brief
description: 当用户提出产品、功能、工具、自动化、工作流或改造想法，需要整理成需求简报、PRD、验收标准或非目标边界时使用。
---

# Requirements Brief

把模糊想法变成可执行需求。它回答“要解决什么、不做什么、如何算完成”。

## Trigger

- “整理需求”“写 PRD”“功能简报”“把想法落成需求”。
- “这个功能应该怎么定义”“拆验收标准”。
- 要做工具、页面、自动化或 workflow，但目标还不清楚。

## Workflow

1. **Capture Intent**：区分底层目标和第一版方案。
2. **Identify Users And Jobs**：谁用、在哪用、完成前后状态如何变化。
3. **Define Scope**：列 in-scope、out-of-scope、后续可选项。
4. **Map Workflow**：写关键路径、输入、系统反馈、失败态和恢复路径。
5. **Acceptance Criteria**：给可验证标准，覆盖成功、失败、边界和权限/数据要求。
6. **Readiness Review**：高模糊或高风险需求按 reference 检查是否足够进入实现计划。

## References

- `references/idea-shaping-patterns.md`：想法塑形、方案对比、readiness gate。

## Output

默认结构：Summary、Users And Jobs、Scope、Workflow、Acceptance Criteria、Non-goals、Open Questions。

## Quality Bar

- 不把用户提出的第一版方案直接当唯一目标。
- 验收标准必须能被测试、页面 smoke、API smoke 或人工验收验证。
- 非目标要写清楚，防止实现时扩 scope。
