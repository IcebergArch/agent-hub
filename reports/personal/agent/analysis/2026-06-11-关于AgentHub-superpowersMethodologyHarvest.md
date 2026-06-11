日期：2026-06-11
类型：analysis
项目：agent-hub
来源：当前对话要求将 Superpowers 的优秀设计、资源和工作流吸收到 Agent Hub，并保持为常驻任务
版式：结论先行，按机制映射到 Hub 资产
最近跟进：2026-06-11 14:33:08 CST (+0800)

# Agent Hub 吸收 Superpowers 方法论

## Summary

本次升级把 Superpowers 视为一个外部 Agent 方法论样本，而不是要复制的目录模板。Agent Hub 采纳它最稳定的机制：技能优先级、设计门、可执行计划、红绿重构、系统化调试、完成前证据、独立审查、并行/子 agent 工作流、分支收尾和技能写作自测。

落地方式是 Hub-native：

- 通用触发规则写入 `AGENTS.md`。
- 可重复方法写入 `skills/methodology-harvest/`。
- 既有执行技能按机制增强，而不是新增一堆近义技能。
- 本报告记录来源、取舍和后续刷新方式。

## Source Scope

本次只读取本地已安装 Superpowers 插件源码，未联网：

```text
/Users/shatang/.codex/plugins/cache/openai-curated/superpowers/c6ea566d
```

插件 manifest 信息：

- name: `superpowers`
- version: `5.1.3`
- repository: `https://github.com/obra/superpowers`
- skills path: `./skills/`

重点读取了：

- `README.md`
- `skills/using-superpowers/SKILL.md`
- `skills/brainstorming/SKILL.md`
- `skills/writing-plans/SKILL.md`
- `skills/test-driven-development/SKILL.md`
- `skills/systematic-debugging/SKILL.md`
- `skills/verification-before-completion/SKILL.md`
- `skills/requesting-code-review/SKILL.md`
- `skills/receiving-code-review/SKILL.md`
- `skills/dispatching-parallel-agents/SKILL.md`
- `skills/subagent-driven-development/SKILL.md`
- `skills/using-git-worktrees/SKILL.md`
- `skills/finishing-a-development-branch/SKILL.md`
- `skills/writing-skills/SKILL.md`
- 相关 references：测试反模式、根因追踪、condition-based waiting、技能压力测试。

资源盘点还覆盖了：

- prompt templates：code reviewer、implementer、spec reviewer、code quality reviewer、plan/spec reviewer。
- scripts：visual companion server/helper、graph render、polluter finder。
- references：debugging、TDD anti-pattern、skill testing、persuasion、authoring best practices。
- assets/plugin metadata：icon、svg、plugin manifest、安装与社区说明。

后续如果用户要求“最新 Superpowers”或要对齐官方新版本，需要重新核对官方来源或当前安装版本。

## Drift Review

本次映射以本地 Superpowers `5.1.3` 为基准。后续出现以下情况时，不应直接沿用本报告结论：

- `.codex-plugin/plugin.json` 的 version、repository、skills path、capabilities 或默认 prompt 变化。
- `skills/` 下新增、删除、改名 skill，或关键 skill 的 description 触发条件变化。
- `requesting-code-review`、`receiving-code-review`、`subagent-driven-development`、`systematic-debugging`、`test-driven-development`、`writing-skills` 等已采纳机制的正文或 references 有语义变化。
- 用户明确要求“最新 Superpowers”“重新吸收”“升级外部方法论”。

复核策略：先比较 manifest、技能清单和本报告采纳的代表性文件；若没有语义变化，只更新版本证据。若有变化，刷新 `superpowers-pattern-map.md`、相关 Hub Skill 和新的 dated analysis。

## Decision

Agent Hub 采用 Superpowers 的“机制内核”，不采用它的完整运行制度。

采纳：

- **技能触发纪律**：进入任务前先按 Hub Loading Order 找相关 Skill；触发后区分强门禁、弹性模式和参考资料，用户给出的 WHAT 不自动豁免适用 Skill 的 HOW；谨慎读取 Skill 后确认不适用时走 Skill False Positive Exit Gate，简短记录原因并退出，不因已经加载就强套流程；被委派执行明确子任务时不重跑全局技能发现，只按 brief scope 读取必要 Skill。
- **设计先行但按风险缩放**：高模糊/高风险任务先设计；当想法跨多个独立子系统时先拆成可独立验收的子问题；复杂设计按架构、对象/组件、数据流、错误/权限和验证分段确认；需求 brief/spec 进入计划前过 Spec Readiness Gate，检查完整性、一致性、清晰度、scope 和 YAGNI，并区分 blocker 与 advisory；新增或重塑单元先说清 purpose、interface、dependencies 和 testability；小修冻结不变量后直接做。
- **计划可执行**：计划要写文件职责、精确路径、步骤、命令、期望结果、自检和可交给无会话记忆执行者的 handoff context；高风险或外部来源计划先过 completeness、spec alignment、task decomposition、buildability review。
- **测试先证明失败**：对 bugfix、核心逻辑和行为变化优先采用 red/green；RED 必须过 Red Failure Reason Gate，证明失败来自目标行为缺失而不是语法、导入、fixture、mock、命令 scope 或 0 tests/skipped；TDD 例外必须过 TDD Exception Gate，先分类为文档、配置、生成代码、低风险样式、一次性探索或工具不可用，并给出等价验证；先写出的实现只能算探索，必须隔离、临时回退或用等价 proof 证明测试能抓住目标缺失；测试难写、setup 过大或必须 mock 多层时，先把它当成接口、行为切分或依赖边界的设计反馈；异步测试必须等真实条件，不用 sleep 掩盖 race。
- **根因优先**：没复现、没追踪、没单假设验证前不堆修复；复杂 bug 用 Hypothesis Ledger Gate 记录 Observation、Hypothesis、Test、Output、Result 和 Next，证伪假设不能继续支撑修复；存在相似成功路径、官方示例或历史测试时，先过 Reference Pattern Comparison Gate，完整阅读参考路径并列 working/broken 差异后再迁移机制；flaky、timeout、hang 优先过 Condition-Based Waiting Gate，用事件、状态、计数、文件、UI 可见性或 API 查询结果等真实条件定位；连续 3 次局部修复失败后转向架构、边界、测试前提或需求理解审查，不继续试第 4 个 patch；系统性排查后仍没有根因时，用 root cause unresolved、已排查边界、证伪假设和后续观测条件收口，不编造根因。
- **完成前证据**：没有本轮新鲜且匹配目标的验证输出，不能说修好、通过、可用或打通；验证输出要检查命令 scope、覆盖目标、测试/断言数量、skipped/no-op/cache 和业务语义；声明 requirements met 或任务完成前，要把用户要求、spec、plan、issue、验收标准、非目标和后续纠偏列成 checklist，并逐条绑定证据；积极语气、满意表达和“应该可以”也要过证据门，避免暗示未验证成功。
- **独立审查与反馈闭环**：重要任务按“规格符合性 -> 代码质量/体验质量”分层 review；实现者报告、PR 描述、subagent 状态和测试摘要都只是 claims，必须用代码、diff、测试或运行信号独立证明；任务卡住时先过 Fresh Perspective Review Gate，带上当前卡点、已证伪假设、diff 和证据缺口，不继续叠补丁；review 最终过 Review Verdict Gate，结论必须与最高严重度、验证缺口和 diff 边界一致；若问题来自计划/spec 本身，标成 Plan issue 回到计划层；多项 feedback 先 intake，模糊且相互影响时先澄清，不抢跑半懂子集；反馈按来源建模，外部 reviewer、subagent 和自动工具不能静默覆盖用户已确认 scope、架构、兼容或提交边界，冲突时标记 source conflict 并请求确认；技术性 pushback 必须绑定代码、测试、约束或用户决策证据，pushback 错了要事实性收回并修正；多任务/子 agent 全部结束后做最终整合 review；PR inline feedback 逐项保留 comment/thread 身份并在原线程用证据收口。
- **并行只给独立域**：独立问题可并行，不把共享文件和共享状态交给并发 agent；agent/tool 能力按任务复杂度选择，机械小改可轻量，架构/安全/权限/review/最终整合要用强判断能力；子任务执行者只做 brief scope，缺上下文返回状态，scope 外问题作为 concern；子 agent 提问先过 Delegated Question Gate，判断是补 context、拆任务、修 plan 还是问用户最小阻塞问题。
- **工作区隔离先检测**：长任务和多文件任务先识别 branch、worktree/宿主隔离和 dirty 状态；非平凡实现遇到默认/受保护分支时先确认 in-place 意图或使用隔离 branch/workspace；优先使用宿主隔离，不默认手动创建 worktree；隔离环境 ready 前记录 clean baseline 或 baseline failure。
- **已有计划先审后执**：用户提供计划/spec/checklist 时，先批判性审查目标、文件、步骤、验证和风险；计划可靠后连续推进。
- **计划进度可恢复**：执行计划时维护轻量任务账本，最多一个任务 `in_progress`；中断或继续前先对账当前 diff、已改文件和验证证据，再从真实未完成任务恢复。
- **分支收尾给选项**：完成后默认保留给用户审查；只有用户要求时才进入 commit、push、PR、merge 或 discard；必要验证失败、未跑或证据不足时，先停在修复、继续验证或保留审查，不提供提交、PR、合并、丢弃菜单；收尾前确认验证、diff 边界、workspace 类型和 base branch，再给有限且按环境裁剪的选项；合并后重新验证，worktree 清理必须有 provenance，丢弃必须二次确认。
- **技能也要可测试并可部署**：新/改 Skill 要有触发条件、真实用户表达/症状/错误/对象/工具/近义词覆盖、抗跑偏检查、Loaded Surface Token Budget Gate、Cross-Tool Compatibility Gate 和引用验证；重规则的压力测试要区分 RED baseline、GREEN re-test、REFACTOR 补洞和必要的 meta clarity；每个 Skill 改完先完成索引、触发、reference 拓扑、工具/脚本假设和 diff 边界验证，再继续改下一个 Skill。

不默认采纳：

- 不把 Superpowers 的“每次对话都必须先加载 using-superpowers”照搬为 Hub P0；Hub 已有 live loader 和技能触发顺序。
- 不强制所有创造性任务都写 design doc 并 commit；Hub 按任务风险决定是否写报告、计划或直接执行。
- 不强制每个实现步骤都用 subagent；Codex/Cursor 能力、权限和用户节奏不同。
- 不复制 `docs/superpowers/` 路径、安装指南、品牌资产、社区链接或外部插件缓存路径。

## Resource Decisions

| Superpowers 资源 | 本次处理 | 原因 |
| --- | --- | --- |
| using-superpowers | 吸收到 `AGENTS.md` Loading Order、`skills/README.md` 和 `methodology-harvest/references/skill-trigger-discipline.md` | Hub 保留“先检查适用 Skill、读当前正文、Delegated Task Boundary、区分 Skill Compliance Mode、Skill False Positive Exit Gate、用户 WHAT 不自动豁免 HOW”的纪律，不采用 1% 强门禁和外部平台工具语义 |
| reviewer / implementer prompt templates | 不原样复制，抽成两段 review、独立上下文、review brief、Implementation Report Skepticism Gate、Fresh Perspective Review Gate、Feedback Source Priority Gate、Review Verdict Gate、Critical/Important/Minor action gate、计划 readiness review、Plan issue 出口、feedback intake、反馈核验、证据型 pushback、错误 pushback 修正、PR thread 处理规则、`implementation-plan/references/plan-review-checklist.md` 和 `code-review/references/review-checklists.md` | 原模板绑定 Superpowers subagent 流程；Hub 需要先保持 Codex/Cursor 共用 |
| brainstorming flow | 吸收到 `requirements-brief` 的上下文先读、范围过大先拆分、单问题澄清、2-3 方案、单元边界设计、分段设计确认、Spec Readiness Gate、设计自审和按风险缩放的确认门 | Hub 不采用所有创意任务都必须写 spec+commit 的硬门禁 |
| debugging scripts、pressure tests 和 condition-based waiting 示例 | 不复制脚本，吸收为 `bug-reproduction` 的根因追踪、边界诊断、Reference Pattern Comparison Gate、Hypothesis Ledger Gate、Condition-Based Waiting Gate、重复失败后的架构/前提审查、no-root-cause 证据门、incident mitigation 与 root cause fix 分层规则和 `references/debugging-patterns.md`，并在 `test-strategy/references/testing-anti-patterns.md` 增加 Async Wait Discipline | 脚本可按项目语言重写；机制比脚本更稳定 |
| TDD anti-patterns | 吸收到 `test-strategy` 的 red/green、Red Failure Reason Gate、TDD Exception Gate、exploration contamination gate、refactor proof、testability feedback、mock discipline 和 `references/testing-anti-patterns.md` | 重点防误报，长反模式按需加载；Hub 不照搬“删除一切”的绝对措辞，但保留“RED 必须证明目标缺失”“TDD 例外要有类别和等价验证”“探索实现不能污染测试设计”和“测试难写先反馈设计”的不变量 |
| verification-before-completion | 吸收到 `task-execution-lifecycle` 的完成证据 reference、Verification Output Match Gate、Requirements Trace Gate、Positive Language Gate 和 `AGENTS.md` 分层验证语义 | Hub 需要区分已验证、部分验证、wiring、错误暴露和阻塞；同时避免用积极语气包装未验证状态，避免把 0 tests、skipped、no-op、cached 或错误 scope 当作通过，也避免用测试绿替代逐项验收 |
| executing-plans / using-git-worktrees / finishing branch | 吸收到 `task-execution-lifecycle` 的计划审查、计划进度账本、中断恢复对账、hidden context 检查、未经确认 git 动作拦截、默认分支工作门、隔离检测、submodule guard、clean baseline gate、base branch 确认、Failed Verification Finish Stop Gate、合并后验证、worktree provenance、detached workspace 收尾裁剪、结构化收尾选项、证据账本和分支收尾 reference | Hub 需要保护用户本地改动和宿主 workspace，不照搬外部自动 worktree/固定菜单或外部 Todo 工具名 |
| dispatching-parallel-agents / subagent-driven-development | 吸收到 `task-execution-lifecycle` 的并行域判断、自包含 brief、Delegated Task Boundary Gate、Delegated Question Gate、Capability Selection Gate、subagent status gate、冲突合并、review loop、两阶段 review 和 Final Integration Review | Hub 不要求所有平台都有 subagent；并行只在独立域成立时使用，按任务复杂度选择能力，局部通过后仍需整体复核 |
| skill testing / writing-skills references | 吸收到 `methodology-harvest`、`skill-authoring-patterns.md`、`skill-pressure-test-template.md` 和 `skills/README.md` | 技能描述只写触发条件，Trigger 覆盖真实用户表达、症状词、错误/状态词、对象类型、文件/工具和近义词，正文渐进加载，高频加载面过 Loaded Surface Token Budget Gate，公共规则过 Cross-Tool Compatibility Gate，reference 一层可达，长 reference 有目录，重规则压力测试要有 baseline/re-test 证据；Skill 变更后必须完成单个 Skill 的部署验证门，脚本/工具说明要写清依赖、权限、输入输出和验证信号 |
| visual companion scripts | 不复制脚本；吸收“看比读更清楚才用视觉辅助”、逐题判断、回到文字时清理 stale visual 的机制 | 与 Superpowers browser companion 运行时耦合，Hub 按当前工具能力选择 Mermaid、截图、浏览器、临时 HTML 或图像生成 |
| brand assets、plugin manifest、安装/社区文档 | No-copy | 不属于 Hub 长期方法论资产 |

## Hub Changes

新增：

- `skills/methodology-harvest/SKILL.md`：外部 Agent 方法论吸收流程。
- `skills/methodology-harvest/references/superpowers-pattern-map.md`：Superpowers -> Hub 机制映射表。
- `skills/methodology-harvest/references/superpowers-coverage-audit.md`：Superpowers 14 个 skills、主要资源类别和机制细节的覆盖审计账本。
- `skills/methodology-harvest/references/external-skill-portability.md`：外部 skill 的工具名、agent manifest、prompt、script、视觉和压力测试资源的 Hub 移植规则。
- `skills/methodology-harvest/references/harvest-evidence-template.md`：后续外部方法论吸收的证据矩阵模板。
- `skills/methodology-harvest/references/skill-trigger-discipline.md`：Skill 触发纪律、最小必要集合、Skill False Positive Exit Gate、Delegated Task Boundary、Skill Compliance Mode、用户 WHAT/HOW 边界和反跑偏检查。
- `skills/methodology-harvest/references/skill-pressure-test-template.md`：新/改高约束 Skill 或 Rule 的压力场景模板，以及 RED baseline、GREEN re-test、REFACTOR 补洞和 meta clarity 的证据链。
- `skills/methodology-harvest/references/skill-authoring-patterns.md`：Skill 写作、Trigger Discovery Coverage、渐进加载、Loaded Surface Token Budget Gate、Cross-Tool Compatibility Gate、reference 拓扑、压力测试证据链、单 Skill 部署验证门、脚本/工具假设、自由度控制和反跑偏 checklist。
- `skills/implementation-plan/references/plan-review-checklist.md`：计划 readiness review，区分完整性、spec 对齐、任务拆分、单元边界、可构建性，以及 blocker 与 advisory。
- `skills/bug-reproduction/references/debugging-patterns.md`：复杂 bug 的根因追踪、Reference Pattern Comparison Gate、Hypothesis Ledger Gate、Condition-Based Waiting Gate、污染源定位、no-root-cause 证据门、重复失败后的架构/前提审查、incident mitigation 分层和多层防御模式表。
- `skills/test-strategy/references/testing-anti-patterns.md`：mock、RED 失败原因匹配、TDD 例外分类、测试先后顺序、探索代码污染测试设计、Async Wait Discipline、测试专用生产 API 和误报测试的反模式清单。
- `skills/code-review/references/review-checklists.md`：规格符合性、review brief、Implementation Report Skepticism Gate、Feedback Source Priority Gate、Review Verdict Gate、Critical/Important/Minor action gate、代码质量、证据要求、Plan issue 出口、Feedback Intake Gate、Technical Pushback、错误 pushback 修正、Final Integration Review、review feedback 和 PR thread 处理的审查清单。
- `skills/task-execution-lifecycle/references/completion-evidence-patterns.md`：完成、修好、通过、可用、已打通等结论词的证据门槛、Verification Output Match Gate、Requirements Trace Gate、Positive Language Gate 和状态分层。
- `skills/task-execution-lifecycle/references/execution-control-patterns.md`：计划执行、计划进度账本、中断恢复对账、默认分支工作门、工作区隔离、submodule guard、clean baseline gate、base branch 确认、Failed Verification Finish Stop Gate、合并后验证、worktree provenance、detached workspace 收尾裁剪、结构化收尾选项、证据账本和分支收尾模式表。
- `skills/task-execution-lifecycle/references/parallel-agent-coordination.md`：并行任务拆分、自包含 brief、Delegated Task Boundary Gate、Delegated Question Gate、Capability Selection Gate、subagent status gate、review loop、Final Integration Review、合并复核和冲突防护模式表。
- `skills/requirements-brief/references/idea-shaping-patterns.md`：模糊想法澄清、范围拆分门、方案探索、Spec Readiness Gate、单元边界设计、分段设计确认、视觉辅助逐题判断、stale visual 清理和 brief 自审模式表。

增强：

- `AGENTS.md`：新增外部 Agent 方法论吸收触发规则，并在 Verification Semantics 中指向完成证据 reference。
- `README.md`：把“持续吸收优秀 Agent 方法论”写入长期协作上下文。
- `.cursor/rules/000-core.mdc`：为 Cursor 增加 `methodology-harvest` 的短触发入口，不复制长规则。
- `agents/codex.md`、`agents/cursor.md`：增加外部 Skill 平台工具名的能力映射规则，避免照搬 `Task`、`TodoWrite`、agent manifest 或不可执行脚本。
- `skills/README.md`：登记 `methodology-harvest`，并指向 Skill 触发和写作参考。
- `skills/implementation-plan/SKILL.md`：加入文件职责、no-placeholder、计划自检、Plan Review Gate、fresh-executor handoff context 和用户确认前不默认 git 收尾。
- `skills/requirements-brief/SKILL.md`：加入想法探索 reference 入口、brief/spec 进入计划前的 readiness review，用于高模糊需求的方案收敛。
- `skills/test-strategy/SKILL.md`：加入 red/green 证明、TDD Exception Gate、探索代码隔离/临时回退 proof、refactor proof、testability feedback、mock discipline 和测试误报检查，并通过 `references/testing-anti-patterns.md` 承载长反模式。
- `skills/bug-reproduction/SKILL.md`：加入根因调查、参考实现完整对比、单假设验证、condition-based waiting 和线上止血/根因修复分层，并通过 `references/debugging-patterns.md` 承载复杂调试模式。
- `skills/task-execution-lifecycle/SKILL.md`：加入证据优先、两段 review、并行边界、workspace isolation、已有计划审查和分支收尾选项，并通过 `references/completion-evidence-patterns.md`、`references/execution-control-patterns.md` 和 `references/parallel-agent-coordination.md` 承载长流程。
- `skills/code-review/SKILL.md`：加入接收 review feedback 的技术核验流程、多项 feedback intake 和 PR inline thread 收口规则，并通过 `references/review-checklists.md` 承载两阶段审查 checklist。
- `skills/design-principle-library/SKILL.md`：加入外部方法论吸收后的原则沉淀路径。

## Classification

| 内容 | 分类 | 处理 |
| --- | --- | --- |
| Superpowers 方法论整体 | Report | 写成本报告，作为来源和取舍索引 |
| 方法论吸收流程 | Skill | 新增 `methodology-harvest` |
| Superpowers 映射表、覆盖审计、外部 skill 移植规则、证据模板、Skill 触发纪律、压力测试模板、Skill 写作模式 | Reference | 放入 `skills/methodology-harvest/references/` |
| 系统化调试模式表 | Reference | 放入 `skills/bug-reproduction/references/` |
| 测试反模式与 review checklist | Reference | 放入 `skills/test-strategy/references/` 和 `skills/code-review/references/` |
| 完成证据模式表 | Reference | 放入 `skills/task-execution-lifecycle/references/` |
| 执行控制模式表 | Reference | 放入 `skills/task-execution-lifecycle/references/` |
| 并行协作模式表 | Reference | 放入 `skills/task-execution-lifecycle/references/` |
| 需求澄清与方案探索模式表 | Reference | 放入 `skills/requirements-brief/references/` |
| 需求澄清、计划、测试、调试、验证、review 机制 | Skill updates | 合并进既有技能 |
| 全局与工具入口触发 | Rule | 写入 `AGENTS.md` Loading Order、External Reference Refresh 和 `.cursor/rules/000-core.mdc` |
| 安装说明、品牌资产、社区链接 | No-copy | 保留在外部来源，不进入 Hub |

## Completion Evidence Matrix

| 要求 | 当前证据 | 状态 |
| --- | --- | --- |
| 系统盘点 Superpowers 入口、技能、references、prompt、script、assets | `Source Scope` 与 `Resource Decisions` 列出读取范围和资源类别；`superpowers-coverage-audit.md` 逐项记录 14 个 skill、agent manifest、tool mapping、pressure test、visual tooling、主要资源类别和机制细节状态 | 已满足 |
| 吸收优秀设计和工作流 | `Decision` 列出技能触发、Skill False Positive Exit Gate、Delegated Task Boundary、Delegated Question Gate、Skill Compliance Mode、用户 WHAT/HOW 边界、Trigger Discovery Coverage、设计先行、Sectioned Design Approval Gate、Spec Readiness Gate、Unit Boundary Design Gate、计划可执行、Plan Review Gate、计划进度可恢复、red/green、Red Failure Reason Gate、TDD Exception Gate、探索代码污染防护、Testability Feedback Gate、Reference Pattern Comparison Gate、Hypothesis Ledger Gate、Async Wait Discipline、Condition-Based Waiting Gate、根因优先、No Root Cause Evidence Gate、证据优先、Verification Output Match Gate、Requirements Trace Gate、Positive Language Gate、review、Implementation Report Skepticism Gate、Fresh Perspective Review Gate、Review Verdict Gate、Feedback Source Priority Gate、Plan issue 出口、Feedback Intake Gate、Technical Pushback、错误 pushback 修正、Final Integration Review、Capability Selection Gate、并行边界、默认分支工作门、workspace isolation、计划执行、Structured Finish Options Gate、Failed Verification Finish Stop Gate、分支收尾、Skill TDD 证据链、Loaded Surface Token Budget Gate、Cross-Tool Compatibility Gate、reference 拓扑和工具/脚本假设 | 当前快照已满足 |
| 资源按 Hub 边界处理 | `Resource Decisions`、`superpowers-pattern-map.md`、`superpowers-coverage-audit.md` 和 `external-skill-portability.md` 标记 prompt/script/assets/manifest/tool mapping 的 adapt 或 no-copy | 已满足 |
| 沉淀为长期可用 Skill | 新增 `skills/methodology-harvest/SKILL.md`、`superpowers-pattern-map.md`、`superpowers-coverage-audit.md`、`external-skill-portability.md`、`harvest-evidence-template.md`、`skill-trigger-discipline.md`、`skill-pressure-test-template.md`、`skill-authoring-patterns.md`，并补充 `bug-reproduction/references/debugging-patterns.md`、`test-strategy/references/testing-anti-patterns.md`、`code-review/references/review-checklists.md`、`task-execution-lifecycle/references/completion-evidence-patterns.md`、`task-execution-lifecycle/references/execution-control-patterns.md`、`task-execution-lifecycle/references/parallel-agent-coordination.md`、`requirements-brief/references/idea-shaping-patterns.md` | 已满足 |
| 沉淀为长期规则 | `AGENTS.md` Loading Order 与 External Reference Refresh 增加外部方法论吸收触发，Verification Semantics 指向 `completion-evidence-patterns.md`；`README.md` 写入常驻升级说明；`.cursor/rules/000-core.mdc` 增加 Cursor 短触发入口 | 已满足 |
| 沉淀为报告 | 本报告写入 `reports/personal/agent/analysis/`，并在 `reports/index.md` 建索引 | 已满足 |
| 既有技能已吸收机制 | `implementation-plan`、`test-strategy`、`bug-reproduction`、`task-execution-lifecycle`、`code-review`、`design-principle-library` 已增强 | 已满足 |
| 可发现链路 | `skills/README.md` 登记 `methodology-harvest` 并指向 portability reference；`.agents/skills` 指向 `skills/`；`.cursor/rules/000-core.mdc` 指向 `@skills/methodology-harvest/SKILL.md`；`agents/codex.md` 和 `agents/cursor.md` 指向外部 Skill 能力映射规则；入口搜索可追到 Skill/reference/report | 已满足 |
| 边界保护 | 不复制 Superpowers 品牌资产、安装说明、plugin manifest、prompt 原文、工具绑定脚本；公司项目报告不进入索引 | 已满足 |

## Future Checklist

后续每次吸收外部 Agent 方法论，按这个顺序执行：

1. 先确认来源范围、版本和是否需要联网刷新。
2. 更新 coverage audit：技能清单、prompt、scripts、assets、references 和 no-copy 项都要有处理状态。
3. 读 Hub 现有入口和相邻技能，避免重复造 Skill。
4. 按机制而不是目录抽取：触发条件、保护不变量、验证方式。
5. 优先合并到现有技能；只有现有技能承载不清才新增。
6. 对高约束 Skill/Rule 使用 `skill-pressure-test-template.md` 设计压力场景；若要宣称规则已改变 Agent 行为，必须记录 RED baseline、GREEN re-test、observed rationalization 和 REFACTOR 补洞。
7. 新增或显著修改 Skill 时检查 reference 拓扑、长 reference 目录、时间敏感信息刷新条件，以及脚本/工具依赖和可验证输出。
8. 长取舍写 report，短触发写入口，稳定映射写 reference。
9. 搜索触发词、技能名和旧引用，确认没有断链或重复规则。

## Follow-ups

- 本次跟进时间记录为 2026-06-11 14:33:08 CST (+0800)。未来若需要跟进其他仓库设计，可用本报告的 `Future Checklist`、`methodology-harvest` 和 `design-principle-library` 作为起点：先核对目标仓库的设计上下文，再抽取可复用机制，最后落到对应 Skill、Rule、Reference 或项目 report。
- 如果未来 Superpowers 安装版本更新，应刷新 `superpowers-pattern-map.md` 并在本报告旁追加新 dated analysis。
- 若 Hub 后续引入真实多 agent 调度工具，可把 `subagent-driven-development` 的两阶段 review 进一步落成专门执行技能。
- 若某些规则反复被违反，应为对应 Skill 设计压力场景，按“Skill TDD”验证它是否真的改变 Agent 行为。
