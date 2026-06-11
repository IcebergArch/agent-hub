---
name: code-review
description: 当用户要求 review、审查 diff、看 PR、检查改动风险、找 bug、评估测试缺口、提交前检查，或任务卡住需要新视角时使用。
---

# Code Review

用这个技能做代码审查。优先找真实 bug、行为回归、接口风险和测试缺口，而不是泛泛夸代码。

## Trigger

当用户说以下任一类需求时使用：

- “review 一下”“看下这个 diff/PR”“帮我检查改动”
- “提交前看一下”“有没有风险”“代码审查”
- “为什么这个改动可能不对”
- “卡住了”“换个视角看看”“复杂 bug 修完后再确认一下”

## Workflow

1. **Define Review Scope**
   - 确认审查对象：当前 diff、staged diff、某个 PR、某个目录或某次提交。
   - 如果用户只说 `git diff`，只读审查，不改文件、不暂存、不提交。

2. **Collect Diff**
   - 查看状态、文件清单、stat、staged/unstaged。
   - 对高风险文件看完整 diff，不只看文件名。
   - 把实现者报告、PR 描述、subagent 状态和测试摘要都当成待验证 claims，不当成事实来源。

3. **Understand Behavior**
   - 找入口、调用者、测试和文档。
   - 对 API、schema、公共组件、配置、运行时入口，搜索真实消费者。

4. **Review For Risk**
   - 行为回归、边界条件、错误处理、权限/租户/数据隔离、并发、资源释放。
   - 生成产物是否来自事实源。
   - mock/fallback 是否掩盖真实链路。

5. **Check Tests**
   - 是否覆盖成功路径、失败路径、边界输入、旧行为防回归。
   - UI 或端到端变更是否有 smoke 信号。

6. **Report Findings First**
   - 发现问题时按严重度排序。
   - 每条问题包含文件/行、影响、复现或推理路径、建议修复方向。
   - 没有发现问题时明确说没有发现阻塞项，并说明残余风险。
   - 如果问题来自计划/spec 本身，标成 `Plan issue`，不要伪装成实现质量问题。
   - 如果 review 是因为任务卡住触发，先输出当前卡点、已证伪假设、失败证据和建议的下一步，不把它伪装成普通收尾审查。
   - 最后给出与最高严重度和验证缺口一致的 verdict：`blocked`、`with fixes`、`ready with risk`、`ready` 或 `insufficient evidence`；不要用模糊的“看起来还行”收口。

7. **Handle Review Feedback**
   - 收到用户、PR、subagent 或外部 reviewer 反馈时，先读完整反馈并复述技术要求；范围不清先问，不抢跑半懂的项。
   - 多项反馈先做 intake：拆分编号、来源、状态、依赖关系和验证方式；若任一不清楚项可能影响 scope、顺序或其它项含义，先澄清再改，不先实现自己懂的子集。
   - 人类反馈优先执行，但仍要核对文件、入口和影响面；外部/agent feedback 先验证是否符合本代码库，再决定采纳、调整或技术性反驳。
   - 外部 reviewer、subagent 或自动工具反馈如果与用户已确认的 scope、架构、兼容边界或提交边界冲突，先标记冲突并回到用户决策；不要让外部反馈静默覆盖用户约束。
   - 多项反馈逐条处理：阻塞问题、简单修复、复杂重构；每项修完都跑对应验证。
   - PR 或远端 inline review 要保留原 comment/thread 身份；能回复线程时回到原线程说明 fixed / clarified / disagreed 和证据，不把逐项反馈只汇总成顶层泛评。
   - 不用表演式附和代替技术判断；正确反馈直接修，错误反馈用代码、测试或约束说明原因。
   - 需要反驳 review 时只用技术证据、约束和用户已确认决策；如果反驳后发现自己错了，事实性说明新证据并修正，不长篇辩解。

## References

- `references/review-checklists.md`：当审查多文件实现、执行计划、subagent 产物、PR 反馈或交付前风险时读取，用于区分规格符合性、代码质量和反馈处理。

## Output

默认输出：

```markdown
## Findings

## Open Questions

## Test Gaps / Residual Risk

## Verdict

## Change Summary
```

## Quality Bar

- 不把风格建议放在真正 bug 前面。
- 不用“可能有问题”结束；要说明为什么、影响什么、怎么验证。
- 没看过的文件或没跑过的测试必须诚实说明。
- reviewer 结论不能替代本地证据；合入、提交或继续下一阶段前仍要独立看 diff 和验证输出。
- 多项 feedback 的完成状态必须可追踪；不能把未澄清、未验证或技术性反驳的项混在“已处理”里。
- review verdict 必须和证据一致；有 Critical、未澄清 blocker 或证据不足时不能写 ready。
