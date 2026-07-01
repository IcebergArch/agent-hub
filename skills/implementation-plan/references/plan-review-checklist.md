# Plan Review Checklist

日期：2026-06-11
来源：吸收外部计划审查机制后整理。
用途：作为 `implementation-plan` 的按需参考；用于多步骤计划、高风险计划、跨模块计划或交给其他 agent/线程执行前的 readiness review。

## Core Rule

计划是执行契约，不是说明文。review 的目标是判断执行者会不会做错、卡住、越界或验证不出来；不要把风格建议伪装成阻塞问题。

## When To Review

出现任一情况时，先跑 `Plan Review Gate` 再进入实现：

- 计划覆盖多个文件、模块、服务、UI workflow、schema、权限或数据迁移。
- 计划会交给另一个 agent、线程、subagent、同事或未来自己执行。
- 用户提供已有 plan/spec/checklist，希望按它执行。
- 计划中包含公共接口、生成产物、状态机、存储、运行时、tool gateway 或外部依赖。
- 计划来自长讨论，可能依赖隐含上下文、历史假设或未写明的验收信号。

小型单文件修复、纯文档 typo 或低风险样式微调不需要完整 review，但仍要检查目标、文件和验证。

## Plan Review Gate

| Category | Blocking issues | Advisory only |
| --- | --- | --- |
| Completeness | `TBD`、`TODO`、占位语、缺任务、缺验证、缺文件路径、缺期望输出 | 章节顺序、表达风格、可以更清楚的命名 |
| Spec Alignment | 漏掉需求、实现了非目标、改变用户硬边界、未解释关键假设 | 可选增强、未来优化、额外 polish |
| Task Decomposition | 单步过大、任务不能独立验证、共享状态并行编辑、前置依赖顺序错误、单元边界说不清 | 可以拆得更优雅，但当前执行不会卡住 |
| Buildability | 命令不可运行、类型/函数名前后不一致、依赖不存在、生成链路缺事实源、没有失败/成功信号 | 更快的命令、更多测试层级、额外诊断建议 |

阻塞问题必须先修计划，再执行。建议项可以记录到风险或 follow-up，不阻止开始。

## Delegation Contract

计划会交给另一个 agent、线程、subagent 或未来自己执行时，除了任务列表，还要显式写清两层契约：

- **Global Constraints**：所有任务共享的硬边界，例如目标范围、非目标、版本/依赖限制、命名和文案约束、权限/数据语义、禁止触碰的模块、必须保留的兼容窗口。
- **Per-task Interfaces**：每个任务消费什么、产出什么、改哪些文件、依赖哪个前置结果、如何被下游任务或 reviewer 观察。

缺 Global Constraints 会让下游执行者按隐含记忆扩写范围；缺 per-task Interfaces 会让相邻任务在字段、类型、状态、入口或验证口径上互相猜。两者任一缺失，且任务存在跨模块、跨 agent 或多阶段依赖时，视为 Plan Review Gate 的 blocking issue。

## Review Steps

1. 读计划和原始 spec/用户目标；没有 spec 时，从当前对话、issue、README 或代码入口重建验收标准。
2. 对每个目标找对应任务；找不到就是 spec alignment blocker。
3. 扫描占位词、含糊词、隐含上下文和未定义符号。
4. 检查单元边界：每个新增或重塑的文件、模块、组件、服务、工具或页面是否说明 purpose、interface、dependencies 和 testability。
5. 检查任务粒度：每个任务是否能在短循环内完成并留下证据。
6. 检查命令和期望结果：命令路径、环境、失败/成功输出是否足以验证目标。
7. 检查风险门：共享接口、生成产物、数据迁移、权限、安全、UI workflow 和外部依赖是否有负向验证或回滚说明。
8. 如果计划会被委派执行，检查 Global Constraints 和 per-task Interfaces 是否足够让新上下文执行者不依赖主会话记忆。
9. 分出 `Blocking Issues` 和 `Advisory Recommendations`；不要因为偏好不同阻塞计划。

## Output Format

```markdown
## Plan Review

Status: Approved | Issues Found | Needs Clarification

Blocking Issues:
- [Task X / Step Y]: 问题 - 为什么会导致执行者做错、卡住、越界或验证不出结果。

Advisory Recommendations:
- 建议 - 为什么有帮助，但为什么不阻塞执行。

Decision:
- Approved: 可以执行。
- Issues Found: 先修计划再执行。
- Needs Clarification: 只问最小阻塞问题。
```

## Fix Loop

- `Approved`：进入执行；执行中仍按 `task-execution-lifecycle` 维护进度和证据。
- `Issues Found`：先改计划，再重新跑阻塞项 review；不要在未修计划时靠执行者猜。
- `Needs Clarification`：只问会改变目标、scope、owner、数据源或验证标准的问题。

## Unit Boundary Review

计划涉及新增或重塑单元时，用这个小表补一遍边界审查：

| Unit | Purpose | Interface | Dependencies | Testability | Verdict |
| --- | --- | --- | --- | --- | --- |
| `<file/module/component>` | 负责什么 | 外部如何调用/观察 | 依赖谁 | 如何验证边界行为 | `clear` / `split first` |

`split first` 是阻塞项：先拆职责、抽接口、调整 owner 或缩小范围，再执行计划。不要把“文件会在实现时自然整理”当成可执行计划。

如果用户明确要求“不要写计划，直接做”，但任务高风险或计划来自外部来源，至少用本 checklist 做轻量内部审查，并在最终回复说明采用了最小审查。
