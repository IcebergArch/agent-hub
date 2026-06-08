# Cursor Adapter

- 先遵守根目录 `AGENTS.md`。
- Cursor 的项目规则只保留在 `.cursor/rules/`，用于引用本仓库的共同入口。
- 不在 `.cursor/rules/` 里复制长规则；长规则应放回 `AGENTS.md` 或本文件。
- 需要上下文时，优先通过 `@AGENTS.md`、`@README.md`、`@agents/cursor.md`、`@skills/<skill-slug>/SKILL.md`、`@reports/personal/agent/...`、`@reports/personal/projects/<project-slug>/...` 或 `@reports/company/projects/<project-slug>/...` 引用。
- 生成长期报告时，写入 `reports/`，不要创建 Cursor 专属内容目录。
- 用户说 **paper record** 时，将其作为聊天归档收尾动作，执行 `skills/paper-record/SKILL.md`。
