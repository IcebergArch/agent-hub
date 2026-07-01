---
name: project-content-curation
description: 当用户要求整理项目内容、规则体系、Skills、报告、长期上下文或删除内容时使用；先分类，再迁移、保留或清理。
---

# Project Content Curation

内容归位 Skill。它负责判断一段内容该成为 Skill、Rule、Reference、Project Helper、Report、Keep 还是 Delete。

## Trigger

- 用户要求整理项目内容、规则、Skills、报告、入口文件、长期上下文。
- 用户明确说抽成 Skill、保留为规则、归档、删除、清理废弃内容。
- 规则维护、Hub 整理或内容清理前的分类阶段。

## Classification

| Class | Destination | Use When |
| --- | --- | --- |
| Skill | `skills/<slug>/SKILL.md` | 可重复触发、有 workflow、输入输出和检查步骤 |
| Rule | `AGENTS.md` / `agents/<tool>.md` | 跨任务硬约束、工具差异、权限或质量门槛 |
| Reference | `skills/<slug>/references/` | 长 checklist、矩阵、模式、示例、执行细则 |
| Project Helper | 文档工作区 `README.md` 指定位置 | 后续运行时高频项目背景和事实源 |
| Report | 文档工作区报告位置 | 需要长期引用的一次性/阶段性证据、完整论证、审查记录 |
| Temp | `/Users/shatang/Documents/temp/` | 过度产物、迁移账本、临时对照、缓存清单、当前任务有用但不应长期加载的材料 |
| Keep | 原位置或兼容入口 | 仍被工具发现、仍有索引价值 |
| Delete | 移除 | 临时日志、重复旧入口、无引用废弃内容、未采纳草稿 |

## Workflow

1. **Scope**：确认用户指定仓库、目录和硬边界。
2. **Inventory**：用 `rg --files` / `find` 列内容，不只看 README。
3. **Read Entrypoints**：读 `AGENTS.md`、`README.md`、adapter、`skills/README.md`、文档工作区 `README.md` 和相关 Skill。
4. **Classify**：先判加载频率和生命周期，再判 owner；业务方案不进通用规则。
5. **Move / Merge / Prune**：入口保持短；长细则进 reference；项目事实进 helper；长期证据进 report；临时证据进 Temp；删除前搜引用。
6. **Sync Maps**：更新目录图、索引、触发说明和旧路径引用。
7. **Validate**：搜旧称、临时文件、重复段落和本地噪声；跑 `git diff --check` 或等价检查。

## References

- `references/rule-system-strategy.md`：规则体系和承载边界。
- `references/hub-architecture.md`：Hub 分层架构。

## Output

说明哪些内容归为 Skill / Rule / Reference / Project Helper / Report / Temp / Keep / Delete，哪些删除前做了引用检查，哪些没有移动及原因。

## Quality Bar

- 整理必须提升加载效率、触发准确度或维护质量。
- 不把一次性报告塞进默认上下文。
- 不把迁移账本、外部组件映射、缓存路径或临时对照表伪装成 Hub 长期资产。
- 不新增目录或 owner 来承载低价值内容。
