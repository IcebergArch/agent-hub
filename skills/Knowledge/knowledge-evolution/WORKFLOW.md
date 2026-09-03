---
name: knowledge-evolution
description: 当任务暴露可复用组织经验、Hub 自迭代、外部方法吸收、规则沉淀或 Skill/Prompt/Helper/Workflow/Agent/Tool 升级需求时使用。
---

# Purpose

判断经验是否值得沉淀、沉淀到哪里，以及如何保持知识库一致。

# When to Use

- 用户说 `/hub get`、`/hub update`、优化 Hub、升级 Skill Library。
- 外部 agent framework、插件、技能库或方法论需要吸收。
- 任务暴露跨场景稳定缺口：owner、接口、验证、prompt、helper、workflow、agent 或 tool。

# When NOT to Use

- 一次性项目事实、临时调试记录、普通代码实现、默认 bug/review/testing/docs 能力。
- 还没有证据表明问题会跨任务重复。

# Inputs

- 当前任务信号、相关入口、已有 Skill/Rule/Reference、用户明确偏好与纠偏、可验证证据和反例。

# Decision Principles

- 先判断是否值得沉淀，再判断沉淀位置。
- 不直接沉淀事故表象、当次修复答案或具体实体；候选经验必须先上提为能覆盖相邻场景的触发条件、不变量和证据门禁。无法稳定泛化的一次事故删除而非入库。
- 优先扩展已有 Skill，不新增近义 Skill。
- 一次性项目经验进 Context/Report/Temp，不进 Skill。
- Skill 保留长期组织经验；Prompt/Helper/Workflow/Agent/Tool 只在 owner 清楚时升级。
- 单次方案是带上下文的“术”；Hub 只沉淀跨场景可复用的“道”。设计指导思想必须表达触发条件、保护的不变量、因果机制、适用边界、权衡、反例和验证方式，不沉淀问题穷举或案例清单。
- 方案设计候选按周汇总：每份 plan 只登记候选，周复盘再合并、强化、观察或淘汰；只有通过长期性、跨场景和证据门禁的内容才更新 Hub。
- 进入本 workflow 后，偏好更新先判作用域与证据强度：用户明确声明且跨任务稳定的低风险偏好可以自动更新对应 owner；单次选择、只在当前项目成立的要求或从行为猜出的能力缺口只进入当前上下文或 `Observe`，不得静默升级为长期规则。
- 自动更新必须可见、聚焦、可回放和可撤回：说明提取了什么、改到哪个 owner、保护什么行为；当前用户明确纠正高于旧偏好，冲突规则不能同时保持 active。涉及权限、安全、产品限制或大幅改变 Agent 自主性的偏好，仍需明确确认。
- 删除重复沉淀，保持唯一 owner 和索引一致。
- `/hub get` 始终异步执行：前台只负责冻结输入、创建后台任务和返回回执，不等待 workflow 完成；`/hub update` 不继承该异步语义。

# `/hub get` Async Contract

1. Freeze Input：前台冻结触发本次提取的用户信号、明确纠偏、来源任务或对话、最小必要上下文和禁止越界范围；后续主对话产生的新内容不静默混入当前任务。
2. Dispatch：使用宿主可持续的后台 agent/task 能力创建唯一任务标识，并立即返回回执，至少包含 `task`、`source/scope`、`status=queued|running` 和结果回报方式。前台不轮询等待，也不先做一半同步提取。
3. Execute：后台任务基于冻结输入执行本 workflow 的归位、泛化、反例和验证流程；没有沉淀价值时允许以 `no-op` 完成，不为异步任务强造规则。
4. Protect Canonical Main：写入前重新读取 Agent Hub canonical `main` 的最新入口、目标 owner、Git 状态和相关 diff。发现其它对话的重叠改动、owner 冲突或无法证明安全写入时，不覆盖、不 reset、不另建 Agent Hub 分支；以 `blocked` 回报候选结论和最小解除条件。
5. Complete：后台独立回报 `updated / observed / no-op / blocked / failed`，并附唯一 owner、修改文件或未修改原因、验证证据和残余风险；完成回报不抢占或改写主对话的当前任务。
6. Capability Fallback：宿主没有真实、可持续且能回报结果的异步能力时，前台返回 `async unavailable` 或明确的排队状态；不得静默退化为同步执行，也不得把未运行任务写成已受理。

# Workflow

1. Freeze Signal：提取用户明确要求、稳定偏好、反复纠偏、风险信号和证据；区分 `durable / project-scoped / one-off / inferred`，周沉淀时先汇总当周 plan 的设计候选并合并近义项。`/hub get` 只消费回执中冻结的输入，不追读前台后续对话扩大范围。
2. Inventory Owners：读最少入口，查是否已有 Rule、Skill、Reference、Helper 或 Prompt owner。
3. Generalize Signal：从当次采用的“术”中提取“道”，去掉项目名、具体 ID、品牌、当次修复方案和错误文案，写出候选策略的触发条件、保护的不变量、因果机制、边界、权衡与验证方式；确认它能在至少一个相邻场景中独立成立，而不是记住本次答案。
4. Trace Constraint：若候选规则会新增或收紧产品限制，必须追溯到明确需求、公开契约或已验证运行事实；只有部署假设、单次实现现状或未验证猜测时，不得沉淀为产品约束。
5. Decide Value：用长期性、组织性、可触发性、可验证性判断 `Strengthen / Add / Observe / Delete`；偏好还要检查作用域、证据强度、风险和可撤回性。优先强化已有指导思想，仅一次事故或机制不清且无法稳定泛化时观察或删除。
6. Classify Target：在 Skill、Rule、Reference、Project Helper、Report、Temp、Prompt、Workflow、Agent、Tool、Delete 中选唯一 owner。
7. Counterexample：检查一个合法反例和一个相邻场景，避免过度绝对。
8. Patch Owner：优先合并、压缩、删除重复内容；确实缺 owner 才新增。明确、低风险、可逆的稳定偏好可直接更新并在结果中披露；歧义或高影响候选保持 `Observe` 或请求最小决策。
9. Sync Index：更新入口、Skill 索引、旧触发词和路径引用。
10. Validate：搜索事故专名、具体实体、旧称、重复规则和失效引用；用原场景、一个不应触发的相邻场景、一个应进入不同分支的场景和一次用户反向纠正回放规则，确认指针能准确加载、规则能退出或撤回、高波动步骤有可观察完成条件，再运行 diff check。

# Checklist

- 新增 Skill 五问是否通过。
- 候选规则是否已经脱离事故表象和当次答案，并能覆盖相邻场景。
- 候选是否表达指导思想和策略，而不是穷举问题、案例或技术选型。
- 新增产品限制是否能追溯到需求、契约或已验证运行事实。
- 偏好是否区分长期、项目和单次作用域；自动更新是否已披露，且没有把能力猜测写成用户缺陷。
- 是否没有把项目名、一次性方案、临时命令或未验证猜测写进通用 Skill。
- 是否没有形成双 owner。
- `/hub get` 是否已立即返回可追踪回执、未阻塞前台、未同步降级，并在后台结束时给出明确终态与验证证据。
- 后台写入前是否重读 canonical `main` 并保护其它对话的未提交改动；重叠时是否安全停止而非覆盖或另建分支。

# Escalation

- 内容归位不清：`skills/Context/project-content-curation/WORKFLOW.md`
- 项目 helper 缺失或过期：`skills/Context/project-context-rebuild/WORKFLOW.md`
- 外部事实不足：`skills/Research/source-grounded-research/WORKFLOW.md`

# References

- `skills/Context/project-content-curation/references/rule-system-strategy.md`
- `skills/Context/project-content-curation/references/hub-architecture.md`
