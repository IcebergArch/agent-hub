# Debugging Patterns

日期：2026-06-11
来源：吸收外部调试方法论中的 root-cause tracing、condition-based waiting、defense-in-depth 和 polluter finder 机制后整理。
用途：作为 `bug-reproduction` 的按需参考；用于复杂 bug、flaky、timeout、污染源、线上止血和多次失败后的根因收敛。

## Core Rule

先找坏值、坏状态或坏边界首次出现的位置，再修复。错误出现处通常只是结果，不是根因。

## Pattern Matrix

| 场景 | 先做 | 证据 | 禁止 |
| --- | --- | --- | --- |
| 深层调用栈报错 | 从报错点向上追调用者、参数来源、状态变更 | 坏值首次出现位置 | 在底层吞错或加 fallback |
| 有可工作的相似实现 | 完整读参考入口、配置、状态和测试 | working/broken 差异表 | 只复制相似函数 |
| 多次假设失败 | 建轻量假设账本 | confirmed / falsified / inconclusive | 继续叠补丁 |
| flaky / timeout / hang | 等真实业务条件 | fresh read、timeout、polling | 加 sleep 或拉长 timeout |
| 环境污染 | 先找污染产生者 | 最小污染命令、第一污染 case | 只清理不追源头 |
| 入口校验可绕过 | 画数据流并分层防御 | entry/domain/env/danger guard | 只修 UI 或 handler |
| 线上事故 | 分开 mitigation 和 root cause fix | 影响范围、止血动作、回滚条件 | 把重启/降级称为修复 |
| 仍无根因 | 明确 unresolved | 已查边界、证伪假设、后续观测 | 编造 likely root cause |

## Root Cause Trace

1. 写下表面症状：错误文本、失败断言、页面状态、请求响应或污染物。
2. 找直接失败点：哪一行、哪次调用、哪个边界产生失败。
3. 向上追输入、调用者、配置、状态变化和外部依赖。
4. 找坏值首次出现的位置；只知道坏值在哪里被使用还不够。
5. 用最小证据确认：单测、临时日志、断言、stack、trace 或定向复现。
6. 在源头修复；危险边界再加防御。

追问：

- 这个值第一次变坏在哪里？
- 失败路径和同仓库成功路径差在哪个条件？
- 当前修复只在报错点生效时，是否还有其它入口能绕过？

## Reference Comparison Gate

附近有相似实现、旧版本、官方示例或成功测试时，先做差异比较：

| Difference | Working | Broken | Class | Evidence / next test |
| --- | --- | --- | --- | --- |
| `<condition>` | `<behavior>` | `<difference>` | candidate / context / irrelevant / unknown | `<test/log/trace>` |

停止信号：

- 未读完整参考路径就开始改代码。
- 只说“参考用了 X”，没有解释 broken path 缺哪个前提。
- 一次搬运多个差异，无法知道哪个真正修复。

## Hypothesis Ledger

复杂 bug、偶发问题或两次假设失败后记录：

| Observation | Hypothesis | Test | Output | Result | Next |
| --- | --- | --- | --- | --- | --- |
| 事实、坏值、日志或复现条件 | 单一根因假设 | 最小验证动作 | 实际输出 | confirmed / falsified / inconclusive | 修复 / 换假设 / 补观测 |

规则：

- 一次只测一个变量。
- `falsified` 不能继续支撑修复。
- `inconclusive` 需要补观测或缩小复现，不是通过。

## Condition-Based Waiting

异步问题等业务信号，不等猜测时间。

| 等待目标 | 条件 |
| --- | --- |
| 事件处理 | 目标事件类型或 id 出现 |
| 状态机 | 状态进入 expected state |
| 批处理 | processed 达预期或 pending 归零 |
| 产物 | 文件存在且大小/内容/checksum 符合预期 |
| UI | 关键元素可见、按钮可用、错误 overlay 不存在 |
| API | 后端查询确认目标状态 |

等待 helper 必须包含 fresh read、timeout、polling interval 和失败诊断。固定等待只允许测试真实 timing 机制，例如 debounce、retry interval 或 protocol deadline。

## Polluter Finder

污染源包括文件、全局变量、数据库记录、缓存、端口、环境变量、mock 状态、定时器和后台进程。

1. 确认污染物在单独复现前不存在。
2. 找到能稳定产生污染的最小命令或测试集合。
3. 按文件、fixture、case 或步骤二分。
4. 每轮前检查污染物是否已存在。
5. 找到第一个污染者后，修复产生者；清理逻辑只作为补充。

## Repeated Failure Gate

第 3 次局部修复仍没有让失败信号变窄时，停止改代码，审查架构或前提。

输出：

```markdown
Tried:
Falsified assumptions:
Architecture / premise question:
Recommended next move:
```

## Mitigation vs Fix

| 层级 | 目标 | 必须留下 |
| --- | --- | --- |
| Mitigation | 降低影响、恢复服务、保留回滚 | 影响范围、触发信号、动作、回滚条件 |
| Root cause fix | 解释并修复坏值/坏状态/坏边界来源 | 根因链路、最小复现、回归测试或等价验收 |

只做 mitigation 时，最终写“止血完成，根因未闭环”。

## No Root Cause Closeout

允许 `root cause unresolved`，但必须说明：

- 已读错误、日志、栈、最近 diff 或配置。
- 已尝试复现或说明无法复现原因。
- 已排查关键数据流或组件边界。
- 已列出证伪假设和未知边界。
- 已给 monitoring / next observation / reopen trigger。

## Defense In Depth

| 层级 | 目的 |
| --- | --- |
| Entry validation | 拒绝明显无效输入 |
| Domain validation | 确保状态转换、owner、资源关系成立 |
| Environment guard | 阻止特定环境危险副作用 |
| Dangerous operation guard | 删除、迁移、外部写入前再确认 |
| Diagnostic trail | 留 trace id、关键参数和上下文 |

多层防御必须防不同绕行路径；重复同层校验只是噪音。
