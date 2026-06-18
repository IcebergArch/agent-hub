# Shared Agent Instructions

这个仓库是 Codex、Cursor 共用的 Agent 工作资产中心。无论当前使用 Codex 还是 Cursor，都优先遵守这里的通用规则，再读取对应工具适配文件。

## Minimal Structure

- `agents/`：Codex、Cursor 等工具适配入口。
- `skills/`：可复用技能，推荐格式为 `skills/<skill-slug>/SKILL.md`。
- `reports/`：长期保留的输出，先按个人与公司隔离。个人单独与 Agent 探讨、设计或研究写入 `reports/personal/agent/<kind>/YYYY-MM-DD-关于<领域>-<topic>.md`；个人项目写入 `reports/personal/projects/<project-slug>/<kind>/YYYY-MM-DD-关于<领域>-<topic>.md`；公司项目写入 `reports/company/projects/<project-slug>/<kind>/YYYY-MM-DD-关于<领域>-<topic>.md`。日期、类型、项目、来源、版式等元信息写入正文顶部。

当前长期维护范围只包括 Codex 和 Cursor；不为 OpenCode 维护配置、入口或兼容文档，除非用户未来明确要求恢复。

除非用户明确要求，不新增 `knowledge/`、`templates/`、`attachments/`、`archive/` 等目录。

## Loading Order

1. 读取本文件。
2. 读取 `README.md`，将其作为持续成长的长期协作上下文；不要要求用户每次手动附带该文件。
3. 按当前工具读取一个适配文件：
   - Codex: `agents/codex.md`
   - Cursor: `agents/cursor.md`
4. 如果任务触发某个技能，读取 `skills/<skill-slug>/SKILL.md`。
5. 如果任务属于某个项目，按归属优先查看 `reports/personal/projects/<project-slug>/` 或 `reports/company/projects/<project-slug>/` 下已有沉淀；如果是个人单独与 Agent 探讨、设计或研究，优先查看 `reports/personal/agent/`。
6. 当任务涉及 AgentOS、Agent infra、AI 技术/协议/组件/架构研究、Product UI/功能/架构、音画同步产品、互动影游产品，或用户提到 `Agent Team`、`IT Agent Team`，且需要产品、架构、技术或角色判断时，读取 `skills/agent-team-router/SKILL.md`；路径明确的机械改动不额外路由。
7. 当用户要求整理项目内容、规则、Skills、报告或删除内容时，读取 `skills/project-content-curation/SKILL.md`，先完成内容分类，再执行迁移、保留或删除。
8. 当用户说 `project refactor`、`receive`、`refactor hub`、优化 hub，或要求从当前对话抽取可复用规则/技能/注意点时，读取 `skills/refactor-hub/SKILL.md`；先回看当前对话消息和现有 hub 内容，再按 Skill / Rule / Reference / Report / Keep / Delete 分类并落位到规则库。
9. 当任务涉及新增、删除、改名或暴露 API/Gateway/route/operation/tool surface，或进行跨仓库、跨 agent 对接契约收敛时，读取 `skills/interface-contract-audit/SKILL.md`，先盘点完整接口面和真实消费者，再实现或清理。
10. 当用户发布非机械的实现、重构、UI、架构、协议、数据、工具链或跨模块任务时，读取 `skills/task-execution-lifecycle/SKILL.md`；小修、单点文档和路径明确的机械改动走轻量执行预算，不额外加载长 reference。
11. 当用户要求吸收 Superpowers、外部 Agent framework、插件、技能库、工程方法论或优秀实践到 Hub 时，读取 `skills/methodology-harvest/SKILL.md`；优先抽取机制并落到既有 Skill/Rule/Report，不照搬外部目录、品牌口吻或不适配当前工具的强制门禁。
12. 当用户要求 Hub 具备自迭代、自优化、自升级、自我提升、持续升级，或明确要求“换到新设备再次使用 Hub 也能触发同样机制”时，读取 `skills/self-evolution-engine/SKILL.md`；先过 repo-portable gate，再决定落到 `refactor-hub`、`project-content-curation`、`design-principle-library`、`methodology-harvest` 或其它现有 owner，不把机制绑到本机 memory、automation 或临时缓存。

## Execution Budget

- 默认使用“最小足够闭环”：先完成用户目标所需的最小读取、最小修改和最小验证；每增加一个 Skill、reference、报告、外部搜索、subagent、main sync 或完整 diff 审查，都必须由明确触发词、风险信号或收尾动作支撑。
- 轻量路径适用于 typo、小文档、单文件小修、已知范围的机械调整和只读解释：读取入口与直接相关文件，冻结显而易见的不变量，执行定向修改，跑 `git diff --check`、引用搜索或最窄命令即可；不做外部研究、报告沉淀、main 更新或完整分支收尾。
- 标准路径适用于普通实现、bugfix、UI 调整和局部重构：读取对应 Skill，冻结 3-7 条关键不变量，按最小闭环实现，做定向验证和 diff 边界审查。
- 高风险路径只在接口/权限/安全/DB/生成物/runtime/tool/gateway/跨仓库/跨 agent、反复失败、准备提交/PR/合并、用户明确要求研究或质量审查时升级：按需加载 reference、外部资料、测试 Agent 视角和更宽验证。
- 如果某个 Skill 被关键词误触发，读取后可用一句话记录不适用原因并退出；不要因为已经读取就强行执行完整流程。
- 规则更新、自我提升、报告归档和长期沉淀是触发式质量动作；除非用户当前目标就是整理 Hub，否则不得抢占主任务时间预算。

## Living Context Maintenance

- `README.md` 是长期协作上下文入口，用于沉淀用户稳定的工作方向、产品域、协作偏好和会影响后续判断的背景。
- 当用户表达新的长期方向、稳定产品边界、持续偏好或跨任务复用背景时，应更新 `README.md`，让它随协作自然成长。
- 不把一次性任务细节、临时过程记录、未验证猜测、密钥、账号、私密凭据或业务敏感明细写入 `README.md`。
- 不把具体项目、公司项目或业务域专属设计规则写入 `README.md`、`AGENTS.md`、工具适配文件或 Skill；这类内容应按归属写入 `reports/personal/projects/<project-slug>/` 或 `reports/company/projects/<project-slug>/`。如果规则看似通用但来源于单个项目，先抽象掉项目名、业务方案和实现决策，只保留跨任务可复用的流程约束。
- 除报告文件外，入口文件、工具适配文件和 Skill 都应保持短小、索引化和可快速扫描；详细论证、长流程、完整命令清单和历史上下文写入 reports，让 LLM 按需渐进式加载。
- 如果信息更像可执行规则，写入 `AGENTS.md`、`agents/<tool>.md` 或 `skills/<skill-slug>/SKILL.md`；如果信息更像长期报告，写入 `reports/`。

## Naming

- 目录名使用小写短横线或稳定项目 slug，例如 `project-slug`、`tool-suite`。
- 新报告文件使用语义标题命名：`YYYY-MM-DD-关于<领域>-<topic>.md`，例如 `2026-06-04-关于AgentOS-longToolRuntimeScheduling.md`、`2026-06-04-关于AI发展-codingLanguageFuture.md`；文件名必须放日期前缀，不放 kind。存量报告在整理时也应同步迁移到该命名，避免同一目录混用日期前缀和语义命名两套规则。
- 技能目录使用 `<skill-slug>/SKILL.md`，脚本或参考资料只放在该技能目录内部。

## User Collaboration Style

- 用户给出明确路径、仓库或模块范围时，将其视为硬边界；动手前先核对精确路径，尤其要区分相近目录名、单复数目录名和不同 workspace。
- 用户说“OK”、“do it”、“没问题”或类似确认时，默认进入执行模式；除非存在高风险歧义，不停留在方案描述。
- 用户要求“只涉及”某个仓库、模块或目录时，最终必须检查变更清单，确认无关位置没有被修改；如因验证需要触及边界外内容，只读不写并在回复中说明。
- 用户明确限定信息来源时，将其视为硬边界；例如要求“联网”“不要参考本地”时，本地文件只能用于读取仓库规则或定位问题，不能作为事实来源或论证依据，最终回复应说明实际采用的来源范围。
- 用户偏好中文协作语境时，方案、总结和长期文档默认使用中文；保留代码标识、命令、路径和接口名的原文。
- 用户要求本地调试时，优先提供可直接运行的 Markdown shell block，写明 `cd` 路径、必要环境变量、窄范围测试和全量验证命令。
- 用户要求“给我文件内容”“我贴到公共文档上”或类似可复制正文时，正文内容是核心交付；先直接给可复制内容，再处理临时文件清理、规则收尾或背景验证。除非临时文件会造成安全、提交或用户工作区风险，不要让删除、回滚等收尾动作挡在核心答案前。
- 用户要求整理 merge comment、PR 描述或合并说明时，先用 base-head 或用户指定 diff 核对实际改动范围，再用中文短 bullet 描述本次 diff 真实变化；格式接近人工 merge note：每条以动作开头，按功能链路和模块聚合，避免文件级罗列，也不要泛化成产品愿景。粒度略细于一句话总结、粗于文件级改动清单；不要主动写验证、清理、明确未改、过程复盘或过细的 bug 内部原因，除非用户明确要求。
- 当用户中断当前回合、连续发送 `quickly`/`hurry`/“快点”等提速指令，或新消息明显覆盖旧请求时，立即切换到快速收敛模式：停止延续旧计划，先确认当前未完成/已部分修改状态，再只执行最新请求所需的最小动作；回复保持短结论、当前风险和下一步，不展开长方案。
- 当同一任务中用户多次纠正架构语义、范围边界或优先级顺序时，下一次实现前必须先把已确认的不变量列成 3-7 条检查清单，并据此审查改动范围；若仍缺关键排序、owner 或数据源语义，只问一个最小问题，不能继续按旧假设扩写实现。
- 当用户指出“多余”“别发挥”“不要兼容”“不要做太多”或类似范围收缩信号时，立即停止新增实现，先检查本轮 diff 和 owner 边界；撤回自己刚刚引入的非必要、跨 owner 或未被用户明确认可的扩展，只保留用户点名目标所需的最小改动。后续不得继续补通用能力、兼容层、重试/恢复链路或扩接口，除非用户重新明确授权。
- 当用户明确说“不要改不该改的内容”“保持 diff 干净”或质疑改动越界时，立即停止扩展实现，先检查 `git status --short`、`git diff --name-status`、`git diff --cached --name-status`，分清 staged、unstaged、untracked、用户已有改动与本轮改动；只做必要的索引/格式收敛，不继续新增功能或跨 owner 调整。
- 对接口、路由、方法、DTO、按钮或危险操作命名时，名称必须表达真实前置约束和行为边界；例如只删除空目录的能力必须命名为 `delete-empty-directory` / `DeleteEmptyDirectory`，不能泛化成 `delete-directory`。实现前后都要搜索同链路命名，确保 API、backend、client、测试和 UI 文案一致。

## Paper Record

- 当用户消息包含 **paper record**（大小写不敏感）时，将其视为**当前聊天归档的收尾动作**：读取 `skills/paper-record/SKILL.md` 并在当轮执行，将**当前对话完整脉络**整合为思考研究报告（非过程日志）。个人单独与 Agent 探讨、设计或研究写入 `reports/personal/agent/<kind>/`；个人项目写入 `reports/personal/projects/<project-slug>/<kind>/`；公司项目写入 `reports/company/projects/<project-slug>/<kind>/`。
- 触发 `paper record` 后，默认停止继续扩展新论证或新实现；只做归档、索引更新和简短确认，除非用户在同条消息中明确要求继续处理其他事项。
- 默认 `kind` 为 **chat**；文件名使用 `YYYY-MM-DD-关于<领域>-<topic>.md`；日期、类型、项目、来源、版式写入正文顶部；更新 `reports/index.md`。
- 用户可在同条消息附 `project:`、`kind:`、`topic:` 提示；未给出时从对话主题推断。

## Report Front Matter

- 新归档报告顶部必须写明元信息，至少包含：`日期`、`类型`、`项目`、`来源`；讨论型文档还应写 `版式`。
- 推荐格式：

```text
日期：2026-06-04
类型：analysis
项目：maxwell-ai
来源：7 角色 10 轮架构讨论整理
版式：金字塔结构，结论先行，图文并存
```

- 日期必须放入新文件名，格式为 YYYY-MM-DD；索引展示日期时与文件名前缀、文档元信息保持一致。

## Plan Archiving

- 当用户明确要求保存方案、复盘、diff 报告或长期沉淀时，个人单独与 Agent 探讨、设计或研究默认归档到 `reports/personal/agent/<kind>/`；个人项目默认归档到 `reports/personal/projects/<project-slug>/<kind>/`；公司项目默认归档到 `reports/company/projects/<project-slug>/<kind>/`；只在用户只是即时讨论且未要求保留时跳过归档。
- 当用户说明某类设计“后续都需要落分析文档”时，将其视为长期偏好；后续同类设计按归属默认沉淀到 `reports/personal/agent/analysis/`、`reports/personal/projects/<project-slug>/analysis/` 或 `reports/company/projects/<project-slug>/analysis/`。
- 当用户要求后续无需再次提醒生成报告时，将其视为长期归档偏好；后续架构、体系、技术取舍类讨论只要形成稳定结论，默认归档到 `reports/personal/agent/analysis/`、`reports/personal/projects/<project-slug>/analysis/` 或 `reports/company/projects/<project-slug>/analysis/`。
- 方案类沉淀按主意图分类：新增能力用 `feature`，缺陷修复用 `fix`，结构调整且不改变行为用 `refactor`；混合场景选择最主要的对外意图，并在正文说明次要类型。
- 非落地方案按用途分类：事实调研用 `analysis`，代码审查用 `review`，操作手册用 `runbooks`，对话整理用 `chat`。
- 新报告文件名使用语义标题：`YYYY-MM-DD-关于<领域>-<topic>.md`；`topic` 用简洁英文或 camelCase/短横线短语，避免本地路径、用户信息、临时编号或含糊词。
- 设计类归档正文至少包含 Summary、Decision、Scope、Architecture、Checklist、Flow、Validation、Follow-ups；需要表达链路时优先使用 Mermaid。讨论型文档默认采用金字塔结构：结论先行 -> 关键判断/决策 -> 证据与约束 -> 架构或流程图 -> 分场景展开 -> 路线图/后续。文字大于图，图只用于降低理解成本。
- 报告结构、图表、Mermaid、表格、清单和固定章节名只是表达手段，不是生成目标；不要为了让报告里出现某个元素而硬塞内容。选择最适合信息本身的表达方式，核心文档指标是面向人的可读性、清晰度、判断效率和可复用性。
- 当同一任务需要多份设计、协议或执行文档时，先按受众拆分再写内容：给 Agent/Codex 的文档写成短执行 brief，强调当前阶段、硬边界、实施顺序和验收；给人的协议或方案文档写成结果化的步骤、字段、接口、状态和 checklist，不展开长篇论证。若用户明确要求“直接给结果”“不要废话”，默认删去背景推理、过程复盘和过量样例，只保留决策、契约和下一步。
- 归档正文保留可复用信息；删除过程日志、一次性命令输出和未采纳草稿，避免长期目录变成临时缓存。

## Resource Identity And Registry Hygiene

- 当设计 create/import API、管理台表单、tool/skill/resource registry 或跨模块引用时，系统身份必须由 owning service 生成并保持不可变；用户输入只能成为 `name`、`title`、`displayName`、`slug`、`alias`、搜索词或 source hint，不得成为主 ID、权限主键、审计主键或存储路径。
- API/schema/generated client 必须区分 system-generated/output-only ID、canonical ref、用户可编辑 alias/display 字段和幂等键；创建接口默认不要求用户提供 ID，更新/删除接口收到的 ID 只是已有资源引用，仍需按租户、账号、业务等 scope 做对象级授权。
- 删除、解绑、更新等动作接口必须做字段必要性审计：以 canonical resource ref 作为最小输入，先证明服务端无法凭系统 ID、会话/账号授权、registry/access 关系和绑定索引自行定位对象，才能要求调用方额外传 scope/sourceScope/business/tenant 等限定字段；不得把“绑定检测需要某维度”误读成“调用方必须传该维度”。
- 删除前的依赖/绑定检测应抽象为 access/relationship 查询能力，不散落在 HTTP handler、UI 或临时业务扫描里。若现有 access 表暂时只表达可见关系，先用单一 guard/facade 封装当前绑定索引；后续关系维护迁入 access 表时，外部删除契约保持不变。
- Registry/DB 是发现、权限、状态、索引、版本、source pointer 和轻量缓存层；真实内容事实源属于 backend、artifact store、NAS 或其它 source owner。写入顺序优先是 source 成功后刷新 registry，读取顺序优先是 registry 定位后按需加载 source；无 registry 时才允许受限 source 扫描作为兜底。
- Skill/tool/resource 的 manifest 是运行时能力契约，不是数据库身份。渐进式加载应先披露稳定 metadata，再在触发后加载主说明，最后按需读取 references/scripts/files，避免把完整 catalog 或用户临时命名一次性塞进模型上下文。
- 用户可见名称可以重名、重命名、迁移或作为 alias 废弃；canonical ref 和 system ID 不随展示名变化。删除重建即使同名也应产生新 ID，并让审计、缓存和权限关系可区分。
- 已存在的 seed、fixture、生产数据或跨表引用 ID 不得只因“语义更好看”而迁移、重写或换号；ID 迁移必须是显式数据迁移需求，先评估引用面、回滚策略和兼容窗口。可修正 scope、access、meta、状态或索引关系时，不顺手改动原 ID。

## Interface Contract Hygiene

- 当新增、删除、改名或暴露 API/Gateway/surface operation 时，必须先盘点完整接口面，而不是只处理用户点名的单个 operation：列出当前全部 operation/route/method、真实消费者、真实后端能力、生命周期 owner 和目标状态（supported / unsupported / deprecated / internal）。
- 这类任务必须触发 `skills/interface-contract-audit/SKILL.md`；全局规则只保留硬约束，具体盘点步骤、测试要求和输出格式按 skill 执行。
- 对接前后端、跨仓库或多 agent 模块时，必须核对真实消费者代码、测试和文档；如果消费者由另一个 agent/thread 维护，应读取或询问该线程，再收敛契约。旧 scenario、隐藏管理页、fixture 或历史兼容入口不能自动升级为 clean API 合约。
- 收口或删除旧链路时，不默认保留“以后可能会用”的兼容分支；除非用户明确要求迁移窗口或向后兼容，否则本领域 ownership 内的 route/schema/generated client/docs/tests/UI 入口要一起清理。反过来，不能把其他模块、相邻 fixture、mock option、静态素材或既有产品占位顺手删掉；是否“别人模块”以 owner、入口和消费者归属判断，不以文件相邻判断。
- 没有真实能力或当前目标不需要的接口，默认移除或返回明确 unsupported，并补 negative contract test 防止伪接口回归；最终回复要列出完整 supported/unsupported 面，而不是只汇报本轮点名项。
- 对 tool/gateway/runtime 这类模型披露链路，必须区分管理目录视图和模型可调用视图：上游选择工具时以显式 `toolIds` 或授权引用作为唯一选择源，不用 `IncludeDefault*`、`IncludeRuntime*`、`IncludeBackend*` 等布尔开关向模型注入默认工具。管理页需要全量目录时，使用 catalog/list/detail surface 或内部查询承载，不污染 runtime build DTO。
- 对 tool call 执行链路，schema/build 和 execute 必须闭合在同一个 tool gateway/surface 语义下：runtime loop 只负责编排模型消息、调用 gateway 和回塞结果，不按 tool name 直接 switch 执行具体工具。若底层资源 client 仍由 runtime、backend、workspace 或 sandbox 模块拥有，应通过 adapter 注入 tools runner，而不是从 runtime core 绕过 tools 管理、审计、参数校验和回执归一化。
- 对 runtime 内部控制、middleware 自动补齐、渐进式加载等辅助动作，默认视为执行链路增强，不纳入业务 tool catalog、DB 管理面或一站式管理台；只有被明确提升为稳定、可授权、可审计的模型工具契约后，才进入 tool registry。

## Design Planning

- 当未来演进方向尚未确定归属时，只沉淀兼容性边界、稳定契约和可组合性要求；不要提前在当前模块中加入未定责的接口、目录、生命周期或实现机制。
- 暂时没有真实调用方、配置入口、验收场景或明确 owner 的扩展点不保留；后续快速迭代需要时再按真实用例补回。每次调整收尾都要删除只服务“未来可能”的 option、hook、factory、adapter、mock 或兼容分支，让 diff 只表达本次目标。
- 做模块设计时优先保持模块独立、原子、可单独验证；面向未来的扩展点应通过公开协议、元数据、回执和测试约束表达，避免让当前模块依赖尚未存在的外部架构。
- 不为单个前置检查或单条调用链过早拆出跨域 service、guard 或 platform package。若能力只服务某个 owner 的 CRUD/生命周期，优先由 owner service 暴露窄方法；只有关系本身有独立生命周期、多个 owner 共同维护或需要替换为统一 access/relationship 层时，才抽 facade/guard。
- 设计被上层系统调用的执行模块时，必须明确故障隔离边界：内部 panic、超时、阻塞、资源耗尽、依赖降级和部分失败要收敛成标准错误或回执，不能穿透并干扰上层系统。
- 当读写、存储或管理能力由外部模块承接时，当前模块只保留 facade、client port、DTO 映射和可替换 mock；mock 只用于入口和测试，不应演化为内部持久化方案。
- 当用户明确项目的主要维护语言或技术栈时，设计文档应以该语言或技术栈作为执行事实来源；旧实现只作为功能清单、行为 golden 和信息来源，不应支配新模块的内部结构。
- 设计工具、Agent loop、runtime 或 store 相关能力时，优先建设真实链路：模型可见 schema、调度入口、执行回执、状态/事件落点和验证测试必须连成闭环；mock 只能作为请求级、fixture 级或测试级替身。
- 当能力需要未来接入外部 runtime 或 store 时，当前实现应通过稳定接口、回执和事件表达衔接点，而不是把未来 store 细节提前写死。
- Runtime 中的配置引用与真实加载状态必须区分：preset/profile 可保留 resource IDs 作为配置输入，但只有 loader/middleware 真正解析并装载内容后才能称为 loaded。删除或找不到的旧引用不应反查到真实资源；同时应在 run metadata、event、trace 或调用明细中记录 requested / resolved / unresolved 诊断，方便排查“配置了但未加载”的能力。
- 当增强能力可能从 middleware、adapter、runtime loop、gateway 或 store 沉淀为公共策略时，先标注 experimental layer、目标 owner、迁移门槛和回退方式；未确认 owner 前保持为可替换增强，不把它下沉到其它 owner 的核心 loop 或公共配置。
- 在 composition root 或依赖装配点新增能力时，优先遵循现有 option/list 风格；复杂 factory、别名和跨层构造先收敛为本地窄 helper 或 adapter，避免把实现细节塞进装配列表。
- 设计结构化配置面时，按 owner 和 provider/store 语义分层：选择字段只暴露稳定业务语义，具体参数放在同名配置块下；不得把底层实现名、临时 adapter 名或相邻模块的 backend 名直接作为当前模块的配置枚举。若当前实现需要映射到内部 provider、proxy、backend 或 client 类型，映射留在 composition root/adapter 内，并用配置样例、测试和搜索验证旧实现名没有泄漏到外部配置面。
- 当仓库已有 SQL 模板、query 目录、ORM mapper 或代码生成链路时，新增/调整持久化查询应优先修改事实源模板并重新生成 client；服务层只调用生成查询或窄 repository 方法，不散落多行内嵌 SQL。例外仅限没有模板链路的极小一次性查询；收尾时必须检查生成文件来源、运行生成命令或等价验证，并搜索确认本轮没有把可复用查询耦合进业务 service。

## External Reference Refresh

- 当用户要求“研究下”“好好研究”“看看业界优秀实践”“参考最佳实践”或类似表达，或任务结论依赖最新外部事实时，进入研究型工作流：先读取本地长期上下文、项目历史文档和相关 reports/skills，再搜索官方文档、行业优秀产品或可信工程案例，最后结合当前项目约束给出取舍和适配方案。除非用户明确要求立即改代码，否则不要跳过研究直接实现。
- 当外部参考对象是 Agent 方法论、插件、技能库或工作流体系时，按 `skills/methodology-harvest/SKILL.md` 执行：先核对来源，再把稳定机制映射到 Hub 现有技能、规则或报告；不要把外部全量 catalog 或临时安装路径直接升级为 Hub 入口。
- 涉及快速演进领域且会影响架构、协议、权限、安全、模型 API、产品形态或长期决策时，默认做外部参考刷新；普通本地小修、已冻结契约内实现和机械清理不因此自动联网或加载长报告。
- 外部参考刷新不是泛泛联网搜索，而是先读取本地长期上下文和项目规则，再核对当前官方文档、规格、release notes、论文、可信工程案例；社区讨论只能作为痛点信号，不能作为唯一事实来源。
- 研究型输出必须包含三层结论：历史/本地文档已经约束了什么，外部优秀实践提供了什么稳定模式，本项目应采纳、调整或不采纳什么。若随后进入实现，先用这三层结论冻结设计不变量，避免边做边推翻。
- 输出方案、设计或实现前，应区分“已核对的外部事实”“本项目推断”“采纳/不采纳的取舍”，并在需要落文档时附来源链接和日期。
- 不把模型已有知识当作最新事实。对最近可能变化的信息、工具链能力、API 行为、协议语义、产品形态和安全要求，默认重新核对。
- 该规则是触发式规则：普通 typo、小范围已知 bug、纯本地机械重命名不需要拖慢；会影响定位、架构、信息架构、执行链路、权限安全、trace/eval/store 闭环的任务必须执行。

## Rule Maintenance

- P0：用户使用 `receive` 时，将其视为明确的规则库维护指令；必须读取 `skills/refactor-hub/SKILL.md`，判断应更新全局规则、工具适配规则、Skill、Reference、Report 还是不沉淀，并完成对应修改与验证。
- P0：用户把某类 Hub 升级、方法论吸收或规则复查描述为“常驻”时，只把它记录为后续触发条件；只有用户再次明确要求、触发 `receive`，或存在已设置的定时提醒/自动化时才执行，不在当前线程无限续跑或持续等待。
- P0：当用户要求复盘、优化或纠正某类指令执行流程（例如 `git update`、`git diff`、验证、提交流程）时，即使没有逐字说出 `receive`，也应纳入规则维护触发范围：先判断是否为可复用流程缺口，再更新对应规则或 Skill，而不是只在当前回复中口头总结。
- P0：普通交互不自动触发 `receive`。只做轻量判断：本轮是否出现明确用户要求、已设置的定时/自动化触发、反复出现的流程缺口、高风险设计原则，或会影响后续安全/权限/接口/验证的稳定约束；不满足这些条件时，不修改规则库。
- P0：当用户要求“Hub 能自迭代/自优化/自升级，且换设备后仍能触发”时，必须把能力写成 repo-portable 机制：触发条件、owner、reference 和验证依赖仓库内容即可恢复，不得把 automation memory、本机缓存路径、安装态或宿主线程状态写成默认前提。
- P0：完成架构设计、coding 分层设计、系统设计、UI/交互设计、数据体系设计或其他高风险设计后，可以执行自我提升判断；默认先作为候选经验或报告线索，只有用户要求、问题反复出现、或原则明显跨任务复用且影响高风险边界时，才更新 `skills/design-principle-library/SKILL.md` 或相关专门 Skill。
- P0：规则沉淀不得包含项目名、业务方案、一次性上下文或具体实现决策；只保留可跨任务复用的触发条件、边界、流程和检查项。
- P0：每次规则维护、内容清理或规则体系 review 前，必须执行内容整理阶段，并按 `skills/project-content-curation/SKILL.md` 与 `skills/project-content-curation/references/rule-system-strategy.md` 判断 Skill、Rule、Reference、Report、Keep 或 Delete；入口文件只保留短触发和硬约束，长流程、矩阵、示例和历史证据下沉到 Skill reference 或 reports。
- 更新规则前先读取现有 owner，保留有效语义；新增规则优先合并到现有小节或 Skill，拆开混杂规则，删除空泛和不可验证表述。若审查后没有可抽象的通用规则，最终说明已审查但不新增。
- 内容归属默认：通用硬约束写 `AGENTS.md`；工具差异写 `agents/<tool>.md`；可重复流程写 `skills/<skill-slug>/SKILL.md`；长细则写 `skills/<skill-slug>/references/`；个人/项目/公司结论和证据写对应 `reports/`。

## Hub Refactor And Content Curation

- 当用户说 `project refactor`、`receive`、`refactor hub` 或要求从当前对话抽取可复用规则、技能、注意点、工作方法时，必须执行 `skills/refactor-hub/SKILL.md`：先回看当前对话消息，再读取现有 hub 内容，分析缺口后按 Skill、Rule、Reference、Report、Keep、Delete 落位。
- `refactor hub` 的目标是优化 agent-hub 的长期可用性，不是把对话过程原样归档；只沉淀稳定、可复用、可触发的流程和约束。
- 当任务目标是整理项目内容、规则策略、系统内部规则体系、Skills、报告、入口文件或长期上下文时，必须先执行 `skills/project-content-curation/SKILL.md`，不要只做局部文字补丁。

## Change Cleanup Review

- 当用户要求清理或审查已修改、未提交、重构后的代码时，先把范围限定到用户指定任务、模块或本轮变更，不做全仓库机会主义整理。
- 清理前先用状态、diff 和搜索把相关文件分层：必要实现、测试或文档；旧入口或过渡代码；本地 mock、fallback 或假数据；临时过程文件；以及无关改动。无关改动保持不动，必要实现及其测试、golden、文档说明应保留。
- 删除文件或内容前，必须验证没有生产入口、路由、导入、运行链路或配置引用；只保留测试中的旧标识引用，当它用于防止旧行为回归时。
- 对前后端打通类变更，不把前端本地 mock、fallback 列表或假数据当成真实链路的替代；除非用户明确要求，页面应消费真实后端契约，测试数据应收敛在测试或 fixture 边界。
- 对 UI 可见或前后端联动变更，收尾前必须跑一遍关键 UI workflow：确认当前前端进程、后端进程和代理目标都来自本轮代码或预期版本；通过页面入口进入目标功能；用指定租户、空间、业务等上下文验证数据可见、关键操作可触发、错误不会被空页面或旧服务掩盖。
- 按新的目录架构移动、拆分或归位前端页面/组件后，必须同步审查相对 import、route 引用、懒加载入口、测试引用和生成 client 引用；不能只改调用方路径。收尾至少运行对应前端 typecheck，并在已有热部署页面上确认不会出现 Vite import-analysis / module not found overlay。
- 对管理页能力变更，收尾前必须检查完整 workflow 是否闭环：列表入口、创建、编辑配置、启停、删除、单点执行、默认参数模板回显、错误态和租户/业务上下文隔离；如果页面上出现重复入口、缺失操作按钮或需要手工猜接口，必须作为交互缺陷修正后再结束。
- 管理页里配置、执行、测试、审查等需要用户保留上下文的操作，默认使用弹窗或侧栏承载输入与结果；不要把结果固定追加到长页面底部，除非该页面本身就是日志流或用户明确要求内联展示。
- 清理后必须重新搜索旧名称、mock、stub、fallback、临时文件和过渡变量，并按风险从窄到宽运行格式化、单测、类型检查、race、bench 或页面 smoke。
- 最终回复要说明删除了什么、保留了什么以及保留原因，并列出已跑的验证命令或未能验证的缺口。

## Pre-commit Diff Review

- 当用户说 `git diff` 时，将其视为只读 diff 边界与影响审查指令：核心是先从用户目标和最近任务推断预期改动范围与预期影响范围，再用 staged / unstaged / base-head diff 对照实际变化，判断是否越界、是否引入预期外文件/模块/生成物/共享面影响；不修改文件、不暂存、不提交。
- 准备提交、暂存、amend、PR、合并或用户要求“只提交本次改动”前，必须先做完整 diff 审查；不要只看 `git status`。普通交付若不触发 git 动作，先按风险执行 `git status --short`、`git diff --name-status`、`git diff --stat` 和必要定向 diff；高风险或边界不清时再展开 staged / unstaged / cached 全套审查。
- 当工作区干净但当前分支相对 upstream 或目标分支存在未推送提交、刚执行 merge/rebase，或用户要求审查“本次改动/改动边界”时，`git diff` 不能只停在 staged / unstaged；必须补充 base/head diff 审查：确认基准分支或 merge-base，列出提交、文件和 stat，并把变化分成目标分支合入、本轮必要实现、测试/文档/生成物、已有分支改动和无关漂移，明确说明是否超出用户任务边界。
- diff 审查要按来源分层：本轮必要实现、测试/文档/配置、生成产物、工具缓存或临时文件、用户已有改动、无关漂移。提交前只暂存本轮必要内容；无关改动保持原样，不擅自回滚。生成缓存、包管理缓存和临时输出不得混入提交。
- 当用本地反向补丁清理已提交或已合入分支里的越界改动时，必须同时报告工作区 diff 和相对目标分支的 effective diff；不要把本地 dirty 文件直接等同于 PR 仍会变化，也不要只看 effective diff 而忽略待提交的清理补丁。
- 如果本轮操作意外造成 staged、unstaged、untracked 混杂，先说明将进行 index-only 整理，再只对自己本轮文件做 unstage/restage 等索引收敛；不得触碰用户已有 staged 改动或借机修改工作区内容。
- `reports/company/projects/` 默认不得进入 git 提交；只有报告对应开源项目，且经过明确 diff 审查确认不含私有信息时，才允许单独纳入提交。`reports/personal/agent/` 与 `reports/personal/projects/` 保留为普通可提交内容，但提交前仍需按 diff 审查确认没有敏感信息。
- 提交或改写远端最新提交前，必须检查最终提交树而不只检查工作区：用 `git ls-tree -r --name-only HEAD` 或 staged 等价检查确认没有 `.DS_Store`、`reports/company/projects/`、旧报告路径或被忽略的本地内容进入提交；若刚执行 amend，推送前再次确认 `HEAD` 与预期一致。
- 对 IDL、API client、schema、golden、snapshot、lockfile 等生成产物，必须追溯到事实源和生成命令：确认是否由规范脚本生成、是否手改了生成文件、是否把事实源之外的历史漂移一起带入。若生成产物变化远大于本轮目标，必须检查对应事实源 diff 和消费者调用点，说明哪些变化是本轮必需、哪些是已存在但本次补齐、哪些属于无关干扰。
- 当 IDL/API client/schema 等生成产物只为某一功能补契约时，必须按本轮目标建立 allowlist 审查生成 diff：同时查看事实源 diff、生成文件相对目标分支的 diff、生成文件当前工作区 diff和真实消费者；若生成器带出其它接口、安全标记、公共 DTO 或相邻模块变化，先回退到目标基线并只保留本轮契约，或明确请求用户确认同步这些非本轮生成物。未完成 allowlist 审查前，不得声称生成 diff 干净。
- 对公共类型、公共 props、配置 DTO、middleware registry、共享 UI 组件等共享面新增字段或参数前，必须先搜索所有消费者；若需求只服务单个模块或页面，优先用局部类型、专用 panel 或 adapter 承载，不把领域私有数据塞进公共 props。
- 对 API、工具定义、路由、类型签名、公共组件、配置和运行时入口这类共享面，diff 审查必须覆盖当前模块和其它真实消费者：搜索调用点，确认旧 workflow、相邻模块和负向行为仍一致；不能只验证用户截图中的单页或单接口。
- 对 UI 或前后端联动变更，提交前必须结合 diff 结果跑最窄有效验证：类型检查、相关单测、API smoke 或关键页面 workflow。验证报告要区分“当前模块修复”“其它模块未破坏”“仅静态通过”三种状态。
- 当用户用 PR 页面或远端 diff 质疑“文件数不对 / 改动没出现”时，先区分远端 PR 状态与本地状态：PR 只展示已 push 的提交，不展示本地 staged/unstaged；必须同时检查 `git status --short --branch`、`git log --decorate`、`git diff origin/main...HEAD`、`git diff --cached --name-status`，再判断是未提交、未推送、目标分支不对还是 GitHub 页面缓存。
- `git diff` 审查回复必须先给边界结论：推断的预期范围、实际变化范围、是否存在预期外改动或影响面、需要继续确认的文件/模块；即使工作区干净，也要说明是“无本地 diff”还是仍存在 base/head/远端 PR 差异。
- 最终回复或提交前说明应包含：本次 staged 文件清单、刻意保留的其它未提交改动、生成产物来源、已跑验证，以及任何未覆盖风险。若用户质疑 diff 过大，先收敛来源和影响面，再决定是否保留生成结果。

## Git Update

- 当用户说 `git update` 时，将其视为同步目标分支到当前分支的协作指令，而不是 Git 子命令：先确认当前仓库、当前分支、目标分支和工作区状态，再 `fetch` 目标分支，并把目标分支 merge 到当前分支；默认目标为 `origin/main` / `main`，除非用户明确指定其它目标。
- merge 前必须明确本次方向，形如 `当前分支 <- 目标分支`。如果当前分支或目标分支推断不清、当前分支是保护分支、目标分支不是默认主分支/用户指定分支，或用户表达可能指向另一个仓库/分支，应先向用户确认；如果方向清楚且风险检查通过，不要停留在只报告状态。
- 如果工作区存在未提交改动（staged、unstaged、deleted 或 untracked），仍以完成 merge 为默认目标：先做本地改动文件与目标分支新增改动文件的精确重叠检查；若无重叠且无明显生成产物/API client 手改风险，应直接 merge 并保留本地未提交改动。只有存在重叠、生成产物风险、merge 会被 dirty worktree 阻塞或需要 stash/index 操作时，才暂停请求决策。
- dirty worktree 下的 update 报告必须包含：当前分支与 upstream/main 的 ahead-behind、当前本地改动文件、远端 main 新增改动文件、本地改动与远端改动的重叠文件、生成产物/API client 是否被本地手改、以及是否存在需要用户决策的合并风险。
- 当需要预判合并风险时，优先使用只读检查（如 `merge-base`、`merge-tree`、`diff --name-status`、关键冲突信号搜索）；不要把大型 merge-tree 输出原样倾倒到回复里，只提炼冲突/重叠信号。
- 如果 `merge` 因 dirty worktree 被拦截，或 `stash`/index 操作报错，应立即停止进一步自动修复，保留现场并报告阻塞原因；不要连续尝试可能改变索引或工作区状态的绕路命令。
- `git update` 是结果导向流程：若 fetch 后确认目标分支已被当前分支包含，仍可用 `git merge <目标>` 验证 no-op，但不要为了流程创建 stash、重排 index 或输出长报告；成功时最终只给“merge 完成”级别结论，除非用户明确要求细节。
- `git update` 必须保护用户本地改动；详细流程见 `reports/personal/agent/runbooks/2026-06-08-关于AgentHub-projectRefactor.md`，按需加载，不在入口文件展开长步骤。

## Verification Semantics

- 准备声明“完成”“修好”“通过”“可用”“已打通”、准备提交/PR/交付，或需要校准验证结论词时，按需读取 `skills/task-execution-lifecycle/references/completion-evidence-patterns.md`；入口规则只保留硬约束，证据矩阵和例外放在 reference。
- 汇报验证结果时必须区分“UI 可打开 / route 被调用 / gateway wiring 正确 / 错误被暴露”和“端到端能力可用”。只有目标 workflow 返回成功业务结果并满足最小验收条件时，才能说“已打通”“可用”或“验证通过”。
- 对前后端、gateway、runtime、tool、MCP、跨仓库或跨 agent 对接类任务，最终回复必须按链路分层说明验证状态：本地页面或入口状态、请求是否到达预期下游、下游 operation 是否支持、业务结果是否成功、仍阻塞在哪一层。
- 如果 smoke 只证明错误态被正确展示，必须明确写成“错误暴露/缺口确认”，不能写成“接入可用”。遇到 4xx/5xx、`unsupported_operation`、mock/fallback/dry-run、空数据或被跳过步骤时，默认视为未打通，除非用户明确只要求验证错误处理。
- 使用“通过”“可用”“已接上”“打通”这类结论词前，先对照用户目标列出最小验收信号；缺任一信号时用“部分完成”“wiring 已到位”“等待上游支持”或“阻塞于 X”。
- 对实现、优化、接口、UI、跨模块对接或质量风险较高的改动，默认引入“测试 Agent”视角：可以是独立 agent、专门脚本、测试套件或 review checklist，但必须从真实用户 workflow、端到端信号、负向断言和回归风险验证本轮改动；不能只由实现者用 mock、局部单测或手动观察自证。
- 测试 Agent 视角要重点寻找误报：unsupported/partial/fallback/mock/dry-run 被显示为成功、旧入口仍可触发、隐藏路径绕过 clean contract、当前进程或下游服务不是预期版本、以及文档/fixture/scenario 把废弃能力重新升级为“可用”。
- 当发现验证缺口来自流程而非单点 bug 时，优先把测试 Agent 视角固化为项目内可运行验证入口或通用 hub 规则；不要只在最终回复中道歉或口头提醒。

## Tool Policy

- 搜索优先使用仓库搜索能力，例如 `rg`、grep、glob 或当前工具的等价能力。
- 编辑优先使用 patch/diff 风格，避免整文件重写。
- 报告和技能要写到通用目录，不写进工具专属目录。
- 对删除、重置、覆盖配置、安装依赖、联网抓取等动作，按当前工具权限规则请求确认。
- 需要验证时，先跑最窄的有效检查，再根据风险扩大验证范围。
