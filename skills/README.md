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
- `docs-refresh`：写或更新 README、运行手册、迁移指南、架构说明、用户指南和 FAQ 时使用。
- `implementation-plan`：实现前拆方案、评估改动范围、列 touch points、制定工程步骤和验证计划时使用。
- `paper-record`：用户说 **paper record** 时，将其作为聊天归档收尾动作，把当前对话整合为思考研究报告并写入 `reports/`。
- `interface-contract-audit`：涉及 API/Gateway/route/operation/tool surface 或跨仓库对接契约时，先盘点完整接口面、真实消费者和真实能力，再决定 supported/unsupported/deprecated/internal。
- `prompt-improvement`：优化 prompt、system/developer instructions、agent 指令、输出格式、few-shot 示例或减少幻觉/跑偏时使用。
- `project-content-curation`：整理项目内容、规则、Skills、报告或删除内容时，先分类为 Skill、Rule、Report、Keep 或 Delete，再迁移、保留或清理。
- `refactor-hub`：用户说 `project refactor`、`rule update`、`refactor hub` 或要求从当前对话抽取可复用规则/技能/注意点时，先回看对话和现有 hub，再分析并落位可优化部分。
- `requirements-brief`：把产品、功能、工具、自动化或工作流想法整理成需求简报、PRD、验收标准和非目标边界。
- `source-grounded-research`：结合业界资料、联网调研、事实核对、竞品/技术/论文/官方文档整理，并输出带来源的结论。
- `test-strategy`：设计测试、补回归覆盖、端到端验收、质量门槛和防误报验证方案时使用。

命名建议：

- 技能目录用小写短横线，例如 `runner-analysis`。
- `SKILL.md` 先写触发场景，再写步骤，再写输出格式。
- 技能内部引用资源时使用相对路径，方便 Codex、Cursor 都能读取。
