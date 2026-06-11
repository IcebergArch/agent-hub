# Testing Anti-Patterns

日期：2026-06-11
来源：吸收 Superpowers `test-driven-development/testing-anti-patterns.md` 和 TDD skill 的测试先行规则后整理。
用途：作为 `test-strategy` 的按需参考；当测试可能只证明 mock、fixture 或错误态时，用它做误报防线。

## Core Rule

测试必须证明目标行为成功，并且能在目标行为缺失时失败。覆盖率、mock 调用次数、页面入口打开和错误态暴露都不能单独证明业务行为成功。

## Anti-Pattern Matrix

| 反模式 | 风险信号 | 更好的做法 | 验证证据 |
| --- | --- | --- | --- |
| 断言 mock 存在 | 断言 `*-mock`、spy 被调用、stub 文案存在 | 断言真实用户可见行为、状态变化、输出或副作用 | 去掉 mock 后测试仍能表达目标；或 mock 只隔离外部慢边界 |
| 为测试给生产类加方法 | 新增 API 只在 test 中调用，例如 cleanup/destroy/debug getter | 放入 test utility、fixture factory 或测试 harness | 生产入口、文档和真实调用点不需要该 API |
| 没理解依赖就 mock | “为了快/保险” mock 高层服务 | 先跑真实路径或读副作用，再只 mock 慢、外部、非确定边界 | mock 保留被测路径依赖的真实副作用 |
| mock 结构不完整 | fixture 只填当前断言字段 | 按真实 schema、官方示例或生成类型构造完整对象 | 下游会消费的字段都存在，类型检查或契约测试能覆盖 |
| 实现后补测试但没证明会失败 | 测试第一次运行就是绿的 | 临时回退/关闭修复，确认测试因目标缺失失败，再恢复修复 | 记录 red/green 命令或等价手动验证 |
| 探索代码污染测试设计 | 先写出生产实现，再围绕它补一个会立即变绿的测试；把旧实现当“参考”边看边写测试 | 把探索代码隔离、回退或标记为草稿；先写目标行为测试并看它因缺失行为失败，再重写或恢复最小实现 | 记录探索结论、RED 失败原因、GREEN 通过命令和未把草稿实现扩成生产路径的 diff |
| 无基线重构 | 声称只重构，但没有现有测试、characterization test 或前后行为对照 | 先捕获当前行为，再做结构调整；重构后同一验证保持通过 | 前后同一测试/脚本/手动 smoke 输出一致 |
| 复杂 mock 大于被测逻辑 | setup 很长、mock 方法不断补洞 | 改用更真实的 integration test 或更窄的单元边界 | 测试主体清楚表达业务，而不是维护假世界 |
| 用 sleep 掩盖异步不确定性 | `setTimeout`、`sleep`、扩大 timeout、CI 偶现失败 | 等事件、状态、计数、文件、UI 可见性或 API 查询结果等真实条件；只有测试 timing 行为时才写固定等待并说明机制 | wait 条件有 fresh read、timeout、polling interval 和失败诊断；flaky 修复后有重复或并行验证 |
| 只验证错误出现 | 测试只看到了错误消息、loading 消失或请求发出 | 同时验证最终状态、持久化结果、UI 可见结果或下游事实 | “失败被暴露”和“业务成功”分开记录 |

## Mock Decision Gate

添加 mock 前先回答：

1. 真实依赖的副作用是什么？
2. 当前测试是否依赖这些副作用？
3. 是否可以 mock 更低层的慢/外部边界，而保留本模块真实行为？
4. mock 数据是否覆盖真实 schema 中会被下游消费的字段？
5. 如果 mock 去掉，测试想验证的行为是否仍然清楚？

如果任一答案不清楚，先用真实实现、fixture 或集成测试跑一次，观察实际依赖链，再决定 mock 边界。

## Red/Green Proof

对 bugfix、核心逻辑、接口契约和行为变化，优先保留以下证据：

- RED：目标行为缺失时，测试失败，且失败原因是预期的业务缺口，不是语法、导入或 fixture 错误。
- GREEN：实现后同一测试通过。
- REGRESSION：相关旧行为、负向输入或权限边界没有被误改。
- SCOPE：没有为了测试便利把生产 API、权限绕行、fallback 或假数据扩进真实路径。

如果测试是实现后补的，使用等价 proof：临时回退/关闭本轮修复，确认测试失败；恢复修复后通过。

## Red Failure Reason Gate

测试红了只说明“有东西失败”，不自动说明测试有效。进入 GREEN 前，先确认 RED 失败原因和目标行为缺失一致。

| RED output | Meaning | Action |
| --- | --- | --- |
| 断言目标结果缺失、状态未改变、权限未拒绝、下游事实不存在 | 有效 RED | 进入最小实现 |
| 语法错误、导入错误、类型生成缺失、测试文件路径错误 | 测试环境失败 | 修测试表达或 setup 后重新 RED |
| fixture/schema/mock 字段缺失导致失败 | 测试数据失败 | 对齐真实 schema 或减少 mock，再重新 RED |
| 命令跑错包、过滤条件没匹配、0 tests、全 skipped | 没有验证目标 | 修命令 scope，再重新 RED |
| 错误消息出现但目标业务状态未检查 | 只证明错误暴露 | 补成功/失败业务信号，再重新 RED |

RED 记录要包含：

```markdown
Command:
Expected missing behavior:
Actual failure:
Why this failure proves the target gap:
```

不能解释“为什么这个失败证明目标缺失”时，不进入 GREEN。

## TDD Exception Gate

Superpowers 的强规则是“production code 必须先有失败测试”。Hub 采用这个不变量，但按任务风险允许少量等价验证；例外必须先分类，不能靠临场理由逃过测试。

| Claimed exception | Acceptable when | Required evidence |
| --- | --- | --- |
| 文档、说明、prompt、纯规则文字 | 不改变运行行为 | diff review、链接/引用搜索、格式检查 |
| 配置或生成代码 | 事实源清楚，生成链路可复现 | 生成命令、schema/source diff、消费者 smoke |
| 低风险样式或文案 | 不影响状态、权限、数据或交互流程 | 视觉/manual smoke、截图或 DOM 检查 |
| 一次性探索/prototype | 用户明确接受不作为正式实现 | 标记探索范围、隔离路径、后续正式化条件 |
| 测试框架不可用 | 当前仓库没有可运行自动化或工具缺失 | 手动步骤、输入输出、缺口和后续补测条件 |

无效豁免信号：

- “太简单，不值得测”
- “我已经手动试过”
- “先做完，之后补测试”
- “这次情况特殊”
- “测试会拖慢速度”
- “已有代码没有测试”

行为变化、bugfix、接口契约、安全/权限、持久化、runtime/tool 调度和关键 UI workflow 默认不接受口头豁免。若确实不能自动化，至少要写出等价验证：目标、步骤、预期输出、失败信号、未覆盖风险。

## Async Wait Discipline

异步测试要等目标条件，不等猜测时间。看到 `sleep`、`setTimeout`、扩大 timeout、重跑才过或并行才失败时，先判断测试真正等待的业务信号是什么。

更好的等待目标：

- event：目标事件类型或 id 出现。
- state：状态机进入 expected state。
- count：队列、事件、记录或 pending 数量达到预期。
- artifact：文件、缓存、生成物存在且内容符合预期。
- UI：关键元素可见、按钮可用、错误 overlay 不存在。
- API：后端查询能证明目标状态完成。

等待 helper 必须包含：

- fresh read：循环内重新读取状态。
- timeout：失败信息说明正在等什么和已经观察到什么。
- polling interval：既不忙等，也不拖慢测试。

固定时间等待只允许用于测试真实 timing 机制；先等待触发条件，再说明等待值来自 debounce、throttle、tick、retry interval 或 protocol deadline。修复 flaky 后，优先用重复运行、并行运行或 CI 等价命令证明不再依赖偶然时序。

## Testability Feedback Gate

测试卡住通常不是“测试太烦”，而是设计在发信号。不要用更多 mock、fixture 或 test-only API 硬压过去；先判断是否需要缩小行为、改善接口或拆开依赖。

| 卡住信号 | 设计反馈 | 下一步 |
| --- | --- | --- |
| 不知道怎么写测试 | 目标行为或 API 还没定义清楚 | 先写期望调用方式和最终断言，再倒推接口 |
| 测试名称里出现多个 “and” | 一个测试覆盖多个行为 | 拆成更小行为，每次只驱动一个变化 |
| setup 比断言长很多 | 对象创建或上下文边界过重 | 抽 fixture factory；仍复杂时重新切分模块边界 |
| 必须 mock 很多层 | 代码耦合过深，依赖方向不利于验证 | 把慢/外部边界下沉或用 dependency injection 暴露窄端口 |
| RED 失败原因不是目标缺失 | 测试在测环境、导入、fixture 或 mock，而不是行为 | 先修测试表达，直到失败原因对准业务缺口 |
| GREEN 需要一次写很多实现 | 测试跨度太大或设计还没分层 | 写更小的 RED；先实现可观察的最小行为 |

恢复顺序：

1. 写一句目标行为：输入、动作、可观察结果。
2. 写理想 API 的一行调用，再写断言。
3. 如果需要大量 setup，抽测试 fixture；如果 fixture 仍压不住复杂度，调整生产接口或依赖边界。
4. 如果需要 mock 多层内部实现，改为 mock 最慢/最外部边界，或把依赖注入成窄端口。
5. 重新跑 RED，确认失败原因是目标行为缺失，再进入 GREEN。

不能因为测试难写就跳过测试；也不能为了让测试好写而把测试专用 API 加进生产路径。

## Exploration Contamination Gate

探索可以先发生，正式实现不能被探索代码偷偷决定。

当已经先写了生产代码、手动修了 bug、或从外部建议粘出一段实现时，先分层处理：

- LEARN：从探索代码里提取行为不变量、输入输出、边界条件和风险。
- ISOLATE：把探索实现隔离为草稿、临时回退、feature flag/off switch，或用最小 diff 证明测试前目标行为缺失。
- RED：写目标行为测试，确认失败原因是缺失行为，不是导入、fixture、语法或 mock 错误。
- GREEN：在不围着旧实现补断言的前提下写最小正式实现；必要时只复用已验证的接口和不变量。
- CLEAN：确认草稿、临时 flag、debug 入口、fallback 和测试专用生产路径没有混进最终 diff。

不能回退或隔离时，不要声称完成了 TDD；把验证写成“实现后补测 + 等价 proof 缺口”，并说明为什么当前风险可接受或需要用户确认。

## Refactor Proof

对不应改变行为的重构，先证明基线，再动结构：

- BASELINE：现有测试、characterization test、API smoke、UI smoke 或人工步骤能捕获当前行为。
- CHANGE：只调整结构、命名、分层、依赖方向或重复逻辑；不夹带功能、默认值、权限、排序或错误态变化。
- SAME：重构后同一验证仍通过，输出、状态、错误语义和用户可见行为保持一致。
- DRIFT：如果重构中发现必须改变行为，停止把它叫 refactor，改成行为变更并补 red/green 或验收测试。

没有基线时，“看起来只是移动代码”不能证明无行为变化。

## Test-Only Code Boundary

允许放在测试边界：

- fixture factory
- cleanup helper
- fake server / fake clock / fake storage
- deterministic data builder
- test-only assertion helper

不应放进生产对象：

- 只服务测试的 lifecycle 方法
- 绕过权限、缓存、状态机或真实校验的 getter/setter
- 为断言内部状态暴露的 debug API
- 只为让 mock 好写而改变的生产接口

例外必须有真实产品需求、生产调用者和测试以外的 owner；否则应留在 test utility。

## Completion Checklist

结束前检查：

- 测试名称描述行为，不描述实现细节。
- 至少一个测试能在目标缺失时失败。
- 如果先有探索/实现代码，已经隔离、临时回退或明确记录等价 proof 缺口。
- 重构类改动有前后行为基线或明确说明无法自动证明的等价验证。
- mock 边界靠近慢/外部依赖，不覆盖被测逻辑。
- fixture 与真实 schema 或契约一致。
- 负向测试覆盖缺权限、非法输入、旧入口、unsupported 或 fallback。
- 最终验证区分了错误暴露、链路打通和业务成功。
