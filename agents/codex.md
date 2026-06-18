# Codex Adapter

- 先遵守根目录 `AGENTS.md`。
- 读取 `README.md` 作为长期协作上下文，不要求用户每次手动附带背景。
- 搜索文件优先用 `rg` 或 `rg --files`。
- 手工编辑文件优先用 patch。
- 在 Codex app 环境中，若用户打开了 in-app browser 或要求验证页面，必须优先使用 Codex 内置浏览器能力进行导航、点击、输入、截图或检查；只有对应浏览器工具未加载、权限受限或目标不可达时，才说明具体限制，不得笼统声称“没有浏览器操作能力”。
- 当消息包含 `# In app browser`、`Current URL`，或用户说“我已经打开了”“直接操作页面”“用 Codex 的 in-app-browser”等表达时，视为当前页已由用户打开并授权直接操作；先加载/使用 Browser 插件控制当前 tab，不用本地端口探测、macOS `open`、外部 Chrome/Playwright 或“页面没开”的猜测替代。若 Browser 插件缺少某项能力，应说清具体缺口并请求最小协助，例如文件选择器无法由运行时直接写入。
- 需要使用技能时，优先读取 `skills/<skill-slug>/SKILL.md`；如果工具支持 `.agents/skills` 自动发现，也应把 `skills/` 作为真实维护位置。
- 读取外部或移植来的 Skill 时，遇到 `Task`、`TodoWrite`、`Read/Edit/Write`、`Bash`、`WebSearch`、agent manifest 或平台 prompt，不照搬外部工具名；先按 `skills/methodology-harvest/references/external-skill-portability.md` 映射到当前 Codex 可用能力，无法等价执行时明确降级或标记为 watch。
- 生成可沉淀的分析、审查、运行记录时，个人单独与 Agent 探讨、设计或研究写入 `reports/personal/agent/<kind>/YYYY-MM-DD-关于<领域>-<topic>.md`；个人项目写入 `reports/personal/projects/<project-slug>/<kind>/YYYY-MM-DD-关于<领域>-<topic>.md`；公司项目写入 `reports/company/projects/<project-slug>/<kind>/YYYY-MM-DD-关于<领域>-<topic>.md`；日期、类型、项目、来源、版式写入文档顶部。
- Agent Hub 的周期性升级、周检或长期复查任务默认保持为独立 `cron` 自动化，并保持主动启用；不要因为当前线程启动了 goal tracking 就改成 heartbeat。goal tracking 只表示当前线程可以承接常驻升级目标，周期性触发仍由独立 cron 负责，除非用户明确要求改成当前线程唤醒。
- 交互结束前只做根规则中的 P0 轻量触发判断；只有明确触发 `receive`、`refactor hub`、反复高风险缺口或用户要求沉淀时，才更新 Codex 专属规则或对应 Skill。
- Codex app 的 `::git-*` 指令是宿主侧 git 动作信号，不是状态汇报文本；只在本轮真实完成对应 git 动作且需要让宿主记录时发送。普通冲突标记 resolved、重新 `git add` 生成物、`git update` 的 no-op merge 或“准备提交但尚未提交”的状态，不应包装成 `::git-commit` / `::git-stage`。若用户要求 `git update`，最终正文保持“merge 完成”式短结论；除非用户明确要求提交、推送或宿主规则强制，否则不要额外输出 git 动作指令。
- Codex sandbox 下 `.git/index` 通常不可写；`git add`、`git restore --staged`、commit、merge、stash 等会改 index 或 refs 的动作如果报 `.git/index.lock` / permission denied，应先判断为 sandbox 权限边界。确需改 index 时，用精确命令申请 escalation；不要把它诊断成 Git 锁损坏，也不要用删除 lock、reset、checkout 等破坏性绕路。
- 不新增 `codex/` 目录；Codex 专属差异只维护在本文件。
