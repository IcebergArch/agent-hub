---
name: project-content-curation
description: 当用户要求整理项目内容、规则体系、Skills、报告、长期上下文或删除内容时使用。
---

# Purpose

判断内容该归为 Skill、Rule、Reference、Project Helper、Report、Temp、Keep 还是 Delete。

# When to Use

- 用户要求整理项目内容、规则、Skills、报告、入口文件、长期上下文。
- 用户明确说抽成 Skill、保留为规则、归档、删除、清理废弃内容。
- 规则维护、Hub 整理或内容清理前的分类阶段。

# When NOT to Use

- 普通实现任务不涉及内容归位。
- 只是一次性回答，不需要更新资产。

# Inputs

- 用户指定范围、现有入口、Skill 索引、相关文件、引用关系、文档工作区入口和本地改动状态。

# Decision Principles

- 先判加载频率和生命周期，再判 owner。
- 入口保持短；长细则进 reference；项目事实进 helper；完整证据进 report；临时材料进 Temp。
- 收拢历史任务或旧文档前，先按当前事实区分 `still_required / already_equivalent / obsolete / needs_decision`；废弃、被替代或归档不等于已完成，不得补写不存在的验收证据。仍需继续的内容进入可独立理解的唯一 active owner，历史载体只保留证据和追踪关系。
- 删除前搜引用；不要新增低价值 owner。

# Workflow

1. Scope：确认仓库、目录、硬边界和用户指定删除/保留要求。
2. Inventory：列入口、Skill、reference、helper、report index 和当前改动。
3. Classify：按 Skill / Rule / Reference / Project Helper / Report / Temp / Keep / Delete 归位；历史任务先核对当前实现、运行态和外部对象，再区分仍需实施、已有等价实现、已失效或待决策。
4. Move / Merge / Prune：优先合并、压缩、下沉或删除重复内容。
5. Sync Maps：更新目录图、索引、触发说明和旧路径引用。
6. Validate：搜旧称、临时文件、重复段落、本地噪声和失效链接。

# Checklist

- 整理是否提升加载效率、触发准确度或维护质量。
- 是否避免把一次性报告塞进默认上下文。
- 历史内容是否没有把 `deprecated/obsolete/archived` 写成 `done`；新 active owner 是否已独立承接仍需工作的边界且没有双 owner。
- 是否已说明删除前的引用检查。

# Escalation

- 需要判断是否升级 Hub：`skills/Knowledge/knowledge-evolution/WORKFLOW.md`
- 项目背景缺失或过期：`skills/Context/project-context-rebuild/WORKFLOW.md`

# References

- `references/rule-system-strategy.md`
- `references/hub-architecture.md`
