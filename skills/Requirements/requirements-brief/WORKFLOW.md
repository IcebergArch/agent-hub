---
name: requirements-brief
description: 当用户提出产品、功能、工具、自动化、workflow 或改造想法，需要整理成需求简报、IDEA、正式 SPEC 和验收边界时使用。
---

# Purpose

把用户输入变成组织可执行的需求简报、IDEA 或正式 SPEC：目标、边界、工作流和验收。

# When to Use

- “整理需求”“写 PRD”“功能简报”“把想法落成需求”。
- 用户说 `/hub spec` 或 `/hub spec-idea,<level>`。
- 目标、用户、非目标或验收标准还不清楚。
- 要做工具、页面、自动化或 workflow，但实现边界需要先收敛。

# When NOT to Use

- 用户已经给出明确实现任务，可以直接进入执行。
- 用户说 `/hub spec 执行` 或 `/hub spec 验收`；这两种模式属于任务执行生命周期。
- 只是代码实现、测试或文档刷新。

# Inputs

- 用户意图、输出模式、目标项目、复杂度级别、目标用户、使用场景、约束、已有方案、非目标、成功信号和开放问题。

# Command Output Modes

- `/hub spec [<需求>]`：产出可独立执行的正式 SPEC。draft 登记不变量、背景、目标、核心问题和非目标；init 按影响面汇总当前事实、历史经验、行业实践和参与 Skill/角色的专家设计输入；update 正式撰写第一版完整方案并评审修正；全部适用维度通过后转为最终定稿 plan。通过即停止，绝大多数方案应在 2-3 轮内收敛；10 轮只是单个实质变更周期的硬上限。阻塞问题未解除时停在 update，不得强行定稿。
- `/hub spec-idea,<level> [<想法>]`：产出探索性 IDEA。按复杂度级别控制整理深度，写入目标项目的 IDEA 位置，不把 IDEA 直接提升为 plan。
- 两种命令都只授权文档编写，不创建 STDD、不进入 execing、不修改业务仓库或开始实现；`/hub spec 执行` 与 `/hub spec 验收` 由 `task-execution-lifecycle` 分别承接实施和验收。

# Complexity Levels

- `simple`（简）：单一目标、边界明确；只保留目标、范围、关键 workflow、验收、非目标和必要开放问题。
- `middle`（中）：普通产品或工程想法；完整执行本 workflow 的需求收敛与评审流程。
- `complex`（繁）：跨系统、高风险或多阶段想法；在完整流程基础上补充依赖、方案取舍、失败恢复、风险和分阶段验收。
- 级别只控制整理深度，不改变 IDEA 生命周期、执行授权或验证门禁。

# Decision Principles

- 区分底层目标和用户提出的第一版方案。
- 验收标准必须能被页面 smoke、API smoke、测试或人工验收验证。
- 非目标要明确，防止实现时扩 scope。
- 正式 SPEC 是后续执行的需求契约，必须能仅依赖项目背景和正文明确列出的来源独立读懂。
- 核心方向、项目归属或执行边界仍有阻塞歧义时先问一个最小问题；其它低风险缺口用显式假设收敛。
- 评审维度先判断 applicable / N/A，再由 `skill-router` 选择最小 Skill 组合；不默认加载全部 Skill，也不允许因缺少专门 Skill 跳过风险维度。
- init 的作用是建设方案输入：把已验证当前事实、历史运行/事故/项目经验、适用行业实践和专家判断提炼为设计约束、可选模式、取舍、失败风险与验证信号；不得只列资料或在成稿后补“最佳实践”。
- 业界实践必须写明要解决的问题、适用前提和代价。高风险、新颖、版本敏感或外部能力不确定的决策加载 `source-grounded-research` 核对权威来源；低风险稳定模式有足够本地证据时不为形式联网。
- 选中的 Skill/角色在 init 阶段共同形成设计输入；update 负责正式撰稿并验收这些输入是否被正确吸收，而不是每轮重新发散一套方案。
- `plan` 表示方案评审通过和内容定稿，不表示已获得实现授权；实质变更必须保持同一 SPECID 并回到最早受影响的 draft、init 或 update。
- 变更回到最早受影响阶段：不变量、背景、目标或核心问题变化回 draft；专家范围或意见变化回 init；方案正文修正留在 update。

# Spec Presentation

- 正式 SPEC 尽量保留一张精简架构图和一张关键时序图；仅在图能提升理解时使用，简单改动或与正文重复时可以省略。
- 架构图同时呈现全局上下文、本次变更边界及受影响的 owner、组件和依赖，不展开无关内部细节。
- 时序图只覆盖主路径，以及会改变设计或验收的关键失败与恢复路径，不枚举普通分支。
- 相关部分简写，关键变更、契约和验收所需细节充分展开；可读、可理解优先于形式完整或图的复杂度。

# Workflow

1. Capture Intent：提炼真正要解决的问题和当前方案假设。
2. Users And Jobs：说明谁用、在哪用、完成前后状态如何变化。
3. Scope：列 in-scope、out-of-scope 和后续可选项。
4. Workflow：写关键路径、输入、系统反馈、失败态和恢复路径。
5. Acceptance：定义成功、失败、边界、权限和数据验收。
6. Draft：查重并按文档工作区规则分配稳定 SPECID；只登记必须保留的不变量、背景、目标、核心问题、非目标和必要假设。IDEA 转正时重写原 owner，不保留重复需求。
7. Init：基于 draft 判断七个维度的适用性，用 `skill-router` 选择最小 Skill/角色组合；按 `references/spec-review-gates.md` 汇总当前事实、历史经验、行业实践和专家判断，提炼关键设计约束、候选模式、适用条件、取舍、风险和验收信号。此阶段完成方案构型输入，但不提前展开完整方案正文。
8. Update Authoring Loop：进入 `update` 后正式撰写第一版完整方案，逐项记录设计输入和专家意见的 `adopted / adapted / rejected` 处置，再按 `references/spec-review-gates.md` 评审、修正和复评；无 blocker 即停止，绝大多数方案在 2-3 轮内收敛。第 3 轮后仍未通过时，先定位不收敛根因并选择缩小范围、补证据、请求决策或调整参与方；只有下一轮有明确新输入或修改目标时才继续，最多 10 轮。
9. Plan：全部适用维度通过、跨维度结论一致且验收可执行后转为 `plan`；实质修改 plan 时，按最早受影响内容退回 `draft`、`init` 或 `update` 开启新的评审周期。
10. Persist Or Return：普通 brief 在当前回复交付；命令模式按文档工作区当前命名与生命周期规则写入 IDEA/draft/init/update/plan，并返回完整路径、当前阶段、评审结论和开放问题。

# Checklist

- 是否没有把第一版方案当唯一目标。
- 是否有可验证验收标准。
- 是否明确非目标和需要用户确认的问题。
- 正式 SPEC 是否独立可读，且没有会改变核心方案的未决问题。
- draft 是否只登记不变量、背景、目标和核心问题；init 是否形成适用维度、参与 Skill/角色，以及事实、经验、行业实践与专家判断驱动的方案设计输入；update 是否正式撰稿并记录输入处置、评审轮次和 blocker；plan 是否不存在未解除 blocker。
- 架构图与时序图是否突出全局关系和关键变更，没有为了形式完整而复杂化。
- 内容详略是否服务于可读、可理解，而不是平均展开所有部分。
- 是否只写入文档工作区指定位置，未误建 STDD、进入执行或修改业务仓库。

# Escalation

- 需要落盘或流转正式 SPEC：`skills/Requirements/spec-lifecycle/WORKFLOW.md`
- 需要实现闭环：`skills/Core/task-execution-lifecycle/WORKFLOW.md`
- 需要外部资料：`skills/Research/source-grounded-research/WORKFLOW.md`
- 涉及接口/tool：Engineering 相关 Skill。

# References

- `references/idea-shaping-patterns.md`
- `references/spec-review-gates.md`
