# Workspace Save

日期：2026-08-21
用途：作为 `task-execution-lifecycle` 的按需 reference；用户明确调用 `/hub save` 时，用于停止当前工作区活动、固化 SPEC 执行检查点并把各项目安全推送到对应远端分支。

## Contract

`/hub save` 是跨项目的 **checkpoint-and-stop** 命令，不代表原任务完成、SPEC 归档或环境清理；其远端收尾包含业务仓库保存，以及 doc-hub、agent-hub 的 PR squash merge。一次明确调用授权以下动作：

1. 发现、冻结并结束当前 workspace 中宿主可验证管理的 workload Agent 活动。
2. 读取这些活动的实际进度，把已完成工作登记到 STDD，把所有未完成、未验证、不确定或无法恢复的事项登记到原执行包 Bug Pool。
3. 在所有受影响项目的文档检查点都落盘后，依次保存业务项目的确认目标分支，再让 doc-hub、agent-hub 通过 PR squash merge 到各自 `main`。

该授权不包含 force push、stash/reset、丢弃改动、删除或归档任务、停止未确认或归属不明的服务、修改数据库或代替项目质量验收。对 doc-hub 与 agent-hub，`/hub save` 明确授权复用现有 PR 流程创建或更新 PR，并在 required checks 通过后 squash merge，使 `main` 只新增一个提交；不直接 push 主干。发现完成后必须展示一张统一确认单，列全本轮业务仓库、本地分支、remote、即将推送的远端分支、主干风险，以及有项目归属证据的 workspace localhost 服务及关闭动作；用户一次明确确认后才执行这些业务仓库和服务动作。`doc-hub` 与 `agent-hub` 不进入确认单。

当前协调 Agent 是控制面，不属于待停止 workload；它只在完成最终核验并返回结果后自然结束。否则命令会在保存工作区之前自我终止。

## Invariants

- **事实优先**：只对宿主 API 可发现、可归属、可核验的活动声明已停止；不扫描 OS 进程猜 Agent，不把消息已发送等同于已暂停。
- **两阶段静默**：先禁止接收新任务并请求 checkpoint，再中止 Agent；不能先中止后虚构其进度。
- **文档先于 Git**：所有受影响 SPEC 执行包必须先完成本地 checkpoint，之后才能开始任何项目 commit/push。
- **一张确认单**：业务仓库范围、local -> remote branch 映射、主干风险和 workspace localhost 服务关闭动作一次完整展示、一次确认；后续不得静默追加目标。
- **SPEC 保持冻结**：需求事实不写进度。进度、决策、证据和 Git 状态进 STDD；未完成项、验证缺口、未知状态、发现/停止覆盖缺口和保存失败进 Bug Pool。
- **不伪装完成**：checkpoint 可以成功而原业务任务仍有 open Bug；不得因此完成或归档 SPEC。
- **不泄露秘密**：凭据、token、私钥、`.env`、缓存、日志中的敏感参数和来源不明的大文件不得提交；发现后记录脱敏 Bug 并阻止受影响仓库的保存完成。
- **复用 Git owner**：业务仓库按确认单保存；doc-hub 与 agent-hub 不另造冲突或合入机制，完整复用现有 `git update` 与 PR 流程。
- **顺序固定**：业务项目全部处理后才处理 doc-hub，agent-hub 永远最后处理；前一层有未解决保存失败时不宣称全局完成。
- **单提交主干**：doc-hub 与 agent-hub 的目标固定为各自 `main`，通过 squash merge 让目标主干只新增一个提交；PR 分支提交数不代替 coherent diff、checks 和最终 squash 结果检查。

## Phase 1: Freeze And Discover

1. 宣布进入 save barrier：协调 Agent 不再分派新工作；后续用户新指令若覆盖 `/hub save`，立即停止本流程并报告已完成的阶段。
2. 使用当前宿主 adapter 提供的 agent/team、task/thread 能力列出当前 workspace 的运行活动。发现范围至少记录：宿主能力、查询时间、活动 ID、状态、owner、项目/仓库、分支和关联 SPEC。
3. 排除当前协调 Agent；已明确 terminal/completed/cancelled 的活动只作对账，不重复中止。
4. 无法证明属于当前 workspace 的其它用户任务不擅自停止。宿主没有全局发现或停止能力时，明确记录覆盖边界；若有证据表明仍存在未覆盖活动，把它作为 open Bug，不能声称“所有 Agent 已暂停”。
5. 对每个业务仓库解析绝对路径、当前本地分支、remote 和即将推送的远端分支，并标记是否为 `main`、`master`、默认/受保护/集成分支；remote 或目标分支不明确时标为 blocker，不得猜测。
6. 只读盘点监听 localhost 的服务，用 PID、监听地址/端口、命令、cwd、父进程或启动账本证明其属于本轮 workspace 项目。只有归属证据充分的服务才进入候选关闭清单；系统服务、数据库、浏览器、容器平台和其它项目进程默认排除。
7. 展示唯一确认单：业务仓库按 `repo | local branch | remote | remote branch | main risk | action` 列出，服务按 `project | PID | address/port | command | ownership evidence | stop action` 列出，同时列出 blocker 和排除项。`doc-hub`、`agent-hub` 不进入确认单。用户确认后冻结清单；新增目标必须重新生成整张确认单，不得逐项补问。

## Phase 2: Checkpoint And Stop

对每个运行中的 workload Agent 执行：

1. 发送 checkpoint 请求，要求立即停止新的写操作，并返回：任务目标、项目、仓库路径、分支、SPEC ID/路径、已完成进度、证据、待完成点、失败/未验证项、dirty/staged/untracked 文件、已启动进程和外部副作用。
2. 在宿主允许的有界等待内收集回复；Agent 返回后核对其状态和实际 repo diff，不能只采信 summary。
3. 收到 checkpoint 后调用宿主停止/中止能力结束活动；再次查询状态，只有 terminal/interrupted/paused 等可验证状态才记为已结束。
4. Agent 无响应时，先中止以阻止继续写入，再从只读 thread、Git 状态和已有执行账本恢复能证明的事实；无法恢复的进度统一标为 `unknown after interruption` Bug，不猜测完成度。
5. “结束活动”只结束 Agent 本轮执行，不删除、归档或抹除 thread/task。只有已进入 Phase 1 统一确认单的 workspace localhost 服务可以关闭；其余服务、worker 和端口只登记为 Bug/风险。

## Phase 3: SPEC Checkpoint Gate

1. 读取 Hub README 登记的 doc-hub 入口及其当前 SPEC lifecycle、lookup、naming 和写入规则；Hub 本 reference 不硬编码任何项目路径或文件名。
2. 按稳定 SPEC ID 合并同一执行包的多个 Agent checkpoint。对每个受影响项目：
   - 已有匹配的 execing SPEC：保持 SPEC 冻结，只更新 STDD 和 Bug Pool。
   - 没有执行包但能证明工作已由用户授权且需求身份稳定：按 doc-hub 当前规则补建对应 execing 恢复包，注明“执行先于检查点建档”及证据来源；需求写 SPEC，已发生进度写 STDD，遗留写 Bug Pool。
   - 无法确认需求身份、项目归属或执行授权：不得伪造 SPEC；在 doc-hub 当前规则允许的项目级恢复记录中登记脱敏 blocker，并把该覆盖缺口列为本次 `/hub save` 未完成项。
3. STDD 至少记录 checkpoint 时间、Agent/task ID、仓库/分支、完成进度、证据、当前 Git 状态、验证状态和恢复入口。
4. Bug Pool 至少覆盖所有待完成点、失败验证、未跑验证、Agent 未响应、宿主覆盖缺口、进程/外部副作用、敏感或不可提交文件、无 upstream/权限和预期 push 风险；相同根因去重但保留来源活动。
5. 检查每个受影响项目的 SPEC/STDD/Bug 链接、状态和 ID 一致性。所有项目本地文档更新完成前，不进入 Git 保存阶段。

## Phase 4: Stop Confirmed Services And Save Repositories

先按确认单对 workspace localhost 服务发送其支持的优雅停止信号或项目声明的停止命令，等待有界时间并复核 PID 与监听端口；不得升级为不受控批量 kill。停止失败时记录 Bug 和实际状态，不能声称已关闭。

先建立 repo ledger，去重同一仓库的多个活动，并按以下顺序执行：

1. 所有业务/代码项目。
2. doc-hub，通过 PR squash merge 到 `main`。
3. agent-hub，通过 PR squash merge 到 `main` 且永远最后。

每个仓库执行：

1. 读取仓库规则；记录绝对路径、当前分支、默认/受保护分支、upstream、HEAD、staged/unstaged/untracked 和 remote。不得因为目标是快速保存而跳过敏感文件、生成物和缓存检查。
2. 业务仓库必须与确认单中的 repo、local branch、remote、remote branch 完全一致；主干动作必须已在同一确认单显式标红。任一目标改变或未确认时不得执行，并把未推送状态登记为 Bug。doc-hub 与 agent-hub 跳过该确认单门禁。
3. 审查实际 diff，把当前活动产生且允许版本控制的改动组成一个 checkpoint commit。保留已有提交，不改写历史；同仓库有多个 Agent 时默认形成一个真实工作区检查点，不伪造按 Agent 拆分。
4. 纯 checkpoint 不要求补跑完整项目测试，但必须执行项目适用的最窄静态安全检查和 `git diff --check`；任何未跑测试都已经在 Bug Pool 中明确记录，不能写成验证通过。
5. 业务仓库提交当前工作树后，按确认单把最终 HEAD push 到确定的远端分支；目标已前进时复用 `git update` rebase 后重新验证。不得猜 remote、改推其它分支或 force push，冲突无法可靠裁决时标记 `save_failed`。
6. doc-hub 与 agent-hub 复用 `review-git-and-cleanup-gates.md` 的 Git Update 和 PR Command：基于最新 `origin/main` rebase，清理并验证完整 diff，push work branch，创建或更新目标为 `main` 的 PR，等待 required checks，通过后执行 squash merge。最终核对 `main` 只新增一个 squash commit且无 merge commit；无法创建 PR、checks 失败、冲突未解决或 squash merge 失败时标记 `save_failed`。
7. 完成远端动作后用 fresh remote ref/HEAD 对账提交 SHA。认证失败、无 remote、分支歧义或本地仍有应保存改动时，记录 Bug 并把该仓库标为 `save_failed`；不使用 merge commit、stash、reset 或 force 绕过。

doc-hub 的 PR 应包含本轮全部项目执行包 checkpoint；agent-hub 的 PR 应包含 Hub 自身待保存改动和本次命令资产。若某仓库没有可提交改动，业务仓库仍需核对当前 HEAD 已存在于确认目标分支；doc-hub 与 agent-hub 则核对本轮没有未合入 `main` 的 checkpoint diff 后记录为 verified no-op，不制造空 PR。

## Phase 5: Completion Gate

只有以下条件同时成立，`/hub save` 才算完成：

- 所有可发现 workload Agent 已处于可验证的终止/中止/暂停状态，发现覆盖边界已披露。
- 确认单内所有 workspace localhost 服务已经停止并通过 PID/端口复核，或失败已使本次 save 保持 incomplete；确认单外服务未被改动。
- 业务仓库实际 local/remote branch 与确认单一致，所有主干风险已显式包含；doc-hub 与 agent-hub 已分别通过 PR squash merge 到 `main`，且每个主干只新增一个提交。
- 每个受影响项目的进度已进入 STDD，所有遗留与不确定项已进入 Bug Pool；没有把 open Bug 转移、隐藏或归档。
- 所有业务项目、doc-hub、agent-hub 都已按固定顺序完成 commit/push 或 verified no-op。
- 每个仓库本地 checkpoint SHA 与目标远端分支一致；没有应保存但仍未跟踪、未提交或未推送的安全文件。
- 所有业务仓库、远端分支、主干动作和服务关闭均已由本轮同一张确认单授权，没有追加未确认目标。

任一条件不成立时，结果必须写 `workspace save incomplete`，列出已停止活动、已落盘文档、已保存仓库和精确 blocker；原任务可以带 open Bug 被安全暂停，但不能把 `/hub save` 本身报成完成。最后输出可恢复入口：项目、SPEC ID/路径、分支、远端 SHA 和 Bug Pool。

## Checklist

- 是否排除了协调 Agent，并禁止新分派。
- 是否用宿主事实核验停止状态，而不是依赖话术。
- 是否用一张确认单列全业务仓库、local/remote branch、主干风险和待关闭服务，并只确认一次。
- 是否只关闭有 workspace 项目归属证据且已确认的 localhost 服务。
- 是否先完成全部 SPEC/STDD/Bug 本地更新，再开始 commit/push。
- 是否没有改写 SPEC 需求事实或错误归档执行包。
- 是否将未知、未验证、敏感文件和 push 失败显式写入 Bug Pool。
- 是否按业务项目 -> doc-hub -> agent-hub 执行且 agent-hub 最后。
- 是否确认 doc-hub、agent-hub 复用现有 PR 流程完成 squash merge，且各自主干只新增一个提交、没有 merge commit。
- 是否在统一确认单中显式列出了全部主干动作并取得确认。
- 是否逐仓核对 remote SHA、dirty 状态和恢复入口。
