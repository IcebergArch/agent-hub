# Review, Git And Cleanup Gates

用途：作为 `task-execution-lifecycle` 的按需参考；当任务进入 cleanup、`/hub refactor`、`coding`、`git update`、`code-update`、`pr`、提交/PR 前审查或完成状态校准时读取。默认入口只保留触发和硬边界，本文件承接细节。

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

## Refactor Command

用户说 `/hub refactor` 时，授权 Agent 审查并直接收敛当前改动，不只是返回 review findings：

1. **冻结范围**：确认当前任务、目标 owner、必须保留的既有效果、target/base 和用户已有无关改动；高风险歧义未消除前不删除或重写。
2. **盘点完整 diff**：检查 staged、unstaged、untracked，以及必要的 base/head、`origin/main...HEAD` 或 PR diff；工作区干净不等于没有分支差异。
3. **最小改动审查**：逐项判断改动是否直接服务当前目标，是否存在重复实现、无真实消费者的扩展、兼容层、fallback、mock、debug、临时文件、过度生成物或机会主义重构。
4. **领域与架构审查**：确认 owner、依赖方向、public surface、数据事实源和模块职责未漂移；共享面改动搜索 API、schema、公共组件、配置、runtime、tool、gateway、生成 client 和 UI 等真实消费者。
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
2. **第一次 git update**：按本文件 `Git Update` 用 rebase 同步最新 target/main。dirty worktree 不得用 reset、checkout 或隐式 stash 绕过；目标已是当前分支祖先时以 fresh fetch 和祖先检查确认 no-op，不制造流程提交。
3. **理解后实现**：用当前代码、配置、接口和运行事实确认缺口，再沿现有依赖方向完成最小 coherent diff。实现过程中持续保持领域 owner 清晰、公共 surface 克制、架构整洁；发现相邻问题只在阻塞验收或破坏不变量时纳入。
4. **严格验证**：开发中先跑最窄测试，PR 前必须覆盖项目声明的 required checks；代码项目还必须通过可用的本地 `quality-orchestrator` 先检查/规划，再覆盖所有适用层。缺少项目质量契约不能静默跳过，应按下一步补齐项目侧 manifest；只有纯文档/内容仓库没有可执行质量面时，才可明确记录为不适用。不能把 `0 tests`、skipped、mock/fallback、缺凭据或仅错误暴露计为通过。
5. **质量 owner 分流**：项目自己的命令、manifest、scenario、fixture、selector、domain assertion 和 impact rule 由目标项目补齐；planner、runner、evidence、reporting、policy 等跨项目通用编排缺口才属于 `quality-orchestrator`。确认后者不完备且阻塞严格验证时，拆出有明确边界的子 Agent 完善编排器并独立验证；不得把产品知识迁入编排器，也不得用子 Agent summary 替代主 Agent 对两个仓库 diff 和最终结果的复核。
6. **PR-ready gate**：只有任务验收、项目 required checks、适用的 quality orchestration、diff/影响面/架构审查和临时产物清理全部通过，才进入 PR 阶段。环境或编排能力仍有缺口时报告 blocker，不降级宣称“准备好 PR”。
7. **第二次 git update 与最终门禁**：再次 fetch/rebase 最新 target，解决冲突后检查 staged、unstaged、untracked、work branch 相对 target 的完整 diff、影响面、领域边界、架构匹配、验证证据和提交形状。将本次任务整理为相对 target **恰好一个**清晰提交；若任务过大，应先拆成多个独立任务/PR，每个 PR 仍保持一个提交。rebase、冲突解决或提交整理改变内容后，重新跑受影响验证。
8. **提交、push 与 PR**：只提交当前任务并只推 work branch，然后创建或更新 PR；不得直接 push target/main。PR 描述包含任务范围、影响面、架构判断、验证证据、quality-orchestrator 覆盖情况和残余风险。创建 PR 是 `coding` 的默认终点。
9. **等待或获准 merge**：`coding` 本身不授权实际 merge。没有额外授权时等待 review/审批；用户已明确授权，或仓库存在明确且适用的 auto-merge 规则时，required checks 与 approvals 全部满足后，按 rebase 合入约束完成 merge，不引入 merge commit。

## PR Command

用户说 `pr` 时，表示进入主动 PR 收尾，不是只读审查。默认目标分支是 `main` / `origin/main`；用户指定其它 target 时才改。流程：

**顺序门禁**：第 1 步的基线更新是 commit、push 和创建/更新 PR 的前置条件，不得因工作区干净、已有提交或已有 PR 而跳过。若误在基线更新前完成 commit/push，立即停止后续 PR 动作，补做 fetch/rebase、冲突处理、验证和单提交整理，再用 `--force-with-lease` 更新 work branch；不得把旧 push 当作流程已完成。

0. **确认工作仓库**：`/hub pr` 从哪个项目上下文发起，就以哪个项目仓库作为 work repo；Agent Hub 只提供流程规则。只有用户明确要求维护 Hub 本身时，才把 `/Users/shatang/Project/agent-hub` 当作 work repo。
1. **更新本地基线**：先 `git fetch`，确认 target、当前分支、ahead/behind、staged/unstaged/untracked；把远端 target 更新到本地 target（默认 `origin/main -> main`）。如果当前就在 target 且有未提交改动，先确认本地 target 与远端 target 是否已对齐；未对齐且 dirty worktree 可能阻塞更新时，停止并保护本地改动。
2. **进入 local work branch**：从已更新的本地 target 切出或确认当前 local work branch。不能把 PR 提交直接落在 target 分支；若发现自己在 `main` 上准备提交，应先切工作分支再继续。
3. **审查两层 diff**：同时看本地工作区变动和 work branch 相对 target 的变动；至少检查 `git status --short`、`git diff --name-status`、`git diff --stat`、`git diff --check`，以及 `git diff --name-status <target>...HEAD` 或等价 PR diff。工作区干净不代表 PR diff 干净。
4. **确认影响面和架构思路**：必须能说清改动影响哪些入口、模块、接口、数据、配置、生成物和用户流程；AI 写的代码可以不逐行细看，但必须理解其实现设计、依赖关系、失败模式和当前架构是否匹配。不理解架构思路时不能进入合入。
5. **调整并清理代码**：修复审查发现的问题；撤销自己引入的无效修改、临时文件、debug、mock/fallback、过渡脚手架和无真实 owner 的扩展；保留用户已有无关改动但不纳入 PR；保持架构、领域边界、接口 owner 和数据事实源不漂移。大改动先拆成解耦、独立、可验证的小改动，避免一次 PR/提交承载一大堆混杂变化。
   - **验证工件边界**：一次性验证所需的脚本、workflow、构建开关或部署资源默认只作为临时验证工件，不进入产品 PR；只有用户明确要求长期保留，且它具备稳定消费者、维护 owner 和回归价值时，才作为正式测试或 CI 能力提交。
6. **验证**：跑最窄有效测试、静态检查、生成物检查或真实 workflow smoke；验证失败回到第 5 步。未验证项必须写进 merge comment 的风险段；main 合入候选必须保持可构建、可测试、可发布。
7. **rebase 与冲突处理**：合入前必须同步最新 target/main，并用 rebase 解决当前 work branch 与 target 的差异；禁止用 merge 合入 main，禁止引入 merge commit。冲突解决后重新检查影响面和跑必要验证。
8. **整理提交、提交信息与 push**：只有 diff 聚焦、验证证据充分、提交内容边界清楚时才 commit/push；push 前审查 `<target>..HEAD` 中每个将进入 PR 的提交，并将单次合入内容尽量整理成一个清晰提交，避免无意义提交记录。
   - **遵循仓库约定**：使用目标仓库约定的语言和格式；没有明确规则时，以近期已合入提交的主语言和 Conventional Commit 使用方式为准，不自行发明新风格。
   - **标题面向 reviewer**：使用具体领域对象和自然、直接的动作说明实际结果，使 reviewer 不看 diff 也能理解改变了什么。不要用模糊评价词、只有实现者才懂的内部缩写、代码动作直译或需要结合 diff 猜测含义的抽象短句；技术机制确实是变更主体时才写进标题。
   - **标题与正文分工**：标题保持简洁，原因、约束和取舍放正文；不能为了短而牺牲语义或语言自然度。保留多个提交时，每条标题必须表达不同且连贯的意图。
   - **安全改写历史**：已有远端提交需要改写时，先确认 work branch 可安全 force-push 且不会覆盖他人工作。

   push 只推 local work branch 的远端，不改 target 分支，不把临时修复、调试提交或流程副作用落到 target。无权限或用户未授权 commit/push 时，停在可 PR 状态并说明缺口。
9. **生成 merge comment**：给可直接贴到 PR/MR 的评论，包含 target、work branch、改动摘要、影响面、架构判断、验证命令与结果、风险/未验证项、保留的无关本地改动、review 关注点；不要把完整 diff 粘进 comment。

### 仓库历史隔离

- PR/MR 正文、评论、review、commit message、release note 等远端可见历史只记录当前仓库的交付事实。内部使用过的文档库、规则库、工具库、验证仓库或其它工作区，不因参与过程而成为当前仓库的关联项。
- 跨仓库 issue/PR shorthand、URL 或 backlink 会生成额外通知和可能长期保留的时间线事件；只有对方确属依赖、共同发布单元或需要双向追踪的契约 owner 时才可建立引用。仅用于过程记录、取证或内部编排时不得写入。
- push、创建/更新 PR 或发布评论前，检查标题、正文、评论草稿、commit message 与将提交的文档中是否出现其它仓库 slug、issue/PR URL 或本地工作区路径；没有明确交付关系的引用必须移除，以仓库内可验证事实表达结果。
- 若已误建跨仓库引用，先编辑或删除可控的引用源并停止继续传播；平台自动生成且不可删除的 cross-reference 事件要明确报告残余，不能通过更多跨仓库评论解释或“修复”。

`pr` 的核心是“更新基线 + 清理 diff + 推远端 + merge comment”。如果目标只是审查并收敛当前改动，使用 `/hub refactor`，不触发提交、push 或 PR。

## Git Update

`git update` 表示用 rebase 将当前分支同步到最新目标分支，不是 Git 子命令。

1. 默认基线：`origin/main` 或 `main`，除非用户指定其它目标。
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
