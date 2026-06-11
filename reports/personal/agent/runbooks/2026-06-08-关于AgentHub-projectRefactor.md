# Project Refactor

日期：2026-06-08
类型：runbooks
项目：personal-agent
来源：agent-hub project refactor 指令整理
版式：清单结构，面向人工翻阅与 LLM 检索

## 使用方式

当用户、Codex、Cursor 或其他 LLM 需要快速确认 agent-hub 的 project refactor 协作规则时，先读本清单，再回到 `AGENTS.md`、`README.md` 和对应 Skill 查看完整规则。本文只做索引和摘要，不替代入口规则。

## 已有指令

| 指令 | 含义 | 主要入口 |
| --- | --- | --- |
| `project refactor` | 整理当前项目或 hub 的指令、报告、规则、Skills 和目录结构，按归属落位 | 本清单、`skills/refactor-hub/SKILL.md`、`skills/project-content-curation/SKILL.md` |
| `git update` | 更新主分支并把最新主分支合入当前分支；保护本地改动，必要时解决冲突和验证 | 本清单的 Git Update 详细流程、`AGENTS.md` 的短触发规则 |
| `git diff` | 只读审查 staged / unstaged diff，按来源分层汇报，不修改文件 | `AGENTS.md` 的 Pre-commit Diff Review |
| `receive` | 从当前对话抽取可复用规则、技能或报告，更新 agent-hub | `skills/refactor-hub/SKILL.md` |
| `paper record` | 将当前聊天冻结为思考研究报告并更新 reports 索引 | `skills/paper-record/SKILL.md` |

## 入口读取

- 先读 `AGENTS.md`，再读 `README.md`，再读当前工具适配文件：Codex 读 `agents/codex.md`，Cursor 读 `agents/cursor.md`。
- 任务触发 Skill 时，读取 `skills/<skill-slug>/SKILL.md`；不要只凭技能名猜流程。
- 项目相关任务先按归属查 reports：个人项目查 `reports/personal/projects/<project-slug>/`，公司项目查 `reports/company/projects/<project-slug>/`。
- 个人单独与 Agent 的探讨、研究和设计沉淀查 `reports/personal/agent/`。

## 渐进式加载

- 除报告文件外，内部入口文件、工具适配文件和 Skill 不写成大而全的长文。
- `AGENTS.md`、`README.md`、`agents/<tool>.md` 和 `skills/*/SKILL.md` 只保留触发条件、判断标准、短流程和指向细节的链接。
- 长流程、完整命令序列、历史上下文、示例矩阵、设计论证和项目细节放到 reports；LLM 先读入口，再按任务需要加载对应报告或 Skill。
- 如果某个非报告文件持续变大，优先拆分为：短入口规则 + 具体 Skill + reports runbook / analysis，而不是继续在入口文件追加段落。

## 执行预算

- 默认走最小足够闭环：最小读取、最小修改、最小验证。
- 轻量任务不自动触发外部研究、完整 review、main sync、报告沉淀或分支收尾。
- 标准任务冻结关键不变量后完成可验证闭环，验证和 diff 审查按风险裁剪。
- 高风险任务才升级到长 reference、测试 Agent / review 视角、外部参考和更宽验证。
- 每次增加一个 Skill、reference、subagent、报告或 git 收尾动作，都要能说明它降低了哪类返工或风险。

## 内容归属

- 跨工具通用规则写入 `AGENTS.md`。
- Codex 或 Cursor 专属差异写入 `agents/<tool>.md`。
- 可重复触发、有步骤和输出形态的流程写入 `skills/<skill-slug>/SKILL.md`。
- 个人 Agent 探讨写入 `reports/personal/agent/<kind>/`。
- 个人项目报告写入 `reports/personal/projects/<project-slug>/<kind>/`。
- 公司项目报告写入 `reports/company/projects/<project-slug>/<kind>/`，默认只在本地保留。
- `README.md` 只沉淀稳定长期上下文，不写具体项目、公司项目或业务域专属设计规则。

## 归档命名

- 报告文件名使用 `YYYY-MM-DD-关于<领域>-<topic>.md`。
- 报告顶部写明：`日期`、`类型`、`项目`、`来源`，讨论型文档补充 `版式`。
- `kind` 常用值：`analysis`、`review`、`chat`、`feature`、`fix`、`refactor`、`runbooks`。
- 讨论型文档默认结论先行；图表只用于降低理解成本，不为了凑格式而添加。

## 触发 Skill

- 用户说 `paper record`：执行 `skills/paper-record/SKILL.md`，只做当前聊天归档和索引更新。
- 用户说 `project refactor`、`receive`、`refactor hub`、优化 hub 或要求抽取可复用规则：执行 `skills/refactor-hub/SKILL.md`。
- 用户要求整理项目内容、规则、Skills、报告或删除内容：执行 `skills/project-content-curation/SKILL.md`。
- 任务涉及 API、route、operation、tool surface 或跨仓库契约：执行 `skills/interface-contract-audit/SKILL.md`。
- 任务涉及 AgentOS、Agent infra、AI 技术、Product UI/功能/架构、音画同步或互动影游：执行 `skills/agent-team-router/SKILL.md`。

## 项目与公司内容边界

- 任何来自具体项目的经验，先判断能否抽象为跨任务流程约束；不能抽象的，只写入对应 reports。
- 公司项目内容不得写入 `README.md`、`AGENTS.md`、工具适配文件或 Skill。
- `reports/company/projects/` 默认由 `.gitignore` 隔离，不进入提交。
- 公司项目报告只有在明确属于开源项目，并完成 diff 审查确认无私有信息时，才允许单独纳入提交。
- 根索引 `reports/index.md` 不列公司项目具体条目，只保留隔离说明。

## 提交前审查

- 提交、暂存、交付或 amend 前，至少检查：`git status --short`、`git diff --name-status`、`git diff --stat`、`git diff --cached --name-status`、`git diff --cached --stat`。
- 按来源分层：本轮必要实现、测试/文档/配置、生成产物、工具缓存或临时文件、用户已有改动、无关漂移。
- 提交前确认没有 `.DS_Store`、`reports/company/projects/`、旧报告路径或被忽略的本地内容进入提交。
- amend 或 force push 前，检查最终提交树，例如 `git ls-tree -r --name-only HEAD`。
- 只暂存本轮必要内容；无关改动保持原样，不擅自回滚。

## Git Update 详细流程

- 当用户说 `git update` 时，将其视为同步主分支的协作指令，而不是 Git 子命令。目标是高效完成：更新 `main`，把最新 `main` merge 到当前分支，解决冲突，并保留用户本地改动。
- 先做快速预检：确认当前仓库、当前分支、远端、`main` / `origin/main` 指向和工作区状态。推荐命令：`git branch --show-current`、`git status --short --branch`、`git remote -v`、`git rev-parse main`、`git rev-parse origin/main`。如果当前目录不是用户目标仓库，先切到用户指定仓库；不要同时操作多个仓库。
- merge 前输出并核对方向：`当前分支 <- 目标分支`。默认目标为 `origin/main` / `main`；如果当前分支或目标分支推断不清、当前分支是保护分支、目标分支不是默认主分支/用户指定分支，或用户表达可能指向另一个仓库/分支，先请求确认。方向清楚且风险检查通过时，直接进入 merge，不把确认变成固定二次等待。
- 更新 `main` 时优先避免无意义切分支：先 `git fetch origin main`，比较 `FETCH_HEAD`、`origin/main` 和本地 `main`。若本地 `main` 落后且可快进，更新本地 `main`；若本地 `main` 有未推送分叉，停止并说明，不强行覆盖。
- merge 前先判断是否必要：执行 `git merge-base --is-ancestor main HEAD`。如果返回成功，说明当前分支已经包含最新 `main`，不要创建空 merge，也不要为了流程而 stash；只汇报 no-op 和当前状态。
- 只有在确实需要 merge 且工作区本地改动会阻塞时，才使用临时 stash。stash 前必须记录 staged / unstaged 清单；stash 名称使用可识别前缀，例如 `codex-git-update-temp`。优先尝试在干净工作区 merge；不要默认把所有任务都 stash 一遍。
- 若使用临时 stash：完成 `git merge main` 后先处理 merge 冲突并提交 merge；再恢复 stash。恢复优先尝试 `git stash pop --index`，失败时改用普通 apply，并重新整理 staged 状态。确认 stash 内容已经完整恢复后再删除临时 stash。
- 解决冲突时按文件类型处理：普通源码用最小语义修复；IDL、API client、schema、snapshot 等生成文件不要手工挑冲突块，应先修事实源，再运行项目规范生成命令重建生成物；随后搜索冲突标记并重新暂存。
- merge 后必须重新做 diff 审查：确认 `main` 已是当前 `HEAD` 祖先、没有未解决冲突、没有临时 stash、没有临时服务或缓存遗留；列出 staged / unstaged 状态和 merge 过程中新增的语义修复。
- 验证从窄到宽：至少运行受 merge 影响的类型检查、单测或 smoke；如果 merge 触碰前后端契约、runtime、gateway、生成文件或 UI workflow，必须验证对应真实消费者，而不只看编译。
- 输出口径：`git update` 的默认交付结果只有 merge 是否完成。成功、无新提交或 `Already up to date` 都收敛为“merge 完成”；只有冲突未解决、工作区风险、目标分支不清或验证失败时才展开原因、状态和下一步。不要把预检、fetch、ahead/behind、stash 细节当成成功路径的最终报告主体。

## 验证口径

- 区分“入口可打开 / route 被调用 / gateway wiring 正确 / 错误被暴露”和“端到端能力可用”。
- 只有目标 workflow 返回成功业务结果并满足最小验收条件时，才说“已打通”“可用”或“验证通过”。
- 如果 smoke 只证明错误态被正确展示，应写成“错误暴露/缺口确认”。
- 对跨模块、UI、runtime、tool、MCP 或 gateway 改动，最终按链路分层说明验证状态和阻塞层。

## 外部参考

- 涉及快速演进领域时，先读本地长期上下文，再核对官方文档、规格、release notes、论文或可信工程案例。
- 用户明确要求“联网”或“不要参考本地”时，本地文件只用于读取仓库规则或定位问题，不能作为事实来源。
- 输出方案时区分：已核对的外部事实、本项目推断、采纳或不采纳的取舍。

## 清理与删除

- 删除前搜索旧路径、旧标题和旧触发词，确认没有仍需保留的入口、引用或兼容约定。
- `.DS_Store`、IDE 状态、临时日志、重复旧入口、未引用废弃内容和未采纳草稿属于 Delete 候选。
- 清理后重新搜索旧名称、mock、stub、fallback、临时文件和过渡变量。
