# Parallel Agent Coordination

日期：2026-06-11
来源：吸收外部 parallel agents 和 subagent-driven development 机制后整理。
用途：作为 `task-execution-lifecycle` 的按需参考；判断何时并行、如何写自包含任务、如何合并结果并防止污染。

## Core Rule

并行只用于独立问题域。共享文件、共享状态、强顺序依赖、同一根因或需要统一架构判断的任务，默认串行。

## Parallel Decision Gate

任一答案为否，不并行：

1. 是否有 2 个以上明确问题域？
2. 每个问题域能否独立理解、独立验证？
3. 是否没有共享写入文件、数据库状态、进程、端口或全局缓存？
4. 一个任务的修复是否不会改变另一个任务前提？
5. 是否能为每个 agent 提供完整 brief，而不是依赖主会话历史？

## Capability Selection

| 任务类型 | 默认能力 | 升级信号 |
| --- | --- | --- |
| 机械小改、单文件、完整 spec | 轻量执行者或本地工具 | 测试失败无法解释、隐藏依赖 |
| 多文件集成、调试、模式对比 | 标准执行/调研 | 架构、权限、数据语义、长期演进 |
| 架构、协议、安全、权限、迁移、review | 强判断或 senior review | 约束冲突、用户多次纠偏、验证不闭环 |
| 只读调研 | 按来源拆分 | 来源冲突、事实时效高、需要取舍 |

如果 blocker 是能力不足，换能力；任务太大则拆；上下文不足则补 brief。

## Good / Bad Parallel Domains

适合：

- 多个失败测试文件，栈和模块不同。
- 子系统 owner、入口和验证命令独立。
- 调研可按官方文档、竞品、项目历史拆分。
- UI、API、测试只读审查后由主 Agent 汇总。

不适合：

- 多个失败很可能同根因。
- 多个 agent 编辑同一公共类型、schema、配置或生成物。
- 需要先统一架构、协议、字段或 owner。
- 依赖同一本地服务、临时端口、DB fixture 或全局缓存。
- 用户刚纠偏范围或优先级，不变量未冻结。

## Task Brief Pattern

```markdown
## Scope
只处理：
不要修改：

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
2. 定位根因或完成审查。
3. 只在 scope 内提出或执行改动。
4. 记录验证信号和风险。

## Return
- Root cause / finding:
- Files changed or inspected:
- Verification:
- Risks / follow-up:
```

长 diff、任务说明或 review package 优先文件化，再把路径交给执行者/reviewer。

## Coordination Rules

- 主 Agent 保留 owner：拆任务、分发、答疑、合并、最终验证。
- 子 agent 不继承主会话隐含约束；关键约束必须写进 brief。
- 子任务只做 brief scope，不重跑全局规划或 Hub 方法论吸收。
- scope 外问题记录为 concern/follow-up，不直接修改。
- spec compliance 先于 code quality。
- 子 agent 自测不能替代主 Agent 的最终 diff 审查和整体验证。

## Delegated Questions

| Question type | 主 Agent 动作 |
| --- | --- |
| 缺背景、文件、命令、验收 | 补具体上下文，重派 |
| scope、owner、权限、数据源不清 | 回主 Agent 判断，必要时问用户一个最小问题 |
| 计划/spec 矛盾或漏项 | 修正计划/brief，再重派 |
| 任务过大 | 拆成更小 brief |
| 不影响目标的实现细节 | 给默认决策并记录假设 |

不要催促“先做再说”，也不要把一长串子 agent 问题原样丢给用户。

## Subagent Status

| 状态 | 主 Agent 动作 |
| --- | --- |
| `DONE` | 最小复核：diff、文件、验证证据 |
| `DONE_WITH_CONCERNS` | 先读 concerns；涉及正确性/范围/验证则先处理 |
| `NEEDS_CONTEXT` | 补背景、约束、文件、命令或验收后重派 |
| `BLOCKED` | 分类：缺上下文、任务过大、能力不足、计划错误 |

同一个失败 brief 不能原样重试；必须改变上下文、scope、工具/能力、任务拆分或目标假设。

## Review Loop

1. Spec compliance 先于 code quality。
2. 规格缺口、越界实现或误解需求存在时，先修正并重审 spec。
3. spec 通过后才处理质量、可维护性和体验。
4. `can't verify from diff` 需要主 Agent 补 runtime、调用点、测试或 source evidence。
5. Critical/Important 修复后必须 re-review。
6. 主 Agent 做最终统一验证。

## Final Integration Review

多任务结束后检查：

- 整体需求是否完成。
- 接口、类型、命名、状态、错误语义、UI、配置、数据结构是否一致。
- 是否重复修复、相反假设、两套路由/入口、冲突 fallback。
- 是否需要端到端 smoke、集成测试、页面/API/tool runtime 验证。
- diff 是否混入工具缓存、临时文件或用户已有改动。

输出：

```markdown
## Final Integration Review
Spec: pass | issues
Cross-task consistency: pass | issues
Whole-flow validation:
Diff boundary:
Residual risk:
```

## Red Flags

- “并行会更快”但没有独立域证据。
- 子 agent 收到窄 brief 后扩成全仓规划。
- 子 agent 只回“已修复”，没有根因、文件和验证。
- 主 Agent 未读 diff 就采纳结论。
- concerns 未处理就进入下一任务。
- spec review 未通过就做 code quality polish。

## Final Report

至少说明：拆分依据、每个任务 scope 和结果、文件/状态冲突、主 Agent 复核证据、统一验证命令或无法统一验证原因。
