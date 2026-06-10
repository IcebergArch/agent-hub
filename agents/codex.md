# Codex Adapter

- 先遵守根目录 `AGENTS.md`。
- 读取 `README.md` 作为长期协作上下文，不要求用户每次手动附带背景。
- 搜索文件优先用 `rg` 或 `rg --files`。
- 手工编辑文件优先用 patch。
- 在 Codex app 环境中，若用户打开了 in-app browser 或要求验证页面，必须优先使用 Codex 内置浏览器能力进行导航、点击、输入、截图或检查；只有对应浏览器工具未加载、权限受限或目标不可达时，才说明具体限制，不得笼统声称“没有浏览器操作能力”。
- 需要使用技能时，优先读取 `skills/<skill-slug>/SKILL.md`；如果工具支持 `.agents/skills` 自动发现，也应把 `skills/` 作为真实维护位置。
- 生成可沉淀的分析、审查、运行记录时，个人单独与 Agent 探讨、设计或研究写入 `reports/personal/agent/<kind>/YYYY-MM-DD-关于<领域>-<topic>.md`；个人项目写入 `reports/personal/projects/<project-slug>/<kind>/YYYY-MM-DD-关于<领域>-<topic>.md`；公司项目写入 `reports/company/projects/<project-slug>/<kind>/YYYY-MM-DD-关于<领域>-<topic>.md`；日期、类型、项目、来源、版式写入文档顶部。
- 每次交互结束前执行根规则中的 P0 规则适配检查；Codex 专属经验写入本文件，技能专属经验写入对应 `skills/<skill-slug>/SKILL.md`，不要写入业务上下文。
- Codex app 的 `::git-*` 指令是宿主侧 git 动作信号，不是状态汇报文本；只在本轮真实完成对应 git 动作且需要让宿主记录时发送。普通冲突标记 resolved、重新 `git add` 生成物、`git update` 的 no-op merge 或“准备提交但尚未提交”的状态，不应包装成 `::git-commit` / `::git-stage`。若用户要求 `git update`，最终正文保持“merge 完成”式短结论；除非用户明确要求提交、推送或宿主规则强制，否则不要额外输出 git 动作指令。
- 不新增 `codex/` 目录；Codex 专属差异只维护在本文件。
