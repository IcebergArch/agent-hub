# Skill Library

Skill 是组织经验卡片，不是 Codex 默认能力说明。唯一目录定义为：

```text
skills/<Category>/<skill-slug>/WORKFLOW.md
skills/<Category>/<skill-slug>/references/   # optional
```

默认一次任务只加载 2-4 个 Skill；如果某个 Skill 不会改变本轮决策，就不要加载。

## Directory

| Category | Skill | Use |
| --- | --- | --- |
| Core | `task-execution-lifecycle` | 任务阶段流转与收口 |
| Core | `skill-router` | 选择本轮最小 Skill 组合 |
| Requirements | `requirements-brief` | 需求简报、IDEA、正式 SPEC 和验收边界 |
| Requirements | `spec-lifecycle` | 项目观察登记及 SPEC 的 draft/init/update/plan、执行包和归档流转 |
| Navigation | `codebase-navigation` | 业务模型、owner 和影响范围建立 |
| Research | `source-grounded-research` | 来源可信度、交叉验证和结论形成 |
| Prompt | `prompt-improvement` | 统一输出规范和 prompt 资产整理 |
| Engineering | `agent-tool-design` | Agent tool / MCP / function calling 设计原则 |
| Engineering | `interface-contract-audit` | API / Gateway / route / tool surface 契约审计 |
| Engineering | `observability-system-design` | 监控、事件聚合、修复与有界自愈体系建设 |
| Context | `project-context-rebuild` | 项目 Context Health Check 与重建 |
| Context | `project-content-curation` | 内容归位、迁移、保留和删除 |
| Knowledge | `knowledge-evolution` | 知识沉淀、升级和去重 |
| Domain | `agent-team-router` | AgentOS / Product / AI / media 领域角色路由 |
| Domain | `video-creation` | 视频创作、拉片、timeline 和产物验收 |

## Maintenance Rules

新增任何 Skill 前，先回答：

1. 这是不是 Codex 已经擅长的能力？如果是，不新增。
2. 这是长期稳定的组织经验，还是一次性项目经验？一次性经验放 Context。
3. 能否扩展已有 Skill，而不是新增一个？
4. 是否可以通过 Router 按需加载，而不是默认加载？
5. 如果一年后模型能力提升，这份 Skill 是否仍然有价值？
6. description / 入口指针是否写出真实触发分支；高波动步骤是否有可观察且足够完整的完成条件？

## Template

```markdown
# Purpose
# When to Use
# When NOT to Use
# Inputs
# Decision Principles
# Workflow
# Checklist
# Escalation
# References
```
