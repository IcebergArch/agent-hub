# Codex Adapter

- 先遵守根目录 `AGENTS.md`。
- 读取 `README.md` 作为长期协作上下文，不要求用户每次手动附带背景。
- 搜索文件优先用 `rg` 或 `rg --files`。
- 手工编辑文件优先用 patch。
- 需要使用技能时，优先读取 `skills/<skill-slug>/SKILL.md`；如果工具支持 `.agents/skills` 自动发现，也应把 `skills/` 作为真实维护位置。
- 生成可沉淀的分析、审查、运行记录时，个人单独与 Agent 探讨、设计或研究写入 `reports/personal/agent/<kind>/YYYY-MM-DD-关于<领域>-<topic>.md`；个人项目写入 `reports/personal/projects/<project-slug>/<kind>/YYYY-MM-DD-关于<领域>-<topic>.md`；公司项目写入 `reports/company/projects/<project-slug>/<kind>/YYYY-MM-DD-关于<领域>-<topic>.md`；日期、类型、项目、来源、版式写入文档顶部。
- 每次交互结束前执行根规则中的 P0 规则适配检查；Codex 专属经验写入本文件，技能专属经验写入对应 `skills/<skill-slug>/SKILL.md`，不要写入业务上下文。
- 不新增 `codex/` 目录；Codex 专属差异只维护在本文件。
