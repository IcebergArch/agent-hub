---
name: self-evolution-engine
description: 当用户要求 Hub 自迭代/自优化/自升级，或任务暴露出跨场景稳定流程缺口时使用；判断是否沉淀为 repo-portable 机制。
---

# Self Evolution Engine

判断一次经验是否值得升级为 Hub 长期机制。它不负责整理具体内容；具体落位交给对应 owner。

## Trigger

- 用户明确要求自迭代、自优化、自升级、自我提升、持续升级，或“换设备后也能触发”。
- 当前任务暴露出反复、跨任务、高风险且不应依赖用户手动 `receive` 的流程缺口。
- 外部方法论吸收、规则体系调整、设计原则沉淀后，需要补 repo-portable 触发和落位机制。

不因 typo、一次性项目细节、临时命令或本机 workaround 触发。

## Invariants

- 机制必须能从仓库恢复：`AGENTS.md`、`README.md`、`agents/`、`skills/`、references，以及文档工作区中直接相关 helper。
- 不依赖 automation memory、插件缓存路径、宿主线程状态、临时本地目录或安装态。
- 优先合并、精简、下沉或删除旧内容；不新增重复 owner，不把入口越写越长。

## Signals

候选信号包括：跨任务不变量、已有规则被绕过、owner / 数据源 / 验收信号反复混淆、Run Case 与 Build Capability 动作不匹配、SDK/API/tool/config 命名语义漂移、规则无法用 diff / 搜索 / workflow / checklist 证明会生效。

## Workflow

1. **Classify Signal**：确认候选属于哪个结构性信号；无法分类则不升级。
2. **Bind Decision Point**：规则要能在任务定位、计划审查、写文件前、契约变更前、工具调用前、收尾验收前或失败复盘后生效。
3. **Repo-Portable Gate**：去掉本机状态依赖，改写为 repo 内触发、Skill owner、reference 检查表或 adapter 语义。
4. **Choose Owner**：内容整理走 `project-content-curation` / `refactor-hub`；设计原则走 `design-principle-library`；外部方法走 `methodology-harvest`；执行 gate 走 `task-execution-lifecycle`；接口/tool/config 走 `interface-contract-audit`。
5. **Patch Minimally**：入口只写短触发和硬约束；长矩阵、样例和执行细则放 references；一次性证据放报告。
6. **Replay And Validate**：用候选场景回放新机制会在哪个动作点触发、检查什么、要求什么证据；再做引用搜索和 diff check。

## References

- `references/evolution-decision-checklist.md`：repo-portable gate 和沉淀判断。
- `../project-content-curation/SKILL.md`：内容分类。
- `../refactor-hub/SKILL.md`：对话经验与 Hub 入口整理。
- `../design-principle-library/SKILL.md`：设计原则沉淀。
- `../methodology-harvest/SKILL.md`：外部方法论吸收。

## Output

说明自升级是否成立、落位到哪些 owner、哪些内容未沉淀及原因、做了哪些验证。

## Quality Bar

- 不把“以后记得”当自升级。
- 不把触发词表当机制；必须有动作点和验证方式。
- 不破坏入口文件的短小和稳定加载面。
