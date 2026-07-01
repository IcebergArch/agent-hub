# Rule System Strategy

日期：2026-06-17
来源：Agent Hub 规则体系 review、整合、清理和完善
用途：作为 `project-content-curation` 的按需参考；用于审查 `AGENTS.md`、`README.md`、`agents/`、`skills/`、项目 helper 和文档工作区之间的规则承载边界。

## Core Rule

规则体系的目标不是把所有好规则放进一个入口，而是让 Agent 用最少上下文读到正确层级：入口文件只保留硬约束和触发索引；项目关键事实进入文档工作区 helper；可重复流程进入 Skill；长步骤、矩阵、示例进入 reference；需要长期引用的历史证据进入正式报告；临时迁移账本、缓存路径和一次性对照进入 Documents temp。

## Layer Ownership

| Layer | 承载什么 | 不承载什么 |
| --- | --- | --- |
| `AGENTS.md` | 跨工具硬约束、目录策略、加载顺序、触发索引、安全/权限/边界规则 | 长流程、完整命令清单、项目事实、历史论证、示例矩阵 |
| `README.md` | Hub 核心定位、少量最高法则、工作方向、路由总览和目录地图 | 一次性任务、项目专属设计、工具专属差异、详细流程、长协作偏好、执行节奏、完整检查表 |
| `agents/<tool>.md` | Codex、Cursor 等宿主差异、工具能力映射、宿主指令 | 跨工具通用规则、项目知识、报告正文 |
| `skills/<slug>/SKILL.md` | 可重复触发的流程：Trigger、输入、步骤、输出、质量门槛、reference 指针 | 大量示例、长命令、历史背景、项目私有事实 |
| `skills/<slug>/references/` | 该 Skill 的长检查表、矩阵、模式、执行细则、可选深读 | 与 Skill 无关的项目结论、通用入口规则 |
| 文档工作区 helper | 当前运行时项目的关键背景、边界、事实源和少数高频约束 | 完整报告、长论证、一次性历史 |
| 文档工作区报告位置 | 产出文档、归档报告、证据、完整论证、运行记录、对话沉淀 | 会被每次任务强制加载的背景或通用规则 |
| `/Users/shatang/Documents/temp/` | 过度产物、迁移账本、一次性对照、缓存路径、临时审计 | Hub 运行规则、正式文稿、长期事实源 |
| `.agents/` | 兼容入口或工具自动发现桥接 | 真实维护源 |

## Classification Order

审查一条候选规则或一段存量内容时，按顺序归位：

1. **项目事实**：运行时需要的短事实写入文档工作区 helper；完整产物和历史证据如果需要长期引用，写入文档工作区报告位置；只在当前任务有用的迁移账本、临时对照、缓存路径写入 Documents temp；能抽象成跨任务机制后，机制才可进入规则或 Skill。
2. **可重复流程**：有触发词、步骤、输出和验证，放入现有 Skill；没有合适 owner 时才新增 Skill。
3. **流程细节**：长 checklist、命令序列、示例、矩阵和反例，放入对应 Skill 的 `references/`。
4. **临时材料**：迁移账本、外部组件映射、版本审计、缓存路径和过度产物，放入 Documents temp，不进入 Skill 运行面。
5. **跨工具硬约束**：短、强、稳定、会影响安全/边界/权限/验证的规则，放入 `AGENTS.md`。
6. **工具差异**：依赖 Codex、Cursor、浏览器、MCP、宿主 UI 或特殊权限，放入 `agents/<tool>.md`。
7. **索引或兼容入口**：保留在 README、skills index、project helper、report artifact index 或 `.agents/`。
8. **噪声和废弃内容**：搜索无引用后删除，例如 `.DS_Store`、临时日志、重复旧入口、未采纳草稿。

## Rule Shape Gate

新增或保留为 Rule 前，必须能回答五个问题：

- **Trigger**：什么用户表达、文件变化或风险信号会触发？
- **Invariant**：它保护什么稳定边界？
- **Artifact**：要检查哪些文件、diff、接口、报告或运行信号？
- **Exception**：什么情况下可以不执行或降级？
- **Verification**：如何确认规则没有断链、重复、越界或变成空话？

答不清时，不直接写入 `AGENTS.md`。优先改成 Skill 步骤、reference 检查项、report 结论，或暂不沉淀。

## De-duplication Strategy

- 先选唯一 owner，再让其它入口只保留指针；不要在 `AGENTS.md`、Skill 和 runbook 中复制同一长流程。
- `AGENTS.md` 可保留一句硬约束，例如“必须触发某 Skill”或“提交前必须审查 diff”；具体步骤进入 Skill/reference/runbook。
- 同一主题若同时存在 Rule 和 Skill：Rule 说明何时必须触发，Skill 说明怎么执行。
- 同一主题若同时存在 Skill 和 Report：Skill 保留可复用流程，Report 保留历史背景、证据和一次性决策。
- 外部方法论只吸收机制，不复制品牌口吻、全量目录、平台专属工具名、版本账本或不适配当前 Hub 的强制门禁；迁移证据只放 temp 或正式报告。

## Cleanup Workflow

1. **Inventory**：列出入口文件、相关 Skill、references、当前项目 helper、必要 report artifact index 和当前 `git status`。
2. **Boundary**：分清本轮改动、用户已有改动、未跟踪文件、私有项目文档和 ignored 内容。
3. **Cluster**：按主题归并规则簇，例如 loading、执行预算、报告归档、接口契约、验证、git、清理、外部研究。
4. **Classify**：对每个规则簇标记 `Rule / Skill / Reference / Report / Temp / Keep / Delete`。
5. **Extract**：把长流程迁入对应 reference；入口只留下触发条件和硬约束。
6. **Merge**：合并近义规则，拆开混杂规则，删除空泛或不可验证表达。
7. **Sync**：更新 `AGENTS.md` loading order、`README.md` 目录图、`skills/README.md`、当前项目 helper、必要 report artifact index 和旧触发词引用。
8. **Validate**：运行 diff check、引用搜索、索引覆盖检查、旧名称搜索和本地噪声搜索；最终说明未覆盖风险。

## Stop Signals

出现以下情况时停止扩写规则，先收敛边界：

- 规则只来自单个项目或一次事故，尚未抽象成可复用机制。
- 候选规则没有明确触发词或验证方式。
- 同一细则已经在 Skill/reference/runbook 中存在，只是入口没有指针。
- 为了“更完整”开始加入未来可能的 owner、目录、流程或兼容层。
- 规则会让普通小修默认加载长 reference、外部研究、subagent 或完整审查。

## Review Output

规则体系 review 的输出应先给归位结论：

```markdown
## Classification
- Skill:
- Rule:
- Reference:
- Report:
- Temp:
- Keep:
- Delete:

## Changes
- ...

## Validation
- ...

## Not Moved
- ...
```

如果没有新增规则，也要说明已审查且当前只需保留、移动或删除哪些内容。
