# Review, Git And Cleanup Gates

用途：作为 `task-execution-lifecycle` 的按需参考；当任务进入 cleanup、`/hub refactor`、`coding`、`git update`、`code-update`、`pr`、PR 代码/架构审查、合入/发布就绪判断、提交前审查或完成状态校准时读取。默认入口只保留触发和硬边界，本文件承接细节。

## Cleanup Review

清理已修改、未提交或重构后的内容时：

1. 先限定范围：用户指定任务、模块或本轮变更；不做全仓库机会主义整理。
2. 用 `git status --short`、`git diff --name-status`、`git diff --stat` 和必要搜索把文件分层：
   - 本轮必要实现
   - 测试、文档、配置或生成产物
   - 旧入口或过渡代码
   - mock、stub、fallback、假数据
   - 临时过程文件和工具缓存
   - 用户已有或无关改动
3. 删除前搜索旧路径、旧名称、导入、route、配置和测试引用；生产入口仍引用时不能删除。
4. 对 UI 或前后端联动变更，验证真实 workflow；不能用前端 mock、空页面或旧服务掩盖后端缺口。
5. 最终说明删除了什么、保留了什么、保留原因和验证命令。

## Review Lenses And Change Decomposition

- 审查前固定 base / merge-base、目标任务或 SPEC 和完整 diff；空 diff、错误 ref 或缺失目标来源先显式标记，不能靠审查者猜范围。
- 独立检查两条轴：`目标符合性` 判断遗漏、偏差和 scope creep；`工程与架构质量` 判断项目规则、owner、依赖方向、可维护性和测试质量。两条轴分别保留结论，一条通过不能抵消另一条失败。
- 非机械大任务默认拆成纵向切片：每片贯穿完成目标所需的层、可独立演示或验证、结束时保持验证通过，并显式写出真正阻塞它的前置依赖；不把 UI、API、数据和测试按层横切成彼此不可验收的批次。
- 机械性宽重构若单片会同时破坏大量调用方，使用 `expand -> migrate -> contract`：先并存新旧形式，再按 blast radius 分批迁移，所有迁移完成后删除旧形式。只有迁移批次确实无法独立保持绿色时才使用共享 integration branch，并保留最终集成验证 gate。

## Review Evidence Closure

- CI、测试和静态检查只证明对应 commit、环境及已执行断言的结果；它们不能替代 diff 级代码/架构审查，也不能单独推出“所有改动均必要、有效且完备”。只有 checks 证据时必须写“检查通过，审查未完成”，不得据此给出全面通过或就绪结论。
- 高风险变更，或用户明确追问是否全部必要、有效、完备时，审查结论必须逐项覆盖：必要性与最小性、语义正确性与实际有效性、影响面与实现完备性、适用的并发/时序/失败/恢复边界、架构 owner 与依赖方向、测试覆盖与盲区、残余风险。每项都要绑定可核对证据，确实不适用的维度说明 `N/A` 理由；任一适用维度缺证据时，结论保持“未审完”。
- 审查证据绑定已固定的 base、head/commit 和完整 diff；审查后 tracked 内容发生实质变化时，受影响的审查结论失效，必须基于新 diff 复审。Git/PR 收尾只能引用仍然新鲜的前序审查证据，不能用 push 成功、CI 变绿或提交形状正确补足失效审查。

## PR Review Freshness Gate

PR 代码/架构审查及合入或发布就绪判断必须“先 `git update`，再审核”；这里的 `git update` 是新鲜度门禁，不自动授权 merge 或 release：

1. **先解析远端事实**：结论前从用户指定对象、现有 PR 或平台元数据确定唯一 PR 与 target，并检查远端 `state`、`mergedAt`、base、head 及对应 commit；对象或 target 不唯一、元数据与本地假设冲突时先停止结论，不能只凭本地分支名或旧 checkout 猜测生命周期。
2. **可变更的自有 work branch**：PR 尚未合入、当前确为该 PR 的自有 work branch，且现有命令或用户已授权 rebase 时，fetch 后按本文件 `/hub git update` 的 rebase 语义同步已确认 target；只有最新远端 target 相对 HEAD 为 `behind 0`，且内容变化后的必要验证已重跑，才能给出代码、架构或就绪结论。
3. **只读或第三方 PR**：review 本身不授权改写分支。无法确认 rebase 权限、当前并非 PR head，或分支由第三方维护时，只刷新 base/head refs 并计算相对最新 target 的状态；`behind > 0` 是审查新鲜度 blocker，不得擅自 rebase，也不得给出“代码/架构通过”“可合入”或“可发布”的结论。
4. **已合入 PR**：以远端 lifecycle metadata 为准，不再把可能已废弃的 head branch ahead/behind 当作合入前状态。检查平台记录的 merge/squash commit、该提交与 target 的 ancestry 及合入 tree；squash 会改变 commit identity，原 head 提交继续显示为独有并不能反证 PR 未合入或还原当时的 readiness。

## Refactor Command

用户说 `/hub refactor` 时，授权 Agent 审查并直接收敛当前改动，不只是返回 review findings：

1. **冻结范围**：确认当前任务、目标 owner、必须保留的既有效果、target/base 和用户已有无关改动；高风险歧义未消除前不删除或重写。
2. **盘点完整 diff**：检查 staged、unstaged、untracked，以及必要的 base/head、`<target>...HEAD` 或 PR diff；工作区干净不等于没有分支差异。
3. **最小改动审查**：逐项判断改动是否直接服务当前目标，是否存在重复实现、无真实消费者的扩展、兼容层、fallback、mock、debug、临时文件、过度生成物或机会主义重构。
4. **双轴审查**：分别检查当前 diff 对目标/SPEC 的符合性，以及工程与架构质量；确认 owner、依赖方向、public surface、数据事实源和模块职责未漂移，共享面改动搜索 API、schema、公共组件、配置、runtime、tool、gateway、生成 client 和 UI 等真实消费者。不得用“实现了需求”掩盖架构问题，也不得用代码整洁掩盖遗漏或 scope creep。
5. **直接调整**：修正不合理实现，删除本任务引入的多余或越界改动，合并重复逻辑并恢复清晰边界；不用 reset、checkout 等方式回滚用户已有无关改动。
6. **验证收口**：执行最窄有效测试、引用搜索、生成物检查和 `git diff --check`；再次核对文件清单、影响面与架构形状。
7. **报告结果**：说明调整了什么、保留了什么、最小性与领域/架构结论、验证证据和残余风险。

`/hub refactor` 不授权暂存、提交、push、PR 或 merge。提交、暂存、amend、PR、合并或“只提交本次改动”前仍必须做完整 diff 审查；无关用户改动保持原样。

## Coding Command

用户说 `coding` 或 `coding <任务>` 时，表示授权 Agent 在目标项目内把已明确的任务连续推进到 PR，默认组合流程为：

```text
git update -> understand -> minimal implementation -> strict verification
-> PR-ready gate -> git update -> final diff/architecture gate -> exactly one task commit
-> push work branch -> create/update PR -> wait or authorized merge
```

1. **冻结任务与仓库**：确认 work repo、target、任务目标、非目标、验收信号、owner、当前分支和用户已有改动。只有仓库、目标或写入边界存在高风险歧义时才停下询问；`coding` 不授权顺手重构、跨项目业务改动或丢弃既有改动。
2. **第一次 git update**：按当前项目与 work branch 已确认的 target，用 rebase 同步最新基线。dirty worktree 不得用 reset、checkout 或隐式 stash 绕过；目标已是当前分支祖先时以 fresh fetch 和祖先检查确认 no-op，不制造流程提交。
3. **理解后实现**：用当前代码、配置、接口和运行事实确认缺口，再沿现有依赖方向完成最小 coherent diff。实现过程中持续保持领域 owner 清晰、公共 surface 克制、架构整洁；发现相邻问题只在阻塞验收或破坏不变量时纳入。
4. **严格验证**：开发中先跑最窄测试，PR 前必须覆盖项目声明的 required checks；代码项目还必须通过可用的本地 `quality-orchestrator` 先检查/规划，再覆盖所有适用层。缺少项目质量契约不能静默跳过，应按下一步补齐项目侧 manifest；只有纯文档/内容仓库没有可执行质量面时，才可明确记录为不适用。不能把 `0 tests`、skipped、mock/fallback、缺凭据或仅错误暴露计为通过。
5. **质量 owner 分流**：项目自己的命令、manifest、scenario、fixture、selector、domain assertion 和 impact rule 由目标项目补齐；planner、runner、evidence、reporting、policy 等跨项目通用编排缺口才属于 `quality-orchestrator`。确认后者不完备且阻塞严格验证时，拆出有明确边界的子 Agent 完善编排器并独立验证；不得把产品知识迁入编排器，也不得用子 Agent summary 替代主 Agent 对两个仓库 diff 和最终结果的复核。
6. **PR-ready gate**：只有任务验收、项目 required checks、适用的 quality orchestration、diff/影响面/架构审查和临时产物清理全部通过，才进入 PR 阶段。环境或编排能力仍有缺口时报告 blocker，不降级宣称“准备好 PR”。
7. **第二次 git update 与最终门禁**：再次 fetch/rebase 最新 target，解决冲突后检查 staged、unstaged、untracked、work branch 相对 target 的完整 diff、影响面、领域边界、架构匹配、验证证据和提交形状。将本次任务整理为相对 target **恰好一个**清晰提交，并确认最新远端 target 相对 HEAD 为 `behind 0 / ahead 1`；若任务过大，应先拆成多个独立任务/PR，每个 PR 仍保持一个提交。rebase、冲突解决或提交整理改变内容后，重新跑受影响验证。
8. **提交、push 与 PR**：只提交当前任务并只推 work branch，然后创建或更新 PR；不得直接 push target。PR 描述包含任务范围、影响面、架构判断、验证证据、quality-orchestrator 覆盖情况和残余风险。创建 PR 是 `coding` 的默认终点。
9. **等待或获准 merge**：`coding` 本身不授权实际 merge。没有额外授权时等待 review/审批；用户已明确授权，或仓库存在明确且适用的 auto-merge 规则时，required checks 与 approvals 全部满足后，按 rebase 合入约束完成 merge，不引入 merge commit。

## PR Command

用户说 `pr` 时，表示进入纯 Git/PR 收尾：保存已经形成的任务代码、更新基线、把任务历史压成一个提交、push work branch，并创建或更新 PR。它不是测试、代码/架构 review、功能验收、UI 审核或问题修复命令；这些工作由 `coding`、`/hub refactor`、`/hub spec-smoke` 或明确 review 任务在前序阶段完成。`pr` 只消费已有证据并如实披露缺口，不重新制造证据，也不借收尾修改产品内容。

target 必须从当前具体项目与 work branch 的真实上下文确定，优先使用用户明确指定、现有 PR base、任务/SPEC 登记或项目分支约定；不得固定或猜测为 `main`。多条证据冲突、任务代码边界不清或无法安全改写远端 work branch 时，停止并请求最小决策。Agent Hub 自维护不进入本节 PR 流程，只在 canonical 主工作目录的 `main` 上形成任务提交，并且只有用户另行明确授权或 `/hub save` 确认后才直接 push。其它项目流程如下。

### Formal SPEC Evidence

当 `/hub pr` 处理已进入 execing 的正式 SPEC 时，只读取 STDD、Bug Pool 和已有 `spec-smoke` evidence，把现状与残余风险如实写入 STDD 和 PR。缺失、过期或因收尾内容变化而失效的证据标为未验证，不在 `pr` 内重跑测试、smoke、UI 审核或最终验收，也不伪造通过结论；后续验证和修复由 `/hub spec-smoke` 承接。

### Common Flow

**顺序门禁**：基线更新是形成最终任务提交、push 和创建/更新 PR 的前置步骤，不得因已有本地提交、远端分支或 PR 而跳过。若误在基线更新前 push，补做 fetch/rebase 与单提交整理，再在确认远端分支无人共享后用 `--force-with-lease` 更新；不得把旧 push 当作流程已完成。`pr` 全程只允许 Git/PR 形态检查，不运行测试、typecheck、build、smoke 或 UI/功能验收。

0. **确认工作仓库与任务边界**：以当前项目仓库为 work repo，核对需要保存的任务代码，以及必须原样保留、不纳入本次提交的用户已有改动。Agent Hub 自维护按上方 canonical `main` 单分支例外处理。
1. **确定并更新基线**：确认当前 work branch、唯一 target 和对应 remote，fetch 最新 target，检查当前分支、ahead/behind、staged/unstaged/untracked。target 不由 remote 默认分支名自动决定；dirty worktree 会阻塞安全更新时先保护现场，不能用 reset、checkout 或隐式 stash 丢弃或隐藏改动。
2. **确认 local work branch**：不能把 PR 提交直接落在 target 分支；若当前在 target 上且任务代码尚未提交，先从已更新 target 建立或切入语义清晰的 local work branch。已有 PR 时应确认当前分支就是其 head，不能另建同义分支。
3. **盘点待保存内容**：同时检查本地工作区与 `<target>...HEAD`，使用 `git status --short`、`git diff --name-status`、`git diff --stat`、`git diff --check` 和提交列表确认文件归属、格式完整性与任务边界。这里不评价实现方案或功能正确性；发现疑似无关、临时或越界内容时停止并报告，不在 `pr` 内改代码或替用户猜测取舍。
4. **rebase 与单提交整理**：用 rebase 同步最新 target，禁止用 merge 同步基线或引入 merge commit；只暂存并提交本任务代码，把相对最新远端 target 的任务历史安全压缩为恰好一个清晰提交。执行 `git rev-list --left-right --count <remote>/<target>...HEAD` 或等价检查，结果必须为 `0 1`，即 `behind 0 / ahead 1`。冲突只能在能证明保持既有语义时解决；无法证明、任务内容实质变化或旧验证证据失效时如实记录风险，不在本命令内补跑验证。
   - **提交信息**：遵循目标仓库约定；没有明确规则时参考近期已合入提交。标题用领域对象和自然动作表达实际结果，原因与约束放正文，不用模糊评价词或内部缩写让 reviewer 猜测。
   - **安全改写历史**：已有远端提交需要压缩或重写时，先确认 work branch 无人共享且不会覆盖他人工作；不能证明时停止。
5. **push work branch**：只 push local work branch，不直接 push target。历史未改写时普通 push；已安全改写时只用 `--force-with-lease`。push 后核对远端 head 等于本地 HEAD，且相对 target 仍为 `behind 0 / ahead 1`。
6. **创建或更新 PR**：以冻结的 target 为 base 创建或更新 PR，核对 PR head/base 和唯一提交。标题与正文只记录当前仓库交付事实；正文包含任务范围、影响面、继承的前序验证证据、未验证或已失效项和残余风险，不把未在本命令执行的测试写成本轮结果。

`pr` 授权本次暂存、commit、必要的安全历史整理、push work branch 和创建或更新 PR，不授权 merge。

### 仓库历史隔离

- PR/MR 正文、评论、review、commit message、release note 等远端可见历史只记录当前仓库的交付事实。内部使用过的文档库、规则库、工具库、验证仓库或其它工作区，不因参与过程而成为当前仓库的关联项。
- 跨仓库 issue/PR shorthand、URL 或 backlink 会生成额外通知和可能长期保留的时间线事件；只有对方确属依赖、共同发布单元或需要双向追踪的契约 owner 时才可建立引用。仅用于过程记录、取证或内部编排时不得写入。
- push、创建/更新 PR 或发布评论前，检查标题、正文、评论草稿、commit message 与将提交的文档中是否出现其它仓库 slug、issue/PR URL 或本地工作区路径；没有明确交付关系的引用必须移除，以仓库内可验证事实表达结果。
- 若已误建跨仓库引用，先编辑或删除可控的引用源并停止继续传播；平台自动生成且不可删除的 cross-reference 事件要明确报告残余，不能通过更多跨仓库评论解释或“修复”。

`pr` 的核心是“保护并保存任务代码 + 确定 target + 更新基线 + 收敛为 behind 0 / ahead 1 + push work branch + 创建或更新 PR”。如果目标是审查、修复、验证或收敛实现内容，使用对应 review、`/hub refactor`、`coding` 或 `/hub spec-smoke` 流程，不把这些职责塞回 `pr`。

## Git Update

`git update` 表示用 rebase 将当前分支同步到最新目标分支，不是 Git 子命令。

1. 用户单独调用 `git update` 时，默认基线为 `origin/main` 或 `main`，除非用户指定其它目标；作为 `coding` 或 `pr` 的内部步骤时，使用调用流程已按具体项目与 work branch 冻结的 target，不继承 `main` 默认值。
2. rebase 前确认当前仓库、当前分支、目标分支、ahead/behind、dirty worktree；重点判断“当前分支是否符合用户预期”。
3. 分支和目标符合预期时，rebase 是默认动作；不要用 merge 或制造 merge commit。先用只读检查识别风险，再执行 rebase，保护本地改动并按冲突继续解决。
4. 只有当前仓库/分支明显不符合用户预期、需要 stash/reset/checkout 等会隐藏或丢失本地改动的操作、或 rebase 被 Git 阻塞且无法在保留本地改动的前提下继续时，才停下请用户决策。
5. 需要预判风险时优先只读检查，例如 merge-base、merge-tree、diff name-status 和冲突信号搜索；不要把大型输出原样倾倒给用户。
6. fetch 后当前分支已基于目标分支时，可用只读状态和 diff 验证，不为流程制造 stash、重排 index、merge commit 或长报告。

## Code Update

`code-update` 表示批量更新本地代码基线，不等同于对当前工作分支执行 `git update`。

1. **目标集合**：同步已登记的文档工作区仓库、live Agent Hub 仓库，以及 `sand-ai` 下运行时发现的全部 Git 仓库；执行 Git 动作前先冻结并去重目标集，不固化绝对路径、目录层级或仓库清单。
2. **逐仓预检**：确认仓库路径、`origin`、本地与远端 `main`、当前分支、dirty worktree、ahead/behind；缺少 `origin/main` 或本地 `main` 时记录为未更新，不猜测替代 remote 或 target。
3. **更新方式**：先 fetch 对应 remote，再仅以 fast-forward 更新本地 `main`。不得 merge、强推、reset、隐式 stash 或 rebase，也不得为了更新 `main` 切换、改写或提交当前工作分支。
4. **冲突保护**：本地 `main` 已分叉、被其它 worktree 占用且无法安全更新、或当前 checked-out `main` 的本地改动阻塞 fast-forward 时，保留原状并报告；不自动清理用户改动。
5. **独立结果**：一个仓库失败不阻止其余仓库继续安全同步。最终结果必须覆盖冻结目标集且数量一致，并逐仓报告 `updated / already up to date / skipped / failed`、旧新 commit 和原因；不把 fetch 成功写成 main 已更新。

## Completion Language

完成状态是接口，不是情绪表达。

- 只有 fresh evidence 覆盖目标验收信号时，才能说“完成”“通过”“可用”“已打通”。
- 验证要分层：入口可打开、请求到达、gateway wiring 正确、下游支持、业务成功、结果可见。
- smoke 只证明错误态时，写“错误暴露/缺口确认”。
- 4xx/5xx、`unsupported_operation`、mock/fallback/dry-run、空数据、0 tests、skipped、旧输出或被跳过步骤，默认不能支撑成功结论。
- 高风险、多文件或多 agent 任务完成后，主 Agent 必须看关键 diff、验证输出和跨任务冲突，不采信子 agent summary 作为最终证据。

## Output Checklist

收尾回复按实际执行面裁剪，但需要覆盖：

- 本轮改了哪些 owner 范围。
- 跑了哪些验证，输出证明了什么。
- 是否仍在默认分支，是否有 staged / unstaged / untracked。
- 哪些既有改动被刻意保留未触碰。
- 未验证或残余风险是什么。
