---
name: project-context-rebuild
description: 当项目背景缺失、过期、冲突或需要 Context Health Check 与重建时使用。
---

# Purpose

从真实事实源重建项目运行时背景，让后续任务能快速找到 owner、入口、边界和验收方式。

# When to Use

- 用户要求重建项目背景、恢复项目资料或检查上下文健康度。
- 文档工作区 helper 缺失、过期、不可信或与当前事实冲突。
- README、SPEC、helper、代码或运行状态互相冲突，导致无法可靠继续。

# When NOT to Use

- 项目背景已足够，直接读少量现有 helper 即可。
- 只是为一次任务写完整历史报告。

# Inputs

- 项目 slug、目标仓库/目录、文档工作区入口、当前任务、现有 helper/SPEC/README、代码事实和运行事实。

# Decision Principles

- 项目专属事实只进文档工作区，不进 Hub 通用规则。
- helper 短而可执行；完整证据和历史进入 report。
- 每条关键背景必须能回到来源；无法确认就标待确认。

# Workflow

1. Health Check：检查 Context 是否过期、Helper 是否缺失、SPEC 是否冲突、README 是否失效、是否需要重建。
2. Scope：确认项目、路径、用户硬边界、当前任务和必须恢复的验收信号。
3. Workspace Map：读取文档工作区入口，确认 helper、SPEC、report 和命名规则。
4. Sources：收集 README、AGENTS/adapter、package/build、路由/API/schema、tests、scripts、deploy/env 示例、docs、git remote 和用户点名材料。
5. Runtime Facts：确认模块职责、owner、数据事实源、关键接口、常用命令、危险操作和不该改的边界。
6. Shape：先写最小 helper，再补公共 SPEC；完整论证只进 report。
7. Confidence：标注来源、证据类型和待确认项。
8. Replay：用当前任务回放检查后续 Agent 是否能启动。

# Checklist

- Context Health Check 五项是否完成。
- helper 是否短、可执行、来源清楚。
- 是否没有用模型记忆或文件名猜测补关键事实。

# Escalation

- 需要代码 owner/影响范围：`skills/Navigation/codebase-navigation/WORKFLOW.md`
- 涉及契约或工具：Engineering 相关 Skill。
- 重建结果需要沉淀方法：`skills/Knowledge/knowledge-evolution/WORKFLOW.md`

# References

- None.
