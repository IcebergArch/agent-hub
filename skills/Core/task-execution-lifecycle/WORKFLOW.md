---
name: task-execution-lifecycle
description: 当用户发布实现、重构、UI、架构、协议、数据、工具链或跨模块任务时使用；只负责下一步流程流转。
---

# Purpose

定义任务从接收到完成的阶段顺序，回答“下一步应该做什么”。

# When to Use

- 非机械实现、重构、UI、架构、协议、数据或工具链任务。
- 用户说开始做、继续、do it、`coding`、`fix`、按计划执行。
- 任务进入验证、收口、提交、PR 或交付前。

# When NOT to Use

- 只需直接回答的问题、单点 typo、纯文本小改。
- 需要具体领域判断但不涉及执行闭环时，只加载领域 Skill。

# Inputs

- 用户目标、硬边界、当前事实、owner、验收信号、本地改动状态、需要加载的领域 Skill。

# Decision Principles

- SPEC/STDD/计划只是引导，执行前必须用当前页面、API、代码、配置和运行事实校验。
- 只加载能改变本轮决策的 Skill 或 reference。
- `fix` 先用复现、交叉验证和正/逆向推理确认根因，方案明确后才执行最小修复；按 `references/root-cause-fix-patterns.md` 闭环。
- Run Case 与 Build Capability 分开：配置和页面能力能完成时，不擅自改服务层或公共契约。
- 完成声明必须有 fresh evidence；错误暴露不能写成业务成功。

# Workflow

1. Receive：确认最新用户目标、非目标、硬边界和是否已有本地改动。
2. Clarify：只有高风险歧义才问；否则用最小合理假设推进。
3. Understand Context：读取直接相关入口，冻结 owner、事实源、影响范围和验收信号。
4. Execute：沿用现有架构，做最小 coherent diff；新增抽象必须有明确收益。
5. Validate：用最窄有效证据验证；高风险任务补真实 workflow smoke 或负向检查。
6. Knowledge Evolution：若暴露跨场景稳定缺口，交给 `knowledge-evolution` 判断是否沉淀。
7. Finish：报告完成内容、验证证据、范围自检和残余风险。

# Checklist

- 是否回答了当前最新请求，而不是旧上下文。
- 是否区分配置操作、运行 case、能力建设和公共契约变更。
- 是否有新鲜证据支撑“完成 / 可用 / 已打通”。
- 是否保持 diff 聚焦，未扰乱 owner 和领域边界。

# Escalation

- Skill 选择不清：`skills/Core/skill-router/WORKFLOW.md`
- 公共接口或模型工具：`skills/Engineering/interface-contract-audit/WORKFLOW.md` / `skills/Engineering/agent-tool-design/WORKFLOW.md`
- 项目上下文缺失：`skills/Context/project-context-rebuild/WORKFLOW.md`
- 知识沉淀：`skills/Knowledge/knowledge-evolution/WORKFLOW.md`

# References

- `references/completion-evidence-patterns.md`
- `references/case-execution-recovery.md`
- `references/execution-control-patterns.md`
- `references/parallel-agent-coordination.md`
- `references/root-cause-fix-patterns.md`
- `references/review-git-and-cleanup-gates.md`
