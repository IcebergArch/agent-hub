---
name: codebase-navigation
description: 当用户要求理解项目、业务模型、修改边界、ownership 或影响范围时使用。
---

# Purpose

建立真实项目地图，帮助判断业务模型、owner、修改边界和影响范围。

# When to Use

- “先看仓库/模块”“梳理结构”“功能在哪里”。
- 需要确认业务模型、数据流、入口、依赖方向或不该改的边界。
- 执行前必须理解陌生模块或跨模块影响。

# When NOT to Use

- 目标文件和改动方式已经明确的小修。
- 只需要通用搜索、grep、Git 或 IDE 操作技巧。

# Inputs

- 用户给定路径、目标功能、相关页面/API/命令、已有文档和当前变更边界。

# Decision Principles

- 用户给出的路径或范围是硬边界。
- 文件名相似不等于调用关系；owner 必须由入口、依赖、配置、测试或运行事实支撑。
- 先建立业务模型，再决定修改点。

# Workflow

1. Boundary：确认用户目标、路径范围和禁止触碰的区域。
2. Entry Model：找业务入口、运行入口、页面/API/命令入口和配置入口。
3. Ownership：标出模块职责、数据事实源、资源 owner、调用 owner 和测试 owner。
4. Flow：追关键路径上的输入、状态、输出、失败态和外部集成。
5. Impact：列出直接影响、间接消费者、兼容风险和需要验证的边界。
6. Decision：给出推荐修改位置、非目标区域和不确定点。

# Checklist

- 是否能说明“为什么这里是 owner”。
- 是否区分业务事实源、缓存/registry、展示层和 adapter。
- 是否列出影响范围和验证入口。

# Escalation

- 涉及公共接口或跨服务契约：`skills/Engineering/interface-contract-audit/WORKFLOW.md`
- 项目背景缺失或过期：`skills/Context/project-context-rebuild/WORKFLOW.md`
- 需要外部事实：`skills/Research/source-grounded-research/WORKFLOW.md`

# References

- None.
