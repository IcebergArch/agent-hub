# Skills

每个技能一个目录：

```text
skills/
  <skill-slug>/
    SKILL.md
    scripts/
    references/
```

只有 `SKILL.md` 是必须的。`scripts/` 和 `references/` 只在该技能确实需要时才创建。

已登记技能：

- `agent-team-router`：涉及 AgentOS、Agent infra、AI 技术研究、Product UI/功能/架构、音画同步或互动影游产品时，自动选择最小必要 Agent Team 视角。
- `agent-tool-design`：设计 AI agent 工具、MCP/function calling/tool gateway、工具 schema、执行回执、权限和 eval 时使用。
- `bug-reproduction`：处理 bug、报错、测试失败或行为异常时，先复现症状，再定位根因、最小修复并回归验证。
- `code-review`：审查 diff、PR、提交前改动和测试缺口时，优先找真实 bug、回归风险和共享接口问题。
- `codebase-navigation`：理解陌生仓库、模块、入口、调用链、数据流或目录结构时，先建立真实代码地图。
- `design-principle-library`：完成或复盘架构、coding 分层、系统、UI/交互、数据体系、工具协议等设计后，抽取可复用原则、反模式和检查清单，减少依赖用户手动 rule update。
- `docs-refresh`：写或更新 README、运行手册、迁移指南、架构说明、用户指南和 FAQ 时使用。
- `implementation-plan`：实现前拆方案、评估改动范围、列 touch points、制定工程步骤和验证计划时使用。
- `methodology-harvest`：吸收 Superpowers、外部 agent framework、插件、技能库或工程方法论到 Agent Hub 时使用。
- `paper-record`：用户说 **paper record** 时，将其作为聊天归档收尾动作，把当前对话整合为思考研究报告并写入 `reports/`。
- `interface-contract-audit`：涉及 API/Gateway/route/operation/tool surface 或跨仓库对接契约时，先盘点完整接口面、真实消费者和真实能力，再决定 supported/unsupported/deprecated/internal。
- `prompt-improvement`：优化 prompt、system/developer instructions、agent 指令、输出格式、few-shot 示例或减少幻觉/跑偏时使用。
- `project-content-curation`：整理项目内容、规则、Skills、报告或删除内容时，先分类为 Skill、Rule、Report、Keep 或 Delete，再迁移、保留或清理。
- `refactor-hub`：用户说 `project refactor`、`rule update`、`refactor hub` 或要求从当前对话抽取可复用规则/技能/注意点时，先回看对话和现有 hub，再分析并落位可优化部分。
- `requirements-brief`：把产品、功能、工具、自动化或工作流想法整理成需求简报、PRD、验收标准和非目标边界。
- `source-grounded-research`：结合业界资料、联网调研、事实核对、竞品/技术/论文/官方文档整理，并输出带来源的结论。
- `task-execution-lifecycle`：发布非机械实现、重构、UI、架构、协议、数据、工具链或跨模块任务后，按轻量、标准、高风险三档完成目标定位、验证、review 和交付收口。
- `test-strategy`：设计测试、补回归覆盖、端到端验收、质量门槛和防误报验证方案时使用。

命名建议：

- 技能目录用小写短横线，例如 `runner-analysis`。
- `SKILL.md` frontmatter 的 `description` 只写触发条件，不概括完整流程，避免 Agent 只读描述就跳过正文。
- `description` 和 Trigger 要覆盖真实用户表达、症状词、错误/状态词、对象类型、文件/工具和近义词；如果关键词过宽，要在 Trigger 或“不用在这些场景”里写排除条件。
- `SKILL.md` 正文先写触发场景，再写步骤，再写输出格式；长示例、模式表、脚本说明放到技能内部 `references/`。
- 频繁触发的 Skill 和入口文件要控制上下文预算：只放触发、硬约束、输出形态和 reference 指针；长样例、全量命令说明、历史论证和模式表放到 references 或 reports。
- 公共 Skill/Rule 默认要让 Codex、Cursor 都能读取和执行；依赖某个宿主工具、MCP、浏览器、subagent 或 UI 指令的内容放到 `agents/<tool>.md`，或在公共 Skill 写成能力语义、fallback 或 watch。
- 技能内部引用资源时使用相对路径，方便 Codex、Cursor 都能读取。
- 任务可能触发多个技能时，按需读取 `methodology-harvest/references/skill-trigger-discipline.md`，优先选择最小必要技能集合，不靠记忆或 description 代替当前正文。
- 触发 Skill 后按其 compliance mode 执行：强门禁按 stop rule 和证据要求走，弹性模式按不变量适配，reference 只读相关小节。
- 谨慎读取 Skill 后若发现不适用，按 `Skill False Positive Exit Gate` 简短记录原因并退出，不要因为已经读取就强行套流程。
- 新增或显著修改 Skill 前，先按需读取 `methodology-harvest/references/skill-authoring-patterns.md`，检查触发描述、渐进加载、压力测试和反跑偏机制。
- 新增或显著修改 Skill 后，按 `methodology-harvest/references/skill-authoring-patterns.md` 的 `Skill Change Deployment Gate` 逐项检查索引、触发、引用、压力测试说明和 diff 边界；不要批量写多个 Skill 后才回头验证。
- 导入或改写外部 Skill 时，按需读取 `methodology-harvest/references/external-skill-portability.md`，先把外部工具名、agent manifest、prompt、script 和视觉 helper 映射为 Hub 可执行能力或 no-copy/watch 决策。
