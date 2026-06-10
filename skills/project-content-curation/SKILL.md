---
name: project-content-curation
description: >-
  当用户要求分析整理项目内容、规则、Skills、报告、长期上下文或删除内容时使用；
  先把内容分类为 Skill、Rule、Report、Keep 或 Delete，再执行抽取、保留、归档或清理。
---

# Project Content Curation

本技能用于整理 Agent 工作资产、规则仓库、长期上下文和类似项目。目标是让内容各归其位：该抽成 Skill 的抽成 Skill，该保留为规则的保留为规则，该成为报告的归档到 reports，该删除的验证后删除。

## Trigger

用户要求整理项目内容、规则、Skills、报告、入口文件、长期上下文、删除废弃内容，或明确说“抽成 Skill / 保留为规则 / 删除”时，先执行本技能。

本技能也是规则维护和内容清理的必要阶段；不要只做局部文字补丁。

## Classification

| Class | 放置位置 | 判断标准 |
| --- | --- | --- |
| Skill | `skills/<skill-slug>/SKILL.md` | 可重复触发、有明确工作流、输入输出形态、检查步骤或领域操作方法 |
| Rule | `AGENTS.md`、`agents/<tool>.md`、轻量工具规则 | 稳定协作约束、目录策略、工具差异、权限要求、质量门槛 |
| Report | `reports/personal/agent/<kind>/`、`reports/personal/projects/<project-slug>/<kind>/` 或 `reports/company/projects/<project-slug>/<kind>/` | 个人单独与 Agent 探讨、设计或研究；个人项目或公司项目的结论、证据、设计取舍、审查记录、运行手册或对话沉淀 |
| Keep | 原位置或兼容入口 | 入口索引、兼容 symlink、轻量触发规则、仍被工具自动发现的文件 |
| Delete | 移除 | `.DS_Store`、IDE 状态、临时日志、重复旧入口、未引用废弃内容、未采纳草稿 |

## Workflow

1. **Scope**：确认用户指定的仓库、目录和硬边界；没有明确范围时，先从当前项目入口文件开始。
2. **Inventory**：用 `rg --files`、`find` 和入口文档列出内容，不只看 README。
3. **Read Entrypoints**：优先读 `AGENTS.md`、`README.md`、工具适配文件、`skills/README.md`、`reports/README.md` 和工具规则入口。
4. **Classify**：按上表把内容簇标记为 Skill、Rule、Report、Keep 或 Delete；业务方案不要写进规则。更新 `README.md`、`AGENTS.md`、工具适配文件或 Skill 前，先判断候选内容是否只来自具体项目、公司项目或单个业务域：若是，写入对应 reports；只有抽象成跨任务流程约束后才可进入规则入口。
5. **Keep Entrypoints Small**：除报告文件外，内部入口、工具适配和 Skill 只保留触发条件、判断标准、短步骤和链接；长说明、完整命令序列、历史上下文、示例矩阵和细节论证迁入 reports 或技能内部按需引用资源。
6. **Extract Skills**：将可复用流程迁到 `skills/<skill-slug>/SKILL.md`，保留清晰 frontmatter、触发条件、步骤、输出和验证；不要创建无关 README 或过程文档。
7. **Preserve Rules**：只把跨任务稳定约束写入规则；通用规则写 `AGENTS.md`，工具专属差异写 `agents/<tool>.md` 或轻量工具规则。
8. **Prune**：删除前搜索旧路径、旧标题和旧触发词，确认没有生产入口、工具入口或兼容约定仍依赖它。
9. **Sync Maps**：更新目录图、索引、触发说明、工具规则和兼容入口说明；公司项目报告默认不在根索引列具体条目，避免把本地隔离内容重新暴露为公共入口。
10. **Validate**：重新搜索迁移前的旧引用、临时文件、重复段落和本地噪声；提交前额外检查最终提交树或 staged 清单，确认 `.DS_Store`、`reports/company/projects/` 和旧报告路径没有进入提交；必要时列出无法验证的缺口。

## Output

最终回复必须说明：

- 抽成了哪些 Skill，以及触发条件。
- 保留或新增了哪些规则，以及为什么属于规则。
- 删除了哪些内容，以及删除前做过什么引用检查。
- 哪些内容刻意保留为兼容入口或索引。

如果审查后没有可抽取、可保留或可删除的内容，也要说明已经执行分类审查。
