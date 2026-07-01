---
name: methodology-harvest
description: 当用户要求吸收外部 agent framework、插件、技能库、工程方法论或优秀实践到 Agent Hub 时使用。
---

# Methodology Harvest

把外部 Agent 方法论转成 Hub 可执行资产。目标是吸收机制，不复制品牌、目录、安装说明、临时路径或不适配当前工具的强制门禁。

## Trigger

- 用户要求吸收、学习、借鉴外部 agent framework / plugin / skill library。
- 用户要求把外部工作流沉淀进 Hub，或复查已有吸收成果。
- 完成方法论调研后，需要长期落位为 Skill、Rule、Reference 或报告。

## Source Order

1. 读 Hub 入口：`AGENTS.md`、`README.md`、当前工具 adapter、`skills/README.md`。
2. 定位外部来源：用户指定路径、已安装插件源码、官方文档、可信 release；需要最新事实时联网核对官方来源。
3. 只读入口、代表性技能和必要 references；不要整库灌入上下文。
4. 记录来源日期、路径和版本；缓存 ID 只作为本轮证据，不写成长期依赖。

## Classification

| Class | Destination |
| --- | --- |
| Skill | 可重复触发、有步骤、检查点、输出形态或抗跑偏机制 |
| Rule | 跨任务稳定约束、工具差异、质量门槛 |
| Reference | 长模式表、脚本说明、压力场景、资源清单 |
| Report | 需要长期引用的来源盘点、取舍理由、采用/不采用清单 |
| Temp | 迁移账本、外部组件版本审计、缓存路径、一次性对照表 |
| No-copy | 品牌、安装文档、原平台工具名、不可执行脚本、过重流程 |

## Adoption Gates

每个候选机制先问：触发什么场景、保护什么不变量、能否合并进现有 owner、是否适配 Codex/Cursor 和中文协作语境、如何用搜索/diff/压力场景证明生效。

## Workflow

1. **Inventory**：列外部入口、技能、references、prompt、script、asset 和 manifest。
2. **Extract**：按机制抽取，标记 adopted / adapted / not adopted。
3. **Decide Resource**：prompt 抽成审查结构；script 抽成诊断流程；asset/brand 默认 no-copy。
4. **Map**：优先落到现有 `bug-reproduction`、`test-strategy`、`implementation-plan`、`task-execution-lifecycle`、`code-review`、`design-principle-library`。
5. **Patch / Archive**：入口只写触发规则；长解释写 Hub-native reference；重要取舍写正式报告；过度产物进 `/Users/shatang/Documents/temp/`。
6. **Validate**：搜索触发词、技能名、旧/新引用，检查 diff 边界和未复制内容。

## References

- `references/harvest-evidence-template.md`：写来源审计报告。
- `references/skill-trigger-discipline.md`：外部方法论涉及强制加载技能。
- `references/skill-pressure-test-template.md`：新增或显著修改强约束 Skill/Rule。
- `references/skill-authoring-patterns.md`：新增或显著修改 Skill。
- `references/external-skill-portability.md`：外部 skill 带工具映射、agent manifest、subagent prompt、可视化 helper 或安装脚本。

## Drift And Completion

- 外部 manifest、version、release、commit、skill 清单、description、reference、prompt/script 或能力字段变化时，先复核再沿用旧映射。
- 结束前检查：来源已盘点、机制已抽取、资源已处理、Hub 落位完成、入口可发现、边界无污染、验证已执行。

## Output

说明已吸收机制、落位文件、未吸收内容和原因、来源范围与日期、验证证据和缺口。
