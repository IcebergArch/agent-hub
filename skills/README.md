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
- `paper-record`：用户说 **paper record** 时，将其作为聊天归档收尾动作，把当前对话整合为思考研究报告并写入 `reports/`。
- `interface-contract-audit`：涉及 API/Gateway/route/operation/tool surface 或跨仓库对接契约时，先盘点完整接口面、真实消费者和真实能力，再决定 supported/unsupported/deprecated/internal。
- `project-content-curation`：整理项目内容、规则、Skills、报告或删除内容时，先分类为 Skill、Rule、Report、Keep 或 Delete，再迁移、保留或清理。
- `refactor-hub`：用户说 `rule update`、`refactor hub` 或要求从当前对话抽取可复用规则/技能/注意点时，先回看对话和现有 hub，再分析并落位可优化部分。

命名建议：

- 技能目录用小写短横线，例如 `runner-analysis`。
- `SKILL.md` 先写触发场景，再写步骤，再写输出格式。
- 技能内部引用资源时使用相对路径，方便 Codex、Cursor 都能读取。
