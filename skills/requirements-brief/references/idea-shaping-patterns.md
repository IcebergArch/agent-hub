# Idea Shaping Patterns

日期：2026-06-11
来源：吸收外部需求探索、spec review 和 visual companion 方法后整理。
用途：作为 `requirements-brief` 的按需参考；把模糊产品、工具、自动化、UI 或 workflow 想法收敛成可执行需求。

## Core Rule

先理解目标，再讨论方案；先收敛需求，再写实现计划。设计门禁按风险缩放：小修冻结不变量即可，高模糊/高风险/多角色任务需要明确 brief 或分段确认。

## Discovery Flow

1. Read context first：读相关代码、helper、用户点名文档或已有产品形态；不默认读报告目录。
2. Scope split early：多系统/多产品面先拆成可独立验收的子问题。
3. Ask one key question：一次只问一个会改变方向的问题。
4. Separate goal from solution：用户第一版方案只是候选。
5. Offer 2-3 approaches：给推荐、替代、最小方案及取舍。
6. Confirm design fidelity：简单任务 3-7 条不变量；复杂任务 brief/PRD/流程图。
7. Check unit boundaries：对象、组件、服务、工具、页面先确认职责、接口、依赖、可验证性。
8. Transition deliberately：需求清楚后再进入 `implementation-plan`。

## Spec Readiness Gate

进入 implementation plan 前检查：

| Category | Blocking issue | Advisory only |
| --- | --- | --- |
| Completeness | 缺核心 user/job、scope、workflow、验收、错误路径或非目标 | 章节顺序、标题命名 |
| Consistency | 角色、状态、数据源、权限、入口或目标矛盾 | 表述风格 |
| Clarity | 可合理解释成两种不同行为 | 个别句子可更顺 |
| Scope | 多个独立子系统无法形成一个计划 | later 还不够细 |
| YAGNI | 加入未请求能力或扩大交付假设 | 记录可选 follow-up |

输出：

```markdown
## Spec Review
Status: Approved | Issues Found | Needs Clarification
Blocking Issues:
Advisory Recommendations:
```

有 blocker 时先修 brief 或问一个最小问题。

## Scope Decomposition Gate

出现这些信号时先拆：

- 2 个以上独立用户任务。
- 2 个以上技术 owner 或数据源。
- 模块有独立成功/错误路径和验收。
- 一个模块决策会改变另一个模块前提。
- 出现“顺便”“完整平台”“全链路”“从 A 到 Z”。

拆分输出：

```markdown
## Decomposition
1. <子问题>
   - User/job:
   - Output:
   - Depends on:
   - Acceptance:

## Build Order
1. First:
2. Later:
```

拆后只选择第一个可独立验收子问题进入详细需求。

## Question Discipline

| 类型 | 何时问 |
| --- | --- |
| Goal | 目标可能被方案遮住 |
| User / job | 使用者和场景不清 |
| Scope | 范围可能膨胀 |
| Constraint | 技术、权限、数据源会改变方案 |
| Acceptance | 完成标准不清 |
| Trade-off | 多个方向都可行 |

少问不会改变决策的问题；低风险可合理假设时写入 `Assumptions`。

## Options Pattern

```markdown
## Options
1. Recommended: <方案>
   - Best when:
   - Trade-off:
2. Alternative: <方案>
   - Best when:
   - Trade-off:
3. Minimal: <方案>
   - Best when:
   - Trade-off:
```

用户选择不同方向时，更新需求和非目标，不沿旧推荐实现。

## Sectioned Design Approval

高模糊、高风险或跨多个对象时分段确认：

| Section | 确认内容 |
| --- | --- |
| Architecture / ownership | owner、边界、模块和非目标 |
| Objects / components | 核心对象、页面/组件、职责 |
| Data / state flow | 输入、状态、source of truth、同步/缓存 |
| Errors / permissions | 失败、权限、空态、取消、unsupported |
| Validation | 成功信号、负向验收、测试/manual smoke |

用户改动某段时，回写对其它段的影响。

## Unit Boundary Gate

新增或重塑单元前回答：

| Question | 需要写清 |
| --- | --- |
| Purpose | 用户任务、系统职责或状态转换 |
| Interface | 输入、输出、事件、route、API、props、tool schema |
| Dependencies | owner、数据源、权限、状态、外部服务、UI 容器 |
| Testability | 单测、契约测试、workflow smoke、手动验收 |

一个对象同时承担展示、持久化、权限、调度、网络和错误解释时，先拆边界。

## Visual Decision Gate

视觉辅助只在“看比读更清楚”时使用。适合布局、导航、mockup、状态流、关系图、风格对比；不适合权限语义、API/数据建模、范围和验收。

规则：

- 每个视觉问题单独判断，不一开视觉就全程使用。
- 旧 mockup、截图或流程图要标记冻结、废弃或待更新，避免暗示它仍是事实源。
- 用户文字反馈优先于点击/截图等结构化信号。

## Brief Self-Review

- 是否还有 TBD/TODO/不可验证表述。
- 目标、角色、状态或数据源是否矛盾。
- 是否把多个独立产品/系统塞进一个验收范围。
- 是否写死未确认技术方案。
- 是否覆盖成功、错误、边界和权限/状态。
- 非目标是否足够防止实现膨胀。

## Output Pattern

```markdown
## Summary
## Confirmed Invariants
## Options
## Scope
## Workflow
## Acceptance Criteria
## Non-goals
## Open Questions
```

`Open Questions` 仍会改变核心方案时，不进入实现计划。
