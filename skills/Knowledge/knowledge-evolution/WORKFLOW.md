---
name: knowledge-evolution
description: 当任务暴露可复用组织经验、Hub 自迭代、外部方法吸收、规则沉淀或 Skill/Prompt/Helper/Workflow/Agent/Tool 升级需求时使用。
---

# Purpose

判断经验是否值得沉淀、沉淀到哪里，以及如何保持知识库一致。

# When to Use

- 用户说 `receive`、`refactor hub`、优化 Hub、升级 Skill Library。
- 外部 agent framework、插件、技能库或方法论需要吸收。
- 任务暴露跨场景稳定缺口：owner、接口、验证、prompt、helper、workflow、agent 或 tool。

# When NOT to Use

- 一次性项目事实、临时调试记录、普通代码实现、默认 bug/review/testing/docs 能力。
- 还没有证据表明问题会跨任务重复。

# Inputs

- 当前任务信号、相关入口、已有 Skill/Rule/Reference、用户纠偏、可验证证据和反例。

# Decision Principles

- 先判断是否值得沉淀，再判断沉淀位置。
- 优先扩展已有 Skill，不新增近义 Skill。
- 一次性项目经验进 Context/Report/Temp，不进 Skill。
- Skill 保留长期组织经验；Prompt/Helper/Workflow/Agent/Tool 只在 owner 清楚时升级。
- 删除重复沉淀，保持唯一 owner 和索引一致。

# Workflow

1. Freeze Signal：提取用户明确要求、反复纠偏、风险信号和证据。
2. Inventory Owners：读最少入口，查是否已有 Rule、Skill、Reference、Helper 或 Prompt owner。
3. Decide Value：用长期性、组织性、可触发性、可验证性判断是否沉淀。
4. Classify Target：在 Skill、Rule、Reference、Project Helper、Report、Temp、Prompt、Workflow、Agent、Tool、Delete 中选唯一 owner。
5. Counterexample：检查一个合法反例和一个相邻场景，避免过度绝对。
6. Patch Owner：优先合并、压缩、删除重复内容；确实缺 owner 才新增。
7. Sync Index：更新入口、Skill 索引、旧触发词和路径引用。
8. Validate：搜索旧称、重复规则和失效引用，运行 diff check。

# Checklist

- 新增 Skill 五问是否通过。
- 是否没有把项目名、一次性方案、临时命令或未验证猜测写进通用 Skill。
- 是否没有形成双 owner。

# Escalation

- 内容归位不清：`skills/Context/project-content-curation/WORKFLOW.md`
- 项目 helper 缺失或过期：`skills/Context/project-context-rebuild/WORKFLOW.md`
- 外部事实不足：`skills/Research/source-grounded-research/WORKFLOW.md`

# References

- `skills/Context/project-content-curation/references/rule-system-strategy.md`
- `skills/Context/project-content-curation/references/hub-architecture.md`
