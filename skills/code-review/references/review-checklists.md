# Review Checklists

日期：2026-06-11
来源：吸收 Superpowers `requesting-code-review/code-reviewer.md`、`subagent-driven-development/spec-reviewer-prompt.md`、`code-quality-reviewer-prompt.md` 和 `receiving-code-review` 的机制后整理。
用途：作为 `code-review` 的按需参考；用于多文件实现、计划执行、subagent 产物、PR 反馈和提交前审查。

## Review Order

先审“是否做对题”，再审“是否做得好”。规格不符合时，不进入质量 polish；质量审查不能掩盖缺功能、越界实现或误解需求。

1. **Scope capture**：确认审查对象、base/head、staged/unstaged、用户要求和非目标。
2. **Report skepticism**：把实现者报告、subagent 状态、PR 描述和总结都当成 claims，不当成事实来源。
3. **Spec compliance**：逐条核对需求、计划和实现，找 missing、extra、misunderstood。
4. **Code quality**：在规格通过后审架构、边界、错误处理、测试、可维护性。一个 reviewer 可以同时给 spec 和 quality verdict，但 spec verdict 不通过时先处理规格问题。
5. **Evidence check**：验证命令、测试覆盖和 diff 边界是否支持“可交付”结论。
6. **Feedback handling**：对外部反馈先技术核验，再采纳、调整或反驳。

## Review Brief

请求 subagent、外部 reviewer 或自己切换到 review 视角时，先准备一个最小上下文包；不要把当前会话历史当成 reviewer 的事实来源。

必备内容：

- What changed：本轮实现或修复的简短描述。
- Requirements：用户要求、计划文件、验收标准和明确非目标。
- Range：base/head、staged/unstaged 或具体文件范围。
- Evidence：已运行的测试、typecheck、页面/API smoke、失败项和未验证项。
- Constraints：用户硬边界、兼容要求、不要触碰的模块、外部依赖或权限限制。

没有 base/head 时，用 `git diff --name-status`、`git diff --stat`、定向 diff 和文件清单代替；没有 subagent 时，也按这个 brief 做自审。

长 diff、长任务说明或 subagent 交接优先写成 review package 文件，再把路径交给 reviewer。reviewer 应只读审查对象和证据，不修改工作树；主 Agent 不能在 brief 里要求 reviewer 忽略某类发现、预设严重度或替实现 rationale 辩护。

## Fresh Perspective Review Gate

任务卡住时，review 的目标不是证明“差不多完成”，而是打破当前执行视角。出现以下信号时，先切到 review 视角或请求独立 reviewer，再继续试补丁：

- 同一失败信号经过多次修复没有变窄。
- bugfix、重构或集成问题已经有复杂 diff，但根因、验收或影响面仍不清楚。
- 计划步骤执行到一半发现文件路径、架构边界、数据源或权限假设可能错了。
- 即将做大范围重构、删除兼容分支或修改共享接口，但缺 baseline / characterization 证据。

Fresh perspective brief 必须包含：

- Current stuck point：当前卡在哪里，失败信号是什么。
- Tried / falsified：已经尝试和证伪的假设，避免 reviewer 建议重复动作。
- Diff so far：当前 diff、临时实现、探索代码和可能污染点。
- Requirements / non-goals：用户目标、已确认不做的范围和任何 source conflict。
- Evidence gap：缺哪类证据才能继续，例如 root cause、baseline、测试、调用者、UI smoke 或权限语义。

输出要给下一步路径，而不是泛泛建议：

```markdown
## Fresh Perspective

Stuck Point:
Falsified:
Likely Next Evidence:
Risk If Continue Patching:
Recommended Next Step:
```

如果 review 发现根因仍不清，应回到 `bug-reproduction` 的根因追踪或 `Repeated Failure Architecture Gate`；如果发现计划/spec 错，应标为 `Plan issue`。

## Implementation Report Skepticism Gate

实现者报告、subagent `DONE`、PR 描述、最终总结和测试摘要都只是审查输入；它们可以帮你定位，但不能证明完成。

检查方式：

- 把每个高风险 claim 拆成 `claim -> evidence -> verdict`：例如“新增字段已接入 UI”要对应 diff、调用点、测试或页面 workflow。
- 对实现报告中的“已实现、已修复、已验证、已清理、无影响”逐项找真实代码、diff、命令输出或运行信号。
- 主动搜索报告没提到的 extra work：新增入口、默认行为、兼容分支、mock/fallback、临时调试、生成产物和配置改动。
- 如果发现问题来自计划/spec 本身，而不是实现偏差，标成 `Plan issue`；先回到计划或用户确认，不把计划错误伪装成代码质量问题。
- 如果无法重建原需求、计划或验收标准，审查结论只能写“证据不足”，不能写“看起来符合”。

高风险审查至少保留一条证据链：

```markdown
Claim: <实现者或 PR 声称完成了什么>
Evidence: <读到的文件/行、diff、测试或运行信号>
Verdict: <proved | contradicted | incomplete | insufficient>
```

## Spec Compliance Review

目标：证明实现与要求一致，不多不少。

检查项：

- 是否实现了用户明确要求的每个能力、状态、入口、字段和错误路径。
- 是否遗漏了计划中的文件、命令、测试或验收信号。
- 是否新增了未要求的功能、兼容分支、默认行为、mock 入口或临时 UI。
- 是否把旧 fixture、隐藏页面、stub 或历史占位误当成正式合约。
- 是否把实现报告里的说法与真实代码、diff、测试输出逐项对上。
- 计划或需求本身是否存在矛盾、不可验证、文件路径错误或与真实架构冲突；若有，单独标记为 `Plan issue`。

输出优先写问题：

```markdown
## Spec Compliance

- [P1] Missing: ...
- [P2] Extra: ...
- [P2] Misunderstood: ...
```

没有问题时，明确说“规格符合，未发现 missing / extra / misunderstood”，并说明看过的证据范围。

如果只凭 diff 无法确认某项要求，例如 runtime wiring、UI workflow、权限语义、生成链路或外部系统行为，标成 `can't verify from diff` / `insufficient evidence for runtime behavior`，并列出需要补的证据；不要把“diff 没看出问题”写成已通过。

## Code Quality Review

只有规格符合后再做质量审查。

检查项：

- 模块边界是否清晰，文件职责是否单一，接口是否符合既有模式。
- 错误处理、超时、资源释放、部分失败和回滚是否合理。
- 权限、租户、owner、ID、数据源和审计语义是否保持一致。
- 生成产物是否来自事实源；手改生成文件是否有依据。
- 测试是否验证真实行为，而不是 mock 行为；是否覆盖负向路径。
- UI/交互是否有真实 workflow smoke，而不是只证明页面能打开。
- 新文件或被显著扩大的文件是否还能独立理解和测试。

严重度校准：

| 等级 | 用于 |
| --- | --- |
| Critical | 数据丢失、安全漏洞、核心功能不可用、会阻塞交付的问题 |
| Important | 架构错误、边界遗漏、测试缺口、可复现回归、维护风险 |
| Minor | 命名、文档、局部简化、非阻塞体验或一致性问题 |

不要把风格建议标成 Critical；也不要把真实行为回归降成 nit。

## Action Gate

review 结果决定下一步，不只是参考意见：

| 结果 | 下一步 |
| --- | --- |
| Critical | 立即修复；修复前不继续后续任务、不 merge、不 PR |
| Important | 继续前默认修复；若暂缓，必须说明原因、风险和用户可接受性 |
| Minor | 可记录为 follow-up；不能阻塞核心交付，除非用户要求 polish |
| Reviewer wrong | 用代码、测试、需求或架构约束说明并技术性反驳 |
| Spec not compliant | 先补规格缺口或删除越界实现，再做代码质量审查 |
| Plan issue | 暂停把问题归咎于实现；先修正计划、澄清验收或请求用户确认 |

不能用“reviewer 说可以”替代主 Agent 的 diff、验证和边界复核；也不能因为 review 发现少量优点就忽略阻塞问题。

## Review Verdict Gate

每次 review 都要给出明确 verdict，且 verdict 必须由 findings、open questions、验证缺口和 diff 边界共同决定。不要用“看起来可以”“整体还行”“建议再看看”这类没有行动含义的收口。

| Verdict | 使用条件 | 下一步 |
| --- | --- | --- |
| `blocked` | Critical 问题、规格不符合、source conflict、未澄清 blocker、会导致错误实现或数据/安全风险 | 先修复或澄清；不继续提交、PR、merge |
| `with fixes` | Important 问题或明确测试缺口，但修复路径清楚 | 修复并重跑对应验证，再复审 |
| `ready with risk` | 未发现阻塞项，但存在未跑验证、外部依赖、无法检查的 runtime 或用户可接受风险 | 可交用户审查；必须列出风险和缺证据 |
| `ready` | 未发现 Critical/Important，规格符合，关键验证和 diff 边界证据充分 | 可进入下一阶段；仍需按任务生命周期做最终验证 |
| `insufficient evidence` | review 范围、diff、需求或验证证据不足以判断 | 先补证据，不给质量结论 |

Verdict 规则：

- 最高严重度控制最低结论：存在 Critical 时只能 `blocked`；存在 Important 时通常是 `with fixes`。
- `ready` 不能靠优点、局部测试或 reviewer confidence 得出；必须有对应证据。
- 如果问题来自计划/spec，本轮实现 review 的 verdict 应指向 `blocked` 或 `insufficient evidence`，并标明 `Plan issue`。
- 如果只做只读 diff 审查，verdict 要说明范围，例如 `ready for current diff scope` 或 `insufficient evidence for runtime behavior`。
- `can't verify from diff` 是证据缺口，不是通过语；需要主 Agent 补调用点、运行时、测试、页面/API smoke 或 source owner 证据。

## Final Integration Review

多任务、并行/subagent 或分阶段计划完成后，做一次整体审查；局部 review 通过只能说明每块可能正确，不能证明组合后正确。

重点检查：

- 需求整体是否完成，是否存在跨任务遗漏。
- 子任务之间的接口、类型、命名、状态和错误语义是否一致。
- 是否出现重复实现、相互覆盖的 fallback、两套入口或冲突配置。
- 是否有任务引入未要求能力、临时 debug、测试专用生产路径或 mock/fallback。
- 最终验证是否覆盖关键端到端路径，而不是只列各子任务局部命令。
- 最终 diff 边界是否清楚，未混入无关文件、公司项目报告、工具缓存或用户已有改动。

如果整体审查发现问题，先修正整体一致性和验收缺口，再回到对应的 spec compliance 或 code quality review；不要只在最终报告里列为风险。

## Evidence Expectations

审查结论必须说明证据来源：

- diff 范围：`git diff --name-status`、`git diff --stat`、高风险文件定向 diff。
- 消费者：调用点、路由、生成 client、测试、文档或 UI 入口。
- 验证：相关单测、typecheck、lint、API smoke、页面 workflow 或手动步骤。
- 未覆盖：没读的文件、没跑的测试、无法验证的 runtime 或外部依赖。

“看起来可以”不是证据；必须指出具体文件、命令、测试或行为信号。

## Handling Feedback

收到用户、PR reviewer、subagent 或外部工具反馈时：

1. 读完整反馈，拆成可执行项。
2. 技术复述每项要求；范围不清时先问。
3. 检查它是否适用于当前代码库、目标版本、架构决策和用户约束。
4. 检查反馈来源是否能改变当前约束；外部来源不能覆盖用户已确认决策。
5. 按阻塞问题、简单修复、复杂重构排序。
6. 每项修完跑对应最小验证，再处理下一项。
7. 如果反馈错误，用代码、测试、文档或约束说明原因。

外部 reviewer 是建议来源，不是事实来源。人类用户的反馈优先级最高，但仍要确认文件、入口和影响面，避免把模糊反馈实现成错误范围。

## Feedback Source Priority Gate

反馈来源决定处理方式，但不直接决定技术真假。

| Source | Default handling | 不能做什么 |
| --- | --- | --- |
| 用户当前反馈 | 优先理解并执行；scope 不清时先复述确认 | 不能按旧假设扩写到未确认范围 |
| 用户历史决策 / 硬边界 | 作为约束校验外部建议 | 不能被 external reviewer、subagent 或工具告警静默覆盖 |
| PR / 外部 reviewer | 当作技术建议，先查真实调用者、兼容边界和用户决策 | 不能因为措辞强就直接改 |
| Subagent / agent reviewer | 当作独立视角，必须复核 diff、证据和冲突 | 不能把 `DONE` 或 review summary 当完成证据 |
| 自动工具 / lint / scanner | 当作信号，按误报、规则适用性和风险分类 | 不能机械满足工具而破坏产品语义或用户 scope |

冲突处理：

- 如果外部反馈与用户当前要求、历史架构决策、兼容窗口、权限边界或提交边界冲突，先把冲突写成 `source conflict`。
- `source conflict` 默认需要用户确认，除非已有明确规则能判定外部反馈不适用。
- 如果外部反馈指出真实安全、数据丢失或核心功能风险，即使与原 scope 有张力，也要先报告风险和证据，再请用户决定是否扩大范围。
- 自动工具告警只能作为问题线索；要落地为改动，必须能说明真实影响、适用规则和验证方式。

最小记录：

| Field | Meaning |
| --- | --- |
| Source | 用户、历史决策、PR reviewer、subagent、自动工具 |
| Claim | 反馈声称什么需要改变 |
| Applies? | `yes` / `no` / `unclear`，以及证据 |
| Conflict | 是否冲突用户 scope、架构、兼容、权限或提交边界 |
| Decision | `accept` / `reject-with-reason` / `ask-user` / `defer` |

## Technical Pushback

反驳 review 不是争输赢，而是保护代码库约束、用户已确认决策和真实行为。只在有证据时反驳；不能验证时先说明缺口并请求方向。

适合技术性反驳的情况：

- 反馈会破坏现有入口、兼容性、权限、安全、数据隔离或用户已确认的架构决策。
- reviewer 缺少当前代码库上下文，例如真实调用者、目标平台、生成链路或历史迁移边界。
- 反馈要求“做完整/更专业”，但搜索不到真实消费者、验收目标或 owner，属于 YAGNI。
- 反馈与用户最新硬边界、scope、信息来源限制或提交边界冲突。
- 反馈本身技术判断错误，且可用代码、测试、文档、规范或运行结果证明。

反驳格式：

```markdown
我核对了 <evidence>，当前约束是 <constraint>。
直接按 review 改会导致 <risk>。
建议 <alternative>；如果要改变 <decision/scope>，需要先确认。
```

如果无法验证：

```markdown
这个点需要 <missing evidence/tool/context> 才能判断。
我可以先 <investigate option>，或按当前约束暂不改。
```

不合格的反驳：

- 只说“我觉得不用”但没有调用点、测试、需求或架构证据。
- 借 YAGNI 拒绝用户已经明确要求的能力。
- 为了维护自己已有实现而忽略 reviewer 指出的真实风险。
- 把用户反馈、外部 reviewer 反馈和自动工具告警混成同一优先级。

## Correcting Your Own Pushback

如果反驳后发现自己错了，快速收回并修正；不要把回复变成自我辩护。

可接受模式：

```markdown
你这个点是对的。我重新核对了 <evidence>，实际行为是 <fact>。
我之前的判断错在 <short reason>。现在按 <fix> 修正，并跑 <verification>。
```

保持三点：

- 说明新证据，而不是只说“我错了”。
- 短句解释误判来源，避免长篇心理或过程说明。
- 立即进入修复和验证；完成后把 feedback 状态更新为 `fixed`，并绑定证据。

## Feedback Intake Gate

多项 feedback 不是待办列表，而是一组可能互相影响的技术约束。动手前先做 intake，避免半懂就改。

最少记录：

| Field | Meaning |
| --- | --- |
| ID / source | 用户消息编号、PR comment/thread ID、reviewer 名称或工具来源 |
| Requirement | 技术要求，用自己的话复述 |
| Status | `understood`、`needs-clarification`、`accepted`、`rejected-with-reason`、`fixed`、`deferred` |
| Dependency | 是否依赖其它 feedback、架构决策、scope 或用户确认 |
| Verification | 修复或反驳后需要的代码证据、测试、diff 或文档 |

规则：

- 如果任一 feedback 不清楚，并且可能影响 scope、顺序、文件边界、架构方向或其它反馈含义，先问最小澄清问题；不要先实现“看懂的部分”。
- 如果 feedback 明确互不相关，可以先处理清楚且阻塞风险低的项，但必须标出仍未澄清的项和不处理原因。
- 外部 reviewer 的建议先核对真实调用者、平台版本、兼容约束和用户历史决策；不能因为措辞强就直接实现。
- 用户反馈优先级最高；但如果技术范围不清，先复述并确认，而不是按旧假设扩写。
- 外部反馈与用户已确认决策冲突时，状态标成 `source-conflict` 或 `ask-user`；不要在未确认时把外部建议实现成新 scope。
- 每项完成后把状态更新成 `fixed`、`rejected-with-reason` 或 `deferred`，并绑定证据；不要用顶层“已处理 review”覆盖逐项状态。

## PR Thread Handling

处理远端 PR、GitHub/GitLab inline review 或工具生成 comment 时，反馈项本身也是需要维护的工作对象：

- 先记录来源、comment/thread ID、文件、行号、原始意图和当前状态：`accepted`、`needs-clarification`、`rejected-with-reason`、`fixed`、`deferred`。
- 能回复 inline thread 时，逐项回到原线程说明处理结果和证据；顶层 PR comment 只用于汇总，不替代 thread reply。
- 修复前不要把 comment 标为 resolved；修复后先跑对应验证或给出代码证据，再回复。
- 如果反馈错误或不适用于当前代码库，在原线程用技术依据说明：相关代码、测试、兼容约束、用户硬边界或 YAGNI 证据。
- 如果当前工具、权限或认证无法回复线程，最终报告要说明限制，并保留 comment ID 与建议回复内容，避免反馈丢失。

示例命令可以按平台能力选择；在 GitHub CLI 可用且已认证时，inline PR review comment 通常应通过 review comment reply API 回到原 comment 线程，而不是新发一个顶层 PR comment。

## YAGNI Check

当 review 建议“完整实现”“做专业一点”“补管理能力”时，先搜索真实使用面：

- 有真实调用者、入口或计划验收：按真实需求实现。
- 没有调用者，也不是当前任务目标：建议删除伪接口或保持 unsupported。
- 未来可能需要但 owner 未确定：只保留稳定边界和诊断，不提前扩实现。

## Final Review Output

建议结构：

```markdown
## Findings

[按严重度列问题；每条含文件/行、影响、证据、修复方向]

## Open Questions

[只列阻塞判断的问题]

## Test Gaps / Residual Risk

[说明没覆盖什么，以及为什么]

## Verdict

[blocked / with fixes / ready with risk / ready / insufficient evidence，并说明依据]

## Change Summary

[次要：概括实际改动，不替代 findings]
```
