---
name: task-execution-lifecycle
description: 当用户发布实现、重构、UI、架构、协议、数据、工具链或跨模块任务，或进入验证、收口、PR、保存阶段时使用；负责路由下一步执行流程。
---

# Purpose

定义执行任务的最小阶段顺序，并把高成本分支路由到按需 reference。

# When to Use

- 非机械实现、重构、UI、架构、协议、数据或工具链任务。
- 用户说开始做、继续、do it、`coding`、`fix`、`/hub refactor`、`/hub spec-exec`、`/hub spec-smoke`、`/hub pr`、`/hub save` 或按计划执行。
- 任务进入验证、收口、提交、PR 或交付前。

# When NOT to Use

- 直接回答、单点 typo 或纯文本小改。
- 只做需求/SPEC 建设且不进入实现。

# Inputs

- 用户目标、硬边界、当前事实、owner、验收信号、工作区状态和本轮真实命中的风险分支。

# Decision Principles

- SPEC、STDD 和计划只提供目标与索引；执行前必须用当前页面、API、代码、配置和运行事实校验。
- 先区分配置操作、Run Case 与 Build Capability；现有配置或页面能力能完成时，不扩大到服务层或公共契约。
- 多步实现优先拆成每片可验证的纵向切片；机械性宽迁移才使用 `expand -> migrate -> contract`。
- 只加载会改变本轮动作或验收结论的 reference；未命中的高成本分支不读。
- 服务、Git、PR 和部署权限以命中的 command reference 为准，不从相邻阶段继承。
- 完成声明必须有 fresh evidence；错误暴露、静态检查或旧证据不能替代真实成功。

# Workflow

1. **Receive**：确认最新目标、非目标、硬边界和已有改动。
2. **Resolve Route**：识别当前命令、风险与领域，只加载下表命中的 reference 和必要 Skill。
3. **Understand**：读取直接事实，冻结 owner、影响范围、不变量和验收信号；高风险歧义才询问。
4. **Execute**：沿用现有架构，实施最小 coherent diff；新增抽象必须有明确收益。
5. **Validate**：先跑受影响 gate，修复后只重跑受影响项；结束前再做一次适用的完整验收与 diff 检查。
6. **Record**：结果、问题、验证和恢复入口只写入各自唯一 owner；跨场景知识候选交给 `knowledge-evolution`。
7. **Finish**：报告真实终态、fresh evidence、范围自检和残余风险；未通过则给出 blocker 与最小解除条件。

# Checklist

- 是否回答最新请求，并区分配置、运行 case、能力建设和公共契约。
- 是否只加载命中的分支，没有为形式读取所有 reference。
- 是否有 fresh evidence 支撑完成或可用结论。
- 是否保持 diff 聚焦、owner 清晰，且未越权执行服务、Git 或部署动作。

# Escalation

- Skill 选择不清：`skills/Core/skill-router/WORKFLOW.md`
- 公共接口或模型工具：对应 Engineering Skill。
- 项目上下文损坏：`skills/Context/project-context-rebuild/WORKFLOW.md`
- 知识沉淀：`skills/Knowledge/knowledge-evolution/WORKFLOW.md`

# References

只在触发条件成立时读取：

| 触发 | Reference |
| --- | --- |
| `/hub spec-exec` 或 `/hub spec-smoke` | `references/spec-execution-and-smoke.md` |
| `coding`、`/hub refactor`、`/hub pr` 或 Git/PR 收口 | `references/review-git-and-cleanup-gates.md` |
| `/hub save` | `references/workspace-save.md`；确认后涉及 Git/PR 时再读 review/git reference |
| `fix` 或根因未明的故障 | `references/root-cause-fix-patterns.md` |
| 页面、Chat/MCP、附件、文件或素材 Run Case | `references/case-execution-recovery.md` |
| 长任务、高模糊设计、历史冲突或需要 checkpoint | `references/execution-control-patterns.md` |
| 可证明独立的并行域 | `references/parallel-agent-coordination.md` |
| 高风险完成声明或证据 freshness 争议 | `references/completion-evidence-patterns.md` |
