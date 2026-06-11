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
   - 对多文件改动，先写清每个文件或单元的职责、接口、依赖和验证方式；职责不清时先拆边界再拆任务。

4. **Choose Approach**
   - 优先沿用现有模式。
   - 只有在能减少复杂度、降低风险或匹配已有抽象时才新增抽象。
   - 写出至少一个放弃的备选方案和原因，除非任务非常小。

5. **Create Steps**
   - 每一步都能执行、验证或回滚。
   - 先做最小闭环，再扩展边界情况。
   - 高风险或多步骤任务拆成 2-5 分钟可执行小步，写明精确文件路径、命令和期望结果。
   - 计划要能交给没有当前会话记忆的执行者：必要背景、用户硬边界、假设、数据源、验收信号和相关文件都要写进计划，不依赖“如前所述”。
   - 不写 `TBD`、`TODO`、“补充适当错误处理”“照上面类似处理”这类占位计划；需要代码或配置时给出足够可执行的具体内容。
   - 不把 commit、push、PR、merge 或 discard 写成默认必做步骤；除非用户明确要求，只记录建议提交边界、建议 commit message 和交付前检查。

6. **Plan Validation**
   - 列出最窄有效测试。
   - 对 UI、API、工具、agent workflow 等链路，区分静态验证、局部验证和端到端验证。

7. **Self Review**
   - 对照目标逐项确认计划覆盖。
   - 扫描占位词、前后命名不一致、步骤顺序倒置和验证缺口。
   - 如果计划太大，拆成能独立验收的子计划。
   - 高风险、多模块、交给其他 agent/线程执行或来自外部来源的计划，按 `references/plan-review-checklist.md` 跑 Plan Review Gate；阻塞问题必须先修计划，建议项只进入风险或 follow-up。

## References

- `references/plan-review-checklist.md`：当计划较大、风险较高、来自外部来源、需要交给其他 agent/线程执行，或准备按现有 plan 开始实现前读取。

## Output

默认输出：

```markdown
## Goal

## Current Context

## Approach

## Touch Points

## Steps

## Validation

## Execution Handoff

## Risks / Open Questions
```

## Quality Bar

- 计划必须能直接指导下一步实现。
- 不用大而空的“优化架构”“完善测试”。
- 对共享接口和行为变化必须写出消费者影响。
- 新增或重塑的模块、组件、服务、工具和页面必须有清晰边界；如果执行者无法用 purpose/interface/dependencies/testability 描述它，计划未完成。
- 用户说“do it/OK/没问题”后，默认进入执行模式。
- 计划的价值是降低执行歧义；如果执行者仍必须猜文件、接口、数据或验证标准，计划未完成。
- 计划中的 git 收尾动作必须尊重 Hub 默认：用户确认前不自动 commit、push、PR、merge 或 discard。
- 计划 review 只阻塞会导致做错、卡住、越界或无法验证的问题；措辞偏好、额外 polish 和可选优化不能冒充阻塞项。
