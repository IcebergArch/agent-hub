---
name: methodology-harvest
description: 当用户要求吸收 Superpowers、外部 agent framework、插件、技能库、工程方法论或优秀实践到 Agent Hub 时使用。
---

# Methodology Harvest

本技能用于把外部 Agent 方法论转成 Agent Hub 可执行资产。目标是吸收机制，不复制品牌口号、目录布局、临时安装路径或不适配当前工具的强制门禁。

## Trigger

- 用户说“吸收/学习/借鉴 Superpowers”“升级 hub”“把优秀 agent 工作流沉淀进来”。
- 用户把外部方法论吸收描述为“常驻升级任务”，或在 `receive`、定时提醒、自动化触发时要求复查/更新这类吸收成果；常驻表示未来触发，不表示当前轮持续自动运行。
- 对比外部 agent framework、plugin、skill library、prompt/workflow 方法论。
- 完成一次方法论调研后，需要长期沉淀为 Hub 的 Skill、Rule、Report 或索引。

## Source Order

1. 先读 Hub 当前入口：`AGENTS.md`、`README.md`、`agents/<tool>.md`、`skills/README.md`。
2. 定位外部来源：优先读用户指定路径、已安装插件源码、官方文档或可信 release；需要“最新”时再联网核对官方来源。
3. 只读入口、代表性技能和必要 references；不要整库灌入上下文。
4. 明确来源日期、路径和版本状态；临时缓存路径只能作为事实来源，不写成永久依赖。

## Classification

| Class | 落位 | 判断标准 |
| --- | --- | --- |
| Skill | `skills/<skill-slug>/SKILL.md` | 可重复触发、有步骤、检查点、输出形态或抗跑偏机制 |
| Rule | `AGENTS.md`、`agents/<tool>.md` | 跨任务稳定约束、工具差异、质量门槛 |
| Report | `reports/personal/agent/analysis/` | 来源盘点、取舍理由、采用/不采用清单 |
| Reference | `skills/<skill-slug>/references/` | 某技能长期要按需查的模式表、脚本说明或资源清单 |
| Prompt / Script | `references/` 或不复制 | 可泛化为审查模板、压力场景、诊断脚本时才保留；绑定外部工具运行时的脚本只记录思路 |
| Keep / No-copy | 原外部路径 | 品牌说明、安装文档、过重流程、与 Hub 结构冲突或只适合原工具的内容 |

## Adoption Gates

每个候选机制写入 Hub 前，先过五个问题：

1. **Trigger**：后续什么用户表达或任务场景会触发？
2. **Invariant**：它保护什么不变量，例如证据优先、根因优先、设计先行、边界隔离？
3. **Destination**：能否合并进现有 Skill；必须新增 Skill 吗？
4. **Fit**：是否符合用户当前偏好、Hub 中文协作语境、Codex/Cursor 共用边界？
5. **Verification**：能用引用搜索、diff 审查、测试/压力场景或报告索引证明它已生效吗？

## Superpowers Reference

吸收 Superpowers 时，先读 `references/superpowers-pattern-map.md` 和 `references/superpowers-coverage-audit.md`。前者记录 Superpowers 机制到 Hub 资产的默认映射，后者确认 14 个 skills 与 prompt/script/assets 等资源类别的处理状态；如果安装版本或官方文档变化，需要先刷新 coverage audit，再刷新映射、相关 Skill 或 dated report。

通用吸收任务可复制 `references/harvest-evidence-template.md` 的结构到报告正文，再按当前来源填写；不要把模板本身当成完成证据。

当外部方法论涉及“必须加载/调用技能”的强规则时，先读 `references/skill-trigger-discipline.md`，按 Hub 的 Loading Order、最小必要技能集合和当前 `SKILL.md` 正文适配；不要照搬外部“1% 可能就强制加载全部”的成本模型。

当新增或显著修改带强约束的 Hub Skill/Rule 时，可用 `references/skill-pressure-test-template.md` 设计 1-3 个压力场景；它用于验证规则是否能抵抗赶时间、已有投入、用户催促、外部 reviewer 误导等跑偏压力。

新增或显著修改 Skill 前，可先读 `references/skill-authoring-patterns.md`；它把触发描述、渐进加载、自由度控制、Skill TDD 和反跑偏机制整理成 Hub-native checklist。

当外部 skill 带有平台工具映射、`agents/*.yaml`、subagent prompt、Graphviz/HTML/browser helper、压力测试或安装脚本时，先读 `references/external-skill-portability.md`；按能力语义迁移，不照搬外部工具名、agent manifest 或运行时脚本。

## Workflow

1. **Inventory**：列出外部方法论入口、技能、references、prompt templates、脚本、assets 和可复用资源。
2. **Extract**：按机制抽取，而不是按外部目录复制；标记 adopted / adapted / not adopted。
3. **Resource Decision**：prompt template 先抽成 review/checklist 结构，script 先抽成诊断流程，asset/brand 默认 no-copy；只有确实可跨任务复用才复制到 Hub。
4. **Map**：优先落到现有 `bug-reproduction`、`test-strategy`、`implementation-plan`、`task-execution-lifecycle`、`code-review`、`design-principle-library`。
5. **Patch**：入口文件只写触发规则；长解释写入 Skill reference 或 report。
6. **Archive**：重要取舍写入 `reports/personal/agent/analysis/` 并更新 `reports/index.md`。
7. **Validate**：搜索触发词、技能名、旧/新引用；检查 diff 边界，说明哪些内容没有复制及原因。

## Drift Review

当外部来源出现以下变化时，先复核再继续使用旧映射：

- plugin manifest、package/version、release tag 或 commit SHA 变化。
- skill 列表新增、删除、改名或 description 触发条件变化。
- 关键 reference、prompt template、script 或 manifest 能力字段变化。
- 用户明确要求“最新”“重新吸收”“升级 Superpowers/外部框架”。

复核时只比较入口、manifest、技能清单和已采纳机制相关文件；确认无语义变化时更新报告的版本证据即可。若有语义变化，刷新对应 pattern map、相关 Skill 和 dated report。

## Completion Audit

结束前用证据矩阵检查，不用“看起来已经写了”代替完成：

| Requirement | Evidence |
| --- | --- |
| 外部来源已盘点 | 来源路径/URL、日期、入口、代表性技能、references、prompt/script/assets 清单 |
| 机制已抽取 | adopted / adapted / not adopted 列表，且说明保护的不变量 |
| 资源已处理 | prompt、script、asset、manifest、安装文档分别标记 copy / adapt / no-copy |
| Hub 落位已完成 | `AGENTS.md`、`README.md`、`skills/README.md`、相关 Skill、reference、report 的具体文件路径 |
| 入口可发现 | 搜索触发词、技能名和报告标题，能从入口追到 Skill/reference/report |
| 边界无污染 | diff 审查确认未复制外部品牌资产、安装说明、临时缓存路径依赖或公司项目报告 |
| 验证已执行 | `git diff --check`、引用搜索、尾随空白/断链检查或其它等价验证 |

如果矩阵任一项缺证据，只能报告“部分完成”并继续补齐；不能宣称方法论吸收完成。

## Output

最终汇报包含：

- 已吸收的机制和落位文件。
- 没有吸收或只保留为 reference/report 的内容与原因。
- 来源范围和日期。
- 完成审计矩阵的证据和缺口。
- 已执行的引用搜索、diff 边界或其它验证。
