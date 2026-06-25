# Parallel Agent Coordination

日期：2026-06-11
来源：吸收 Superpowers `dispatching-parallel-agents` 和 `subagent-driven-development` 的并行协作机制后整理。
用途：作为 `task-execution-lifecycle` 的按需参考；用于判断什么时候并行、如何写自包含任务、如何合并结果并防止 agent 互相污染。

## Core Rule

并行只用于独立问题域。共享文件、共享状态、顺序依赖、同一根因或需要统一架构判断的任务，默认串行。

## Parallel Decision Gate

先回答：

1. 是否有 2 个以上明确问题域？
2. 每个问题域是否能独立理解、独立验证？
3. 是否没有共享写入文件、共享数据库状态、共享进程或同一测试资源？
4. 一个任务的修复是否不会改变另一个任务的前提？
5. 是否能为每个 agent 提供完整上下文，而不是让它读取主会话历史？

任一答案是否定时，不并行；先串行定位根因或拆小边界。

## Capability Selection Gate

选择 agent、模型、工具或 review 方式时，目标是能力匹配任务，而不是默认用最重资源，也不是为了省资源牺牲判断质量。

| 任务类型 | 默认能力 | 升级信号 |
| --- | --- | --- |
| 机械小改、单文件、完整 spec、无共享接口 | 轻量执行者或本地工具 | 测试失败无法解释、涉及隐藏依赖 |
| 多文件集成、调试、模式对比、跨模块调用 | 标准执行/调研能力 | 需要架构判断、权限/数据语义、长期演进 |
| 架构、协议、安全、权限、数据迁移、代码 review、最终整合 review | 强判断能力或 senior review 视角 | 仍有相互矛盾约束、用户多次纠偏、验证无法闭环 |
| 只读调研或资料整理 | 按来源拆分的轻量/标准调研能力 | 来源冲突、事实时效性高、需要最终取舍 |

使用规则：

- 任务越清晰、越孤立、越可测试，越可以使用轻量能力。
- 任务涉及 owner、权限、安全、数据一致性、公共接口、runtime、tool gateway 或跨 agent 合约时，不降级判断能力。
- review、spec compliance 和最终整合 review 默认需要比机械实现更强的判断能力；不能让实现者自审替代独立 review。
- 平台支持显式选择模型、agent、工具或 review 方式时，brief 里写出本任务需要的能力级别；平台不支持时，用“是否派 subagent / 是否加 senior checklist / 是否缩小 scope / 是否串行”等等价方式表达。
- 如果子 agent/tool 返回 `BLOCKED` 且 blocker 是推理能力不足，升级能力；如果 blocker 是任务太大，先拆任务；如果 blocker 是上下文不足，补 brief。
- 当前平台没有显式模型选择能力时，把本规则映射为：选择更合适的工具、是否派 subagent、是否增加 senior review checklist、是否缩小 scope 或改成串行。

最终汇报无需暴露内部模型选择细节，但应说明关键高风险判断是否经过足够能力的复核。

## Good Parallel Domains

适合并行：

- 多个失败测试文件，错误栈和负责模块不同。
- 多个子系统各自有独立 owner、入口和验证命令。
- 调研型任务可以分来源并行，例如官方文档、竞品、项目历史。
- UI、API、测试分别只读审查，最终由主 Agent 汇总。

不适合并行：

- 多个失败很可能同一根因。
- 多个 agent 需要编辑同一个文件或同一 schema。
- 需要先确定统一架构、协议、字段或 owner。
- 任务依赖同一个本地服务、临时端口、数据库 fixture 或全局缓存。
- 用户刚纠偏了范围或优先级，当前不变量尚未冻结。

## Task Brief Pattern

每个并行任务都要自包含：

```markdown
## Scope

只处理：<文件/模块/问题域>
不要修改：<明确禁止范围>

## Context

- 目标：
- 已知错误/需求：
- 相关文件：
- 相关命令：
- 当前假设：
- Global constraints：
- Consumes：
- Produces：
- Capability / tool level：

## Expected Work

1. 读取指定文件和必要调用点。
2. 定位根因或完成指定审查。
3. 只在 scope 内提出或执行改动。
4. 记录验证信号和残余风险。

## Return

- Root cause / finding:
- Files changed or inspected:
- Verification:
- Risks / follow-up:
```

不要让子 agent “自己去看计划全文”或“凭上下文理解”。主 Agent 负责裁剪上下文。

长任务或需要 review 的任务，优先把 task brief、关键 diff、验证输出或 review package 放入文件，再把路径交给执行者/reviewer。直接粘贴长 diff 容易淹没上下文，也会让后续 reviewer 只能凭记忆重建事实。

## Coordination Rules

- 主 Agent 保留 owner：拆任务、分发、回答问题、合并结果、最终验证。
- 用户明确要求“启一个子 agent”“分工”“并行处理”时，先识别可独立推进的清理、审查、调研或验证任务并派发；主 Agent 不在原地等待非关键路径子任务，而是继续推进当前最重要的交付链路。
- 子 agent 不继承主会话隐含约束；关键约束必须写进 brief。
- 并行任务返回后，先看文件重叠，再看行为是否冲突。
- spec compliance 先于 code quality；一次 review 可以同时返回 spec 与 quality verdict，但 spec verdict 不通过时不能用“代码写得好”掩盖任务做错。
- 子 agent 自测不能替代主 Agent 的最终 diff 审查和整体验证。

## Delegated Task Boundary Gate

被派发执行明确子任务时，不重新运行全局任务发现、全仓规划或 Hub 方法论吸收流程。子 agent / 子任务执行者只做 brief 指定的 scope，并按需要读取与该 scope 直接相关的 Skill。

| Signal | Correct action |
| --- | --- |
| brief 已给定明确文件、模块、问题域或验证命令 | 按 brief 执行，不扩写成全仓审计 |
| 发现 brief 缺关键上下文或与用户硬边界冲突 | 返回 `NEEDS_CONTEXT` 或 `BLOCKED`，不要猜测主会话 |
| 发现 scope 外但可能相关的问题 | 记录为 concern/follow-up，不直接修改 |
| 需要读取 Skill | 只读取当前子任务必需的 Skill；不触发完整 Hub loading chain |
| 任务要求 review 或实现 | 仍按该任务的 compliance mode 执行；不能用“我是子 agent”跳过证据和 stop rule |

主 Agent 负责全局技能选择、任务拆分、跨任务一致性和最终验证；委派执行者负责局部证据和清晰回执。

## Delegated Question Gate

子 agent / 工具执行者提出问题时，不把它当成噪音或拖延。问题通常说明 brief 缺上下文、计划有歧义、scope 边界冲突或任务粒度过大；主 Agent 必须先分类处理，再让执行继续。

| Question type | 主 Agent 动作 |
| --- | --- |
| 缺背景、文件、命令或验收信号 | 补充具体上下文，重派同一任务 |
| scope、owner、权限或数据源不清 | 回到主 Agent 判断；必要时问用户一个最小阻塞问题 |
| 计划/spec 自相矛盾或漏项 | 修正计划/brief，再重派；不要让子 agent 猜 |
| 任务过大或含多个独立问题 | 拆成更小 brief，再分派 |
| 只是实现细节选择且不影响目标 | 给出约束内的默认决策，并记录假设 |

禁止动作：

- 催促子 agent “先做再说”。
- 用主会话隐含记忆回答含糊问题，而不写进新的 brief。
- 子 agent 问题暴露计划缺口时，继续按旧 plan 执行。
- 把问题直接转发给用户一长串；只问会改变目标、scope、owner、数据源或验收标准的最小问题。

## Subagent Status Gate

子 agent 返回状态时先分类处理，不把 summary 里的 “done” 自动当作完成：

| 状态 | 主 Agent 动作 |
| --- | --- |
| `DONE` | 进入 spec compliance 或最小复核；仍要看 diff、文件和验证证据 |
| `DONE_WITH_CONCERNS` | 先读 concerns；若涉及正确性、范围或验证，先处理再 review；若只是观察，记录为风险或 follow-up |
| `NEEDS_CONTEXT` | 补充缺失背景、约束、文件、命令或验收信号后再派发；不要让子 agent 猜主会话隐含信息 |
| `BLOCKED` | 判断 blocker 类型：缺上下文则补上下文，任务过大则拆小，能力不足则换更合适工具/模型，计划错误则回到主 Agent 修计划或问用户 |

不要让同一个失败 brief 原样重试。每次重派都必须改变至少一项：上下文、scope、模型/工具、任务拆分、验证入口或目标假设。

## Review Loop Gate

对 subagent 实现结果使用闭环，不用一次 review 代替完成：

1. Spec compliance review 先于 code quality review；同一个 reviewer 可以一次返回两个 verdict，但行动顺序仍是先处理 spec。
2. 规格缺口、越界实现或误解需求存在时，先让实现修正并重新做 spec review。
3. 只有 spec review 通过后，才进入代码质量、可维护性和体验质量审查。
4. reviewer 返回 `can't verify from diff` 或等价结论时，主 Agent 负责补 runtime、调用点、测试或 source evidence；不能把它当成 pass，也不能机械当成 fail。
5. 质量 review 发现 Critical/Important 时，修复后必须 re-review；不能把 reviewer 发现的问题标成“后续再说”后继续下一任务。
6. 主 Agent 负责最终统一验证；子 agent 的 self-review 和测试输出只是输入证据。

## Final Integration Review Gate

每个任务局部通过，不等于整体完成。多任务、并行/subagent 或分阶段计划全部结束后，先做最终整合 review，再进入交付、commit、PR 或分支收尾。

检查项：

- Cross-task consistency：接口、类型、命名、状态、错误语义、UI 交互、配置和数据结构在各任务之间是否一致。
- Duplicate or conflicting fixes：多个 agent 是否修了同一问题、重复加了 fallback、产生相反假设或留下两套路由/入口。
- Scope drift：是否有任务引入未要求能力、测试专用生产路径、临时 debug、mock/fallback 或兼容分支。
- Whole-flow validation：局部测试通过后，是否还需要端到端 smoke、集成测试、页面 workflow 或 API/tool runtime 链路验证。
- Diff boundary：最终文件清单是否只包含本轮必要实现、测试、文档和生成产物；是否混入工具缓存、临时文件或无关用户改动。

输出至少说明：

```markdown
## Final Integration Review

Spec: pass | issues
Cross-task consistency: pass | issues
Whole-flow validation: <commands/signals>
Diff boundary: <staged/unstaged/untracked summary>
Residual risk:
```

若最终整合 review 发现 Critical/Important 问题，先修复并重新做对应层级 review；不能因为每个子任务此前已经 approved 就跳过。

## Merge And Review

结果回来后按顺序处理：

1. 列出每个 agent 的 scope、结论、改动文件、验证命令。
2. 检查是否有同文件改动、相反假设、重复修复或接口不一致。
3. 对每个结论做最小复核：看关键 diff、调用点、失败信号是否真的解释了问题。
4. 运行最窄整体验证；若多个修复可能交互，扩大到集成或 full suite。
5. 更新最终报告：哪些并行结果采纳、调整、拒绝及原因。

## Red Flags

- “并行会更快”但没有独立域证据。
- 多个 agent 被要求改同一公共类型、schema、配置或生成产物。
- 子 agent 收到窄 brief 后重新做全局规划、扩写 scope 或加载无关 Skill。
- 子 agent 只返回“已修复”但没有根因、文件和验证。
- 主 Agent 未读 diff 就采纳子 agent 结论。
- 子 agent 发现 blocker，主 Agent 让它原样重试。
- 子 agent 返回 concerns，但主 Agent 直接进入下一任务。
- spec review 未通过就开始 code quality polish。
- 并行调研的来源质量不同，却被合并成同等事实。

## Verification

最终汇报至少包含：

- 并行拆分依据。
- 每个任务的 scope 和结果。
- 是否存在文件/状态冲突。
- 主 Agent 复核了哪些证据。
- 统一运行的验证命令或无法统一验证的原因。
