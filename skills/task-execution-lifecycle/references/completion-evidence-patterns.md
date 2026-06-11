# Completion Evidence Patterns

日期：2026-06-11
来源：吸收 Superpowers `verification-before-completion` 的完成前验证机制后整理。
用途：作为 `task-execution-lifecycle` 的按需参考；用于声明完成、修好、通过、可用、已打通、准备 commit/PR 或交付前。

## Core Rule

先有证据，再有结论。任何成功性表述都要绑定本轮新鲜验证；没有验证时，只能说“推断”“尚未验证”“部分完成”或“阻塞于某层”。

## Claim Gate

在说出结论前按顺序检查：

1. **Identify**：这个结论需要什么证据才能成立？
2. **Run / Inspect**：运行命令、打开页面、查询 API、读取 diff 或检查 artifact。
3. **Read**：读取完整输出、退出码、失败数量、错误文本和关键状态。
4. **Match**：证据是否覆盖用户目标，而不是只覆盖相邻信号？
5. **State**：只陈述证据真正支持的状态。

跳过任何一步时，不使用“完成”“通过”“修好”“可用”“打通”这类结论词。

## Verification Output Match Gate

验证输出必须证明目标，不只是命令成功。读取输出时至少确认：

- Command：命令、路径、过滤条件和环境变量是否对应本轮目标。
- Coverage：输出是否显示目标测试、构建、页面、API、workflow 或 artifact 被实际检查。
- Count：测试数、断言数、文件数、失败数、skipped 数是否合理；`0 tests`、全 skipped 或 no-op 不能证明通过。
- Freshness：输出是否来自当前代码和当前进程，不是缓存、旧日志、旧服务或子 agent 口头摘要。
- Scope：窄范围命令只能证明窄范围；不能外推到 build、全量测试或端到端能力。
- Semantics：错误态暴露、route 被调用、mock/fallback 成功和 dry-run 成功不能等同业务 workflow 成功。

如果输出无法确认这些点，结论只能写成 `部分验证`、`静态通过`、`wiring 到位` 或 `未验证`，并说明缺少哪一层信号。

## Requirements Trace Gate

当准备说“需求已满足”“任务完成”“计划阶段完成”或“可以交付”时，不能只用测试、lint 或实现摘要代替需求核对。先把用户要求、计划、spec、issue、验收标准和本轮后续纠偏转成一张 checklist，再逐项找证据。

最小格式：

```markdown
| Requirement | Evidence | Verdict |
| --- | --- | --- |
| <用户明确要求/验收项/非目标边界> | <文件、diff、测试、页面/API smoke、命令输出或未验证原因> | met / partial / missing / not verified |
```

规则：

- 用户最新消息、明确路径/范围、非目标和后续纠偏都要进入 checklist；不要只检查最初计划。
- 每项证据必须直接覆盖该要求；窄测试不能证明宽需求，静态检查不能证明端到端 workflow。
- 非目标也要核对：确认没有顺手实现、删除或改动范围外内容。
- 任一 `missing`、`partial` 或 `not verified` 都不能汇总成“需求已满足”；只能报告当前状态和缺口。

## Claim Matrix

| 声明 | 需要的证据 | 不足以证明 |
| --- | --- | --- |
| 测试通过 | 本轮测试命令输出、退出码、测试数/失败数且覆盖目标 | 旧输出、0 tests、全 skipped、只跑了无关测试、agent 口头报告 |
| lint/typecheck/build 通过 | 对应命令 fresh output | 只看编辑器无红线、其它命令通过 |
| bug 修好 | 原始复现路径或回归测试成功 | 改了代码、错误消失但目标行为未成功 |
| regression test 有效 | red/green 或等价临时回退证明 | 测试只绿一次 |
| UI workflow 可用 | 真实入口、真实数据/上下文、关键操作和结果可见 | 页面能打开、mock/fallback 显示成功 |
| API/tool/runtime 打通 | 请求到达目标下游且业务结果成功 | route 被调用、gateway wiring 到位、错误被暴露 |
| requirements met | 逐条验收标准和非目标边界审查 | 测试通过但没对照需求 |
| agent/subagent 完成 | 主 Agent 复核 diff、文件、验证和冲突 | 子 agent summary 写了 done |
| 可以 commit/PR | diff 边界、敏感文件、生成物来源和验证结果清楚 | 工作区看起来没问题 |

## Layered Status Words

按证据选择更精确的状态：

- `已验证`：最小验收信号已满足，证据覆盖目标。
- `部分验证`：局部命令或静态检查通过，但端到端或业务信号缺失。
- `wiring 到位`：入口、route、gateway 或调用链能到目标，但业务结果未成功。
- `错误暴露`：错误态正确展示或返回，但能力未可用。
- `未验证`：没有 fresh evidence。
- `阻塞`：缺依赖、缺权限、缺上游能力或用户决策，且已说明下一步。

## Positive Language Gate

积极语气也会暗示状态。没有证据时，不只要避免“完成/通过/修好”，也要避免让用户以为工作已经成功。

需要先过 Claim Gate 的表达：

- “Done / all set / 完成了 / 搞定了”。
- “Great / perfect / 很好 / 没问题了”。
- “应该可以 / 应该没问题 / looks good / seems fixed”。
- “可以提交 / 可以 PR / 可以合并”。
- “测试应该会过 / build 应该没问题”。

没有 fresh evidence 时，改用事实状态：

- “已修改 X，验证未跑”。
- “静态检查通过；端到端未验证”。
- “wiring 到位，但业务结果还缺下游证据”。
- “当前推断是 X，下一步需要 Y 验证”。

进度更新也遵守这条规则：可以说正在做什么、已改哪些文件、下一步验证什么；不能用兴奋或满意语气包装未验证成功。

## Delegated Work

对子 agent、外部 reviewer、生成工具或自动化的成功报告：

1. 先读它声称改了什么、验证了什么。
2. 检查实际 diff 或产物是否存在。
3. 运行或复核最小验证。
4. 检查是否有 scope drift、mock/fallback、未提交文件或冲突。
5. 再把结论纳入最终状态。

代理报告是线索，不是完成证据。

## Red Flags

看到这些表述或冲动时停下补证据：

- “应该可以”“看起来没问题”“我觉得修好了”。
- “完成了”“搞定了”“perfect”“all set”等满意或成功暗示。
- 想用旧测试输出支撑当前代码。
- 只跑了 lint，却想说 build 或 tests pass。
- 只看到错误态，却想说 workflow 可用。
- 子 agent 说 done，主 Agent 还没看 diff。
- 累了、赶时间、想先提交再补验证。
- 用户问进度时，想把部分完成包装成完成。

## Final Response Checklist

交付前确认最终回复包含：

- 本轮实际完成内容。
- 已跑的命令、页面/API/manual smoke 或引用搜索。
- 每个关键结论对应的证据层级。
- 未验证、部分验证或阻塞项。
- diff 边界：staged、unstaged、untracked、无关保留内容。

如果任务只是文档/规则资产，验证可以是 `git diff --check`、引用搜索、尾随空白搜索、目录/索引可发现性和边界检查；不需要伪造无意义的项目测试。
