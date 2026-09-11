# Workspace Checkpoint: Save And Resume

日期：2026-09-09
用途：作为 `task-execution-lifecycle` 的 `/hub save` 与 `/hub resume` 唯一详细 owner；用一份 checkpoint 文档完成全工作空间的暂停、远端保护、代码刷新和新会话恢复。

## Contract

`/hub save` 是跨项目的 checkpoint-and-stop；`/hub resume` 是 checkpoint-and-rehydrate-and-pause。二者不代表原任务完成、SPEC 验收、PR 内容审查或通用环境清理，也不重新判断业务实现是否合理。checkpoint commit、远端 ref 或 checkpoint PR 只证明现场可恢复，不证明其中全部差异属于同一任务，也不得自动成为后续交付 PR 的 scope。

唯一恢复文档固定为 `/Users/shatang/Documents/temp/workspace-checkpoint.md`。每次 save 都完整重写这一份 active checkpoint，以 `checkpoint_id` 区分代次；它是“保存时变更清单 + 任务进度快照 + 恢复入口 + 服务停止结果”的唯一 owner。Git commit/ref、PR、SPEC/STDD 和问题池只作为链接或事实来源，不再为 save 复制同一份进度；不得另建按任务、仓库或会话分散的 checkpoint。代码内容仍以 Git 为事实源，文档只记录业务化变更摘要、文件状态、SHA 和远端入口，不粘贴完整 diff、凭据或大段日志。

save 固定顺序为：

`保护现场 -> 写 checkpoint 草稿 -> 结束本地服务 -> 整理推送计划 -> doc-hub 直接 MR 并收拢历史 worktree -> agent-hub canonical main 直接保存并收拢历史现场 -> 其它仓库表格确认 -> 执行 -> 回写 checkpoint 实际结果 -> 表格核验`

该顺序约束有副作用的写操作和最终收口，不要求把只读盘点串行化。互不依赖的任务/Agent 状态、Git 现场、监听进程和其它仓库 `PR` 行应批量读取或有界并行；同一 worktree、branch、index、服务或远端 ref 的操作继续串行。

前四步、checkpoint 文档写入及 `doc-hub` MR、`agent-hub` canonical main 直接保存由 `/hub save` 本身授权，无需再次确认；其它仓库的 stage、commit、push、PR、merge 和 Cleanup 必须等待完整确认单被用户明确确认。当前协调 Agent 是控制面，在最终核验前不自我中断。

`/hub save` 启动后保持当前流程最高优先级。后续用户消息默认作为 checkpoint、待办、范围修正或确认单输入继续纳入 save，不得因为出现新的实现性表述就自行暂停或切换回开发；只有用户明确要求暂停、停止或取消 save 时才中断。

禁止借 save 执行 reset、checkout、stash、force push、直接 push target、删除或归档任务、丢弃改动、修改数据库或替代项目质量验收；唯一直接 push target 的例外是 `/hub save` 已直接授权的 `agent-hub main -> origin/main` 单分支保存。除本文件定义的 `doc-hub` 历史 linked worktree 和 `agent-hub` 历史隔离现场收拢外，不删除 worktree 或本地分支。

`/hub resume` 只授权读取 checkpoint、查询当前账号的 PR 元数据、fetch，以及对 checkpoint 记录的本地 branch、当前账号所建 open PR 的 head branch 和相应 baseline 做可证明安全的 fast-forward 更新，并创建新的暂停会话。它不授权 rebase、merge、force、清理 worktree、修改业务内容、运行测试、启动服务、commit、push、更新 PR 或继续执行原任务。

## Phase 1: Protect

1. 立即停止分派和新增实现，保留 staged、unstaged、untracked、现有提交、忽略配置和外部副作用，不撤销或清理现场。
2. 一次冻结 `save snapshot`：用宿主能力批量或并发读取当前项目/任务、Agent tree、Git workspace/worktree/branch/remote、当前代码托管账号在这些仓库创建的 open PR 及监听进程；排除普通对话与当前协调 Agent。能力不能并发时也只各取一次，不按 workspace 重复全量扫描。
3. 对 active workload 一次性发出 checkpoint 请求，只做一次有界汇集和一次 fresh 状态复核；仍在运行的 workload 随即用宿主能力中止或暂停。只有项目归属、SPEC 或恢复入口仍不清时才读对应任务正文；不逐任务串行等待、不穷举已完成/归档历史、不长轮询。
4. 宿主缺少可靠停止能力、归属无法确认或任务状态无法核实时，保留现场并记为 blocker，不把“已发消息”或“未发现”写成已暂停。

## Phase 2: Write The Single Checkpoint

1. 在任何 Git 保存动作前完整重写 `/Users/shatang/Documents/temp/workspace-checkpoint.md`，先标记 `save_status: saving` 与 `resume_status: not_started`。文件必须可独立恢复，不能要求新会话去猜旧聊天或遍历散落记录。
2. 顶层固定记录：`checkpoint_id`（上海时区 `YYYYMMDDHHmmss`）、保存时间、协调宿主、代码托管账号、save 状态、resume 状态、覆盖范围、明确排除项、全局 blocker 和最后更新时间。
3. 每个需恢复 workload 使用稳定条目；范围是 active/dirty workspace 与当前账号在 checkpoint 仓库中创建的 open PR head 的并集。每条至少记录：项目与仓库、绝对 workspace、旧 task/thread 标识、关联 SPEC/STDD 链接、local branch、remote/upstream、target、PR URL/number/author/head/base、`single-task / mixed / unknown`、业务化变更摘要、changed files/status、已完成、进行中、下一步、验证证据、未验证项、blocker、服务/PID/端口、计划动作和恢复 ref/SHA。未知值显式写 `unknown`，不得省略后让 resume 猜测；没有旧会话的 clean PR head 也保留条目，并将 progress 明确写为 `unknown` 或可由 PR 事实证明的状态。
4. 任务进度只写 checkpoint；已有 SPEC/STDD、Bug/Optimization Pool 保持原样并仅由路径链接。save 不创建、扩写或迁移正式执行包，也不把相同进度复制到任务评论、多个临时文件或其它账本。
5. 所有 workload snapshot 汇集后回读文档，确认每个 active/dirty workspace 和当前账号 open PR head 恰有一个条目、字段完整且不含凭据、完整 diff 或大日志，再进入服务停止和仓库计划。

最小结构如下；允许增加必要字段，但不得拆成多份 owner：

```markdown
# Workspace Checkpoint

- checkpoint_id: `<YYYYMMDDHHmmss>`
- saved_at: `<Asia/Shanghai timestamp>`
- code_host_account: `<provider + authenticated account>`
- save_status: `saving | saved | incomplete`
- resume_status: `not_started | queued | paused | incomplete`
- scope: `<workspace set>`
- blockers: `<none or concise list>`

## Workloads

### `<stable workload key>`
- project/repository/workspace: `<...>`
- previous task: `<title + id>`
- SPEC/STDD: `<links or N/A>`
- local/remote/target: `<branch + refs>`
- PR: `<url + author + head + base or N/A>`
- change ownership: `single-task | mixed | unknown`
- changes: `<business summary + changed file/status list>`
- progress: `<done / in progress / next>`
- validation/blockers: `<...>`
- services: `<pid/port/result or N/A>`
- save action/recovery: `<Local|Checkpoint|Cleanup|PR|MR + ref/SHA/result>`
- resume packet: `<facts the new task must replay, then pause>`
```

## Phase 3: Stop Local Services

1. active workload 停止后只取一次监听进程 delta，并与 Phase 1 snapshot 合并；把 PID、监听端口和 cwd/可执行文件/启动记录一次性交叉映射到全部 active/dirty workspace，不为每个 workspace 重跑全量发现。范围不能只限于当前 Agent 亲自启动或仍持有 session 的服务。
2. 只处理能由 cwd、PID/PGID、端口、启动记录或任务事实证明属于本轮 workspace 的本地服务。单凭进程名、端口惯例或模糊路径不得认领；共享、归属不明或只能强杀的服务不动并列为 blocker。
3. 先按 PID/PGID 去重；互不共享进程树的服务用项目停止命令或优雅终止信号有界并行停止，再对受影响 workspace 的监听进程和端口做一次定向复核，防止 watcher、父进程或停止前竞态产生的新子服务遗漏。不得自动升级为 `SIGKILL`。
4. 把已停止服务、端口、方法与核验结果回写到对应 checkpoint 条目；不把浏览器、数据库、系统服务、容器平台或无关项目进程纳入。

## Phase 4: Build Repository Plan

用冻结的 `save snapshot` 并发形成仓库行，并写回同一 checkpoint：逐个有未合入内容的隔离空间记录仓库、绝对路径、local branch、upstream/remote、对应远端 ref、target、diff 归属、提交意图、最窄验证和风险。同一 repo/ref 的事实只查一次，一个仓库有多个隔离空间仍逐行列出；只有快照后发生变化或证据冲突时才定向刷新该行。此阶段不 stage、commit、push、创建 PR 或 merge。

- 先把相对 target 的完整差异标为 `single-task`、`mixed` 或 `unknown`；判断依据必须来自明确任务/SPEC、已有 PR scope、提交与文件归属，不能因隔离空间或 branch 名相同就推断为同一交付。
- `Checkpoint`：把精确现场 push 到独立、明确标为 recovery-only 的远端 ref，不创建或更新 PR；这是 `mixed`、`unknown` 或只需暂停恢复时的默认远端保存方式。
- `PR`：只有完整差异已证明为 `single-task`，且目标 PR 与该任务严格一致时，才把隔离空间 push 到对应远端 work branch 并创建或更新 PR，到此停止。dirty、ahead 或已有未合入提交的隔离空间必须进入 `Checkpoint` 或 `PR`，不能只登记计划而不推送；已合入且 clean 的隔离空间只核验，不制造空 PR。
- `MR`：创建或更新 PR，适用门禁通过后继续合并。
- `doc-hub` 固定为 `MR -> main`，当前和后续 save 都不降级为只提 PR。`doc-hub` 的新改动只在主工作目录建立 work branch；历史 linked worktree 的未合入内容必须先 push 并创建或更新 PR，能安全合入的完成 MR，因范围或冲突阻塞的保留远端 branch/PR 作为恢复入口，然后才允许移除干净 worktree 和本地 branch。
- `agent-hub` 对外动作固定记为 `MR`：只在 canonical 主工作目录整理现有 `main` diff，由 `/hub save` 直接授权形成边界清楚的任务提交并同步 `origin/main`，不创建 work branch 或 PR。这里的 `MR` 表示 Hub 的目标集成结果，底层仍遵守 canonical `main` 单分支例外。历史隔离 worktree/branch 先逐一证明内容已进入 `main` 或已有远端恢复入口、现场为空且无运行任务，再安全删除；任何条件不满足都保留并登记 blocker。
- 其它仓库默认 `Checkpoint`；只有 `single-task` 证明成立且确认单明确选择 `PR` 时才进入交付 PR，只有明确标为 `MR` 才合并，不随 `agent-hub` 自动清理隔离空间。
- clean 隔离空间已有对应 merged PR 时，默认标为 `拟 cleanup`，不重复 push、不制造空 PR；确认后只按门禁收拢本地 worktree 与 local branch，除非确认单另行明确，否则保留远端 branch。
- clean 隔离空间相对唯一 target 已明显落后、没有 active owner，且现有提交已被替代或用户明确废弃时，默认标为 `拟 cleanup`，备注披露准确 behind 数和恢复入口；没有远端恢复入口时必须明确写出 cleanup 将删除最后一个普通 Git ref，并等待确认，不能伪装成无风险清理。dirty 隔离空间不得仅因落后而 cleanup。
- 无改动不制造空提交；用户已有或归属不明改动保持原样并写明风险。

## Phase 5: Confirmation Sheet

`doc-hub` 与 `agent-hub` 在计划完成后直接执行并进入结果表，不进入待确认项。其它仓库一次性输出全部可执行行、blocker 和唯一确认点，不按仓库逐轮提问；无歧义项直接套用本文件默认动作，只有会改变 target、远端写入或删除边界的未知项才进入 blocker。确认单和最终结果统一使用三列表格：`仓库`、`备注`、`动作`；工作空间与远端信息合并进备注，不再单独占列。对话中展示的每一行必须与 checkpoint 中对应 workload 的计划动作一致，不维护第二份不同内容的计划。

- 工作空间放在备注 `空间` 行，只写 `主空间` 或工作空间短名，不加“隔离空间”前缀、不重复绝对路径；需要恢复时在表格外统一列绝对路径。
- 本地 branch 只用于内部执行核对，不在用户确认单展示。remote ref 放在备注 `分支` 行：新建远端写 `New origin/<branch>`，推送已有远端写 `Push -> origin/<branch>`；Cleanup 保留已有远端时写 `Keep origin/<branch>`，没有远端写入或恢复入口时写 `Local only`。`Checkpoint` 行还必须在 `背景` 标明 `recovery-only` 及 `single-task / mixed / unknown` 结论，禁止用“同名远端”等省略表达。
- `动作` 只使用五个枚举：`Local`、`Checkpoint`、`Cleanup`、`PR`、`MR`，不组合、不增加其它词。`Local` 表示只保护或形成本地 checkpoint，不写远端；`Checkpoint` 表示同步 recovery-only ref、不创建或更新 PR；`Cleanup` 表示按门禁收拢本地 worktree/local branch；`PR` 表示保存并同步已证明为 `single-task` 的 work branch、创建或更新 PR 后停止；`MR` 表示保存并同步后完成 target 集成，doc-hub 通过 PR merge，Agent Hub canonical main 仍按单分支例外直接同步而不创建 PR。verify、blocked、MAIN、sync 等属于结果或内部策略，不写进动作列。
- `备注` 固定为四行且顺序固定：`空间`、`分支`、`改动`、`背景`。`改动` 用业务化短句回答“这里写的是什么、解决什么问题”，禁止用“几个文件、多少处改动”等数量代替内容；`背景` 只陈述选择当前动作的关键事实，例如 `PR 已上线`、`落后 main 很多`、`main 已有改动`。动作已经由动作列表达，不在备注中重复“计划操作”。target、验证证据和共性风险放在表格后的简短说明。

```markdown
| 仓库 | 备注 | 动作 |
| --- | --- | --- |
| `<other-repo>` | 空间：`主空间`<br>分支：`New origin/<recovery-branch>`<br>改动：`<这里实现或记录的业务内容>`<br>背景：`recovery-only；mixed` | `Checkpoint` |
| `doc-hub` | 空间：`<短名>`<br>分支：`Push -> origin/<branch>`<br>改动：`<这里保存的 SPEC 或执行记录>`<br>背景：`PR 已具备合入条件` | `MR` |
| `agent-hub` | 空间：`主空间`<br>分支：`Push -> origin/main`<br>改动：`<这里沉淀的流程或方法>`<br>背景：`main 已有改动` | `MR` |
```

同时从 checkpoint 摘要已保存的任务进度/待办、已停止服务和 blocker。用户修正任何内容时，先更新 checkpoint，再重发完整确认单；只有对当前版本的明确确认才生效，不能沿用旧确认或静默追加目标。

## Phase 6: Execute Confirmed Sheet

用户确认后直接执行，不再停留在方案说明：

每个表格行是一个执行单元。互不共享 repo/worktree/branch/index/remote ref 的其它仓库 `Checkpoint` / `PR` 行可有界并行，但单行内部的审查、提交、push、PR 与核验保持顺序；共享 Git 状态的行串行。动作列中的 `MR` 按仓库解释为 doc-hub PR merge 或 agent-hub canonical main 直接同步，仍按下述固定收口顺序执行。

1. 只核对完整 diff 的归属和可保存性，执行 `git diff --check`；不得把 `/hub save` 路由为 branch/PR 业务审查，不评价或改写实现内容，也不为保存追加测试。只暂存归属明确且不含凭据、缓存或临时产物的文件。
2. 形成边界清楚的 checkpoint commit，保留当前 branch 历史，不为保存 rebase、merge target、解决冲突或改写提交；不 force push、不直接 push target。直接授权的 `agent-hub MR` 执行单元按单分支例外提交 `main`，fetch 后必须确保可 fast-forward 更新 `origin/main`，分叉时停止而不覆盖远端。
3. 按表将每个有未合入内容的隔离空间 push 到对应远端 ref。`Checkpoint` 行核验远端 ref 指向 checkpoint SHA 后停止，不创建或更新 PR；`PR` 行再次核对 `single-task` 结论，成立时创建或更新对应 PR 后立即停止，不读取或判断 mergeability/conflict，不成立则保持 recovery-only 并报告 blocker；`MR` 行创建或更新 PR，并按仓库既有合入规则尝试完成 merge，平台拒绝时保留 PR 作为恢复入口并报告 blocker。仅 `agent-hub MR` 的底层执行允许 `agent-hub main -> origin/main`，不得推广到其它仓库。
   `Cleanup` 行只处理确认单点名的 clean 隔离空间：再次核对无 active task、归属服务、staged/unstaged/untracked 后，按普通 `git worktree remove` 收拢并删除 local branch；不得 force remove。对应 PR 已 merged 时以 merged commit 为恢复入口；明显落后且用户明确废弃、又没有远端恢复入口时，确认单必须已经披露将删除最后一个普通 Git ref。默认不删除 remote branch。
4. 顺序固定为：先直接执行 `doc-hub` MR 与历史 linked worktree 收拢，再执行 `agent-hub` MR 与历史现场 Cleanup，最后等待并执行其它仓库确认单。不得把这两个 Hub 的直接授权和清理规则扩散到其它仓库。
5. 收拢 `doc-hub` 历史 linked worktree 前，逐个证明工作树无 staged、unstaged、untracked，所有本地提交已进入 `main` 或与同 SHA 的远端 work branch/PR 对齐，且没有运行任务或归属服务；协调控制面离开待删除路径后才正常 remove worktree、删除有远端恢复入口的本地 branch 并 prune。阻塞 PR 不得为了清理而强行合入或删除远端恢复入口。
6. `agent-hub` canonical `main` 推送后，先核验本地与远端 `main` 指向同一 SHA 且包含目标改动；再逐个复核历史隔离现场没有 staged、unstaged、untracked、仅本地提交、仍运行任务或归属服务，并证明其内容已进入 `main` 或已有远端恢复入口。协调控制面离开待删除路径后，删除符合门禁的 linked worktree 与本地 branch 并 prune；任一证明缺失都不得删除。
7. 任一核验不成立时保留该隔离空间并登记 blocker；不得 force remove、丢弃改动或把未清理写成完成。
8. 远端、权限、冲突、验证或归属发生变化时，只停止受影响项并登记 blocker，不静默改 target、分支或动作。
9. 每个执行单元完成、阻塞或被用户取消后，立即把实际动作、local SHA、remote ref、PR/MR URL、核验结果和 blocker 回写到原 checkpoint 条目；计划值不能冒充实际结果。

## Phase 7: Verify And Report

复用执行回执，对每个已执行行和受影响端口各做一次定向 fresh 核验；只有快照失效、共享状态变化或跨行冲突时才重新扩大扫描。逐项核对 checkpoint 条目完整、服务端口已停止、local/remote branch 与 PR/MR 状态一致、未确认改动保持原样。结果继续使用同形表格；SHA、PR/MR URL、状态或 blocker 在表格后按仓库简短列出：

```markdown
| 仓库 | 备注 | 动作 |
| --- | --- | --- |
```

只有确认单内动作均完成，`doc-hub` 已合入并同步 `main`、`agent-hub` canonical `main` 已直接保存并同步、两者满足门禁的历史隔离现场已收拢，服务停止状态已核验，且每个 active/dirty workspace 与当前账号 open PR head 都在 checkpoint 中有实际恢复入口时，才把 `save_status` 改为 `saved` 并报告 save 完成；否则改为 `incomplete`，列出精确 blocker 和仍可恢复的范围。原业务任务可以保持 `execing` 和 open Bug，不影响一次 checkpoint 本身被安全保存。

## Phase 8: Resume From Checkpoint

1. `/hub resume` 先读取且只读取 `/Users/shatang/Documents/temp/workspace-checkpoint.md` 作为恢复索引；校验文件存在、`checkpoint_id` 唯一、`save_status` 为 `saved` 或可部分恢复的 `incomplete`、每个 workload 的仓库/branch/target/PR/progress/recovery 字段完整。缺文件、字段冲突或恢复 ref 不可达时停止受影响条目，不从旧聊天、散落 STDD 或目录猜测补全。
2. 用 checkpoint 中的仓库集合冻结恢复范围；识别当前代码托管账号，并对每个记录的 branch 查询 head 匹配且由当前账号创建的 open PR。PR 已 merged/closed、作者不符、head/base 改变或一个 branch 对应多个候选时，记录 fresh 状态并阻止该条目的新会话创建，不沿用保存时假设。
3. 形成去重集合：checkpoint 记录的 local branches、上述 open PR 的 head branches、这些 PR 的 base branches。一个 repo/ref 只刷新一次；未出现在 checkpoint 仓库集合中的本地仓库或账号 PR 不纳入本次 resume。

## Phase 9: Refresh Code Safely

1. 对冻结集合先 fetch 精确 remote refs，再核对 worktree、dirty 状态、upstream、remote SHA 与本地 SHA。fetch 成功不等于 local branch 已更新。
2. local branch、当前账号的 PR head 和 baseline 只有在无 staged/unstaged/untracked 阻塞、没有分叉、不会改写其它 worktree 的情况下才做 fast-forward；branch 已等于远端时记为 `up-to-date`。不得把 PR head rebase/merge 到新 baseline，也不得 checkout、stash、reset、force 或解决冲突。
3. dirty、diverged、remote ref 缺失、branch 被不明 worktree 占用或 baseline 不唯一时保持原样并标记 `blocked`；其它独立条目继续。逐条记录 old/new SHA、head/base 与 `updated / up-to-date / blocked / missing`。
4. 将 fresh Git 结果与保存时 snapshot 并列展示，不用新 baseline 覆盖 checkpoint 原值；这样新会话能区分“save 时状态”和“resume 时状态”。

## Phase 10: Open Paused Sessions

1. 只为 Phase 8 解析成功、仍 open、作者为当前账号且 exact head 可定位的 PR 创建新会话；按 `provider/repository/head` 去重，一条 PR head 对应一个会话。仅有 recovery-only ref、Local/Checkpoint 行或已 merged/closed PR 的 workload 不自动开会话。
2. 新会话必须绑定精确仓库与 PR head。优先复用 checkpoint 记录且仍存在的 workspace；宿主只能创建新 worktree 时，从精确 head/ref 创建，不退回默认分支、不在旧主工作区偷偷切 branch。无法证明绑定正确时不创建。
3. 首条消息使用 checkpoint 中该 workload 的 `resume packet`，并追加 fresh Git 结果。内容必须完整回放 `checkpoint_id`、PR/head/base、变更摘要、已完成、进行中、下一步、验证、blocker 和恢复入口；结尾明确要求：只回复恢复快照与当前暂停状态，不改文件、不运行测试、不启动服务、不执行 Git/PR 动作，等待用户的新指令。
4. 创建后做一次有界状态确认，验证新会话已给出回放回复并处于 idle/paused。worktree 仍在创建时记为 `queued`；会话报错、开始执行或无法等待时，停止受影响会话并报告 blocker。单次 resume 内若已有带同一 `checkpoint_id + PR URL` 的恢复会话，则复用它完成确认，避免部分重试产生重复会话。
5. 宿主缺少创建或确认新会话的能力时返回 `resume incomplete`，并给出该 workload 的完整 replay packet；不得让当前协调会话代替新会话，也不得声称已恢复。

## Phase 11: Resume Report

1. 先按 checkpoint 简要梳理保存范围、任务进度、恢复入口和未解决 blocker，再列出每个 repo/ref 的代码更新结果与每个 PR head 的新会话标识/状态。
2. 只有所有可恢复 PR head 均绑定正确分支、完成快照回放并处于 idle/paused 时，才报告 resume 完成。部分分支或会话失败时写 `resume incomplete`，但保留其它已恢复会话。
3. checkpoint 文档保持保存时事实不变；resume 只更新 `resume_status`、`last_resumed_at` 和本次会话标识，不得改写保存时进度、SHA 或 `save_status`。全部新会话确认暂停后记为 `paused`，部分失败记为 `incomplete`，仍在创建记为 `queued`。

## Checklist

- 是否只冻结一次批量 `save snapshot`，后续复用并只定向刷新变化项，没有按 workspace 重复全量扫描或穷举无关历史。
- 是否对 active workload 只做一次 checkpoint fan-out、一次有界汇集和一次 fresh 状态复核，没有逐任务串行等待。
- 是否在任何 Git 保存前完整重写唯一 `workspace-checkpoint.md`，且所有 active/dirty workspace 与当前账号 open PR head 恰有一个自包含条目。
- 是否把变更、进度、验证、blocker、服务和恢复入口都收敛到 checkpoint，而没有为 save 分散改写 STDD、问题池、任务评论或其它临时文档。
- 是否发现每个 active/dirty workspace 的全部可证明归属监听服务，而不是只停止当前 Agent 启动的进程；是否优雅停止并核对 watcher 不会重新拉起端口。
- 是否在其它仓库的任何 Git 写操作前给出一张完整表格确认单；doc-hub 与 agent-hub 是否按直接授权执行且未混入待确认项。
- 确认单是否只用“仓库、备注、动作”三列，是否不展示本地 branch，动作只使用 `Local`、`Checkpoint`、`Cleanup`、`PR`、`MR`，备注是否严格按“空间、分支、改动、背景”四行展示。
- 是否把恢复性 `Checkpoint` 与交付性 `PR` / `MR` 的语义写清，且其它仓库默认只建立 recovery-only 远端 ref。
- 是否保持 checkpoint branch 原历史，未为 save rebase、读取或处理 target 冲突，并在 PR 创建或更新后立即停止。
- 已合并 PR 或被明确废弃且明显落后的 clean 隔离空间是否优先标为 `拟 cleanup`，并准确披露 behind、恢复入口和最后普通 ref 风险。
- 是否让每个有未合入内容的隔离空间都有对应远端恢复 ref，没有把 `mixed` / `unknown` 现场加入交付 PR，也没有只登记不推送。
- 是否先直接执行 doc-hub MR + 历史 linked worktree 收拢 -> agent-hub MR + 历史现场 Cleanup，再等待并执行其它仓库确认单，且未把 Agent Hub 直接 push `main` 的例外扩散到其它仓库。
- 是否保证 `doc-hub` 后续只使用主工作目录和 work branch，并在清理历史 worktree 前建立了主干或远端 PR 恢复入口。
- 是否在清除 `agent-hub` 历史隔离现场前证明目标 diff 已进入远端 `main` 或已有远端恢复入口、本地现场为空且控制面已离开待删除路径。
- 是否除 `/hub save` 直接授权的 `agent-hub main -> origin/main` 外没有直接 push target，且没有 force push、stash/reset 或丢弃用户改动。
- 是否只并行互不共享 Git/服务状态的行，并保持 `doc-hub MR -> agent-hub MR -> 其它仓库确认执行` 的收口顺序。
- 是否以同形表格报告实际远端状态和恢复入口。
- resume 是否只处理 checkpoint 范围内、由当前账号创建且仍 open 的 PR，并同时刷新记录的 local/head/base refs。
- resume 是否只使用 fetch 与安全 fast-forward，没有 rebase、merge、force、stash/reset、业务实现、测试、服务或远端写操作。
- 每个可恢复 PR head 是否以精确 branch 打开一个新会话，完整回放 save 时快照和 fresh Git 状态后保持暂停；失败项是否明确而未伪造已恢复。
