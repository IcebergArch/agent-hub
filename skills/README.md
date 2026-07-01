# Skills

每个 Skill 是一张短执行卡：触发、少数可组合 SOP、输出和硬门禁。长检查表、示例、矩阵、脚本说明放到同目录 `references/`，不塞进 `SKILL.md`。

```text
skills/<skill-slug>/
  SKILL.md
  references/   # optional
  scripts/      # optional
```

## Owner Map

| Owner | Skill | Primary Use |
| --- | --- | --- |
| Task execution | `task-execution-lifecycle` | 实现、重构、UI、架构、协议、数据、工具链任务的执行闭环 |
| Requirements | `requirements-brief` | 模糊想法、产品/功能/workflow 需求和验收标准 |
| Planning | `implementation-plan` | 需求已明确后，拆工程方案、touch points 和验证计划 |
| Navigation | `codebase-navigation` | 理解陌生代码、入口、调用链、数据流 |
| Bugfix | `bug-reproduction` | 报错、测试失败、flaky、timeout、root cause 和回归验证 |
| Review | `code-review` | diff/PR/提交前风险、反馈处理、卡住时换视角 |
| Testing | `test-strategy` | 测试设计、回归覆盖、E2E 验收、防误报 |
| Docs | `docs-refresh` | README、runbook、迁移指南、架构说明、用户指南 |
| Research | `source-grounded-research` | 联网调研、官方文档、论文、竞品、带来源结论 |
| Prompt | `prompt-improvement` | prompt / agent instructions / 输出格式 / few-shot 优化 |
| Tool design | `agent-tool-design` | Agent tool、MCP、function calling、schema、回执、权限 |
| Contract audit | `interface-contract-audit` | API/Gateway/route/tool surface、SDK、跨服务契约 |
| Product roles | `agent-team-router` | AgentOS、Agent infra、Product UI、音画同步、互动影游的角色视角 |
| Video | `video-creation` | 视频策划、拉片、shot list、timeline、AI 视频 workflow |
| Hub curation | `project-content-curation` | 内容分类、迁移、保留、删除、helper/report 边界 |
| Hub refactor | `refactor-hub` | 从当前对话回流可复用规则、Skill 或注意点 |
| Self evolution | `self-evolution-engine` | 判断稳定经验是否应升级为 repo-portable 机制 |
| Design principles | `design-principle-library` | 高风险设计复盘后的原则沉淀路由 |
| Methodology | `methodology-harvest` | 吸收外部 agent framework / plugin / skill library / 工程方法论 |
| Archive | `paper-record` | `paper record` 聊天归档收尾 |

## Boundary Rules

- `requirements-brief` 定义要做什么；`implementation-plan` 定义怎么做；`task-execution-lifecycle` 负责执行闭环。
- `agent-tool-design` 设计模型可调用工具；`interface-contract-audit` 审计对外契约面和真实消费者。
- `project-content-curation` 做内容归类；`refactor-hub` 做对话经验回流；`self-evolution-engine` 只判断是否值得升级为长期机制。
- `design-principle-library` 不存放各领域长原则；它把原则路由回专门 Skill/reference。
- `source-grounded-research` 做事实调研；`methodology-harvest` 只处理外部方法论吸收到 Hub。
- 外部组件迁移表、版本审计、缓存路径和一次性对照账本不属于 Skill 运行面；需要暂存时放 `/Users/shatang/Documents/temp/`，需要长期引用时写成正式报告或 Hub-native 规则。

## Maintenance Rules

- 新增 Skill 前先找现有 owner；能合并就不新增。
- Skill 不是场景条件树；如果只是在 “A 场景下的 B 情况” 补规则，优先改成既有 SOP 的参数、stop rule 或 reference 示例。
- 高频 `SKILL.md` 控制在短执行卡级别；长内容进入 `references/` 或文档工作区报告。
- Reference 也应以地图、矩阵、检查表为主；超过约 150 行时优先压缩或拆出更明确的小 owner。
- Skill 数量增长可以是探索期现象；长期应定期合并、删除或降级被模型能力、通用规则或更高阶 Skill 覆盖的低价值 Skill。
- 删除或合并 Skill 前，搜索触发词、入口引用、reference 和工具发现约定。
- 修改 Skill 后同步本索引、相关入口和旧触发词引用，并运行 `git diff --check` / 引用搜索。
