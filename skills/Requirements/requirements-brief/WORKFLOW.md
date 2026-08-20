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
- 只是代码实现、测试或文档刷新。

# Inputs

- 用户意图、输出模式、目标项目、复杂度级别、目标用户、使用场景、约束、已有方案、非目标、成功信号和开放问题。

# Command Output Modes

- `/hub spec [<需求>]`：产出可独立执行的正式 SPEC。按文档工作区当前规则写入目标项目的 `requirements/` pending 位置；阻塞执行的核心问题必须先澄清，非阻塞假设与开放问题写入正文。
- `/hub spec-idea,<level> [<想法>]`：产出探索性 IDEA。按复杂度级别控制整理深度，写入目标项目的 IDEA 位置，不把 IDEA 提升为 pending。
- 两种命令都只授权文档编写，不创建 STDD、不进入 execing、不修改业务仓库或开始实现。

# Complexity Levels

- `simple`（简）：单一目标、边界明确；只保留目标、范围、关键 workflow、验收、非目标和必要开放问题。
- `middle`（中）：普通产品或工程想法；完整执行本 workflow 的六步需求收敛。
- `complex`（繁）：跨系统、高风险或多阶段想法；在六步基础上补充依赖、方案取舍、失败恢复、风险和分阶段验收。
- 级别只控制整理深度，不改变 IDEA 生命周期、执行授权或验证门禁。

# Decision Principles

- 区分底层目标和用户提出的第一版方案。
- 验收标准必须能被页面 smoke、API smoke、测试或人工验收验证。
- 非目标要明确，防止实现时扩 scope。
- 正式 SPEC 是后续执行的需求契约，必须能仅依赖项目背景和正文明确列出的来源独立读懂。
- 核心方向、项目归属或执行边界仍有阻塞歧义时先问一个最小问题；其它低风险缺口用显式假设收敛。

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
6. Readiness：判断是否足够进入执行，或还需研究/导航/contract。
7. Persist Or Return：普通 brief 在当前回复交付；命令模式按文档工作区当前命名与生命周期规则写入 IDEA 或 pending SPEC，并返回完整路径和开放问题。

# Checklist

- 是否没有把第一版方案当唯一目标。
- 是否有可验证验收标准。
- 是否明确非目标和需要用户确认的问题。
- 正式 SPEC 是否独立可读，且没有会改变核心方案的未决问题。
- 架构图与时序图是否突出全局关系和关键变更，没有为了形式完整而复杂化。
- 内容详略是否服务于可读、可理解，而不是平均展开所有部分。
- 是否只写入文档工作区指定位置，未误建 STDD、进入执行或修改业务仓库。

# Escalation

- 需要实现闭环：`skills/Core/task-execution-lifecycle/WORKFLOW.md`
- 需要外部资料：`skills/Research/source-grounded-research/WORKFLOW.md`
- 涉及接口/tool：Engineering 相关 Skill。

# References

- `references/idea-shaping-patterns.md`
