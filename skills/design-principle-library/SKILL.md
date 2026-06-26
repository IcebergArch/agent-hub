---
name: design-principle-library
description: 当完成或复盘架构设计、coding 分层设计、系统设计、UI/交互设计、数据体系设计、工具协议设计等高风险设计后，用于抽取可复用原则、反模式、检查清单和实践积累，减少后续依赖用户手动 receive。
---

# Design Principle Library

本技能用于把设计与实现中的稳定经验沉淀为可复用原则。目标不是记录某个项目过程，而是让后续类似任务更早做对。

## Trigger

出现以下情况时触发：

- 用户明确说 `receive`、要求复盘设计质量、指出“为什么没想到”“应该自我提升”。
- 完成架构、coding 分层、系统、UI、交互、数据体系、工具协议或运行时设计后。
- 同类问题被用户连续纠偏，或一次问题暴露出明显的通用反模式。
- 外部优秀实践、本地历史文档和当前项目适配之间出现可复用取舍。
- 吸收外部 Agent 方法论、插件、技能库或工程工作流后，需要把稳定机制沉淀为 Hub 原则或检查清单。

不要在普通 typo、机械重命名、一次性实现细节后强行沉淀。

## Workflow

1. **Classify Design Domain**
   - 归类为 architecture、coding-layering、system-design、product-ui、interaction、data-modeling、tool-protocol、runtime、security、observability 或 testing。
   - 若已有专门 Skill 覆盖，优先更新专门 Skill；跨领域通用原则写入本技能或 `AGENTS.md`。

2. **Extract Principle**
   - 抽取保护的不变量：例如 owner 边界、用户任务流、信息层级、状态可见性、数据一致性、执行回执、故障隔离。
   - 抽取反模式：例如把 onboarding 当常态管理页、把局部动作做成页面主按钮、先写 mock 再宣称链路打通、让用户输入系统 ID。
   - 抽取检查清单：设计前需要看的历史文档、外部实践、真实调用链、数据源、状态机、错误态和验证信号。

3. **Ground And Adapt**
   - 原则必须来自三类依据之一：本地历史文档、当前项目真实约束、官方/优秀实践。
   - 写入规则前先去项目名、业务名和一次性上下文，只保留跨任务可复用的机制。
   - 若原则还不稳定，只写到对应 reports；等重复出现或被确认后再升级为 Skill/Rule。

4. **Update Destination**
   - Product UI / 交互：优先更新 `skills/agent-team-router/SKILL.md` 的 UX/Product gate。
   - Product UI / 交互涉及配色、视觉层级、loading、状态反馈、动效、表单、可访问性，或用户说“太快/太慢/抢眼/不舒服/不清楚”时，读取 `references/product-ui-ux-psychology-patterns.md`。
   - 接口、协议、工具调用：优先更新 `skills/interface-contract-audit/` 或 `skills/agent-tool-design/`。
   - 实现计划和分层：优先更新 `skills/implementation-plan/`、`skills/codebase-navigation/` 或 `AGENTS.md`。
   - 测试和验证：优先更新 `skills/test-strategy/`。
   - 外部 Agent 方法论吸收：优先更新 `skills/methodology-harvest/`，再按机制落到专门 Skill。
   - 跨领域自我提升机制：更新本技能和 `AGENTS.md`。

5. **Validate**
   - 搜索近义触发词，避免重复规则。
   - 检查新增规则是否包含触发条件、保护对象、检查工件、例外和验证方式。
   - 最终回复说明沉淀了什么、没有沉淀什么，以及原因。

## Output Shape

规则维护完成后简短汇报：

- `新增/调整`：文件和规则主题。
- `触发条件`：后续什么时候自动使用。
- `自检方式`：如何判断是否做对。
- `未沉淀`：哪些项目细节没有写入长期规则。

## Principle Seeds

- 设计不是视觉或结构的后置美化；先判断对象、任务、状态、动作和反馈归属，再选择布局。
- Product UI 顶层先定设计哲学和体验气质；配色、样式、交互和动效只是具体场景里的执行选择与搭配，不能反过来主导产品判断。
- 长时媒体或生成任务默认要建立等待预期：低权重说明大致耗时、仍在进行和完成后如何更新；不能只给无语义 loading。
- 管理态和创建/onboarding 态默认分离；常态页面承载对象管理，创建流程只在 modal、drawer、wizard 或独立 route 中出现。
- 状态颜色必须服务语义；当前定位、成功、失败、危险、草稿、离线不能混用同一强调色。
- 动作视觉重量必须低于其作用域：页面级主动作可以强，局部刷新/检查/重试应贴近对象且轻量。
- 系统设计先保护 owner、边界、协议、状态机、错误回执和验证信号，再考虑扩展性。
- coding 分层先查真实调用链和事实源；不要把临时 UI、handler 或 service 写成跨模块协议。
- 数据体系设计先区分系统 ID、用户可编辑字段、canonical ref、source hint、权限主键和审计字段。
- 平台资源迁移先过 fidelity gate：Skill、Prompt、Tool 或 Resource 必须是通用稳定资产，不把项目示例、smoke 产物、路径引用或待补齐清单伪装成基座能力；详细检查见 `references/platform-resource-fidelity.md`。
- 外部方法论吸收先问“保护的不变量是什么”，再决定落位；不因外部体系完整就照搬它的目录、品牌语言或所有门禁。
- 外部技能里的工具名、agent manifest、subagent 类型和运行时脚本是平台能力提示，不是 Hub 合约；先按能力语义映射到当前工具 adapter，不能为迁移方便伪造不可执行入口。
- 外部 prompt 模板默认抽成审查结构、输入占位、证据要求和输出 checklist；只有当 Hub 拥有同等运行时与调度语义时，才沉淀为可直接执行的 prompt。
- 外部分支、worktree、发布或清理流程默认抽成权限、provenance、验证和用户确认 gate；不要把固定菜单或自动清理动作照搬成 Hub 默认行为。
- 公共 Agent 资产要先过跨工具兼容门：Codex/Cursor 都成立的不变量写入公共 Skill/Rule；工具专属能力、UI 指令、MCP、浏览器、subagent 或宿主线程语义写入 adapter、fallback 或 watch，不能让另一个工具误以为自己具备该能力。
- 创意和产品想法先区分底层目标与第一版方案；设计确认按风险缩放，不把所有小修都升级为重 spec，也不在高模糊任务里跳过方案对比。
- 需求 brief/spec 进入计划前要过 readiness gate：完整性、一致性、清晰度、scope 和 YAGNI 只在会导致计划做错、卡住、越界或无法验收时阻塞；文字 polish 和可选增强不能冒充 blocker。
- 复杂设计确认要分段：架构、对象/组件、数据流、错误/权限和验证各自确认；不要让用户一次性批准一个未逐段理解的大方案。
- 单元边界设计先证明 purpose、interface、dependencies 和 testability：一个模块、组件、服务、工具或页面若不能被外部消费者通过接口理解和验证，就先拆职责、收窄 owner 或调整依赖边界，再进入实现计划。
- Skill 写作按“触发描述 -> 短正文 -> 按需 reference -> 压力测试”分层；不要用长 description、一次性复盘或未验证口号替代可触发、可验证的流程资产。
- Skill 可发现性要按用户真实表达设计：description / Trigger 覆盖症状、错误/状态词、对象类型、工具/文件和近义词，同时写清排除条件，避免过宽误触发。
- Skill 执行语义要区分强门禁、弹性模式和参考资料：强门禁保护 stop rule 和证据要求，弹性模式保护不变量，reference 只提供按需材料；用户给出的 WHAT 不自动豁免适用 Skill 的 HOW。
- Skill 谨慎触发要允许 false positive exit：读完当前正文确认不适用时，简短记录原因并退出；不要因已经加载就把相邻但错误的流程套到任务上。
- 高频加载面要有上下文预算：`AGENTS.md`、`README.md`、adapter、frontmatter 和常用 `SKILL.md` 只保留触发、硬约束、stop rule、输出形态和指针；长解释、完整 catalog、历史论证和命令细节下沉到 reference、report 或 script help。
- 高约束 Skill 测试要形成 RED baseline、GREEN re-test、REFACTOR 补洞和必要的 meta clarity 证据；只有压力场景草稿不能证明规则会改变 Agent 行为。
- Skill references 要保持一层可达、长文有目录、时间敏感信息有刷新条件；新增脚本或工具说明必须声明运行时、权限、输入输出和验证信号，不能假设外部工具存在。
- 并行 agent 设计先证明问题域独立，再写自包含 brief、合并复核和统一验证；不要把“可并发运行”误当成“适合并行决策或并行编辑”。
- 委派任务要有边界：子 agent / 子任务执行者不重新做全局技能发现、全仓规划或方法论吸收；只执行 brief scope，缺上下文就返回状态，scope 外问题只作为 concern。
- 子 agent 提问是设计反馈：先判断是 brief 缺上下文、scope 冲突、计划缺口、任务过大还是普通实现选择；补充到 brief 或修 plan 后再继续，不催促执行者猜。
- 完成状态设计要把结论词绑定证据层级；不能用局部静态信号、旧输出或代理报告支撑端到端成功声明。
- 验证输出要和目标匹配：命令成功只是原始信号；必须确认路径、过滤条件、测试/断言数量、skipped/no-op/cache、当前服务版本和业务语义都支撑结论。
- 需求满足是逐项核对结论：有 spec、plan、issue、验收标准或多轮纠偏时，先列 Requirement -> Evidence -> Verdict；测试绿、build 过或 agent summary 不能替代 requirements trace。
- 根因状态也是证据声明：系统性排查后仍未知时，应写 root cause unresolved、已排查边界、证伪假设、临时处理和后续观测条件；不要为了收口编造“很可能”的根因。
- 调试假设要形成账本：Observation、Hypothesis、Test、Output、Result、Next 缺一不可；被证伪的假设不能继续支撑修复，inconclusive 不能包装成通过。
- 参考实现不是可直接复制的答案：遇到相似成功路径、官方示例或历史测试时，先完整阅读入口、调用链、配置、错误处理和测试，列 working/broken 差异并逐项验证；不要只凭函数名或局部片段搬代码。
- Skill 触发纪律要平衡覆盖率和上下文成本：读当前适用 Skill 正文，不靠记忆；选择最小必要集合，不把外部强门禁照搬成全量加载。
- 测试设计要防实现污染：探索代码只能提供不变量和风险信号，不能反向决定测试；正式行为变更需要 red/green、临时回退 proof 或明确的等价验证缺口。
- RED 失败原因必须匹配目标缺口：测试红了还不够，失败必须来自目标行为缺失；语法、导入、fixture、mock、命令 scope 或 0 tests/skipped 都只是测试表达问题。
- TDD 例外要先归类再验证：文档、配置、生成代码、低风险样式、一次性探索或工具不可用可以走等价验证；行为变化和 bugfix 不能用“太小、太急、手动测过、之后补测”作为豁免。
- 可测试性是设计反馈：测试难写、setup 巨大或必须 mock 多层时，优先检查 API、行为切分和依赖边界，而不是扩大 mock 世界或给生产对象加测试专用入口。
- 异步和 flaky 问题优先等真实条件：事件、状态、计数、文件、UI 可见性或 API 查询结果；任意 sleep 只有在测试真实 timing 机制时才允许，并必须说明触发条件、等待来源、timeout 和失败诊断。
- 计划审查要围绕可执行性而不是审美：只把会导致做错、卡住、越界或无法验证的问题列为 blocker；完整性、spec 对齐、任务拆分和可构建性是默认四个检查面。
- 默认/受保护分支上的工作要先判断意图：非平凡实现优先隔离，用户明确指定的规则/文档维护可以原地进行，但收尾必须报告 branch、diff 边界和未提交状态。
- 分支或工作区收尾要给有限且按环境裁剪的选项：先确认验证、diff 边界、workspace 类型和 base branch，再让用户选择保留、提交、PR、合并或丢弃；不要用开放式“下一步怎么办”掩盖风险判断。
- Review feedback 处理要先建模再动手：多项反馈先拆成状态、依赖和验证证据；模糊且可能影响其它项的反馈必须先澄清，不能先修半懂子集制造返工。
- Review 要把实现报告、PR 描述、subagent 状态和测试摘要当成待验证 claims：每个高风险声明都要用代码、diff、测试或运行信号证明；若问题来自计划/spec 本身，应标成 Plan issue 回到计划层。
- 卡住时的 review 是换视角，不是收尾背书：必须带上当前卡点、已证伪假设、diff、证据缺口和继续叠补丁的风险，再决定回到根因追踪、修计划、补 baseline 还是继续实现。
- Review verdict 是行动接口：`blocked`、`with fixes`、`ready with risk`、`ready` 或 `insufficient evidence` 必须由最高严重度、验证缺口和 diff 边界决定；不能用“整体还行”替代可执行结论。
- Review 反馈要按来源建模：用户当前反馈和已确认决策是约束，外部 reviewer、subagent 和自动工具只是建议或信号；它们不能静默覆盖用户 scope、架构、兼容或提交边界，冲突时先标记 source conflict 并请求确认。
- Review pushback 不是社交姿态而是证据动作：只用代码、测试、约束和用户决策反驳；反驳后发现自己错了，先给新证据和短误判原因，再修复验证。
- 多任务或多 agent 工作的局部通过不能相加成整体完成；最终必须做跨任务接口、状态、fallback、端到端验收和 diff 边界的整合 review。
- Agent/tool 能力选择要按任务复杂度匹配：机械小改可轻量，集成调试用标准能力，架构/安全/权限/review/最终整合需要强判断；阻塞重试必须改变能力、上下文或任务粒度。
- 产品 case 不是单点 smoke：页面上下文、素材来源、工具契约、运行时存储、最终产物和用户验收共同构成成功条件；直接 API 成功、单图 smoke、tool running 或局部产物 URL 都不能替代页面端到端 case 成功。
- 完成状态的语言也属于接口：积极语气、满意表达和“应该可以”都会被用户理解成状态信号；没有 fresh evidence 时只能报告事实进度、验证缺口和下一步。
- 分支或交付收尾必须先过验证门：必要验证失败、未跑或证据不足时，只能继续验证、修复或保留审查；不要提前提供提交、PR、合并、丢弃或清理工作区选项。
