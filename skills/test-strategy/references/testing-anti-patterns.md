# Testing Anti-Patterns

日期：2026-06-11
来源：吸收外部 TDD 和 testing anti-patterns 机制后整理。
用途：作为 `test-strategy` 的按需参考；防止测试只证明 mock、fixture、错误态或 no-op。

## Core Rule

测试必须证明目标行为成功，并且能在目标行为缺失时失败。覆盖率、mock 调用次数、页面能打开和错误态暴露都不能单独证明业务成功。

## Anti-Pattern Matrix

| 反模式 | 风险信号 | 更好做法 | 证据 |
| --- | --- | --- | --- |
| 断言 mock 存在 | spy/stub 被调用即通过 | 断言真实用户可见结果、状态、副作用 | mock 只隔离慢/外部边界 |
| 为测试改生产 API | cleanup/debug getter 只给 test 用 | test utility、fixture factory、harness | 生产调用者不需要 |
| 没理解依赖就 mock | 高层服务全 mock | 先跑真实路径，再 mock 外部边界 | 保留被测路径真实副作用 |
| fixture 不完整 | 只填当前断言字段 | 对齐真实 schema / 生成类型 | 下游字段被覆盖 |
| 补测无 RED | 测试第一次就是绿 | 临时回退/关闭修复，看目标缺失失败 | red/green 或等价 proof |
| 探索代码污染 | 围绕已写实现补断言 | 隔离探索，先写目标行为测试 | diff 无草稿/flag/debug |
| 无基线重构 | 声称只重构但无对照 | characterization / smoke 先捕获行为 | 前后同验证一致 |
| setup 大于逻辑 | mock 不断补洞 | 更真实 integration 或更窄单元 | 测试主体表达业务 |
| sleep 掩盖异步 | setTimeout、扩大 timeout | 等事件、状态、计数、文件、UI、API | fresh read、timeout、polling |
| 只验证错误出现 | 看到错误、loading 消失、请求发出 | 验证最终业务状态或持久化结果 | 错误暴露和业务成功分开 |

## Mock Decision Gate

添加 mock 前问：

1. 真实依赖副作用是什么？
2. 当前测试是否依赖这些副作用？
3. 能否 mock 更低层慢/外部边界，保留本模块真实行为？
4. mock 数据是否覆盖下游消费字段？
5. 去掉 mock 后，测试意图是否仍清楚？

任一不清楚，先用真实实现、fixture 或集成测试观察依赖链。

## Red / Green Proof

适用：bugfix、核心逻辑、接口契约、行为变化。

- RED：目标行为缺失时失败，且原因是业务缺口，不是语法、导入、fixture、mock 或命令 scope。
- GREEN：实现后同一测试通过。
- REGRESSION：旧行为、负向输入、权限边界没有被误改。
- SCOPE：没有为测试便利扩生产 API、权限绕行、fallback 或假数据。

实现后补测试时，用临时回退/关闭修复做等价 proof。

## RED Failure Reason

| RED output | Meaning | Action |
| --- | --- | --- |
| 目标结果缺失、状态未变、权限未拒绝、下游事实不存在 | 有效 RED | 进入最小实现 |
| 语法、导入、类型生成、测试路径错误 | 测试环境失败 | 修测试表达后重跑 RED |
| fixture/schema/mock 字段缺失 | 测试数据失败 | 对齐真实 schema |
| 命令跑错、0 tests、全 skipped | 没验证目标 | 修命令 scope |
| 只看到错误消息 | 只证明错误暴露 | 补业务成功/失败信号 |

不能解释“为什么这个失败证明目标缺失”时，不进入 GREEN。

## TDD Exception Gate

| Exception | Acceptable when | Required evidence |
| --- | --- | --- |
| 文档、prompt、规则文字 | 不改变运行行为 | diff review、链接/引用、格式检查 |
| 配置或生成代码 | 事实源清楚、生成可复现 | 生成命令、source diff、consumer smoke |
| 低风险样式/文案 | 不影响状态、权限、数据、流程 | 视觉/manual smoke、截图或 DOM 检查 |
| 探索/prototype | 用户接受非正式实现 | 隔离路径、正式化条件 |
| 测试框架不可用 | 仓库没有可运行自动化或工具缺失 | 手动步骤、输入输出、缺口 |

无效豁免：太简单、已手动试过、之后补、情况特殊、测试拖慢、旧代码没测。

## Async Wait Discipline

等待真实目标条件：

- event：目标事件类型或 id。
- state：状态机 expected state。
- count：队列、记录或 pending 数。
- artifact：文件/缓存/产物存在且内容符合。
- UI：关键元素可见、按钮可用、错误 overlay 不存在。
- API：后端查询证明状态完成。

helper 要有 fresh read、timeout、polling interval 和失败诊断。固定时间只允许测试真实 timing 机制，并说明来源。

## Testability Feedback

| 卡住信号 | 设计反馈 | 下一步 |
| --- | --- | --- |
| 不知道怎么测 | 行为或 API 未定义 | 先写期望调用和断言 |
| 测试名多个 “and” | 一个测试覆盖多个行为 | 拆小 RED |
| setup 很长 | 上下文边界过重 | fixture factory 或拆模块 |
| mock 很多层 | 耦合过深 | 下沉外部边界或注入窄端口 |
| RED 不是业务缺口 | 测环境/fixture/mock | 修测试表达 |
| GREEN 要大实现 | 测试跨度太大 | 写更小 RED |

不能因为测试难写就跳过，也不能把测试专用 API 加进生产路径。

## Exploration Contamination

已有探索代码时：

1. LEARN：提取行为不变量、输入输出、边界。
2. ISOLATE：草稿、临时回退、off switch 或最小 diff 证明缺失。
3. RED：目标行为测试因缺失失败。
4. GREEN：最小正式实现，不围着旧实现补断言。
5. CLEAN：草稿、debug、flag、fallback、测试专用路径不进最终 diff。

不能隔离时，不声称 TDD；写明等价 proof 缺口和风险。

## Refactor Proof

不改变行为的重构需要：

- BASELINE：现有测试、characterization、API/UI smoke 或人工步骤捕获当前行为。
- CHANGE：只改结构、命名、分层、依赖方向或重复逻辑。
- SAME：重构后同一验证仍通过。
- DRIFT：发现必须改行为时，停止叫 refactor，改按行为变化补测试/验收。

## Completion Checklist

- 测试名描述行为，不描述实现。
- 至少一个测试能在目标缺失时失败。
- 补测有 red/green 或等价 proof。
- 重构有前后基线。
- mock 靠近慢/外部依赖，不覆盖被测逻辑。
- fixture 与真实 schema/契约一致。
- 负向测试覆盖权限、非法输入、旧入口、unsupported、fallback。
- 最终验证区分错误暴露、链路打通和业务成功。
