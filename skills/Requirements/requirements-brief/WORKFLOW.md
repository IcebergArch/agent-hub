---
name: requirements-brief
description: 当用户提出产品、功能、工具、自动化、workflow 或改造想法，需要整理成需求简报、IDEA、正式 SPEC 和验收边界时使用；负责内容收敛，不负责执行。
---

# Purpose

把输入收敛为目标、边界、关键流程和可验证验收；正式 SPEC 再按风险形成可独立执行的需求契约。

# When to Use

- “整理需求”“写 PRD”“功能简报”“把想法落成需求”。
- `/hub spec-idea` 或 `/hub spec`。
- 目标、用户、非目标、工作流或验收仍需收敛。

# When NOT to Use

- 已是明确实现任务，或命中 `/hub spec-exec`、`/hub spec-smoke`、`/hub pr`。
- 只做代码、测试或机械文档刷新。

# Inputs

- 用户意图、输出模式、项目、目标用户与 job、场景、约束、已有事实/方案、非目标、成功信号和开放问题。

# Command Modes

- 普通 brief：在当前回复给出最小可用需求，不落正式生命周期。
- `/hub spec-idea`：登记可升级 IDEA；至少包含项目、问题/背景、目标结果、初步边界、升级价值和开放问题，不分配 SPECID。
- `/hub spec`：建设 `draft -> init -> update -> plan` 的正式 SPEC；阻塞未解除时停在 update。
- 两个命令都只授权文档编写，不创建 STDD、不进入 execing、不修改业务仓库。状态、路径和查重由 `spec-lifecycle` 负责。
- 用户明确指定 `simple / middle / complex` 时只调整展开深度，不改变生命周期或授权。

# Decision Principles

- 区分底层目标和用户提出的第一版方案；明确 in-scope、out-of-scope 和必要假设。
- 核心方向、项目归属或执行边界有阻塞歧义时只问一个最小问题；其它低风险缺口用显式假设推进。
- 验收必须能由页面/API smoke、测试或人工操作验证，并覆盖成功、失败、边界、权限和数据。
- 正式 SPEC 必须能仅依赖项目背景、正文事实和明确来源独立读懂。
- 系统性方案按 `系统定位 > 系统架构 > 功能设计 > 领域架构 > 模块设计` 决策；只展开实际影响层，未影响层记录 `unchanged` 依据，下层不得静默改写上层。
- 先判断风险维度适用性，再选会改变结论的最小 Skill/角色；行业实践必须说明机制、前提、代价和本地取舍。外部事实不稳定或高风险时才加载 Research。
- `plan` 只表示内容定稿，不授权实现；实质变化保持同一 SPECID 并回到最早受影响阶段。

# Presentation

- 仅在能显著提升理解时保留一张精简架构图和一张关键时序图；图只呈现全局关系、本次边界及关键失败/恢复路径。
- 相关背景简写，关键变更、契约、取舍和验收充分展开；不按模板平均铺陈。

# Workflow

1. **Capture**：提炼问题、目标用户与 job、当前方案假设及事实来源。
2. **Bound**：明确范围、非目标、依赖、约束和会改变方案的开放问题。
3. **Describe Flow**：写主路径、输入、系统反馈、失败态和恢复路径。
4. **Define Acceptance**：冻结可观察的成功、失败、边界、权限和数据验收。
5. **Choose Mode**：普通 brief 直接交付；IDEA 达到最小升级资格后登记；正式 SPEC 进入下述评审循环。
6. **Draft**：查重并建立稳定 SPECID，只登记不变量、背景、目标、核心问题、非目标和必要假设；IDEA 转正时重写原 owner。
7. **Init**：按 `spec-review-gates` 判断七维和五层设计适用性，选择最小 Skill/角色，把当前事实、历史经验、适用行业机制和专家判断压缩为设计约束、候选机制、取舍、风险与验收信号；不提前写完整方案。
8. **Update**：按最高受影响层向下撰写方案，对非显然输入记录 `adopted / adapted / rejected`，只复评未关闭 blocker、改动维度和连锁影响。通过即停止；第 3 轮后先诊断不收敛根因，只有新输入或明确修改目标才继续，单个实质周期最多 10 轮。
9. **Plan Or Stop**：所有适用维度无 blocker、跨维度一致且验收可执行时转 plan；否则保持 update，记录 owner 和最小解除条件。
10. **Return**：返回正文或完整路径、当前阶段/升级资格、评审结论和开放问题。

# Checklist

- 目标、用户/job、范围、非目标、关键流程和验收是否闭合，且未把第一版方案当成目标本身。
- IDEA 是否达到升级门禁但未越权成为正式 SPEC；正式 SPEC 是否独立可读且没有改变核心方案的未决问题。
- draft/init/update/plan 是否各守职责；适用维度、设计层级、输入取舍和 blocker 是否可追溯。
- 是否只加载会改变结论的 Skill/reference，图和内容详略是否服务理解。
- 是否仅写文档工作区 owner，未创建执行包、修改业务仓库或开始实现。

# Escalation

- 生命周期、路径与状态：`skills/Requirements/spec-lifecycle/WORKFLOW.md`
- 实现与验证：`skills/Core/task-execution-lifecycle/WORKFLOW.md`
- 外部事实或公共接口：只加载实际命中的 Research / Engineering Skill。

# References

只在触发时读取：

- 输入模糊、需比较候选方向，或 IDEA 尚未达到最小门禁：`references/idea-shaping-patterns.md`
- 正式 `/hub spec` 的 init/update/plan：`references/spec-review-gates.md`
