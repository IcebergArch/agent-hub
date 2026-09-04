# Workspace Save

日期：2026-08-25
用途：作为 `task-execution-lifecycle` 的 `/hub save` 唯一详细 owner；快速保护工作现场、登记可恢复进度、停止本地服务，并在确认后完成仓库保存。

## Contract

`/hub save` 是跨项目的 checkpoint-and-stop，不代表原任务完成、SPEC 验收或通用环境清理。`doc-hub` 是中央文档事实源，只使用主工作目录和短生命周期 work branch，不创建 linked worktree；发现历史 linked worktree 时，先把未合入内容保存到远端 branch/PR，再按门禁收拢。`agent-hub` 只使用 canonical 主工作目录的 `main`，不创建 work branch、PR 或 linked worktree；所有对话直接共享该工作树，历史隔离现场仅在内容已有恢复入口且工作树为空后收拢。固定顺序为：

`保护现场 -> 登记进度与待办 -> 结束本地服务 -> 整理推送计划 -> doc-hub 直接 MR 并收拢历史 worktree -> agent-hub canonical main 直接保存并收拢历史现场 -> 其它仓库表格确认 -> 执行 -> 表格核验`

该顺序约束有副作用的写操作和最终收口，不要求把只读盘点串行化。互不依赖的任务/Agent 状态、Git 现场、监听进程和其它仓库 `PR` 行应批量读取或有界并行；同一 worktree、branch、index、服务或远端 ref 的操作继续串行。

前四步及 `doc-hub` MR、`agent-hub` canonical main 直接保存由 `/hub save` 本身授权，无需再次确认；其它仓库的 stage、commit、push、PR、merge 和 Cleanup 必须等待完整确认单被用户明确确认。当前协调 Agent 是控制面，在最终核验前不自我中断。

`/hub save` 启动后保持当前流程最高优先级。后续用户消息默认作为 checkpoint、待办、范围修正或确认单输入继续纳入 save，不得因为出现新的实现性表述就自行暂停或切换回开发；只有用户明确要求暂停、停止或取消 save 时才中断。

禁止借 save 执行 reset、checkout、stash、force push、直接 push target、删除或归档任务、丢弃改动、修改数据库或替代项目质量验收；唯一直接 push target 的例外是 `/hub save` 已直接授权的 `agent-hub main -> origin/main` 单分支保存。除本文件定义的 `doc-hub` 历史 linked worktree 和 `agent-hub` 历史隔离现场收拢外，不删除 worktree 或本地分支。

## Phase 1: Protect

1. 立即停止分派和新增实现，保留 staged、unstaged、untracked、现有提交、忽略配置和外部副作用，不撤销或清理现场。
2. 一次冻结 `save snapshot`：用宿主能力批量或并发读取当前项目/任务、Agent tree、Git workspace/worktree/branch/remote 和监听进程；排除普通对话与当前协调 Agent。能力不能并发时也只各取一次，不按 workspace 重复全量扫描。
3. 对 active workload 一次性发出 checkpoint 请求，只做一次有界汇集和一次 fresh 状态复核；仍在运行的 workload 随即用宿主能力中止或暂停。只有项目归属、SPEC 或恢复入口仍不清时才读对应任务正文；不逐任务串行等待、不穷举已完成/归档历史、不长轮询。
4. 宿主缺少可靠停止能力、归属无法确认或任务状态无法核实时，保留现场并记为 blocker，不把“已发消息”或“未发现”写成已暂停。

## Phase 2: Record SPEC Progress And Todos

1. 只更新已有且归属明确的 `execing` 执行包；SPEC 保持冻结，不为无法唯一归属的工作补造 SPEC。
2. STDD 记录 checkpoint 时间、任务/隔离空间、分支、已完成、进行中、验证证据、当前 Git 状态和最小恢复入口。
3. 待办、未验证项、失败、未知状态和 blocker 写入 STDD 或对应 Bug/Optimization Pool；不得把未完成工作标为完成或验收通过。

## Phase 3: Stop Local Services

1. active workload 停止后只取一次监听进程 delta，并与 Phase 1 snapshot 合并；把 PID、监听端口和 cwd/可执行文件/启动记录一次性交叉映射到全部 active/dirty workspace，不为每个 workspace 重跑全量发现。范围不能只限于当前 Agent 亲自启动或仍持有 session 的服务。
2. 只处理能由 cwd、PID/PGID、端口、启动记录或任务事实证明属于本轮 workspace 的本地服务。单凭进程名、端口惯例或模糊路径不得认领；共享、归属不明或只能强杀的服务不动并列为 blocker。
3. 先按 PID/PGID 去重；互不共享进程树的服务用项目停止命令或优雅终止信号有界并行停止，再对受影响 workspace 的监听进程和端口做一次定向复核，防止 watcher、父进程或停止前竞态产生的新子服务遗漏。不得自动升级为 `SIGKILL`。
4. 记录已停止服务、端口与核验结果；不把浏览器、数据库、系统服务、容器平台或无关项目进程纳入。

## Phase 4: Build Repository Plan

用冻结的 `save snapshot` 并发形成仓库行，逐个有未合入内容的隔离空间记录：仓库、绝对路径、local branch、upstream/remote、对应远端 work branch、target、diff 归属、提交意图、最窄验证和风险。同一 repo/ref 的事实只查一次，一个仓库有多个隔离空间仍逐行列出；只有快照后发生变化或证据冲突时才定向刷新该行。此阶段不 stage、commit、push、创建 PR 或 merge。

- `PR`：把该隔离空间 push 到同名或确认单明确映射的远端 work branch，并创建或更新对应 PR，到此停止。dirty、ahead 或已有未合入提交的隔离空间不能只登记计划而不推送；已合入且 clean 的隔离空间只核验，不制造空 PR。
- `MR`：创建或更新 PR，适用门禁通过后继续合并。
- `doc-hub` 固定为 `MR -> main`，当前和后续 save 都不降级为只提 PR。`doc-hub` 的新改动只在主工作目录建立 work branch；历史 linked worktree 的未合入内容必须先 push 并创建或更新 PR，能安全合入的完成 MR，因范围或冲突阻塞的保留远端 branch/PR 作为恢复入口，然后才允许移除干净 worktree 和本地 branch。
- `agent-hub` 对外动作固定记为 `MR`：只在 canonical 主工作目录整理现有 `main` diff，由 `/hub save` 直接授权形成边界清楚的任务提交并同步 `origin/main`，不创建 work branch 或 PR。这里的 `MR` 表示 Hub 的目标集成结果，底层仍遵守 canonical `main` 单分支例外。历史隔离 worktree/branch 先逐一证明内容已进入 `main` 或已有远端恢复入口、现场为空且无运行任务，再安全删除；任何条件不满足都保留并登记 blocker。
- 其它仓库默认 `PR`；只有确认单明确标为 `MR` 才合并，不随 `agent-hub` 自动清理隔离空间。
- clean 隔离空间已有对应 merged PR 时，默认标为 `拟 cleanup`，不重复 push、不制造空 PR；确认后只按门禁收拢本地 worktree 与 local branch，除非确认单另行明确，否则保留远端 branch。
- clean 隔离空间相对唯一 target 已明显落后、没有 active owner，且现有提交已被替代或用户明确废弃时，默认标为 `拟 cleanup`，备注披露准确 behind 数和恢复入口；没有远端恢复入口时必须明确写出 cleanup 将删除最后一个普通 Git ref，并等待确认，不能伪装成无风险清理。dirty 隔离空间不得仅因落后而 cleanup。
- 无改动不制造空提交；用户已有或归属不明改动保持原样并写明风险。

## Phase 5: Confirmation Sheet

`doc-hub` 与 `agent-hub` 在计划完成后直接执行并进入结果表，不进入待确认项。其它仓库一次性输出全部可执行行、blocker 和唯一确认点，不按仓库逐轮提问；无歧义项直接套用本文件默认动作，只有会改变 target、远端写入或删除边界的未知项才进入 blocker。确认单和最终结果统一使用三列表格：`仓库`、`备注`、`动作`；工作空间与远端信息合并进备注，不再单独占列。

- 工作空间放在备注 `空间` 行，只写 `主空间` 或工作空间短名，不加“隔离空间”前缀、不重复绝对路径；需要恢复时在表格外统一列绝对路径。
- 本地 branch 只用于内部执行核对，不在用户确认单展示。remote ref 放在备注 `分支` 行：新建远端写 `New origin/<branch>`，推送已有远端写 `Push -> origin/<branch>`；Cleanup 保留已有远端时写 `Keep origin/<branch>`，没有远端写入或恢复入口时写 `Local only`。禁止用“同名远端”等省略表达。
- `动作` 只使用四个枚举：`Local`、`Cleanup`、`PR`、`MR`，不组合、不增加其它词。`Local` 表示只保护或形成本地 checkpoint，不写远端；`Cleanup` 表示按门禁收拢本地 worktree/local branch；`PR` 表示保存并同步 work branch、创建或更新 PR 后停止；`MR` 表示保存并同步后完成 target 集成，doc-hub 通过 PR merge，Agent Hub canonical main 仍按单分支例外直接同步而不创建 PR。verify、blocked、MAIN、sync 等属于结果或内部策略，不写进动作列。
- `备注` 固定为四行且顺序固定：`空间`、`分支`、`改动`、`背景`。`改动` 用业务化短句回答“这里写的是什么、解决什么问题”，禁止用“几个文件、多少处改动”等数量代替内容；`背景` 只陈述选择当前动作的关键事实，例如 `PR 已上线`、`落后 main 很多`、`main 已有改动`。动作已经由动作列表达，不在备注中重复“计划操作”。target、验证证据和共性风险放在表格后的简短说明。

```markdown
| 仓库 | 备注 | 动作 |
| --- | --- | --- |
| `<other-repo>` | 空间：`主空间`<br>分支：`New origin/<branch>`<br>改动：`<这里实现或记录的业务内容>`<br>背景：`main 已有改动` | `PR` |
| `doc-hub` | 空间：`<短名>`<br>分支：`Push -> origin/<branch>`<br>改动：`<这里保存的 SPEC 或执行记录>`<br>背景：`PR 已具备合入条件` | `MR` |
| `agent-hub` | 空间：`主空间`<br>分支：`Push -> origin/main`<br>改动：`<这里沉淀的流程或方法>`<br>背景：`main 已有改动` | `MR` |
```

同时简短列出已登记的 SPEC 进度/待办、已停止服务和 blocker。用户修正任何内容时，更新计划并重发完整确认单；只有对当前版本的明确确认才生效，不能沿用旧确认或静默追加目标。

## Phase 6: Execute Confirmed Sheet

用户确认后直接执行，不再停留在方案说明：

每个表格行是一个执行单元。互不共享 repo/worktree/branch/index/remote ref 的其它仓库 `PR` 行可有界并行，但单行内部的审查、提交、push、PR 与核验保持顺序；共享 Git 状态的行串行。动作列中的 `MR` 按仓库解释为 doc-hub PR merge 或 agent-hub canonical main 直接同步，仍按下述固定收口顺序执行。

1. 审查完整 diff，执行最窄安全检查和 `git diff --check`，只暂存归属明确的文件。
2. 形成边界清楚的 checkpoint commit，必要时 rebase 最新 target；不 force push、不直接 push target、不用 merge commit 绕过冲突。直接授权的 `agent-hub MR` 执行单元按单分支例外提交 `main`，fetch 后必须确保可安全更新 `origin/main`，分叉或冲突时停止而不覆盖远端。
3. 按表将每个有未合入内容的隔离空间 push 到其对应远端 work branch。`PR` 行创建或更新对应 PR 后停止；`MR` 行创建或更新 PR，等待 required checks/approvals，并按仓库既有合入规则完成 merge；仅 `agent-hub MR` 的底层执行允许 `agent-hub main -> origin/main`，不得推广到其它仓库。
   `Cleanup` 行只处理确认单点名的 clean 隔离空间：再次核对无 active task、归属服务、staged/unstaged/untracked 后，按普通 `git worktree remove` 收拢并删除 local branch；不得 force remove。对应 PR 已 merged 时以 merged commit 为恢复入口；明显落后且用户明确废弃、又没有远端恢复入口时，确认单必须已经披露将删除最后一个普通 Git ref。默认不删除 remote branch。
4. 顺序固定为：先直接执行 `doc-hub` MR 与历史 linked worktree 收拢，再执行 `agent-hub` MR 与历史现场 Cleanup，最后等待并执行其它仓库确认单。不得把这两个 Hub 的直接授权和清理规则扩散到其它仓库。
5. 收拢 `doc-hub` 历史 linked worktree 前，逐个证明工作树无 staged、unstaged、untracked，所有本地提交已进入 `main` 或与同 SHA 的远端 work branch/PR 对齐，且没有运行任务或归属服务；协调控制面离开待删除路径后才正常 remove worktree、删除有远端恢复入口的本地 branch 并 prune。阻塞 PR 不得为了清理而强行合入或删除远端恢复入口。
6. `agent-hub` canonical `main` 推送后，先核验本地与远端 `main` 指向同一 SHA 且包含目标改动；再逐个复核历史隔离现场没有 staged、unstaged、untracked、仅本地提交、仍运行任务或归属服务，并证明其内容已进入 `main` 或已有远端恢复入口。协调控制面离开待删除路径后，删除符合门禁的 linked worktree 与本地 branch 并 prune；任一证明缺失都不得删除。
7. 任一核验不成立时保留该隔离空间并登记 blocker；不得 force remove、丢弃改动或把未清理写成完成。
8. 远端、权限、冲突、验证或归属发生变化时，只停止受影响项并登记 blocker，不静默改 target、分支或动作。

## Phase 7: Verify And Report

复用执行回执，对每个已执行行和受影响端口各做一次定向 fresh 核验；只有快照失效、共享状态变化或跨行冲突时才重新扩大扫描。逐项核对 SPEC/STDD 可恢复、服务端口已停止、local/remote branch 与 PR/MR 状态一致、未确认改动保持原样。结果继续使用同形表格；SHA、PR/MR URL、状态或 blocker 在表格后按仓库简短列出：

```markdown
| 仓库 | 备注 | 动作 |
| --- | --- | --- |
```

只有确认单内动作均完成，`doc-hub` 已合入并同步 `main`、`agent-hub` canonical `main` 已直接保存并同步、两者满足门禁的历史隔离现场已收拢，服务停止状态已核验，且其它现场/待办均有恢复入口时，save 才写完成；否则明确写 `save incomplete` 并列精确 blocker。原业务任务可以保持 `execing` 和 open Bug，不影响一次 checkpoint 本身被安全保存。

## Checklist

- 是否只冻结一次批量 `save snapshot`，后续复用并只定向刷新变化项，没有按 workspace 重复全量扫描或穷举无关历史。
- 是否对 active workload 只做一次 checkpoint fan-out、一次有界汇集和一次 fresh 状态复核，没有逐任务串行等待。
- 是否先登记已有执行包进度和待办，再进行 Git 保存。
- 是否发现每个 active/dirty workspace 的全部可证明归属监听服务，而不是只停止当前 Agent 启动的进程；是否优雅停止并核对 watcher 不会重新拉起端口。
- 是否在其它仓库的任何 Git 写操作前给出一张完整表格确认单；doc-hub 与 agent-hub 是否按直接授权执行且未混入待确认项。
- 确认单是否只用“仓库、备注、动作”三列，是否不展示本地 branch，动作只使用 `Local`、`Cleanup`、`PR`、`MR`，备注是否严格按“空间、分支、改动、背景”四行展示。
- 是否把 `PR` 与 `MR` 的语义写清，且其它仓库默认只到 PR。
- 已合并 PR 或被明确废弃且明显落后的 clean 隔离空间是否优先标为 `拟 cleanup`，并准确披露 behind、恢复入口和最后普通 ref 风险。
- 是否让每个有未合入内容的隔离空间都有对应远端 work branch 和 PR，没有只登记不推送。
- 是否先直接执行 doc-hub MR + 历史 linked worktree 收拢 -> agent-hub MR + 历史现场 Cleanup，再等待并执行其它仓库确认单，且未把 Agent Hub 直接 push `main` 的例外扩散到其它仓库。
- 是否保证 `doc-hub` 后续只使用主工作目录和 work branch，并在清理历史 worktree 前建立了主干或远端 PR 恢复入口。
- 是否在清除 `agent-hub` 历史隔离现场前证明目标 diff 已进入远端 `main` 或已有远端恢复入口、本地现场为空且控制面已离开待删除路径。
- 是否除 `/hub save` 直接授权的 `agent-hub main -> origin/main` 外没有直接 push target，且没有 force push、stash/reset 或丢弃用户改动。
- 是否只并行互不共享 Git/服务状态的行，并保持 `doc-hub MR -> agent-hub MR -> 其它仓库确认执行` 的收口顺序。
- 是否以同形表格报告实际远端状态和恢复入口。
