---
name: skill-router
description: 根据任务类型选择本轮应加载的最小 Skill 集合，避免默认加载全部 Skill。
---

# Purpose

用最小 Skill 组合服务当前任务，默认一次只加载 2-4 个 Skill。

# When to Use

- 任务可能命中多个 Skill。
- 用户要求重构、研究、MCP/tool、视频、上下文恢复或 Hub 知识沉淀。
- 任务开始前需要判断是否还要加载领域 Skill。

# When NOT to Use

- 单文件小改、明确路径的机械编辑、直接回答问题。
- 已有上层指令明确指定 Skill。

# Inputs

- 用户目标、硬边界、风险信号、是否涉及外部事实、接口/tool、项目上下文或知识沉淀。

# Decision Principles

- Skill 只承载组织经验；不要为 Codex 默认能力加载 Skill。
- 如果某个 Skill 不会改变本轮决策，不加载。
- 优先一个主 Skill，加 1-3 个必要辅助 Skill。

# Workflow

1. 识别任务类型：执行、需求、导航、研究、prompt、tool/contract、observability、context、knowledge、domain。
2. 执行类任务先选 `task-execution-lifecycle`。
3. Bug 或普通修复只加 `codebase-navigation`，不加载 Bugfix Skill。
4. Research 任务组合 `task-execution-lifecycle` 与 `source-grounded-research`。
5. MCP/tool 任务组合 `agent-tool-design` 与 `interface-contract-audit`。
6. 监控、告警聚合、故障修复或自愈体系设计使用 `observability-system-design`；只查日志、处理一次事故或机械添加既定指标时不加载。
7. Video 任务组合 `task-execution-lifecycle` 与 `video-creation`。
8. Hub 维护、规则沉淀、外部方法吸收统一走 `knowledge-evolution`。
9. 正式 SPEC 建设固定使用 `requirements-brief` 管内容、`spec-lifecycle` 管状态；只有适用性或风险命中时才追加 requirements 的 spec-review reference、领域、导航、Engineering 或 Research。`spec-exec`、`spec-smoke`、`pr` 转入 `task-execution-lifecycle` 的对应 command reference，不重复加载需求建设流程。

# Checklist

- 本轮 Skill 数量是否不超过必要的 2-4 个。
- 是否避免加载 Bugfix、Review、Testing、Docs 这类默认能力 Skill。
- 是否区分了执行 Skill、领域 Skill 和知识沉淀 Skill。
- SPEC 评审的每个适用维度是否有 Skill 或角色 owner；N/A 是否有影响面理由，而不是因为没有对应 Skill 就跳过。

# Escalation

- 公共接口或模型工具变更：加载 Engineering 相关 Skill。
- 外部事实：加载 Research。
- 沉淀或重构 Hub：加载 Knowledge。

# References

- `skills/README.md`
