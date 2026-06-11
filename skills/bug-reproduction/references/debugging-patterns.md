# Debugging Patterns

日期：2026-06-11
来源：吸收 Superpowers `systematic-debugging` 的 root-cause tracing、condition-based waiting、defense-in-depth 和 polluter finder 思路后整理。
用途：作为 `bug-reproduction` 的按需参考；遇到复杂 bug 时先选择模式，再回到当前仓库语言、测试框架和边界实现。

## Principles

- 先追根因，再修症状。错误出现的位置通常只是坏值、坏状态或错误副作用被使用的位置。
- 一次只验证一个假设。每个假设都要能被日志、最小测试、临时断言或定向复现证伪。
- 等真实条件，不等猜测时间。异步问题优先等待事件、状态、计数、文件、请求完成或 UI 可见性。
- 修复后让问题结构上更难复发。入口、业务逻辑、环境 guard 和诊断信息各自防住不同绕行路径。
- 临时诊断必须可删除、可解释。调试日志、断言、脚本只服务定位，最终要么移除，要么沉淀为正式保护。

## Pattern Matrix

| 场景 | 先做什么 | 证据 | 常见错误 |
| --- | --- | --- | --- |
| 错误出现在深层调用栈 | 从报错点向上追调用者、参数来源和状态变更点 | 调用链、坏值首次出现的位置、触发输入 | 在最底层吞错或加 fallback |
| 有相似参考实现 | 完整阅读参考路径，列 working/broken 的全部相关差异 | 差异表、解释每个差异是否影响目标 bug | 只看函数名或局部片段就复制改造 |
| 不知道哪个测试或步骤污染环境 | 先确认污染物存在条件，再按测试文件、fixture、入口或时间顺序二分 | 第一个产生污染的最小用例 | 清理污染物但不找产生者 |
| 异步测试偶发失败 | 把 sleep 改成条件等待，超时信息写清正在等什么 | 目标事件、状态、数量或文件在超时前出现 | 任意加长 timeout |
| 入口校验修了但仍可绕过 | 画出数据流，按入口、业务、环境、危险操作前检查 | 每一层都有明确错误和测试或 smoke 覆盖 | 只在 UI 或 handler 层校验 |
| 线上或集成链路只能间接观察 | 在边界加临时诊断：入参、出参、配置、上下文、下游响应 | 能证明断点在哪个边界的日志或 trace | 直接猜下游 bug |
| 修复尝试连续失败 | 停止叠补丁，重读错误、diff、调用链和相似实现；3 次失败后进入架构/前提审查 | 新假设能解释全部已知现象，或确认当前架构模式/测试前提不成立 | 基于上一次失败继续局部补丁 |
| 生产事故需要止血 | 先采集最低证据，再做可逆 mitigation；根因分析保持打开 | 事故时间线、影响范围、止血动作、回滚方式、后续根因假设 | 把 retry/fallback/重启/清缓存包装成“修好了” |
| 排查后仍无稳定根因 | 先证明已排查边界，再标记为外部/环境/时序未闭环 | 已排查项、未知项、观测条件、监控或诊断补强 | 为了交付编造一个“很可能”的 root cause |

## Root Cause Tracing

1. 写下表面症状：错误文本、失败断言、页面状态、请求响应或污染物。
2. 找直接原因：哪一行代码、哪次调用或哪个边界直接产生失败。
3. 继续向上追：谁调用它，传入的值从哪里来，状态何时改变。
4. 找坏值首次出现的位置：如果只知道坏值在哪里被使用，还没有根因。
5. 用最小证据确认：单测、临时日志、断言、stack、trace 或定向复现。
6. 在源头修复；必要时在危险边界加防御。

可用追踪问题：

- 这个值第一次变坏在哪里？
- 这个状态在进入当前模块前是否已经错误？
- 失败路径和同仓库成功路径差在哪个条件？
- 如果当前修复只在报错点生效，是否还有其它入口能绕过？

## Reference Pattern Comparison Gate

当 bug、测试失败或集成问题附近存在可工作的相似实现、官方示例、历史测试、旧版本代码或相邻模块时，先完整理解参考路径，再决定修复。这个 gate 防止“看起来差不多”的局部复制把隐藏前提漏掉。

执行步骤：

1. **Locate**：列出参考实现、成功测试、官方示例或历史提交路径。
2. **Read through**：阅读完整相关路径，包括入口、调用链、配置、错误处理、状态更新和测试；不要只读目标函数。
3. **Compare**：列出 working 与 broken 的差异，包括数据形状、权限/scope、状态机、默认值、依赖注入、异步等待、缓存、错误语义和环境。
4. **Classify**：把差异标成 root-cause candidate、required context、irrelevant 或 still unknown。
5. **Test**：用一个最小假设验证关键差异；不要一次把参考实现的多个差异都搬过来。
6. **Adapt**：只迁移已解释且与目标 bug 相关的机制；保留当前模块 owner、接口和既有约束。

对照表：

| Difference | Working reference | Broken path | Classification | Evidence / next test |
| --- | --- | --- | --- | --- |
| `<condition>` | `<how it behaves>` | `<how it differs>` | candidate / context / irrelevant / unknown | `<test, log, trace or reason>` |

停止信号：

- 还没读完整参考路径，却已经开始改代码。
- 只列出“参考实现用了 X”，没有说明 broken path 缺的是哪个前提。
- 同时搬运多个差异，验证不出到底哪个修好了问题。
- 参考实现属于不同 owner、scope、权限或 runtime，却没有适配说明。

## Hypothesis Ledger Gate

复杂 bug、偶发问题、多组件链路或前两次假设失败后，维护一个轻量假设账本。目标是防止“试了很多东西”但没有积累知识。

| 字段 | 记录什么 |
| --- | --- |
| Observation | 当前事实：错误文本、坏值、状态、日志、diff 或复现条件 |
| Hypothesis | 单一假设：`我认为 X 是根因，因为 Y` |
| Test | 最小验证动作：日志、断言、窄测试、临时开关或只读查询 |
| Output | 实际输出、失败信号、计数、状态或 trace |
| Result | confirmed / falsified / inconclusive |
| Next | 修复、换新假设、补观测、收窄复现或进入架构/前提审查 |

规则：

- 一次只测一个变量；不要把多个猜测合成一个 patch。
- `falsified` 的假设不能继续作为修复依据；下一轮必须解释新假设如何覆盖旧证据。
- `inconclusive` 不是通过；要么补观测，要么缩小复现条件。
- 账本只保留决策相关证据；不粘贴长日志。

输出示例：

```markdown
| Observation | Hypothesis | Test | Output | Result | Next |
| --- | --- | --- | --- | --- | --- |
| API 500，db error 为空 | handler 丢失 tenant id | 打印 handler->repo 入参 | tenant id 已存在 | falsified | 追 repo query 条件 |
```

## Condition-Based Waiting Gate

遇到 flaky、timeout、hang、并行测试偶发失败或异步链路不稳定时，先过这个门。目标是等业务上真实完成的信号，而不是猜一个机器刚好够快的时间。

任意 sleep / timeout extension 默认是反模式，除非本轮正在验证真实 timing 行为，例如 debounce、throttle、固定 tick、重试间隔或超时窗口。即便如此，也要先等待触发条件，再说明等待时长来自哪个已知机制。

优先等待业务上真实完成的信号：

| 等待目标 | 更好的条件 |
| --- | --- |
| 异步事件处理完成 | 事件列表包含目标类型或目标 id |
| 状态机完成 | 状态变成 expected state |
| 批处理完成 | 已处理数量达到预期，或 pending 归零 |
| 文件/产物生成 | 文件存在且大小、内容或 checksum 符合预期 |
| 页面渲染完成 | 关键元素可见、按钮可用、错误 overlay 不存在 |
| API 链路完成 | 目标状态可由后端查询确认 |

任意 sleep 只有在测试 timing 行为时才合理，例如 debounce、throttle、重试间隔或固定 tick。即便如此，也要先等待触发条件，再写明等待时长来自哪个已知机制。

条件等待要有三个要素：

- fresh read：每轮都重新读取状态，不用循环外的旧快照。
- timeout：失败时抛出包含等待目标的清晰错误。
- polling interval：间隔要足够小以保持测试快，足够大以避免忙等。

等待门输出：

```markdown
Waiting for: <业务条件，不是时间>
Fresh read: <每轮如何重新读取状态>
Timeout: <上限和失败信息>
Polling: <间隔与原因>
Timing exception: <如果用了固定等待，说明真实 timing 机制>
```

修复 flaky 后，验证不能只跑一次目标测试。至少要说明选择了哪种稳定性证据：重复运行目标测试、并行运行、CI 等价命令、或用更强的状态断言证明 race 已消除。

## Finding Polluters

污染源包括意外文件、全局变量、数据库记录、缓存、端口占用、环境变量、mock 状态、定时器和后台进程。

定位顺序：

1. 确认污染物在单独复现前不存在。
2. 找到能稳定产生污染的最小命令或测试集合。
3. 按文件、describe、case、fixture 或操作步骤做二分。
4. 每次运行前检查污染物是否已存在；已存在说明清理边界不干净。
5. 找到第一个污染者后，继续追它为什么会写到错误位置或泄露状态。
6. 修复产生者；只在必要时补清理逻辑，不能用清理掩盖错误写入。

如果需要脚本化，脚本应接收两个参数：污染物检查表达式和测试/命令列表；脚本本身不要假设固定包管理器或测试框架。

## Repeated Failure Architecture Gate

连续失败不是“再多试一个 patch”的信号。第 3 次局部修复仍没有让失败信号变窄时，停止写代码，转向架构/前提审查。

先判断失败模式：

- 每个修复都暴露新的共享状态、隐式耦合或跨层副作用。
- 修复一个入口后，另一个入口以相似方式失败。
- 修复需要大范围重构才能不破坏其它行为。
- 失败信号没有变窄，只是移动到不同文件、不同测试或不同 UI 状态。
- 测试、需求或数据源前提可能不成立，例如断言了错误行为、mock 与真实 schema 不一致、旧设计已经不支持目标能力。

审查顺序：

1. 重写当前事实：已尝试的修复、每次失败信号、哪些假设被证伪。
2. 对比同仓库可工作的模式，确认当前模式是不是例外、历史债务或错误抽象。
3. 检查测试/验收是否在证明真实目标，还是在固化旧实现、mock 或错误前提。
4. 标出架构选择：继续小修、局部重构、回滚/换路径、拆分任务或向用户确认目标。
5. 得到新的架构判断或用户确认前，不做第 4 个局部修复。

最终汇报不要只写“还没修好”；要写清：

- Tried:
- Falsified assumptions:
- Architecture / premise question:
- Recommended next move:

## Incident Mitigation

生产事故或高压线上问题可以先止血，但止血不是根因修复。处理时分两层说清：

| 层级 | 目标 | 必须留下的证据 |
| --- | --- | --- |
| Mitigation | 降低影响、恢复服务、隔离风险、保留回滚路径 | 影响范围、触发信号、采取动作、为什么可逆、回滚条件、当前业务状态 |
| Root cause fix | 找到坏值/坏状态/坏边界的来源，并让同类问题不再复发 | 根因链路、最小复现、相似实现对比、回归测试或等价验收 |

应急规则：

- 可以先做 feature flag、回滚、限流、禁用入口、临时降级或重启这类可逆动作，但要说成 mitigation。
- 做 mitigation 前尽量保留最低诊断证据：错误样本、时间线、版本/配置、关键日志、受影响范围。
- mitigation 后不要关闭 bug；继续执行 root cause tracing，直到能解释为什么发生、为什么 mitigation 有效、正式修复如何防复发。
- 如果只能确认外部依赖或环境故障，记录已排查边界、当前证据、监控/告警和后续观测条件。
- 最终汇报必须分开写 `Mitigation`、`Root Cause`、`Fix`、`Verification`；缺根因时明确写“止血完成，根因未闭环”。

## No Root Cause Evidence Gate

有些问题经过系统排查后仍只能暂时归因为环境、外部依赖、时序窗口或观测不足。可以这样收口，但必须把“不知道”说清楚；不要把猜测包装成 root cause。

允许标记为 `root cause unresolved` 的条件：

- 已读完整错误、日志、栈、最近 diff 或配置变更。
- 已尝试稳定复现或说明为什么无法复现。
- 已沿关键数据流或组件边界排查到可观测断点。
- 已对比同仓库可工作的相似路径或外部依赖状态。
- 已列出被证伪的假设和仍未知的边界。

收口格式：

```markdown
## Root Cause Status

- Status: root cause unresolved / likely external / likely environmental / timing-dependent
- Investigated:
- Falsified:
- Current evidence:
- Temporary handling:
- Monitoring / next observation:
- Next trigger to reopen:
```

如果需要加 retry、timeout、降级、错误提示或额外 logging，它们属于 handling 或 mitigation；只有能解释原始坏值、坏状态或坏边界为什么出现时，才能升级为 root cause fix。

## Defense In Depth

多层防御用于让同类 bug 更难复发，不是把责任推给最外层。

| 层级 | 目的 | 例子 |
| --- | --- | --- |
| Entry validation | 拒绝明显无效输入 | 空路径、缺字段、越权 scope、非法状态 |
| Domain validation | 确保操作语义成立 | 状态转换、owner 匹配、资源关系存在 |
| Environment guard | 阻止特定环境的危险副作用 | 测试中禁止写源码目录、生产禁用 fixture |
| Dangerous operation guard | 在破坏性或不可逆操作前再确认 | 删除、迁移、外部写入、进程 kill |
| Diagnostic trail | 失败后能快速定位 | trace id、关键参数、调用栈、上下文摘要 |

添加多层防御前先确认它们防的是不同绕行路径。重复的同层校验会制造噪音，不算真正的 defense in depth。

## Stop Rules

- 连续 3 次修复尝试没有改变失败信号：停止改代码，回到根因追踪。
- 第 3 次局部修复仍失败：进入 `Repeated Failure Architecture Gate`，不要尝试第 4 个局部补丁。
- 只能让错误消失但不能证明目标成功：标记为未完成，不称为修复。
- 只完成 mitigation 但未定位根因：标记为止血，不称为 root cause fixed。
- 诊断需要跨模块或跨服务时：先证明断点所在边界，再决定是否扩大修复范围。
- 发现相邻 bug：记录并说明，不顺手扩大本轮修复，除非它阻塞目标验证。
