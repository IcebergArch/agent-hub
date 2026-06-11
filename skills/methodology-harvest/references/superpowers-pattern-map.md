# Superpowers Pattern Map

日期：2026-06-11
来源：本地已安装 Superpowers 插件 `/Users/shatang/.codex/plugins/cache/openai-curated/superpowers/c6ea566d`
版本：`5.1.3`（来自 `.codex-plugin/plugin.json`）
用途：作为 `methodology-harvest` 吸收 Superpowers 设计时的默认映射表。

覆盖审计：`superpowers-coverage-audit.md` 记录 14 个 Superpowers skills 与主要 prompt/script/assets 资源类别的处理状态；刷新版本时先更新审计账本，再调整本映射。

## Adoption Principle

Superpowers 的核心价值是“方法论门禁 + 可组合技能 + 证据优先”。Agent Hub 采用这些机制，但按 Hub 的中文协作语境、Codex/Cursor 共用规则、`skills/` + `reports/` 结构进行适配；不复制 `docs/superpowers/` 目录、安装文档、品牌表述或对所有任务一刀切的强制门禁。

## Pattern Mapping

| Superpowers 机制 | 稳定价值 | Hub 默认落位 | Hub 适配 |
| --- | --- | --- | --- |
| `using-superpowers` | 先检查适用技能，避免无流程行动，并区分强门禁、弹性模式、委派边界和误触发退出 | `AGENTS.md` Loading Order、`skills/README.md`、`methodology-harvest/references/skill-trigger-discipline.md` | 使用 Hub 触发规则、当前 `SKILL.md`、最小必要集合、Delegated Task Boundary、Skill Compliance Mode 和 Skill False Positive Exit Gate；不采用“1% 可能就强制加载所有技能”的高成本规则 |
| `brainstorming` | 对创造性工作先澄清目标、约束、方案和设计 | `requirements-brief`、`implementation-plan`、`task-execution-lifecycle` | 高风险/高模糊任务先设计；复杂设计按架构、对象、数据流、错误态和验证分段确认；新增或重塑单元先过 purpose/interface/dependencies/testability 边界门；brief/spec 进入计划前过 Spec Readiness Gate；小修直接冻结不变量后执行 |
| `writing-plans` | 计划要有文件职责、细粒度步骤、验证命令和自检 | `implementation-plan` | 采用 no-placeholder、自审、Plan Review Gate 和可执行步骤；不强制写到 `docs/superpowers/` |
| `executing-plans` | 执行计划前先审查，执行中维护任务状态和验证证据 | `task-execution-lifecycle` | 采用轻量进度账本、单任务 `in_progress`、中断恢复前对账和 blocker 停止规则；不绑定外部 Todo 工具名 |
| `test-driven-development` | 行为变更优先红绿重构，测试先证明会因目标缺失而失败 | `test-strategy`、`bug-reproduction` | 对 bugfix/核心逻辑默认要求回归测试；先写的实现只能算探索，需隔离后用 red/green 或等价 proof 证明；RED 先过 Red Failure Reason Gate；TDD 例外先过 TDD Exception Gate，分类为文档/配置/生成代码/低风险样式/探索/工具不可用并给等价验证；测试难写时先当成接口、依赖边界或行为切分的设计反馈 |
| `systematic-debugging` | 根因优先、复现优先、单假设验证 | `bug-reproduction` | 强化 root cause tracing、Hypothesis Ledger Gate、Reference Pattern Comparison Gate、Condition-Based Waiting Gate；flaky/timeout/hang 先等真实条件，不用 sleep 掩盖 race；3 次局部修复失败后转入架构/前提审查；查不到根因时标记 unresolved 并留下排查证据、观测条件和下一步 |
| `verification-before-completion` | 没有新鲜且匹配目标的验证证据不得宣称完成 | `task-execution-lifecycle`、`AGENTS.md` Verification Semantics、`task-execution-lifecycle/references/completion-evidence-patterns.md` | 与 Hub 分层验证语义合并，加入 Verification Output Match Gate 和 Requirements Trace Gate；强调业务成功与 wiring/错误暴露的区别；积极语气也要过证据门，不能暗示未验证成功 |
| `requesting-code-review` | 重要节点和卡住时引入独立审查 | `code-review`、`task-execution-lifecycle` | 可用 subagent、测试 Agent、脚本或 checklist；实现者报告、PR 描述和 subagent 状态只作为 claims，必须用 diff、代码、测试或运行信号验证；卡住时过 Fresh Perspective Review Gate；review 最终过 Review Verdict Gate；不强制每个小任务都派 review agent |
| `receiving-code-review` | 外部反馈先技术核验，不表演式附和 | `code-review` | 人类反馈优先执行但仍澄清范围；多项 feedback 先 intake，模糊且互相关联时不先修半懂子集；外部/agent/tool feedback 先按 Feedback Source Priority Gate 验证来源、适用性和用户决策冲突；技术性反驳要绑定证据，反驳错了要事实性收回并修正 |
| `dispatching-parallel-agents` | 独立问题域并行处理，任务 brief 自包含，子任务不重新扩张全局 scope | `task-execution-lifecycle`、`task-execution-lifecycle/references/parallel-agent-coordination.md` | Codex 可用多工具/多 agent 时采用；共享状态或同文件编辑时保持串行；被委派任务按 brief scope 执行，不重跑全局技能发现 |
| `subagent-driven-development` | 任务级 fresh context，两阶段 review，完成后整体复核，按任务复杂度选择能力 | `task-execution-lifecycle`、`code-review` | 对多任务实现采用“规格符合性 -> 代码质量”审查顺序；子 agent 提问先过 Delegated Question Gate；先证明实现 claims，再判断质量；若计划本身错误，标成 Plan issue 回到计划层；所有子任务完成后做 Final Integration Review；按任务复杂度选择 agent/model/tool 能力，不要求所有平台都有 subagent |
| `using-git-worktrees` | 先检测隔离环境，再决定是否创建 worktree | `task-execution-lifecycle`、`agents/codex.md` | 遵守当前工具权限和用户偏好；非平凡实现遇到默认/受保护分支时先确认 in-place 意图或使用隔离 branch/workspace；不自动创建未请求的 worktree；隔离环境 ready 前记录 clean baseline 或 baseline failure |
| `finishing-a-development-branch` | 完成前测试、环境识别、集成选项清晰 | `task-execution-lifecycle`、Git 规则 | Hub 默认准备交用户审查；验证失败、未跑或证据不足时先停在修复/继续验证/保留审查，不提供提交、PR、merge 或 discard 菜单；只有用户要求才执行集成动作；收尾使用有限且按环境裁剪的选项 |
| `writing-skills` | Skill 也要用压力场景测试，描述只写触发条件 | `methodology-harvest`、`project-content-curation`、`design-principle-library` | 新/改技能先检查触发、真实用户表达/症状/错误/对象/工具/近义词覆盖、抗跑偏、Loaded Surface Token Budget Gate、Cross-Tool Compatibility Gate、token 成本和引用搜索；重规则可设计压力场景验证 |

## Resource Mapping

| Superpowers 资源 | Hub 处理 | 原因 |
| --- | --- | --- |
| `using-superpowers/SKILL.md` | 机制归入 `skill-trigger-discipline.md`、`AGENTS.md` Loading Order 和 `skills/README.md` | Hub 采用先检查相关技能、读当前正文、最小必要集合、Delegated Task Boundary、Skill Compliance Mode、Skill False Positive Exit Gate 和“用户 WHAT 不自动豁免 HOW”；不复制外部平台工具调用方式 |
| `using-superpowers/references/*-tools.md`、`skills/*/agents/openai.yaml` | 机制归入 `external-skill-portability.md` | 外部工具名和 agent manifest 只作为能力映射来源；Hub 按当前工具 adapter、frontmatter、Cross-Tool Compatibility Gate 和索引承载 |
| `requesting-code-review/code-reviewer.md`、`receiving-code-review/SKILL.md`、`subagent-driven-development/*-prompt.md`、`brainstorming/spec-document-reviewer-prompt.md`、`writing-plans/plan-document-reviewer-prompt.md` | 不原样复制；抽象为 `code-review/references/review-checklists.md`、`implementation-plan/references/plan-review-checklist.md` 和 `task-execution-lifecycle` 的两段 review、Implementation Report Skepticism Gate、Fresh Perspective Review Gate、Feedback Source Priority Gate、Review Verdict Gate、feedback intake、反馈核验、证据型 pushback、错误 pushback 修正、计划 readiness review、Plan issue 出口和 PR thread 处理规则 | prompt 绑定 Superpowers subagent 流程，Hub 先采用审查结构，等真实多 agent 调度稳定后再模板化 |
| `brainstorming/SKILL.md` | 机制已归入 `requirements-brief/SKILL.md` 和 `requirements-brief/references/idea-shaping-patterns.md` | Hub 采用上下文先读、范围过大先拆分、单问题澄清、2-3 方案、单元边界设计、分段设计确认、Spec Readiness Gate、设计自审和按风险缩放的确认门 |
| `systematic-debugging/find-polluter.sh` | 暂不复制；思路归入 `bug-reproduction/references/debugging-patterns.md` 的污染源追踪 | 脚本与外部测试约定耦合，Hub 只保留可按项目语言重写的诊断模式 |
| `systematic-debugging/condition-based-waiting*.md/ts`、`root-cause-tracing.md`、`defense-in-depth.md` | 机制已归入 `bug-reproduction/SKILL.md`、`bug-reproduction/references/debugging-patterns.md` 和 `test-strategy/references/testing-anti-patterns.md` | 作为调试模式比原样复制更适合跨语言 Hub；单假设验证用 Hypothesis Ledger Gate 累积证据；参考实现先过 Reference Pattern Comparison Gate；异步/flaky 问题用真实条件、fresh read、timeout 和 polling 取代任意 sleep；重复失败时用架构/前提审查替代继续试 patch；无法定位根因时用 no-root-cause 证据门替代编造结论 |
| `test-driven-development/testing-anti-patterns.md` | 机制已归入 `test-strategy/SKILL.md` 和 `test-strategy/references/testing-anti-patterns.md` | 反模式适合按需加载，覆盖 mock 误报、Red Failure Reason Gate、TDD Exception Gate、实现后补测、探索代码污染测试设计、testability feedback 和 red/green proof，避免把长示例塞进常用技能正文 |
| `verification-before-completion/SKILL.md` | 机制已归入 `task-execution-lifecycle/references/completion-evidence-patterns.md` 和 `AGENTS.md` Verification Semantics | Hub 采用证据层级、结论词校准、Verification Output Match Gate、Requirements Trace Gate、积极语气门和 diff 边界检查；不复制外部强措辞 |
| `executing-plans`、`using-git-worktrees`、`finishing-a-development-branch` | 机制已归入 `task-execution-lifecycle/SKILL.md` 和 `task-execution-lifecycle/references/execution-control-patterns.md` | Hub 采用计划审查、进度账本、中断恢复对账、默认分支工作门、隔离检测、clean baseline gate、base branch 确认、Failed Verification Finish Stop Gate、合并后验证、worktree provenance、证据账本和按环境裁剪的结构化收尾选项；不照搬外部固定菜单、外部 Todo 工具名或自动 worktree 策略 |
| `dispatching-parallel-agents`、`subagent-driven-development` | 机制已归入 `task-execution-lifecycle/references/parallel-agent-coordination.md` 和 `code-review/references/review-checklists.md` | Hub 采用独立域判断、自包含 brief、Delegated Question Gate、能力选择门、冲突合并、两阶段 review 和最终整合 review；不要求所有任务都有 subagent |
| `writing-skills/SKILL.md`、`testing-skills-with-subagents.md`、`persuasion-principles.md`、`anthropic-best-practices.md` | 机制归入 `methodology-harvest/references/skill-authoring-patterns.md`、`skill-pressure-test-template.md` 与 `skills/README.md` | 只保留触发描述、Trigger Discovery Coverage、渐进加载、reference 拓扑、Loaded Surface Token Budget Gate、Cross-Tool Compatibility Gate、压力场景证据链、单 Skill 部署验证门、脚本/工具假设、自由度控制和 token efficiency 原则 |
| `systematic-debugging/test-*.md`、`CREATION-LOG.md`、`writing-skills/examples/CLAUDE_MD_TESTING.md` | 机制归入 `skill-pressure-test-template.md`、`skill-authoring-patterns.md` 和 `external-skill-portability.md` | 压力测试与 creation log 保留场景类型和反跑偏机制，不复制长场景原文 |
| `writing-skills/graphviz-conventions.dot`、`render-graphs.js` | 机制归入 `external-skill-portability.md` 的 visualization handling | Hub 默认用 Mermaid/Markdown/当前工具视觉能力；Graphviz runtime 暂不复制 |
| `brainstorming/scripts/*`、`visual-companion.md` | 不复制脚本；视觉使用判断归入 `requirements-brief/references/idea-shaping-patterns.md` | 脚本依赖 Superpowers browser companion 运行时；Hub 按当前工具能力选择 Mermaid、截图、浏览器、临时 HTML 或图像生成 |
| `assets/app-icon.png`、`assets/superpowers-small.svg` | No-copy | 品牌资产，不属于 Hub 方法论资产 |
| `.codex-plugin/plugin.json`、安装文档、license/community 文件 | No-copy | 外部插件元信息与分发说明，不应成为 Hub 运行入口 |

## Not Adopted By Default

- 不采用 Superpowers 的全局“每次对话必须先读 using-superpowers”作为 Hub 规则；Hub 已有 live loader 和 Loading Order。
- 不强制所有创造性工作写设计文档并提交；Hub 根据任务风险选择报告、计划或直接执行。
- 不把每个任务都要求严格 TDD；Hub 对行为变更、bugfix、核心逻辑优先测试先行，对文档、配置、生成代码、低风险样式、一次性探索或测试工具不可用场景允许有类别和证据的等价验证。
- 不复制外部安装说明、社区链接、品牌资产、prompt 模板原文、工具绑定脚本或 `docs/superpowers/` 保存路径。
