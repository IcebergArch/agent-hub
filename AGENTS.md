# Shared Agent Instructions

这个仓库是 Codex、Cursor 共用的 Agent 工作资产中心。默认目标是让 Agent 用最少上下文读到正确规则，也让人能快速看懂 Hub 结构。

## Core Structure

- `agents/`：工具适配层，只放 Codex、Cursor 等宿主差异。
- `skills/`：可复用 workflow，格式为 `skills/<skill-slug>/SKILL.md`；长检查表放同技能 `references/`。
- `/Users/shatang/Documents/workspace/`：文档工作区入口；helper、报告和项目文档目录以该目录下的 `README.md` 为准。
- `/Users/shatang/Documents/temp/`：临时材料入口；过度产物、迁移账本、一次性对照和缓存清单放这里，不进入 Hub 或正式 workspace。
- `.agents/skills` 是兼容发现入口，真实维护源仍是 `skills/`。

当前长期维护范围只包括 Codex 和 Cursor。除非用户明确要求，不恢复 OpenCode，也不新增 `knowledge/`、`templates/`、`attachments/`、`archive/` 等目录。

## Core Laws

1. **实事求是**：先尊重真实需求、上下文、证据、能力边界和产物状态，再抽象规则或执行方案。
2. **精简有效**：入口文件只保留硬约束、触发索引和短规则；Skill 应是可组合 SOP，不写成场景树；长流程、示例、矩阵和历史证据下沉到 reference 或文档工作区报告。
3. **分层承载**：通用约束放 `AGENTS.md`，工具差异放 `agents/`，可重复流程放 `skills/`，项目背景和报告放文档工作区。
4. **按需加载**：默认只读必要入口；Skill、reference、helper、外部搜索和 subagent 必须由任务类型、风险信号或用户要求触发。
5. **可验证沉淀**：新增或保留规则前，必须能说清触发条件、保护的不变量、检查工件、例外和验证方式。

## Loading

默认读取顺序：

1. 读取本文件。
2. 读取 `README.md` 作为长期协作上下文。
3. 按当前工具读取一个适配文件：Codex 读 `agents/codex.md`，Cursor 读 `agents/cursor.md`。
4. 命中任务信号后，再读取对应 `skills/<skill-slug>/SKILL.md`；reference 只在 Skill 明确需要或风险命中时读取。
5. 需要项目背景、helper 更新或报告写入时，先读文档工作区 `README.md` 定位目录；报告目录不作为默认上下文来源。

常用触发路由：

| Signal | Read |
| --- | --- |
| AgentOS、Agent infra、AI 技术/协议/架构、Product UI/功能/架构、音画同步、互动影游、`Agent Team` / `IT Agent Team` 且需要判断 | `skills/agent-team-router/SKILL.md` |
| 非机械实现、重构、UI、架构、协议、数据、工具链或跨模块任务 | `skills/task-execution-lifecycle/SKILL.md` |
| 整理项目内容、规则、Skills、报告、长期上下文、删除内容 | `skills/project-content-curation/SKILL.md` |
| `project refactor`、`receive`、`refactor hub`、优化/整理 hub、抽取可复用规则或方法 | `skills/refactor-hub/SKILL.md` |
| 新增、删除、改名或暴露 API/Gateway/route/operation/tool surface，跨仓库/跨 agent 契约 | `skills/interface-contract-audit/SKILL.md` |
| 设计 AI agent tool、MCP/function calling、tool gateway、schema、回执、权限 | `skills/agent-tool-design/SKILL.md` |
| 研究、业界优秀实践、最新资料、官方文档、论文、竞品或需要来源链接 | `skills/source-grounded-research/SKILL.md` |
| 吸收外部 agent framework、插件、技能库或工程方法论到 Hub | `skills/methodology-harvest/SKILL.md` |
| 制作、优化、拉片、规划或评审视频、storyboard、shot list、timeline、字幕/overlay | `skills/video-creation/SKILL.md` |
| `paper record` | `skills/paper-record/SKILL.md` |
| Hub 自迭代、自优化、自升级，或任务暴露 repo-portable 的稳定流程缺口 | `skills/self-evolution-engine/SKILL.md` |

如果某个 Skill 被误触发，读完正文后用一句话说明不适用并退出，不因为已经读取就套完整流程。

## Execution Budget

- 默认使用“最小足够闭环”：最小读取、最小修改、最小验证。
- 轻量路径：typo、小文档、单文件小修、已知范围机械调整。只读直接相关文件，跑 `git diff --check`、引用搜索或最窄命令即可。
- 标准路径：普通实现、bugfix、UI 调整、局部重构。冻结 3 到 7 条关键不变量，按最小闭环实现并做定向验证。
- 高风险路径：接口、权限、安全、DB、生成物、runtime、tool/gateway、跨仓库/跨 agent、反复失败、准备提交/PR/合并、用户要求研究或质量审查。按需加载 reference、外部资料、测试/审查视角和更宽验证。
- 每次处理完问题后做收尾三检：review 改动范围和影响范围；确认未扰乱架构、owner、领域划分；用 `git diff` / `git diff --check` 或等价检查确认 diff 聚焦。

## Content Ownership

- `README.md`：Hub 定位、最高法则、工作方向、路由总览和目录地图。
- `AGENTS.md`：跨工具硬约束、加载顺序、核心 gate 和高频协作规则。
- `agents/<tool>.md`：宿主能力、权限差异、fallback 和工具专属行为。
- `skills/<slug>/SKILL.md`：可重复触发流程，保留 Trigger、Workflow、Output、Quality Bar 和 reference 指针。
- `skills/<slug>/references/`：长 checklist、矩阵、模式、示例、执行细则。
- 文档工作区 helper：运行时高频项目背景、边界和事实源。
- 文档工作区报告：产物、完整论证、审查记录和历史证据；写入和点名查找，不默认加载。
- `Documents/temp/`：当前任务有用但不值得长期保留的临时证据、外部组件迁移账本、缓存路径和过度产物；不默认加载，不当事实源。

入口文件、工具适配文件和高频 Skill 都应短小、索引化、可快速扫描。为了“更完整”而增加内容量，默认不通过；优先合并、删除、下沉或重命名旧内容。

## Collaboration Defaults

- 用户给出明确路径、仓库、模块或来源范围时，将其视为硬边界；动手前先核对精确路径。
- 用户说“OK”“do it”“没问题”等确认时，默认进入执行模式；除非存在高风险歧义，不停留在方案描述。
- 用户要求“只涉及”某范围时，最终检查变更清单；验证若只读越界内容，需要说明。
- 用户偏好中文协作语境；方案、总结和长期文档默认中文，代码标识、命令、路径和接口名保留原文。
- 用户要求本地调试时，优先给可直接运行的 Markdown shell block，写明 `cd` 路径、必要环境变量、窄范围测试和全量验证命令。
- 用户要求“给我文件内容”“最终文案”“完整 prompt”“新窗口测试文案”等可复制正文时，核心交付先给完整正文，不让用户拆段拼装。
- 用户中断、连续催促或新消息覆盖旧请求时，进入快速收敛模式：先确认当前状态，只做最新请求需要的最小动作。
- 用户指出“多余”“别发挥”“不要兼容”“不要做太多”时，停止新增实现，检查本轮 diff 和 owner 边界，撤回自己引入的非必要扩展。
- 用户质疑越界或要求保持 diff 干净时，先检查 `git status --short`、`git diff --name-status`、`git diff --cached --name-status`，分清 staged、unstaged、untracked、用户已有改动与本轮改动。

## Case And Runtime Gates

- 页面 case、演示验证、Chat/MCP workflow、视频出片或多素材任务，动手前先冻结 case context：页面/URL、业务或空间、thread、agent/preset、素材来源、可见存储、工具 schema/批量能力、最终产物和验收信号。
- 这类任务默认先视为 **Run Case**：仓库代码、schema、config、平台资源和公开契约写操作默认锁住。只有目标转为 **Build Capability**，且 owner、影响面、最小 diff、真实验证链路清楚时，才写代码或公共契约。
- 用户切换窗口、业务、agent、thread、素材或说“不是 / 在这里 / 空间错了 / 三方 MCP / 多图 / 不要改”时，立即重新冻结上下文，不沿用旧假设。
- Runtime 中的配置引用与真实加载状态必须区分：preset/profile 可以保留 resource IDs，但只有 loader/middleware 解析并装载后才能称为 loaded；requested / resolved / unresolved 应可诊断。

## Interface, Tool, Resource Gates

- API、route、operation、SDK surface、tool/MCP、runtime/gateway 链路变更，必须先盘点完整接口面、真实消费者、真实能力、owner 和目标状态：supported / unsupported / deprecated / internal。
- 新增跨服务接口、SDK surface、tool/MCP 对接或 runtime/gateway 链路前，先冻结 public surface、application owner、data/source owner、consumer 四项 owner；不因当前目录方便而落错 owner。
- 资源身份由 owning service 生成且保持不可变；用户输入只能成为 name/title/displayName/slug/alias/search/source hint，不得成为主 ID、权限主键、审计主键或存储路径。
- Create/import/update/delete 契约要区分 system-generated/output-only ID、canonical ref、用户可编辑字段和幂等键；删除、解绑、禁用默认只接收 canonical resource ref，额外 scope 字段必须证明服务端无法自行定位。
- Registry/DB 是发现、权限、状态、索引、版本和轻量缓存层；真实内容事实源属于 backend、artifact store、NAS 或其它 source owner。写入先 source 成功再刷新 registry，读取先 registry 定位再按需加载 source。
- MCP 协作默认只有两条公开通道：平台通过 MCP provider/gateway 调 MCP tool；MCP 通过平台 SDK/API、callback、artifact/resource refs 回到平台。本地路径、脚本、本机端口和 runtime 内部 route 只能是实现细节或测试夹具。
- 模型可调用工具必须来自显式 `toolIds`、授权引用或等价配置；管理目录、全量 catalog、默认工具集合和 runtime 兜底不得混入 model build contract。
- Tool schema/build 和 execute 必须闭合在同一 gateway/surface 语义下；runtime loop 只编排模型消息、调用 gateway、回塞结果，不按 tool name 直连具体工具。

## Design And Data Gates

- 未来演进方向未定责时，只沉淀兼容性边界、稳定契约和可组合性要求，不在当前模块提前加入未定责接口、目录或 lifecycle。
- 暂无真实调用方、配置入口、验收场景或明确 owner 的扩展点不保留；diff 只表达本次目标。
- 模块设计优先保持独立、原子、可验证；共享能力通过公开协议、元数据、回执和测试约束表达。
- 设计上层调用的执行模块时，panic、超时、阻塞、资源耗尽、依赖降级和部分失败要收敛成标准错误或回执。
- 结构化配置面按 owner 和 provider/store 语义分层；外部配置只暴露稳定业务语义，内部 adapter/backend/proxy 映射留在 composition root/adapter。
- 仓库已有 SQL 模板、query 目录、ORM mapper 或生成链路时，新增/调整持久化查询优先修改事实源模板并重新生成 client，不在 service 散落可复用 SQL。
- 接口、路由、方法、DTO、按钮或危险操作命名必须表达真实前置约束和行为边界；例如只删空目录应命名为 `delete-empty-directory` / `DeleteEmptyDirectory`，不能泛化成 `delete-directory`。

## Research, Docs, Reports

- 用户要求“研究下”“好好研究”“看看业界优秀实践”“参考最佳实践”，或结论依赖最新外部事实时，进入研究型工作流：先读本地直接相关上下文，再核对官方文档、规格、release notes、论文和可信案例。
- 研究型输出区分三层：本地/历史约束、已核对外部事实、项目采纳/调整/不采纳的取舍；需要落文档时附来源链接和日期。
- `paper record` 是当前聊天归档收尾动作：读取 `skills/paper-record/SKILL.md`，只做归档、索引更新和简短确认。
- 报告写入文档工作区；文件名使用 `YYYY-MM-DD-关于<领域>-<topic>.md`，正文顶部至少写 `日期`、`类型`、`项目`、`来源`，讨论型文档写 `版式`。
- 方案归档按主意图分类：新增能力 `feature`，缺陷修复 `fix`，结构调整 `refactor`；非落地文档可用 `analysis`、`review`、`runbooks`、`chat`。

## Hub Maintenance

- `receive` 是明确规则库维护指令：读取 `skills/refactor-hub/SKILL.md`，判断更新 Rule、Skill、Reference、Project Helper、Report，还是不沉淀。
- 规则维护、内容清理或规则体系 review 前，先执行 `skills/project-content-curation/SKILL.md`，按 `hub-architecture.md` 和 `rule-system-strategy.md` 分类落位。
- 普通交互不自动触发 Hub 更新。只有用户明确要求、已设置自动化触发、反复高风险缺口、跨场景稳定约束、owner/边界混淆或验证门禁缺失时，才沉淀。
- Hub 自升级必须是 repo-portable 机制：触发条件、owner、reference 和验证依赖仓库内容即可恢复；不得依赖本机 memory、automation 缓存、安装态或宿主线程状态。
- 规则沉淀不得包含项目名、业务方案、一次性上下文或具体实现决策；只保留跨任务可复用的触发条件、边界、流程和检查项。

## Review, Git, Verification

- 用户要求 review 时，按代码审查姿态：发现优先于总结，按严重度列 bug、风险、回归和测试缺口；没有发现问题也要说明残余风险。
- 用户说 `git diff` 时，视为只读 diff 边界与影响审查：不改文件、不暂存、不提交；先给预期范围、实际范围、是否越界和未确认风险。
- 准备提交、暂存、amend、PR、合并或“只提交本次改动”前，必须做完整 diff 审查；无关用户改动保持原样。
- 用户说 `git update` 时，表示把目标分支 merge 到当前分支，默认方向是 `当前分支 <- origin/main` 或 `当前分支 <- main`；先检查当前分支、目标分支和 dirty worktree，保护用户本地改动。
- 清理已修改内容时，先按本轮必要实现、测试/文档、旧入口、mock/fallback、临时文件、无关改动分层；删除前必须搜索确认没有生产入口、路由、导入、运行链路或配置引用。
- 声称“完成”“通过”“可用”“已打通”前必须有 fresh evidence；验证报告要区分入口可打开、请求到达、下游支持、业务成功、结果可见。
- smoke 只证明错误态暴露时，写“错误暴露/缺口确认”，不能写“接入可用”。遇到 4xx/5xx、unsupported、mock/fallback/dry-run、空数据或跳过步骤，默认未打通。
- 详细 cleanup、git diff、git update 和验证语言门禁见 `skills/task-execution-lifecycle/references/review-git-and-cleanup-gates.md`。

## Naming

- 目录名使用小写短横线或稳定项目 slug，例如 `project-slug`、`tool-suite`。
- Skill 目录使用 `<skill-slug>/SKILL.md`，脚本或参考资料只放在该技能目录内部。
- 新报告文件必须带日期前缀，不放 kind；存量报告整理时同步迁移，避免语义命名和日期前缀混用。

## Tool Policy

- 搜索优先用 `rg` 或 `rg --files`。
- 手工编辑优先用 patch/diff 风格。
- 报告和 helper 写入 `/Users/shatang/Documents/workspace/` 中 `README.md` 指定位置；技能写入 `skills/`；不写进工具专属目录。
- 删除、重置、覆盖配置、安装依赖、联网抓取等动作按当前工具权限规则请求确认。
- 需要验证时，先跑最窄有效检查，再按风险扩大。
