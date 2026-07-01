---
name: implementation-plan
description: 当用户要求实现前出方案、拆任务、评估改动范围、设计落地路径、制定验证计划或把需求转成工程计划时使用。
---

# Implementation Plan

把需求转成可执行工程计划。它回答“怎么做”，不替代 `requirements-brief` 的“做什么”。

## Trigger

- “先给方案/计划”“怎么实现”“拆任务”“落地步骤”。
- “评估改动范围”“先不要写代码，先设计”。
- 用户给已有计划并要求执行前审查。

## Workflow

1. **Read Before Planning**：先读相关代码、文档、配置和测试，不凭空发明结构。
2. **Define Target**：用 1-3 条写目标行为和非目标。
3. **List Touch Points**：列文件、模块、接口、数据、UI、测试和生成物。
4. **Choose Approach**：优先沿用现有架构；新增抽象必须有明确复杂度收益。
5. **Break Down Steps**：步骤要可执行、可验证、有顺序和依赖。
6. **Plan Validation**：列最窄有效命令、smoke、手动检查和无法验证的缺口。
7. **Review Plan**：高风险、多模块、交给其他 agent/线程或外部来源计划，按 reference 做 Plan Review Gate。

## References

- `references/plan-review-checklist.md`：计划审查、占位词、阻塞/建议分类。

## Output

默认结构：Goal、Current Context、Approach、Touch Points、Steps、Validation、Risks / Open Questions。交给其他 agent 执行时补 Execution Handoff。

## Quality Bar

- 计划必须能执行，不写空泛路线图。
- 不把“要重构一下”“补测试”当成步骤。
- 验证命令要尽量具体；不确定项标为风险或问题。
