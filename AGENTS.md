# Shared Agent Instructions

这个仓库是 Codex、Claude、Cursor 共用的 Agent 工作资产中心。默认目标是让 Agent 用最少上下文读到正确规则，也让人能快速看懂 Hub 结构。

## Core Structure

- `agents/`：工具适配层，只放 Codex、Cursor 等宿主差异；Claude 专属入口暂放根目录 `CLAUDE.md`。
- `skills/`：Skill Library，唯一目录定义为 `skills/<Category>/<skill-slug>/WORKFLOW.md`；长检查表放同目录 `references/`。
- 文档工作区：入口见 `README.md`，任务查找与正式文档规则以该入口为准。
- `/Users/shatang/Documents/temp/`：临时材料入口；过度产物、迁移账本、一次性对照和缓存清单放这里，不进入 Hub 或正式 workspace。
- `skills/` 是 Hub 内部 workflow 库，由 `/hub` 按需读取；内部文件使用 `WORKFLOW.md`，不暴露为一组独立命令。

当前长期维护范围只包括 Codex、Claude 和 Cursor。除非用户明确要求，不恢复 OpenCode，也不新增 `knowledge/`、`templates/`、`attachments/`、`archive/` 等目录。

`SIMP_README.md` 已废弃，不再作为路由入口；项目任务和正式文档流程以 Hub README 登记的文档工作区入口为准。

## Core Laws

1. **实事求是**：先尊重真实需求、上下文、证据、能力边界和产物状态，再抽象规则或执行方案。
2. **精简有效**：入口文件只保留硬约束、触发索引和短规则；Skill 应是可组合 SOP，不写成场景树；长流程、示例、矩阵和历史证据下沉到 reference 或文档工作区报告。
3. **分层承载**：通用约束放 `AGENTS.md`，工具差异放 `agents/`，可重复流程放 `skills/`，项目背景和报告放文档工作区。
4. **按需加载**：默认只读必要入口；Skill、reference、helper、外部搜索和 subagent 必须由任务类型、风险信号或用户要求触发。
5. **可验证沉淀**：新增或保留规则前，必须能说清触发条件、保护的不变量、检查工件、例外和验证方式。
6. **项目隔离**：Hub 只沉淀跨项目可复用的泛化规则、workflow 和工具适配；具体项目路径、仓库约束、业务事实、SPEC/STDD、helper、报告和路由映射只写入文档工作区或项目自身，不写进 Hub。

## Loading

默认读取顺序：

1. 读取本文件。
2. 读取 `README.md` 作为长期协作上下文。
3. 按当前工具读取一个适配文件：Codex 读 `agents/codex.md`，Claude 读 `CLAUDE.md`，Cursor 读 `agents/cursor.md`。
4. 命中任务信号后，再读取对应 `skills/<Category>/<skill-slug>/WORKFLOW.md`；reference 只在 workflow 明确需要或风险命中时读取。
5. 需要项目背景、helper 更新、报告写入、任务查找、SPEC 或 STDD 时，先读 Hub README 登记的文档工作区入口；报告目录不作为默认上下文来源。
6. 任务涉及文档工作区下具体项目的需求拆解、改动、调整或执行某个 SPEC 时，无需用户手动引用：具体查找、状态判断、命名和写入规则均以文档工作区入口为准。只有用户明确说“Claude 执行”或等价指令时，Claude 才进入执行模式。
7. SPEC/STDD 是引导和索引，不是改代码授权；执行前必须用当前页面、API、代码、配置和运行事实校验目标、已有能力、缺口和 owner，不能只因 SPEC 写了某路径就越界实现。

常用触发路由：

| Signal | Read |
| --- | --- |
| 任务可能命中多个 Skill，需要选择最小组合 | `skills/Core/skill-router/WORKFLOW.md` |
| 非机械实现、重构、UI、架构、协议、数据、工具链或跨模块任务 | `skills/Core/task-execution-lifecycle/WORKFLOW.md` |
| 模糊产品、功能、工具、自动化或 workflow 需求 | `skills/Requirements/requirements-brief/WORKFLOW.md` |
| 理解项目、业务模型、修改边界、ownership 或影响范围 | `skills/Navigation/codebase-navigation/WORKFLOW.md` |
| 研究、业界优秀实践、最新资料、官方文档、论文、竞品或需要来源链接 | `skills/Research/source-grounded-research/WORKFLOW.md` |
| 优化 prompt、agent instructions 或统一输出模板 | `skills/Prompt/prompt-improvement/WORKFLOW.md` |
| 设计 AI agent tool、MCP/function calling、tool gateway、schema、回执、权限 | `skills/Engineering/agent-tool-design/WORKFLOW.md` |
| 新增、删除、改名或暴露 API/Gateway/route/operation/tool surface，跨仓库/跨 agent 契约 | `skills/Engineering/interface-contract-audit/WORKFLOW.md` |
| 文档工作区项目背景缺失、被删除、过期，或要求 Context Health Check / 重建 | `skills/Context/project-context-rebuild/WORKFLOW.md` |
| 整理项目内容、规则、Skills、报告、长期上下文、删除内容 | `skills/Context/project-content-curation/WORKFLOW.md` |
| `/hub get`、`/hub update`、Hub 自迭代、外部方法吸收、知识沉淀或 Skill/Prompt/Helper/Workflow/Agent/Tool 升级 | `skills/Knowledge/knowledge-evolution/WORKFLOW.md` |
| AgentOS、Agent infra、AI 技术/协议/架构、Product UI/功能/架构、音画同步、互动影游、`Agent Team` / `IT Agent Team` 且需要判断 | `skills/Domain/agent-team-router/WORKFLOW.md` |
| 制作、优化、拉片、规划或评审视频、storyboard、shot list、timeline、字幕/overlay | `skills/Domain/video-creation/WORKFLOW.md` |

如果某个 Skill 被误触发，读完正文后用一句话说明不适用并退出，不因为已经读取就套完整流程。

## Hub Commands

`/hub cmd` 从本表读取当前有效命令并返回命令名与简短用途；不列内部 Skill、普通自然语言触发条件或已废弃命令。

| Command | Contract |
| --- | --- |
| `/hub cmd` | 列出当前登记的全部有效命令及简短用途。 |
| `/hub spec [<需求>]` | 将用户输入整理为可独立执行的正式 SPEC，写入 doc-hub 的 pending 位置，不进入执行。 |
| `/hub spec-idea,<level> [<想法>]` | 按 `simple / middle / complex`（简 / 中 / 繁）整理 IDEA SPEC，不进入执行。 |
| `/hub fix [<问题>]` | 在目标范围内完成根因定位、最小修复和回归验证。 |
| `/hub refactor` | 审查并调整当前改动，使 diff 最小、领域边界清晰、架构整洁。 |
| `/hub coding [<任务>]` | 更新基线、实现、严格验证并完成 PR 收尾，不自动 merge。 |
| `/hub pr` | 清理并验证当前任务改动，推送 work branch，创建或更新 PR。 |
| `/hub git update` | 用 rebase 将当前分支同步到最新目标分支。 |
| `/hub get` | 从当前信号提取可复用经验，判断是否沉淀及其唯一 owner。 |
| `/hub update` | 审查并更新 Hub 的规则、Skill、reference、入口或结构。 |

旧名 `/hub receive`、`/hub refactor hub` 已分别更名为 `/hub get`、`/hub update`，不保留别名。`/hub review`、`/hub diff review`、`/hub check`、`/hub git check`、`/hub push`、`/hub git merge` 已废弃。

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
- `skills/<Category>/<skill-slug>/WORKFLOW.md`：可重复触发流程，统一保留 Purpose、When to Use、Inputs、Decision Principles、Workflow、Checklist、Escalation 和 References。
- `skills/<Category>/<skill-slug>/references/`：长 checklist、矩阵、模式、示例、执行细则。
- 文档工作区 helper：运行时高频项目背景、边界和事实源。
- 文档工作区报告：产物、完整论证、审查记录和历史证据；写入和点名查找，不默认加载。
- `Documents/temp/`：当前任务有用但不值得长期保留的临时证据、外部组件迁移账本、缓存路径和过度产物；不默认加载，不当事实源。
- 具体项目路径、仓库名、业务约束、SPEC/STDD 状态、helper、报告和路由映射不属于 Hub 内容；如需长期保留，放入文档工作区对应项目或项目自身规则文件。

入口文件、工具适配文件和高频 Skill 都应短小、索引化、可快速扫描。为了“更完整”而增加内容量，默认不通过；优先合并、删除、下沉或重命名旧内容。

## Collaboration Defaults

- 用户给出明确路径、仓库、模块或来源范围时，将其视为硬边界；动手前先核对精确路径。
- 修改既有实现前，先冻结必须保留的现有效果与本次必须达成的目标；方案应显式权衡并同时守住两者，默认选择能闭环的最小高价值 diff，保持领域 owner、依赖方向和架构边界清晰，并分别验证目标达成与既有效果未回退。
- 用户说 `fix`、`fix <问题>` 或明确要求修复某个问题时，视为授权在指定范围内连续完成 `复现 -> 定位 -> 交叉验证与正/逆向推理 -> 根因 -> 方案 -> 最小修复 -> 原始路径与回归验证`；根因必须能解释症状及其边界，证据不足时不得编造或先改症状，详细见 `skills/Core/task-execution-lifecycle/references/root-cause-fix-patterns.md`。
- `fix` 默认授权诊断和执行修复，但不自动授权 commit、push、PR 或 merge；需要完整 PR 收尾时使用 `coding` 或另行明确授权。
- 用户说 `/hub spec` 或 `/hub spec <需求>` 时，视为正式 SPEC 编写指令：以用户输入为需求源，先读文档工作区入口和当前项目的最小必要背景，再按 `requirements-brief` 收敛为可独立执行的 pending SPEC，并依文档工作区当前命名与生命周期规则写入指定位置。核心方向、项目归属或执行边界仍有阻塞歧义时只问一个最小问题；否则显式记录必要假设并直接完成。该指令不创建 STDD、不进入 execing、不修改业务仓库或开始执行；回复必须给出文件完整路径和仍待确认的问题。
- 用户说 `spec-idea,<level>` 或 `spec-idea,<level> <想法>` 时，视为 SPEC IDEA 整理指令；`simple / middle / complex` 分别表示简 / 中 / 繁，具体深度以 `requirements-brief` 为准。先读文档工作区入口和当前项目的最小必要背景，再依文档工作区当前命名与生命周期规则写入对应项目的 IDEA 位置；级别只控制整理深度，不改变生命周期或授权边界。IDEA 不进入 pending/execing，不创建 STDD，不读改业务仓库或开始执行。只有项目归属或核心方向存在高风险歧义时才问一个最小问题，否则直接完成；回复必须给出文件完整路径和仍待确认的问题。
- 用户说 `/hub refactor` 时，视为授权审查并直接调整当前改动：核对 staged、unstaged、untracked 及必要的 work branch 相对 target 差异，判断是否保持最小改动、领域 owner 与依赖方向清晰、架构整洁；删除或修正本任务内不合理、多余、临时或越界改动，保留用户已有无关改动，再做最窄有效验证和 diff 检查。该命令不授权暂存、提交、push、PR 或 merge。
- 用户说“OK”“do it”“没问题”等确认时，默认进入执行模式；除非存在高风险歧义，不停留在方案描述。
- 用户要求“只涉及”某范围时，最终检查变更清单；验证若只读越界内容，需要说明。
- 用户偏好中文协作语境；方案、总结和长期文档默认中文，代码标识、命令、路径和接口名保留原文。
- 用户要求本地调试时，优先给可直接运行的 Markdown shell block，写明 `cd` 路径、必要环境变量、窄范围测试和全量验证命令。
- 用户明确处于热部署、已有本地服务、页面已打开、或要求“改代码就完了”时，默认不得启动、重启、替换、kill 或抢占任何服务/端口；除非用户明确授权服务动作，验证只能使用不影响现有进程的只读检查、定向测试或代码证据。
- 用户主线任务包含架构调整、链路清理、数据治理或多事项推进时，测试补齐、验证矩阵、边界 case 扩写这类不阻塞下一步实现的支线默认交给异步/后台 agent；主 Agent 不在每个测试文件写完前卡住用户主线。后台 agent 只负责明确测试范围，不启动服务、不抢端口、不改生产实现。
- 用户要求“给我文件内容”“最终文案”“完整 prompt”“新窗口测试文案”等可复制正文时，核心交付先给完整正文，不让用户拆段拼装。
- 用户中断、连续催促或新消息覆盖旧请求时，进入快速收敛模式：先确认当前状态，只做最新请求需要的最小动作。
- 用户指出“多余”“别发挥”“不要兼容”“不要做太多”时，停止新增实现，检查本轮 diff 和 owner 边界，撤回自己引入的非必要扩展。
- 用户质疑越界或要求保持 diff 干净时，先检查 `git status --short`、`git diff --name-status`、`git diff --cached --name-status`，分清 staged、unstaged、untracked、用户已有改动与本轮改动。

## Case And Runtime Gates

- 页面 case、演示验证、Chat/MCP workflow、视频出片或多素材任务，动手前先冻结 case context：页面/URL、业务或空间、thread、agent/preset、素材来源、可见存储、工具 schema/批量能力、最终产物和验收信号。
- 这类任务默认先视为 **Run Case**：仓库代码、schema、config、平台资源和公开契约写操作默认锁住。只有目标转为 **Build Capability**，且 owner、影响面、最小 diff、真实验证链路清楚时，才写代码或公共契约。
- 用户说“配置”、点名已有页面/API/上传/prompt/preset/skill/tool 配置能力，或目标可由现有配置面完成时，只走配置和页面/API 操作；除非拿到现有能力缺口的代码或运行证据并向用户说明，否则不得改服务层、公共契约或初始化链路。
- 仿真 smoke / 页面验收中，主 Agent 保持用户主线和页面闭环；测试矩阵、旁支验证或子问题交给子 Agent。页面链路不通时先修配置、工具或 runtime 链路，不能用 prompt、手工路径或离线产物绕过。
- 真实页面 smoke / 用户可见 workflow 验收由主 Agent 闭环：主 Agent 必须亲自确认当前页面、操作路径、关键结果和证据状态；异步/子 Agent 只能做旁支测试、只读复核、日志归纳或候选命令，不能替代主 Agent 完成真实页面 smoke，也不能把页面验收外包到等待用户提醒后才补跑。
- 当验收目标是建设系统或验证真实链路时，Codex 不能用 internal complete、DB 手改、本地假素材、mock/fallback、一次性脚本或手工状态推进把 case 做成“依赖 Codex 才能跑”的半成品；空数据或链路失败应回到 owner 定位、系统修复和真实重试。
- 用户切换窗口、业务、agent、thread、素材或说“不是 / 在这里 / 空间错了 / 三方 MCP / 多图 / 不要改”时，立即重新冻结上下文，不沿用旧假设。
- Runtime 中的配置引用与真实加载状态必须区分：preset/profile 可以保留 resource IDs，但只有 loader/middleware 解析并装载后才能称为 loaded；requested / resolved / unresolved 应可诊断。
- Canvas/Chat/附件/文件读写类任务先确认真实消费路径：当前 thread runtime backend、附件 runtimePath、可见 canvas/artifact 目录、preset/tool refs 和页面上传/配置接口；没有证据表明 MCP 是事实源时，不得为页面显示或文件读写去改 MCP server 的项目存储或工具 schema。

## Interface, Tool, Resource Gates

- API、route、operation、SDK surface、tool/MCP、runtime/gateway 链路变更，必须先盘点完整接口面、真实消费者、真实能力、owner 和目标状态：supported / unsupported / deprecated / internal。
- 新增跨服务接口、SDK surface、tool/MCP 对接或 runtime/gateway 链路前，先冻结 public surface、application owner、data/source owner、consumer 四项 owner；不因当前目录方便而落错 owner。
- 新增业务入口、自动初始化、GET/list 页面副作用、跨模块持久化路径、MCP/tool 变更或 resource/business/prompt/preset/service 自动创建链路，默认都是高风险扩展；必须先列出现有能力缺口、真实消费者、owner、最小 diff 和验证链路，并取得用户明确确认。
- 资源身份由 owning service 生成且保持不可变；用户输入只能成为 name/title/displayName/slug/alias/search/source hint，不得成为主 ID、权限主键、审计主键或存储路径。
- Create/import/update/delete 契约要区分 system-generated/output-only ID、canonical ref、用户可编辑字段和幂等键；删除、解绑、禁用默认只接收 canonical resource ref，额外 scope 字段必须证明服务端无法自行定位。
- Registry/DB 是发现、权限、状态、索引、版本和轻量缓存层；真实内容事实源属于 backend、artifact store、NAS 或其它 source owner。写入先 source 成功再刷新 registry，读取先 registry 定位再按需加载 source。
- MCP 协作默认只有两条公开通道：平台通过 MCP provider/gateway 调 MCP tool；MCP 通过平台 SDK/API、callback、artifact/resource refs 回到平台。本地路径、脚本、本机端口和 runtime 内部 route 只能是实现细节或测试夹具。
- 新增或迁移可被其他主体发现、选择、调用或复用的能力/资源/surface 时，默认按真实 workspace、business、owner、provider 或授权关系收敛 visibility 和 scope；调试、项目或私有资源不能注册成全局可见，必须审计目标消费者可见/可用、非目标消费者不可见/不可用。
- 模型可调用工具必须来自显式 `toolIds`、授权引用或等价配置；管理目录、全量 catalog、默认工具集合和 runtime 兜底不得混入 model build contract。
- Tool schema/build 和 execute 必须闭合在同一 gateway/surface 语义下；runtime loop 只编排模型消息、调用 gateway、回塞结果，不按 tool name 直连具体工具。
- Tool name 唯一性、授权集合和启停状态治理属于 preset/config 保存阶段或对应管理 owner；runtime/gateway 默认接收已验证配置，只做普通 name -> tool 映射，不为契约上不应出现的重复态定义“谁赢”的运行时语义。

## Design And Data Gates

- 未来演进方向未定责时，只沉淀兼容性边界、稳定契约和可组合性要求，不在当前模块提前加入未定责接口、目录或 lifecycle。
- 暂无真实调用方、配置入口、验收场景或明确 owner 的扩展点不保留；diff 只表达本次目标。
- 模块设计优先保持独立、原子、可验证；共享能力通过公开协议、元数据、回执和测试约束表达。
- 设计上层调用的执行模块时，panic、超时、阻塞、资源耗尽、依赖降级和部分失败要收敛成标准错误或回执。
- 结构化配置面按 owner 和 provider/store 语义分层；外部配置只暴露稳定业务语义，内部 adapter/backend/proxy 映射留在 composition root/adapter。
- 用户连续纠正配置命名、层级或服务边界时，先暂停新增实现并重建领域 owner map：区分业务服务、AI/Agent 服务、provider、store、runtime adapter 和本地调试替身；同品牌但职责不同的服务必须用真实 owner 语义显式分名。不要因为用户说某个词、点号或方向就机械新增层级、制造别名，或把一个服务的配置塞进另一个服务 owner。
- 仓库已有 SQL 模板、query 目录、ORM mapper 或生成链路时，新增/调整持久化查询优先修改事实源模板并重新生成 client，不在 service 散落可复用 SQL。
- 接口、路由、方法、DTO、按钮或危险操作命名必须表达真实前置约束和行为边界；例如只删空目录应命名为 `delete-empty-directory` / `DeleteEmptyDirectory`，不能泛化成 `delete-directory`。

## Research, Docs, Reports

- 用户要求“研究下”“好好研究”“看看业界优秀实践”“参考最佳实践”，或结论依赖最新外部事实时，进入研究型工作流：先读本地直接相关上下文，再核对官方文档、规格、release notes、论文和可信案例。
- 研究型输出区分三层：本地/历史约束、已核对外部事实、项目采纳/调整/不采纳的取舍；需要落文档时附来源链接和日期。
- 报告写入文档工作区；文件名使用 `YYYY-MM-DD-关于<领域>-<topic>.md`，正文顶部至少写 `日期`、`类型`、`项目`、`来源`，讨论型文档写 `版式`。
- 方案归档按主意图分类：新增能力 `feature`，缺陷修复 `fix`，结构调整 `refactor`；非落地文档可用 `analysis`、`review`、`runbooks`、`chat`。

## Hub Maintenance

- `/hub get` 是明确规则库提取指令：读取 `skills/Knowledge/knowledge-evolution/WORKFLOW.md`，判断当前信号是否值得沉淀，以及唯一 owner 应为 Rule、Skill、Reference、Project Helper、Report、Temp、Prompt、Workflow、Agent、Tool 还是 Delete。
- `/hub update` 是明确 Hub 更新指令：先按 `skills/Context/project-content-curation/WORKFLOW.md` 审查内容归位、重复和入口负担，再按 `knowledge-evolution` 更新必要 owner、索引和引用；不把一次性项目事实写入 Hub。
- 用户指出 `/hub get` 漏抽、忽略高亮信号或 Hub 规则未生效时，必须先修正对应 Hub workflow 的抽取门禁，再补沉淀目标规则。
- 规则维护、内容清理或规则体系 review 前，先执行 `skills/Context/project-content-curation/WORKFLOW.md`，按 `hub-architecture.md` 和 `rule-system-strategy.md` 分类落位。
- 普通交互不自动触发 Hub 更新。只有用户明确要求、已设置自动化触发、反复高风险缺口、跨场景稳定约束、owner/边界混淆或验证门禁缺失时，才沉淀。
- Hub 自升级必须是 repo-portable 机制：触发条件、owner、reference 和验证依赖仓库内容即可恢复；不得依赖本机 memory、automation 缓存、安装态或宿主线程状态。
- 规则沉淀不得包含项目名、业务方案、一次性上下文或具体实现决策；只保留跨任务可复用的触发条件、边界、流程和检查项。

## Review, Git, Verification

- `/hub refactor` 按代码与架构审查姿态处理当前改动，但发现不合理、多余、临时或越界内容后直接在本任务范围内调整，而不是只报告问题；调整后重新检查 diff 边界、影响面、领域 owner、依赖方向和验证证据。
- 用户说 `pr` 时，视为主动 PR 收尾指令：先更新本地 target 基线（默认 `origin/main -> main`），再切出或确认 local work branch；检查本地工作区和 work branch 相对 target 的全部变动，清理无效/临时 diff，保持架构和领域边界，验证后 push work branch，并生成 merge comment。
- 用户说 `coding` 或 `coding <任务>` 时，视为授权在目标项目内执行 `git update -> 最小实现 -> 严格验证 -> PR 收尾`：实现必须保持最小 coherent diff、领域边界和架构整洁；测试同时使用项目内质量契约和可用的本地 `quality-orchestrator`，详细流程见 `task-execution-lifecycle` 的 Git/cleanup reference。
- `coding` 默认授权创建或更新 work branch、单个任务提交和 PR，不单独授权实际 merge。PR 建立后等待审批；只有用户已明确授权合入，或仓库已有明确且适用的 auto-merge 规则时，才可在 required checks / approvals 全部满足后完成 merge。
- 后续 push / PR 收尾前，必须清楚改动影响面和架构思路是否符合当前架构；可以不逐行审 AI 代码，但不能不理解实现设计、影响范围和验证证据。
- 合入 main 必须走 rebase，不使用 merge；work branch 必须基于最新 target rebase，并尽量压成一个清晰提交后再推送；不得把 merge commit、无意义提交、临时修复或流程副作用落到目标分支，目标分支必须始终保持可构建、可测试、可发布。
- 大改动必须拆成解耦、独立、可验证的小改动，尽早且频繁合入 main；每次合入前都要同步最新 main、解决冲突并完成验证，避免长期维护大型功能分支。
- 准备提交、暂存、amend、PR、合并或“只提交本次改动”前，必须做完整 diff 审查；无关用户改动保持原样。
- 用户说 `git update` 时，表示把当前分支 rebase 到最新目标分支，默认基线是 `origin/main` 或 `main`；先检查当前分支、目标分支和 dirty worktree，保护用户本地改动。
- 实际 push 或 merge 只作为 `coding`、`pr` 的内部步骤或用户另行明确授权执行；不得把已废弃的独立 `/hub push`、`/hub git merge` 当作授权。修改 target/main 仍需单独明确授权或适用的 auto-merge 规则。
- 清理已修改内容时，先按本轮必要实现、测试/文档、旧入口、mock/fallback、临时文件、无关改动分层；删除前必须搜索确认没有生产入口、路由、导入、运行链路或配置引用。
- 业务空间、preset、prompt、skill、tool、provider 或配置数据清理属于高风险数据治理：执行前必须先判断目标是局部废弃、能力迁移、形式收敛还是整体退役，并列出 `keep / delete / migrate / restore` 清单和 dry-run 计数。只有用户明确要求整体退役/清空时，才允许把业务空间 active agent/preset 清零；若目标是“保留能力但收敛形式”，应迁移为合适数量的 Agent 加必要 prompt + skill 支撑，再删除旧分散对象；删除必须有可回滚方式，软删除后按本次目标核对保留/清空结果。
- 声称“完成”“通过”“可用”“已打通”前必须有 fresh evidence；验证报告要区分入口可打开、请求到达、下游支持、业务成功、结果可见。
- smoke 只证明错误态暴露时，写“错误暴露/缺口确认”，不能写“接入可用”。遇到 4xx/5xx、unsupported、mock/fallback/dry-run、空数据或跳过步骤，默认未打通。
- 详细 cleanup、`/hub refactor`、`coding`、`git update`、`pr` 和验证语言门禁见 `skills/Core/task-execution-lifecycle/references/review-git-and-cleanup-gates.md`。

## Naming

- 目录名使用小写短横线或稳定项目 slug，例如 `project-slug`、`tool-suite`。
- Skill 目录使用 `skills/<Category>/<skill-slug>/WORKFLOW.md`，脚本或参考资料只放在该技能目录内部。
- 新报告文件必须带日期前缀，不放 kind；存量报告整理时同步迁移，避免语义命名和日期前缀混用。

## Tool Policy

- 搜索优先用 `rg` 或 `rg --files`。
- 手工编辑优先用 patch/diff 风格。
- 报告、helper、SPEC 和 STDD 写入文档工作区入口指定的位置；技能写入 `skills/`；不写进工具专属目录。
- 删除、重置、覆盖配置、安装依赖、联网抓取等动作按当前工具权限规则请求确认。
- 需要验证时，先跑最窄有效检查，再按风险扩大。
