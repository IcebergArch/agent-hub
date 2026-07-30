# Review, Git And Cleanup Gates

用途：作为 `task-execution-lifecycle` 的按需参考；当任务进入 cleanup、diff review、`coding`、`git update`、`check`、`push`、`git merge`、`pr`、提交/PR 前审查或完成状态校准时读取。默认入口只保留触发和硬边界，本文件承接细节。

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

## Diff Boundary Review

`git diff` 用户指令废弃；用户要求 `review`、`diff review`、检查改动边界或提交前审查时，默认只读审查：

1. 不修改文件、不暂存、不提交。
2. 先推断预期范围，再对照 staged、unstaged、untracked、base/head 或远端 PR 差异。
3. 回复先给边界结论：预期范围、实际范围、是否越界、是否有预期外影响面、哪些仍待确认。
4. 共享面变更必须搜索真实消费者：API、schema、公共组件、配置、runtime、tool、gateway、生成 client 和 UI 入口。
5. 生成产物要追溯事实源和生成命令；生成 diff 过大时，按本轮目标建立 allowlist。
6. 工作区干净不等于没有分支差异；必要时补充 merge-base、`origin/main...HEAD` 或 PR diff。

提交、暂存、amend、PR、合并或“只提交本次改动”前，完整 diff 审查是必需步骤。无关用户改动保持原样，不借机回滚。

## Coding Command

用户说 `coding` 或 `coding <任务>` 时，表示授权 Agent 在目标项目内把已明确的任务连续推进到 PR，默认组合流程为：

```text
git update -> understand -> minimal implementation -> strict verification
-> PR-ready gate -> git update -> check -> exactly one task commit
-> push work branch -> create/update PR -> wait or authorized merge
```

1. **冻结任务与仓库**：确认 work repo、target、任务目标、非目标、验收信号、owner、当前分支和用户已有改动。只有仓库、目标或写入边界存在高风险歧义时才停下询问；`coding` 不授权顺手重构、跨项目业务改动或丢弃既有改动。
2. **第一次 git update**：按本文件 `Git Update` 用 rebase 同步最新 target/main。dirty worktree 不得用 reset、checkout 或隐式 stash 绕过；目标已是当前分支祖先时以 fresh fetch 和祖先检查确认 no-op，不制造流程提交。
3. **理解后实现**：用当前代码、配置、接口和运行事实确认缺口，再沿现有依赖方向完成最小 coherent diff。实现过程中持续保持领域 owner 清晰、公共 surface 克制、架构整洁；发现相邻问题只在阻塞验收或破坏不变量时纳入。
4. **严格验证**：开发中先跑最窄测试，PR 前必须覆盖项目声明的 required checks；代码项目还必须通过可用的本地 `quality-orchestrator` 先检查/规划，再覆盖所有适用层。缺少项目质量契约不能静默跳过，应按下一步补齐项目侧 manifest；只有纯文档/内容仓库没有可执行质量面时，才可明确记录为不适用。不能把 `0 tests`、skipped、mock/fallback、缺凭据或仅错误暴露计为通过。
5. **质量 owner 分流**：项目自己的命令、manifest、scenario、fixture、selector、domain assertion 和 impact rule 由目标项目补齐；planner、runner、evidence、reporting、policy 等跨项目通用编排缺口才属于 `quality-orchestrator`。确认后者不完备且阻塞严格验证时，拆出有明确边界的子 Agent 完善编排器并独立验证；不得把产品知识迁入编排器，也不得用子 Agent summary 替代主 Agent 对两个仓库 diff 和最终结果的复核。
6. **PR-ready gate**：只有任务验收、项目 required checks、适用的 quality orchestration、diff/影响面/架构审查和临时产物清理全部通过，才进入 PR 阶段。环境或编排能力仍有缺口时报告 blocker，不降级宣称“准备好 PR”。
7. **第二次 git update 与 check**：再次 fetch/rebase 最新 target，解决冲突后执行 `Check Command`。将本次任务整理为相对 target **恰好一个**清晰提交；若任务过大，应先拆成多个独立任务/PR，每个 PR 仍保持一个提交。rebase、冲突解决或提交整理改变内容后，重新跑受影响验证。
8. **Push 与 PR**：按 `Push Command` 只推 work branch，然后创建或更新 PR；PR 描述包含任务范围、影响面、架构判断、验证证据、quality-orchestrator 覆盖情况和残余风险。创建 PR 是 `coding` 的默认终点。
9. **等待或获准 merge**：`coding` 本身不授权实际 merge。没有额外授权时等待 review/审批；用户已明确授权，或仓库存在明确且适用的 auto-merge 规则时，required checks 与 approvals 全部满足后，按 `Git Merge Command` 的 rebase 合入约束完成 merge。

## Check Command

用户说 `check` 或 `git check` 时，默认是合入前只读审查，不自动修改、暂存、提交或推送。流程：

1. **确认范围**：确认当前仓库、当前分支、target/main、staged/unstaged/untracked、work branch 相对 target 的差异。
2. **边界审查**：看 `git status --short`、`git diff --name-status`、`git diff --stat`、`git diff --check`，以及 `git diff --name-status <target>...HEAD` 或等价 PR diff；确认改动是否只覆盖预期 owner，是否改动收敛且不突破领域边界。
3. **影响面审查**：说明改动影响的入口、模块、接口、数据、配置、生成物、运行链路和用户流程；共享面变更必须补真实消费者搜索。
4. **架构审查**：可以不逐行审 AI 写的代码，但必须能判断实现思路、依赖方向、失败模式、扩展点和当前架构是否匹配；重点判断是否不污染架构设计；不理解时直接标为不可合入风险。
5. **提交与合入形状**：检查是否保持一次提交，是否存在大而混杂的变更、无意义提交、merge commit、临时修复提交或长期功能分支风险；大改动建议拆成独立可验证的小改动。
6. **验证证据**：列出已跑验证和缺口；main 合入候选必须有证据支持可构建、可测试、可发布。
7. **输出结论**：先给能否 push / PR / 合入的判断，再列 blocker、风险和建议下一步；只读审查不替用户完成写操作。

## PR Command

用户说 `pr` 时，表示进入主动 PR 收尾，不是只读 diff review。默认目标分支是 `main` / `origin/main`；用户指定其它 target 时才改。流程：

0. **确认工作仓库**：`/hub pr` 从哪个项目上下文发起，就以哪个项目仓库作为 work repo；Agent Hub 只提供流程规则。只有用户明确要求维护 Hub 本身时，才把 `/Users/shatang/Project/agent-hub` 当作 work repo。
1. **更新本地基线**：先 `git fetch`，确认 target、当前分支、ahead/behind、staged/unstaged/untracked；把远端 target 更新到本地 target（默认 `origin/main -> main`）。如果当前就在 target 且有未提交改动，先确认本地 target 与远端 target 是否已对齐；未对齐且 dirty worktree 可能阻塞更新时，停止并保护本地改动。
2. **进入 local work branch**：从已更新的本地 target 切出或确认当前 local work branch。不能把 PR 提交直接落在 target 分支；若发现自己在 `main` 上准备提交，应先切工作分支再继续。
3. **审查两层 diff**：同时看本地工作区变动和 work branch 相对 target 的变动；至少检查 `git status --short`、`git diff --name-status`、`git diff --stat`、`git diff --check`，以及 `git diff --name-status <target>...HEAD` 或等价 PR diff。工作区干净不代表 PR diff 干净。
4. **确认影响面和架构思路**：必须能说清改动影响哪些入口、模块、接口、数据、配置、生成物和用户流程；AI 写的代码可以不逐行细看，但必须理解其实现设计、依赖关系、失败模式和当前架构是否匹配。不理解架构思路时不能进入合入。
5. **调整并清理代码**：修复审查发现的问题；撤销自己引入的无效修改、临时文件、debug、mock/fallback、过渡脚手架和无真实 owner 的扩展；保留用户已有无关改动但不纳入 PR；保持架构、领域边界、接口 owner 和数据事实源不漂移。大改动先拆成解耦、独立、可验证的小改动，避免一次 PR/提交承载一大堆混杂变化。
6. **验证**：跑最窄有效测试、静态检查、生成物检查或真实 workflow smoke；验证失败回到第 5 步。未验证项必须写进 merge comment 的风险段；main 合入候选必须保持可构建、可测试、可发布。
7. **rebase 与冲突处理**：合入前必须同步最新 target/main，并用 rebase 解决当前 work branch 与 target 的差异；禁止用 merge 合入 main，禁止引入 merge commit。冲突解决后重新检查影响面和跑必要验证。
8. **提交与 push**：只有 diff 聚焦、验证证据充分、提交内容边界清楚时才 commit/push；push 前将单次合入内容尽量整理成一个清晰提交，避免无意义提交记录。push 只推 local work branch 的远端，不改 target 分支，不把临时修复、调试提交或流程副作用落到 target。无权限或用户未授权 commit/push 时，停在可 PR 状态并说明缺口。
9. **生成 merge comment**：给可直接贴到 PR/MR 的评论，包含 target、work branch、改动摘要、影响面、架构判断、验证命令与结果、风险/未验证项、保留的无关本地改动、review 关注点；不要把完整 diff 粘进 comment。

`pr` 的核心是“更新基线 + 清理 diff + 推远端 + merge comment”。如果用户只想看 diff，应使用 `review` / `diff review`，不触发 push 或清理。

## Git Update

`git update` 表示用 rebase 将当前分支同步到最新目标分支，不是 Git 子命令。

1. 默认基线：`origin/main` 或 `main`，除非用户指定其它目标。
2. rebase 前确认当前仓库、当前分支、目标分支、ahead/behind、dirty worktree；重点判断“当前分支是否符合用户预期”。
3. 分支和目标符合预期时，rebase 是默认动作；不要用 merge 或制造 merge commit。先用只读检查识别风险，再执行 rebase，保护本地改动并按冲突继续解决。
4. 只有当前仓库/分支明显不符合用户预期、需要 stash/reset/checkout 等会隐藏或丢失本地改动的操作、或 rebase 被 Git 阻塞且无法在保留本地改动的前提下继续时，才停下请用户决策。
5. 需要预判风险时优先只读检查，例如 merge-base、merge-tree、diff name-status 和冲突信号搜索；不要把大型输出原样倾倒给用户。
6. fetch 后当前分支已基于目标分支时，可用只读状态和 diff 验证，不为流程制造 stash、重排 index、merge commit 或长报告。

## Push Command

用户说 `push` 时，默认是推送 work branch，不直接修改 main。流程：

1. **先执行 check 门禁**：必须确认 diff 边界、影响面、架构思路、验证证据和提交形状；check 有 blocker 时不 push。
2. **同步最新 main**：push 前必须 fetch 并确认 work branch 已 rebase 到最新 target/main；禁止使用 merge 同步，禁止携带 merge commit。
3. **整理提交**：单次合入尽量只保留一个清晰 commit；大改动必须拆成解耦、独立、可验证的小改动后分别推进。
4. **验证后推送**：冲突解决、提交整理或 rebase 后必须重新跑必要验证；未验证或验证失败时不声明可合入。
5. **只推工作分支**：push 到 local work branch 对应远端；不得直接 push main，不得把临时修复、调试提交、流程副作用或未验证产物带入目标分支。
6. **输出状态**：说明 target、work branch、commit 形状、验证结果、是否仍有 staged/unstaged/untracked、以及 main 是否保持干净可发布。

## Git Merge Command

用户说 `git merge` 时，默认执行完整合入收尾链路：`git update -> check -> push -> merge 待确认`。流程：

1. **执行 git update**：先确认仓库、work branch 和 target，再 fetch 并用 rebase 同步最新 target/main；禁止使用 merge 同步，禁止携带 merge commit。
2. **执行 check 门禁**：同步后审查 diff 边界、影响面、架构思路、验证证据和提交形状；单次合入尽量只保留一个清晰 commit，大改动拆成解耦、独立、可验证的小改动。check 有 blocker 时不 push。
3. **验证后推送**：冲突解决、提交整理或 rebase 后必须重新跑必要验证；只 push local work branch 对应远端，不得直接 push main，不得带入临时修复、调试提交、流程副作用或未验证产物。
4. **进入 merge 待确认**：push 成功后说明 source/target、commit 形状、检查与验证结果、staged/unstaged/untracked 状态及残余风险，停在可合入状态。
5. **单独确认实际 merge**：`git merge` 指令本身不授权修改 target/main。只有用户在看到上述状态后再次明确确认由 Agent 合入，Agent 才按 rebase 合入约束执行；用户也可以自行 merge。没有确认时不得自动 merge。

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
