# Workspace Save

日期：2026-08-25
用途：作为 `task-execution-lifecycle` 的 `/hub save` 唯一详细 owner；快速保护工作现场、登记可恢复进度、停止本地服务，并在确认后完成仓库保存。

## Contract

`/hub save` 是跨项目的 checkpoint-and-stop，不代表原任务完成、SPEC 验收或通用环境清理。`doc-hub` 是中央文档事实源，只使用主工作目录和短生命周期 work branch，不创建 linked worktree；发现历史 linked worktree 时，先把未合入内容保存到远端 branch/PR，再按门禁收拢。`agent-hub` 合入 `main` 后，清除确认单中对应的本地隔离 worktree 与 work branch。固定顺序为：

`保护现场 -> 登记进度与待办 -> 结束本地服务 -> 整理推送计划 -> 表格确认 -> 执行 -> doc-hub 合入并收拢历史 worktree -> agent-hub 合入主干并清除隔离空间 -> 表格核验`

前四步由 `/hub save` 本身授权；stage、commit、push、PR 和 merge 必须等待完整确认单被用户明确确认。当前协调 Agent 是控制面，在最终核验前不自我中断。

`/hub save` 启动后保持当前流程最高优先级。后续用户消息默认作为 checkpoint、待办、范围修正或确认单输入继续纳入 save，不得因为出现新的实现性表述就自行暂停或切换回开发；只有用户明确要求暂停、停止或取消 save 时才中断。

禁止借 save 执行 reset、checkout、stash、force push、直接 push target、删除或归档任务、丢弃改动、修改数据库或替代项目质量验收；除本文件定义的 `doc-hub` 历史 linked worktree 收拢和 `agent-hub` 已合入隔离空间外，不删除 worktree 或本地分支。

## Phase 1: Protect

1. 立即停止分派和新增实现，保留 staged、unstaged、untracked、现有提交、忽略配置和外部副作用，不撤销或清理现场。
2. 用宿主当前项目/任务列表、Agent tree 和 Git workspace/worktree 事实做一次快速发现；排除普通对话和当前协调 Agent。对 active workload 取得即时 checkpoint 并用宿主能力中止或暂停，再以 fresh 状态复核。
3. 覆盖有变更的隔离空间和未完成任务即可。只有项目归属、SPEC、恢复入口或状态不清时才读任务正文；不穷举已完成/归档历史，不用重复扫描、长轮询或无关搜索拖慢保存。
4. 宿主缺少可靠停止能力、归属无法确认或任务状态无法核实时，保留现场并记为 blocker，不把“已发消息”或“未发现”写成已暂停。

## Phase 2: Record SPEC Progress And Todos

1. 只更新已有且归属明确的 `execing` 执行包；SPEC 保持冻结，不为无法唯一归属的工作补造 SPEC。
2. STDD 记录 checkpoint 时间、任务/隔离空间、分支、已完成、进行中、验证证据、当前 Git 状态和最小恢复入口。
3. 待办、未验证项、失败、未知状态和 blocker 写入 STDD 或对应 Bug/Optimization Pool；不得把未完成工作标为完成或验收通过。

## Phase 3: Stop Local Services

1. 对每个 active/dirty workspace 做一次监听进程发现，把 PID、监听端口和 cwd/可执行文件/启动记录交叉映射到 workspace；范围不能只限于当前 Agent 亲自启动或仍持有 session 的服务。用户、其它任务或先前运行遗留的服务，只要归属证据明确，也属于 save 的停止范围。
2. 只处理能由 cwd、PID/PGID、端口、启动记录或任务事实证明属于本轮 workspace 的本地服务。单凭进程名、端口惯例或模糊路径不得认领；共享、归属不明或只能强杀的服务不动并列为 blocker。
3. 使用项目停止命令或优雅终止信号，并复核进程和监听端口；不得自动升级为 `SIGKILL`。停止后再次按 workspace 映射检查监听进程，防止 watcher 或父进程重新拉起子服务。
4. 记录已停止服务、端口与核验结果；不把浏览器、数据库、系统服务、容器平台或无关项目进程纳入。

## Phase 4: Build Repository Plan

逐个有未合入内容的隔离空间记录：仓库、绝对路径、local branch、upstream/remote、对应远端 work branch、target、diff 归属、提交意图、最窄验证和风险。一个仓库有多个隔离空间时逐行列出，不因仓库相同合并或遗漏。此阶段不 stage、commit、push、创建 PR 或 merge。

- `PR`：把该隔离空间 push 到同名或确认单明确映射的远端 work branch，并创建或更新对应 PR，到此停止。dirty、ahead 或已有未合入提交的隔离空间不能只登记计划而不推送；已合入且 clean 的隔离空间只核验，不制造空 PR。
- `MR`：创建或更新 PR，适用门禁通过后继续合并。
- `doc-hub`、`agent-hub` 固定为 `MR -> main`，当前和后续 save 都不降级为只提 PR。`doc-hub` 的新改动只在主工作目录建立 work branch；历史 linked worktree 的未合入内容必须先 push 并创建或更新 PR，能安全合入的完成 MR，因范围或冲突阻塞的保留远端 branch/PR 作为恢复入口，然后才允许移除干净 worktree 和本地 branch。`agent-hub` 在 MR 后继续执行 `Cleanup`，只清除确认单内、PR 已合入且本地无未保存内容的隔离 worktree 与 work branch。
- 其它仓库默认 `PR`；只有确认单明确标为 `MR` 才合并，不随 `agent-hub` 自动清理隔离空间。
- 无改动不制造空提交；用户已有或归属不明改动保持原样并写明风险。

## Phase 5: Confirmation Sheet

确认单和最终结果统一使用表格，至少包含 `仓库`、`隔离空间`、`Local branch -> origin/branch`、`动作`：

```markdown
| 仓库 | 隔离空间 | Local branch -> origin/branch | 动作 | Target / 提交意图 | 验证 / 风险 |
| --- | --- | --- | --- | --- | --- |
| `<other-repo>` | `<workspace>` | `<local>` -> `origin/<branch>` | PR | `<target / intent>` | `<evidence / risk>` |
| `doc-hub` | `<primary workspace>` | `<local>` -> `origin/<branch>` | MR + Legacy Cleanup | `main` | `<evidence / recovery entry / risk>` |
| `agent-hub` | `<workspace>` | `<local>` -> `origin/<branch>` | MR + Cleanup | `main` | `<evidence / cleanup blocker>` |
```

同时简短列出已登记的 SPEC 进度/待办、已停止服务和 blocker。用户修正任何内容时，更新计划并重发完整确认单；只有对当前版本的明确确认才生效，不能沿用旧确认或静默追加目标。

## Phase 6: Execute Confirmed Sheet

用户确认后直接执行，不再停留在方案说明：

1. 审查完整 diff，执行最窄安全检查和 `git diff --check`，只暂存归属明确的文件。
2. 形成边界清楚的 checkpoint commit，必要时 rebase 最新 target；不 force push、不直接 push target、不用 merge commit 绕过冲突。
3. 按表将每个有未合入内容的隔离空间 push 到其对应远端 work branch。`PR` 行创建或更新对应 PR 后停止；`MR` 行创建或更新 PR，等待 required checks/approvals，并按仓库既有合入规则完成 merge。
4. 顺序固定为：其它仓库先处理，之后 `doc-hub` MR 与历史 linked worktree 收拢，最后 `agent-hub` MR 与 Cleanup。不得把这两个 Hub 的清理规则扩散到其它仓库。
5. 收拢 `doc-hub` 历史 linked worktree 前，逐个证明工作树无 staged、unstaged、untracked，所有本地提交已进入 `main` 或与同 SHA 的远端 work branch/PR 对齐，且没有运行任务或归属服务；协调控制面离开待删除路径后才正常 remove worktree、删除有远端恢复入口的本地 branch 并 prune。阻塞 PR 不得为了清理而强行合入或删除远端恢复入口。
6. `agent-hub` PR 合入后，先核验远端 `main` 已包含目标改动，并让可用的本地 `main` fast-forward 到同一 SHA；再逐个复核确认单内隔离空间没有 staged、unstaged、untracked、未推送提交、仍运行任务或归属服务。协调控制面离开待删除路径后，删除对应 linked worktree；本地 work branch 已由合入 PR 完整承载时一并删除并 prune worktree 元数据。Squash/rebase 合入导致 Git 祖先关系不足时，只有 PR merged 状态、目标 diff 已在 `main`、远端 work branch 可恢复且本地现场为空四项都成立，才允许删除本地分支。
7. 任一核验不成立时保留该隔离空间并登记 blocker；不得 force remove、丢弃改动或把未清理写成完成。
8. 远端、权限、冲突、验证或归属发生变化时，只停止受影响项并登记 blocker，不静默改 target、分支或动作。

## Phase 7: Verify And Report

逐项核对 SPEC/STDD 可恢复、服务端口已停止、local/remote branch 与 PR/MR 状态一致、未确认改动保持原样。结果继续使用同形表格，并增加 SHA、PR/MR URL、状态或 blocker：

```markdown
| 仓库 | 隔离空间 | Local branch -> origin/branch | 动作 | 结果 / 证据 |
| --- | --- | --- | --- | --- |
```

只有确认单内动作均完成，`doc-hub` 和 `agent-hub` 已合入并同步 `main`、`doc-hub` 不再遗留 linked worktree、`agent-hub` 对应本地隔离空间已清除，服务停止状态已核验，且其它现场/待办均有恢复入口时，save 才写完成；否则明确写 `save incomplete` 并列精确 blocker。原业务任务可以保持 `execing` 和 open Bug，不影响一次 checkpoint 本身被安全保存。

## Checklist

- 是否快速覆盖 active/dirty 现场，没有穷举无关历史。
- 是否先登记已有执行包进度和待办，再进行 Git 保存。
- 是否发现每个 active/dirty workspace 的全部可证明归属监听服务，而不是只停止当前 Agent 启动的进程；是否优雅停止并核对 watcher 不会重新拉起端口。
- 是否在任何 Git 写操作前给出一张完整表格确认单。
- 是否把 `PR` 与 `MR` 的语义写清，且其它仓库默认只到 PR。
- 是否让每个有未合入内容的隔离空间都有对应远端 work branch 和 PR，没有只登记不推送。
- 是否按其它仓库 -> doc-hub MR + 历史 linked worktree 收拢 -> agent-hub MR + Cleanup 的顺序执行，且未把清理规则扩散到其它仓库。
- 是否保证 `doc-hub` 后续只使用主工作目录和 work branch，并在清理历史 worktree 前建立了主干或远端 PR 恢复入口。
- 是否在清除 `agent-hub` 隔离空间前证明 PR 已合入、目标 diff 已进入远端 `main`、本地现场为空且控制面已离开待删除路径。
- 是否没有直接 push main、force push、stash/reset 或丢弃用户改动。
- 是否以同形表格报告实际远端状态和恢复入口。
