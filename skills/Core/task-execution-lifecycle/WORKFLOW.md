---
name: task-execution-lifecycle
description: 当用户发布实现、重构、UI、架构、协议、数据、工具链或跨模块任务时使用；只负责下一步流程流转。
---

# Purpose

定义任务从接收到完成的阶段顺序，回答“下一步应该做什么”。

# When to Use

- 非机械实现、重构、UI、架构、协议、数据或工具链任务。
- 用户说开始做、继续、do it、`coding`、`fix`、`/hub refactor`、`/hub spec 执行`、`/hub spec 验收`、`/hub save`、按计划执行。
- 任务进入验证、收口、提交、PR 或交付前。

# When NOT to Use

- 只需直接回答的问题、单点 typo、纯文本小改。
- 需要具体领域判断但不涉及执行闭环时，只加载领域 Skill。

# Inputs

- 用户目标、硬边界、当前事实、owner、验收信号、本地改动状态、需要加载的领域 Skill。

# Decision Principles

- SPEC/STDD/计划只是引导，执行前必须用当前页面、API、代码、配置和运行事实校验。
- 正式 SPEC 的实施与验收是两个独立阶段：实施完成只代表具备验收条件，不能代替增量验证和项目级存量回归。
- `/hub save` 采用快速 checkpoint 协议：命令本身授权保护现场、登记已有执行包的进度与待办、停止归属明确的本地服务并整理推送计划；Git 写操作等完整确认单通过后直接执行。
- 只加载能改变本轮决策的 Skill 或 reference。
- `fix` 先用复现、交叉验证和正/逆向推理确认根因，方案明确后才执行最小修复；按 `references/root-cause-fix-patterns.md` 闭环。
- Run Case 与 Build Capability 分开：配置和页面能力能完成时，不擅自改服务层或公共契约。
- 长任务、高模糊设计或历史信息较重时，按 `references/execution-control-patterns.md` 分离当前事实、稳定约束、已废弃历史和冲突信息，并冻结 demo/checkpoint、停止信号与恢复点。
- 完成声明必须有 fresh evidence；错误暴露不能写成业务成功。

# SPEC Command Modes

- `/hub spec 执行 [<SPEC>]`：只消费最终定稿的 plan SPEC，或恢复已有执行包的 execing SPEC。draft、init、update 和 IDEA 不是执行候选。首次修改前建立执行包并冻结范围；按真实运行事实完成修改改造和最窄开发验证，结果写入 STDD，最终保持 `execing` 并给出验收入口。实施阶段不得扩写冻结 SPEC，也不得把“代码已改”写成“验收通过”。
- `/hub spec 验收 [<SPEC>]`：只消费已有执行包的 execing SPEC。验收矩阵至少覆盖冻结验收项、实际 diff 与影响范围、本次新增/变更场景、项目 `ACCEPTANCE.md` 登记的适用存量回归，以及必要的失败与恢复路径；同时证明增量与存量功能正常且符合预期。
- 验收必须记录命令或真实操作路径、环境/版本、断言、结果和证据。只跑新增测试、只做静态检查、mock/dry-run、空数据、跳过步骤或仅暴露错误，都不能替代项目级回归或形成通过结论。
- 验收失败、证据缺失或存量回归未执行时，将问题写入 Bug Pool 并保持 `execing`；验收模式不擅自修改生产实现。全部通过时更新 STDD 验收证据，再按文档工作区完成门禁判断是否可收口。`spec 验收` 不额外授权 commit、push、PR 或 merge。

# Save Command Mode

- `/hub save` 在安全边界冻结新工作，用宿主任务事实和 Git workspace/worktree 状态快速保护现场；只对已有、归属明确的 `execing` 执行包登记真实进度、待办、验证缺口和恢复入口，只优雅停止归属明确的本地服务。
- 准备阶段按隔离空间整理代码记录和推送计划，但不执行 Git 写操作；每个有未合入内容的隔离空间都必须映射到对应远端 work branch 并创建或更新 PR。`doc-hub` 只在主工作目录使用短生命周期 work branch，不创建 linked worktree；历史 linked worktree 必须先建立远端恢复入口，再在干净门禁下收拢。确认单与结果统一使用仓库表格；其它仓库默认止于 PR，随后依次完成 `doc-hub`、`agent-hub` 的 `MR -> main`，agent-hub 最后，并完成各自适用的安全清理。清理条件不满足时保留现场并登记 blocker，不强制删除。确认后直接执行，用户修正则更新并重发完整版本。
- 详细盘点、确认单、执行顺序和恢复门禁见 `references/workspace-save.md`；提交、push 与 PR 质量门禁见 `references/review-git-and-cleanup-gates.md`。

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
- `references/workspace-save.md`
