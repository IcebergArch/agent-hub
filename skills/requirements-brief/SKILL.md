---
name: requirements-brief
description: 当用户提出产品、功能、工具、自动化、工作流或改造想法，需要整理成可执行需求简报、PRD、验收标准或非目标边界时使用。
---

# Requirements Brief

用这个技能把模糊想法变成可执行需求。重点不是写长 PRD，而是让后续设计、实现、测试都知道“要解决什么、不做什么、如何算完成”。

## Trigger

当用户说以下任一类需求时使用：

- “帮我整理需求”“写个 PRD”“做个功能简报”“把想法落成需求”
- “这个功能应该怎么定义”“帮我拆一下验收标准”
- “我要做一个工具/页面/自动化/workflow”

## Workflow

1. **Capture Intent**
   - 用一句话写清楚用户真正想达成的结果。
   - 区分用户说的方案和底层目标；不要把第一版方案当成唯一解。

2. **Identify Users And Jobs**
   - 谁使用这个能力。
   - 他们在什么场景下使用。
   - 他们完成任务前后状态有什么变化。

3. **Define Scope**
   - `In scope`：必须交付的能力。
   - `Out of scope`：本轮明确不做的能力。
   - `Assumptions`：当前为了推进而采用的假设。

4. **Map Workflow**
   - 写出主路径：入口、输入、处理、反馈、产物。
   - 写出至少 3 个异常路径：缺权限、缺数据、失败、取消、重复操作、超时等。

5. **Write Acceptance Criteria**
   - 使用可验证语言，不写“体验更好”“更智能”这种不可测表述。
   - 至少覆盖成功路径、错误路径、边界输入和权限/状态变化。

6. **Surface Decisions**
   - 标出需要用户决策的问题。
   - 不阻塞的问题先做合理假设，并在文档里写清楚。

## Output

默认输出：

```markdown
## Summary

## Users And Jobs

## Scope

## Workflow

## Acceptance Criteria

## Non-goals

## Open Questions
```

## Quality Bar

- 需求必须能指导实现，而不是只描述愿景。
- 验收标准必须能被测试或人工检查。
- 非目标要具体，避免范围不断膨胀。
- 不把技术实现细节提前写死，除非用户已经明确约束。
