# Review Checklists

日期：2026-06-11
来源：吸收外部 code review、spec review、code quality review 和 feedback intake 机制后整理。
用途：作为 `code-review` 的按需参考；用于多文件实现、计划执行、subagent 产物、PR 反馈和提交前审查。

## Review Order

先审“是否做对题”，再审“是否做得好”。

1. Scope：审查对象、base/head、staged/unstaged、用户要求和非目标。
2. Evidence：实现报告、PR 描述、subagent 状态都是 claims，不是事实。
3. Spec compliance：找 missing、extra、misunderstood、plan issue。
4. Code quality：规格通过后审边界、错误、权限、测试、可维护性。
5. Verification：检查测试、smoke 和 diff 边界是否支撑 verdict。
6. Feedback：对外部反馈先技术核验，再采纳、调整或反驳。

## Review Brief

请求 reviewer 或自切 review 视角前，准备最小上下文包：

- What changed：本轮实现或修复。
- Requirements：用户要求、验收、非目标。
- Range：base/head、staged/unstaged 或文件范围。
- Evidence：已跑验证、失败项、未验证项。
- Constraints：用户硬边界、兼容要求、禁止触碰范围。

Reviewer 只读审查对象和证据，不修改工作树。主 Agent 不能要求 reviewer 忽略问题、预设严重度或替实现辩护。

## Fresh Perspective Gate

任务卡住、根因不清、复杂 diff 已形成或即将改共享接口时，先切 review 视角。

```markdown
## Fresh Perspective
Stuck Point:
Falsified:
Likely Next Evidence:
Risk If Continue Patching:
Recommended Next Step:
```

若 review 发现根因仍不清，回到 `bug-reproduction`；若发现计划/spec 错，标为 `Plan issue`。

## Claim Skepticism

把每个高风险声明拆成：

| Claim | Evidence | Verdict |
| --- | --- | --- |
| 实现/报告声称完成什么 | 文件/行、diff、测试、运行信号 | proved / contradicted / incomplete / insufficient |

重点搜索报告没提到的 extra work：新入口、默认行为、兼容分支、mock/fallback、临时 debug、生成产物和配置改动。

## Spec Compliance

检查：

- 用户要求的能力、状态、入口、字段、错误路径是否齐全。
- 是否遗漏计划文件、命令、测试或验收。
- 是否新增未要求功能、兼容分支、默认行为、mock 入口或临时 UI。
- 是否把旧 fixture、隐藏页面、stub 或历史占位当正式合约。
- 计划或需求是否路径错误、矛盾、不可验证或与真实架构冲突。

输出问题优先：

```markdown
## Spec Compliance
- [P1] Missing: ...
- [P2] Extra: ...
- [P2] Misunderstood: ...
- [Plan issue] ...
```

## Code Quality

规格通过后检查：

- 模块边界、文件职责、接口模式。
- 错误处理、超时、资源释放、部分失败和回滚。
- 权限、租户、owner、ID、数据源和审计语义。
- 生成产物是否来自事实源。
- 测试是否证明真实行为和负向路径。
- UI 是否有真实 workflow smoke。

严重度：

| Level | Use For |
| --- | --- |
| Critical | 数据丢失、安全漏洞、核心功能不可用、阻塞交付 |
| Important | 架构错误、边界遗漏、测试缺口、可复现回归 |
| Minor | 命名、文档、局部简化、非阻塞一致性 |

## Verdict Gate

| Verdict | 条件 |
| --- | --- |
| `blocked` | Critical、规格不符、source conflict、未澄清 blocker、数据/安全风险 |
| `with fixes` | Important 或明确测试缺口，修复路径清楚 |
| `ready with risk` | 未发现阻塞，但验证、runtime 或外部依赖有残余风险 |
| `ready` | 规格符合，无 Critical/Important，关键验证和 diff 边界充分 |
| `insufficient evidence` | 范围、diff、需求或验证证据不足 |

`can't verify from diff` 是证据缺口，不是通过；需要调用点、运行时、测试、页面/API smoke 或 source owner 证据。

## Feedback Intake

收到用户、PR reviewer、subagent 或工具反馈时记录：

| Field | Meaning |
| --- | --- |
| Source | 用户、历史决策、PR reviewer、subagent、自动工具 |
| Claim | 反馈声称需要改变什么 |
| Applies? | yes / no / unclear 与证据 |
| Conflict | 是否冲突用户 scope、架构、兼容、权限或提交边界 |
| Decision | accept / reject-with-reason / ask-user / defer / fixed |

规则：

- 人类用户反馈优先，但 scope 不清时先复述确认。
- 外部 reviewer 是建议来源，不是事实来源。
- 自动工具告警只是信号，必须说明真实影响和适用规则。
- 多项 feedback 互相依赖时，先做 intake，不半懂就改。

## Technical Pushback

适合反驳：

- 会破坏入口、兼容、权限、安全、数据隔离或用户确认决策。
- reviewer 缺少真实调用者、生成链路、平台版本或历史迁移边界。
- 建议“更完整”但无真实消费者、验收目标或 owner。

格式：

```markdown
我核对了 <evidence>，当前约束是 <constraint>。
直接按 review 改会导致 <risk>。
建议 <alternative>；如果要改变 <decision/scope>，需要先确认。
```

如果反驳后发现自己错了，短句说明新证据、误判来源，并立即修复和验证。

## PR Thread Handling

- 记录 comment/thread ID、文件、行号、意图和状态。
- 能 inline 回复时，回到原线程说明处理结果和证据。
- 修复前不标 resolved；修复后先跑验证或给代码证据。
- 无权限回复时，最终报告保留 comment ID 与建议回复内容。

## Final Integration Review

多任务、subagent 或分阶段计划结束后检查：

- 需求整体是否完成。
- 子任务接口、类型、命名、状态和错误语义是否一致。
- 是否有重复实现、两套入口、冲突 fallback 或临时 debug。
- 最终验证是否覆盖端到端关键路径。
- diff 是否混入无关文件、私有文档、工具缓存或用户已有改动。

## Final Review Output

```markdown
## Findings
[按严重度列问题；每条含文件/行、影响、证据、修复方向]

## Open Questions
[阻塞判断的问题]

## Test Gaps / Residual Risk
[没覆盖什么，以及为什么]

## Verdict
[blocked / with fixes / ready with risk / ready / insufficient evidence]

## Change Summary
[次要：概括实际改动]
```
