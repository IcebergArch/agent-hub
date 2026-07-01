# Evolution Decision Checklist

日期：2026-06-18
来源：Agent Hub 自迭代/自优化/自升级机制设计
用途：供 `self-evolution-engine` 在任务内判断是否需要升级 Hub，以及升级内容是否具备跨设备可携带性。

## Decision Matrix

| Question | Yes | No |
| --- | --- | --- |
| 用户是否明确要求 Hub 具备自迭代/自优化/自升级能力？ | 进入 repo-portable gate | 继续检查其它触发信号 |
| 当前经验是否会在后续任务重复出现？ | 考虑沉淀到 Skill/Rule/Reference | 只保留在当前任务或 report |
| 经验是否保护高风险不变量，如验证、owner、边界、触发、落位？ | 继续分类 | 不升级为长期机制 |
| 经验是否需要换设备后仍能成立？ | 必须写进 repo 资产 | 可只作为本机操作说明 |
| 现有 owner 是否已能承载？ | 更新现有 owner | 评估是否新增 owner |
| 模型能力、通用规则或更高阶 Skill 是否已覆盖？ | 合并、删除或降级为 reference 示例 | 继续判断独立 owner 价值 |

## Repo-Portable Gate

候选机制只有通过以下检查，才可升级为 Hub 长期能力：

1. 触发条件能从仓库内容和用户表达中恢复，不依赖某次对话记忆。
2. 执行步骤能从 `AGENTS.md`、`README.md`、`agents/`、`skills/`、`references/` 读取，不依赖本机 automation memory。
3. 若引用本机状态，只能作为来源证据，不得成为默认运行依赖。
4. 工具专属能力要落入 adapter 或 fallback，而不是伪装成公共能力。
5. 换到新设备重新 clone/打开仓库后，读取 Hub 即能重新触发同样判断链路。

## Destination Heuristics

| Candidate | Landing |
| --- | --- |
| 跨工具硬约束 | `AGENTS.md` |
| 长期协作能力定义 | `README.md` |
| 工具差异、宿主能力 | `agents/<tool>.md` |
| 可重复流程 | `skills/<slug>/SKILL.md` |
| 长检查表、矩阵、反模式 | `skills/<slug>/references/` |
| 来源、证据、一次性取舍 | 文档工作区报告位置 |

## Stop Signals

出现以下任一情况时，不应升级为长期机制：

- 只能依赖当前设备目录、cache-id、线程状态或自动化实例。
- 本质是单个项目业务结论，而不是跨任务方法。
- 现有 owner 已覆盖，只是入口还没加指针。
- 只是“感觉以后有用”，没有明确 trigger 或 verification。
- 只是某个场景分支，没有新的方法原语、owner 或证据门。
- 为了完整性开始引入定时自动化、后台守护或额外目录，而用户当前只要求任务内触发能力。

## Validation

最小验证应覆盖：

- trigger 词是否可从入口搜到。
- `SKILL.md` 与 `skills/README.md`、`AGENTS.md`、`README.md` 是否连通。
- 长内容是否留在 reference，而不是堆进高频入口。
- `git diff --check` 是否通过。
