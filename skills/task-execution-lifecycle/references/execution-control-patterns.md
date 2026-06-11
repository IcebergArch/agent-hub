# Execution Control Patterns

日期：2026-06-11
来源：吸收 Superpowers `executing-plans`、`using-git-worktrees`、`finishing-a-development-branch`、`writing-plans` 和 `subagent-driven-development` 的执行控制机制后整理。
用途：作为 `task-execution-lifecycle` 的按需参考；用于长任务、计划执行、多文件实现、工作区隔离、并行/subagent 协作和分支收尾。

## Core Rule

执行控制的目标不是多加仪式，而是保护四件事：计划不跑偏、用户本地改动不被污染、完成结论有新鲜证据、收尾动作由用户明确选择。

## Intake Checklist

开始执行前先确认：

- 当前目标：用户要实现、修复、重构、审查、同步还是归档。
- 当前仓库和硬边界：路径、模块、禁止修改范围、是否有多个相近目录。
- 当前 git 状态：branch、upstream、staged、unstaged、untracked、本地改动是否与任务重叠。
- 当前隔离状态：宿主是否已提供 workspace/worktree；是否为 linked worktree；是否 detached HEAD。
- 当前计划状态：是否有 plan/spec/checklist；是否含占位、断链、过大步骤或验证缺口。
- 当前验收信号：最小业务成功、负向行为、回归风险和必须运行的验证。

不清楚这些信息时，先用只读命令或搜索补证据；只有缺口会导致误改时才问用户。

## Plan Execution Gate

用户提供计划或要求按计划执行时，不直接机械照抄：

1. 读完整计划和当前代码上下文。
2. 标出目标、文件、步骤、验证、风险和非目标。
3. 检查计划是否能被执行者独立完成：精确路径、具体命令、期望结果、回滚/验证点。
4. 检查计划是否依赖当前会话隐含上下文：`如上`、`前面讨论的`、`按之前方案`、未写明的用户硬边界、未列出的数据源或验收信号。
5. 检查是否包含未经用户确认的 commit、push、PR、merge、discard、stash 或 destructive cleanup；这类步骤只能作为可选收尾或在用户明确要求后执行。
6. 搜索占位词和含糊语：`TBD`、`TODO`、“适当处理”、“类似上面”、“补测试”。
7. 对高风险、多模块、外部来源或交给其他 agent/线程执行的计划，按 `implementation-plan/references/plan-review-checklist.md` 做 readiness review；只把会导致做错、卡住、越界或无法验证的问题列为 blocker。
8. 若计划可靠，连续执行，不在每个小步骤后问“是否继续”。
9. 若计划不可靠，先修正计划或提出最小阻塞问题；不要边猜边实现。
10. 计划通过审查后，先抽成轻量进度账本；执行期间最多一个任务处于 `in_progress`，任务完成必须有对应验证或证据。

执行时每个任务至少要留下一个状态证据：完成的文件、运行的命令、观察到的输出、未覆盖的风险或下一步 blocker。

## Plan Progress And Resume Ledger

执行已有计划时，原始计划不是当前状态。当前状态由进度账本、文件 diff 和验证证据共同决定。

账本最少记录：

| 字段 | 记录什么 |
| --- | --- |
| Task | 计划里的任务编号或短标题 |
| Scope | 预期涉及的文件、模块或接口 |
| Status | `pending`、`in_progress`、`completed`、`blocked`、`skipped` |
| Evidence | 完成文件、验证命令、观察输出、diff 或人工验收信号 |
| Blocker / risk | 阻塞原因、失败命令、未覆盖风险或需要用户确认的问题 |

执行规则：

- 开始前把计划任务同步到账本；如果计划步骤过大，先拆到能单独验证的粒度。
- 每次只推进一个 `in_progress` 任务；切换任务前先写下已完成证据或 blocker。
- 只有验证通过、证据充分或用户明确接受风险后，才能把任务标为 `completed`。
- 如果跳过任务，必须记录 skip 原因、影响面和后续恢复条件。
- 如果用户修改计划、代码状态与计划不一致，或验证失败反复出现，回到 `Plan Execution Gate` 重新审查，不沿旧状态硬推。
- 如果中断后恢复执行，先用 `git status`、定向 diff、已改文件、账本状态和最近验证输出对账；只有确认哪些任务真的完成，才能从下一个未完成任务继续。
- 如果阻塞来自缺依赖、权限、环境、计划缺口或需求不清，把任务标为 `blocked` 并提出最小问题；不要猜测执行。

最终交付时说明哪些任务完成、哪些被跳过或阻塞、每个结论对应的验证证据，以及是否还有未恢复的中断状态。

## Execution Mode Matrix

| 场景 | 默认模式 | 关键约束 |
| --- | --- | --- |
| 小修、单文件、文档整理 | Inline execution | 不制造额外 worktree；保持 diff 边界清楚 |
| 多文件但强顺序依赖 | Inline execution with checkpoints | 一步一验证；共享文件串行 |
| 多个独立问题域 | Parallel tools / subagents when available | 每个任务上下文自包含；共享状态不并行；详见 `parallel-agent-coordination.md` |
| 用户给出完整计划 | Plan execution | 先审计划，再连续推进 |
| 计划含独立任务且支持 subagent | Subagent-style execution | 规格符合性 review 先于代码质量 review |
| 不确定 owner、接口或数据源 | Pause for boundary | 只问最小阻塞问题，不继续按旧假设扩写 |

并行不是速度按钮，而是隔离要求：只有文件、状态、验证和决策互不阻塞时才并行。

## Default Branch Work Gate

默认分支适合承载稳定结果，不适合承载未隔离的试错。开始非平凡实现前，先判断当前分支是否是 `main`、`master`、仓库默认分支、受保护分支或用户明确指定的发布/集成分支。

检查顺序：

1. 记录当前 branch、upstream、dirty worktree、staged/untracked 状态。
2. 判断任务类型：
   - 功能实现、bugfix、接口/DB/运行时/UI 多文件改动：默认需要隔离 branch/workspace。
   - Hub 规则、Skill、reference、报告维护，且用户明确指定当前仓库/路径：可以在当前 checkout 原地维护。
   - 小型 typo、纯文档单点修正：可以原地维护，但仍做 diff 边界检查。
3. 若当前在默认/受保护分支且任务需要隔离，优先使用宿主原生隔离或创建/切换明确 feature branch；没有权限或不清楚用户偏好时，问一个最小确认问题。
4. 若用户已明确要求在当前 checkout 处理，或当前任务是规则/文档维护且已在默认分支形成工作区状态，不强行迁移；继续前记录这是 in-place maintenance。
5. 收尾必须说明当前 branch、是否仍在默认分支、staged/unstaged/untracked 状态、未提交内容和没有自动 merge/discard。

不要把“在默认分支上能编辑”理解为“可以跳过隔离判断”。反过来，也不要为了已经明确的 Hub 规则/报告维护临时创建 worktree，制造更多未跟踪状态。

## Workspace Isolation

隔离决策顺序：

1. 先检测当前是否已在宿主隔离 workspace 或 linked worktree。
2. 如果 `GIT_DIR != GIT_COMMON`，再查 `git rev-parse --show-superproject-working-tree`；在 submodule 中不要误判为 linked worktree。
3. 如果已隔离，沿用当前环境，不再创建嵌套 worktree。
4. 如果未隔离，先执行 `Default Branch Work Gate`，再判断任务是否真的需要隔离：长任务、多文件改动、风险较高、用户本地改动复杂或需要独立 branch。
5. Codex/Cursor 等宿主已有隔离机制时，优先使用宿主机制。
6. 只有用户同意、工具没有原生隔离、且任务收益明显时，才考虑手动 `git worktree`。
7. 手动 worktree 必须验证目录被 ignore；否则可能把 worktree 内容混进仓库。
8. 隔离环境 ready 前先做 clean baseline gate；确认依赖/setup 与最小验证信号能运行，或记录 baseline 已失败。

不要为了普通小修、纯文档 reference 或规则整理创建新 worktree。当前仓库已有大量用户/本轮未提交改动时，默认保护现场，先做 diff 边界审查。

## Clean Baseline Gate

长任务、多文件功能、手动 worktree 或宿主隔离 workspace 开始实现前，先证明当前基线状态。目标不是强制全量测试，而是避免把旧失败误判成本轮问题，或在不健康环境上继续堆实现。

检查顺序：

1. 确认当前路径、branch、worktree/detached 状态和 dirty worktree。
2. 按项目事实源选择最小 baseline 验证：已有 quick test、typecheck、build、lint、API smoke、页面 smoke 或本任务明确相关的最窄命令。
3. 如果需要 dependency setup 或生成步骤，优先使用项目现有命令；不要为 baseline 临时发明新安装流程。
4. Baseline 通过：记录命令和结果，再进入实现。
5. Baseline 失败：记录失败数量、关键错误和是否与本任务相关；先问用户是修 baseline、继续但标记风险，还是缩小任务范围。不要把后续同类失败说成本轮引入。
6. 无法运行 baseline：说明缺权限、依赖、网络、环境或时间成本，并记录替代证据，例如只读 diff、已有 CI 状态、相关测试历史或手动 smoke。

Baseline 证据只证明“起点状态”，不证明最终完成。实现后仍必须重新跑本轮目标相关验证。

## Verification Ledger

每轮执行维护一个轻量证据账本：

| 证据类型 | 记录什么 |
| --- | --- |
| Static | typecheck、lint、format、diff check、引用搜索 |
| Unit / integration | 相关测试命令、失败/通过信号、是否有 red/green proof |
| Runtime / UI | 页面入口、请求下游、业务结果、错误态是否只是暴露 |
| Diff boundary | staged/unstaged/untracked、生成产物、无关改动、公司报告边界 |
| Review | 规格符合性、代码质量、反馈处理和残余风险 |

准备声明完成、修好、通过、可用、已打通或交付前，读取 `completion-evidence-patterns.md` 做结论词校准。

结论词只能由证据支持：

- “可用/打通/通过”：业务验收信号真的成功。
- “wiring 到位”：入口和请求链路到达目标，但业务结果未成功。
- “错误暴露”：系统正确显示或返回错误，但能力未打通。
- “部分完成”：有明确未验证或未完成项。

## Branch Finish Gate

实现完成后不自动 merge、push、PR 或 discard。先确认：

1. 本轮必要验证已跑，失败项已说明。
2. diff 边界清楚，无关本地改动未被混入。
3. 当前环境是普通 checkout、linked worktree 还是宿主托管 workspace。
4. 目标 base branch 已确认：优先用户明确指定；其次当前 upstream / PR target；再次 `origin/HEAD`、`main`、`master` 等仓库事实；仍不清楚时先问。
5. staged/untracked 文件清单已看过。
6. 用户要求哪种收尾动作。

可提供的选项：

- 保留当前工作区给用户审查。
- 暂存/提交本次必要改动。
- 推送并创建 PR。
- 合并回目标分支。
- 丢弃本轮工作。

选项要按环境裁剪：

- 普通 checkout 或 named-branch worktree：可以提供提交、推送/PR、合并、保留、丢弃。
- detached HEAD 或宿主托管的 external workspace：不要默认提供“本地合并回目标分支”；应先创建/切换明确分支或交给宿主原生分支/PR 控制。
- 未验证或 diff 边界不清：只提供保留审查、继续验证或收敛边界，不提供 merge/discard 快捷路径。

危险动作规则：

- 丢弃必须二次确认，并说明会删除哪些文件、提交或分支。
- 不清理由宿主创建或来源不明的 worktree。
- 不在验证失败时建议合并。
- 合并回目标分支时，先完成 merge 并在合并后的结果上重新跑必要验证；只有 merge 与验证都成功，才考虑删除分支或清理 worktree。
- 创建 PR 或保留分支时，默认保留当前 worktree，方便用户继续处理 review feedback 或后续迭代。
- 不为“准备提交”伪造 `::git-*` 宿主指令；只有动作真实完成才发。

## Failed Verification Finish Stop Gate

验证失败、未跑或证据不足时，分支收尾必须停在修复和证据收集层；不能把集成动作提前包装成选项。

触发信号：

- 必要测试、typecheck、build、lint、API smoke、UI smoke 或手动验收失败。
- 验证命令没有运行，或输出不是当前代码的新鲜结果。
- 验证只证明 wiring、错误暴露或局部静态状态，但用户目标需要业务成功。
- diff 边界不清，无法证明待提交/PR/合并内容只包含本轮必要改动。

允许回复：

- 报告失败命令、失败数量、关键错误和对应风险。
- 继续修复失败项。
- 继续运行缺失验证。
- 保留当前工作区给用户审查，并明确未达到集成条件。

禁止回复：

- “可以 PR / 可以合并 / 可以提交”。
- 提供 merge、PR、discard 快捷菜单。
- 把失败验证写成“仅剩测试问题”但不说明影响。
- 删除分支、清理 worktree 或 force-delete 本轮工作。

只有当必要验证重新通过、或用户明确接受风险并指定某个动作时，才能继续 `Structured Finish Options Gate`。即便用户接受风险，也必须在最终回复中保留失败证据和未满足的验收项。

## Structured Finish Options Gate

收尾不是开放式聊天问题。只有在验证、diff 边界和环境状态足够清楚后，才给用户有限选项；选项必须按当前环境裁剪。

选项前置条件：

- 本轮必要验证已跑，或明确标记哪些验证失败/未跑。
- staged、unstaged、untracked 和无关保留内容已看过。
- 当前环境已识别：普通 checkout、named worktree、detached HEAD、宿主托管 workspace。
- base branch / PR target 已确认或已说明仍不清楚。

选项规则：

| 状态 | 允许给出的选项 |
| --- | --- |
| 验证通过且 diff 边界清楚 | 保留审查、暂存/提交、推送/PR、合并、丢弃 |
| 验证失败、未跑或证据不足 | 继续验证、修复失败、保留审查；不提供提交、PR、合并或丢弃快捷路径 |
| detached HEAD 或宿主托管 workspace | 保留、创建/切换分支后 PR、交给宿主流程；不默认提供本地合并 |
| worktree provenance 不清 | 保留 workspace；不提供自动清理 |

输出方式：

```markdown
当前状态：
- Branch/workspace:
- Verification:
- Diff boundary:

可选下一步：
1. 保留当前工作区给你审查
2. 暂存/提交本次必要改动
3. 推送并创建 PR
4. 合并回 <base>
5. 丢弃本轮工作（需要二次确认）
```

根据环境删掉不适用项；不要把不可执行或高风险项列出来再靠解释弥补。用户选定动作前，不执行对应 git 操作。

## Worktree Cleanup Provenance

清理 workspace/worktree 前必须证明它由当前流程拥有，不能只因为路径像 worktree 就删除。

清理顺序：

1. 先确认收尾动作允许清理：本地合并成功且合并后验证通过，或用户已对丢弃动作给出明确二次确认。
2. 记录待清理对象：worktree path、branch、未合入 commits、staged/unstaged/untracked 文件。
3. 确认 provenance：只清理由当前 agent 显式创建、项目约定目录（如 `.worktrees/`、`worktrees/`）且已验证不会污染仓库，或用户明确授权清理的 worktree。
4. 不清理由 Codex/Cursor/CI/harness 创建或来源不明的 external workspace；这类环境交给宿主工具或用户处理。
5. 执行清理命令前切回主 repo/root，不在将要删除的 worktree 内运行删除命令。
6. 删除 worktree 后再删除已合并或已确认丢弃的 branch；必要时运行等价的 prune/cleanup 来移除 stale registration。

如果任一项缺证据，只能报告“保留 workspace，等待用户/宿主处理”，不能把清理包装成完成收尾。

## Stop Rules

停止执行并重新收敛边界的信号：

- 计划和当前代码明显不一致。
- 本地未提交改动与目标分支或任务文件重叠，继续会污染用户改动。
- 验证连续失败且失败信号没有变窄。
- 用户中断、提速或覆盖旧请求。
- 发现 scope、owner、数据源或权限语义不清。
- 需要破坏性命令、安装依赖、联网或写出工作区外内容。

停止不是放弃；停止点要留下当前状态、已改文件、风险和最小下一步。
