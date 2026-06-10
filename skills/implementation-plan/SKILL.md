---
name: implementation-plan
description: 当用户要求实现前先出方案、拆任务、评估改动范围、设计落地路径、制定验证计划或把需求转成工程计划时使用。
---

# Implementation Plan

用这个技能把需求转成可执行工程计划。计划要服务实现，不写空泛路线图。

## Trigger

当用户说以下任一类需求时使用：

- “先给我方案/计划”“怎么实现”“拆一下任务”
- “评估改动范围”“先不要写代码，先设计”
- “给我 checklist”“落地步骤是什么”

## Workflow

1. **Read Before Planning**
   - 先读相关代码、文档、配置和测试。
   - 不在不了解现有模式时直接发明新结构。

2. **Define Target Behavior**
   - 用 1 到 3 条描述目标行为。
   - 明确本轮不改变的行为。

3. **List Touch Points**
   - 按层列出可能要改的文件或模块。
   - 标记共享接口、生成产物、数据迁移、权限、安全和 UI 风险。

4. **Choose Approach**
   - 优先沿用现有模式。
   - 只有在能减少复杂度、降低风险或匹配已有抽象时才新增抽象。
   - 写出至少一个放弃的备选方案和原因，除非任务非常小。

5. **Create Steps**
   - 每一步都能执行、验证或回滚。
   - 先做最小闭环，再扩展边界情况。

6. **Plan Validation**
   - 列出最窄有效测试。
   - 对 UI、API、工具、agent workflow 等链路，区分静态验证、局部验证和端到端验证。

## Output

默认输出：

```markdown
## Goal

## Current Context

## Approach

## Touch Points

## Steps

## Validation

## Risks / Open Questions
```

## Quality Bar

- 计划必须能直接指导下一步实现。
- 不用大而空的“优化架构”“完善测试”。
- 对共享接口和行为变化必须写出消费者影响。
- 用户说“do it/OK/没问题”后，默认进入执行模式。
