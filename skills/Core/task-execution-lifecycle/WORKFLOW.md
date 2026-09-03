---
name: task-execution-lifecycle
description: 当用户发布实现、重构、UI、架构、协议、数据、工具链或跨模块任务时使用；只负责下一步流程流转。
---

# Purpose

定义任务从接收到完成的阶段顺序，回答“下一步应该做什么”。

# When to Use

- 非机械实现、重构、UI、架构、协议、数据或工具链任务。
- 用户说开始做、继续、do it、`coding`、`fix`、`/hub refactor`、`/hub spec-exec`、`/hub spec-smoke`、`/hub pr`、`/hub save`、按计划执行。
- 任务进入验证、收口、提交、PR 或交付前。

# When NOT to Use

- 只需直接回答的问题、单点 typo、纯文本小改。
- 需要具体领域判断但不涉及执行闭环时，只加载领域 Skill。

# Inputs

- 用户目标、硬边界、当前事实、owner、验收信号、本地改动状态、需要加载的领域 Skill。

# Decision Principles

- SPEC/STDD/计划只是引导，执行前必须用当前页面、API、代码、配置和运行事实校验。
- 正式 SPEC 按 `spec-exec -> spec-smoke -> pr` 分层：执行阶段完成资源分配、实施、隔离回测与修复闭环；smoke 阶段把变更提档到主工作空间，只在主工作空间完成最终链路回测以及适用的真实 UI/交互审核验收，通过后异步更新本地 Docker 部署；PR 阶段只负责更新基线、保存代码、收敛单提交、push work branch 和创建或更新 PR。smoke 发现内容问题时必须保持 `execing`、在当前承载完整 SPEC diff 的执行面修复并重跑受影响 gate；重放完成后该执行面就是主工作空间，不撤销重放或把实现物理迁回隔离空间。
- 执行面不仅是代码目录，也包括已分配且只服务于该工作空间验证链路的本地容器、开发服务和端口。`spec-smoke` 回测前停止与受测应用对应的本地 Docker 服务，防止旧实例接受响应或占用端口；依赖和无关容器保持运行。回测循环结束前不重复构建或部署 Docker，全部通过后再只异步派发一次当前主工作空间版本的本地部署。
- `/hub save` 采用快速 checkpoint 协议：命令本身授权保护现场、登记已有执行包的进度与待办、停止归属明确的本地服务并整理推送计划；Git 写操作等完整确认单通过后直接执行。
- `/hub pr` 直接进入 `references/review-git-and-cleanup-gates.md` 的 PR Command，只执行 Git/PR 收尾，不套用本文件通用 Workflow 的实现与验证步骤。
- 只加载能改变本轮决策的 Skill 或 reference。
- `fix` 先用复现、交叉验证和正/逆向推理确认根因，方案明确后才执行最小修复；按 `references/root-cause-fix-patterns.md` 闭环。
- Run Case 与 Build Capability 分开：配置和页面能力能完成时，不擅自改服务层或公共契约。
- 多步实现默认拆成可独立演示、验证且每片结束时保持验证通过的纵向切片；只有无法逐片保持可用的机械性宽重构，才按 expand -> migrate -> contract 顺序迁移并显式登记依赖。
- 长任务、高模糊设计或历史信息较重时，按 `references/execution-control-patterns.md` 分离当前事实、稳定约束、已废弃历史和冲突信息，并冻结 demo/checkpoint、停止信号与恢复点。
- 完成声明必须有 fresh evidence；错误暴露不能写成业务成功。

# SPEC Command Modes

- `/hub spec-exec [<SPEC>]`：只消费最终定稿的 plan SPEC，或恢复已有执行包的 execing SPEC；完成执行闭环后保持 `execing`，等待提档。
- `/hub spec-smoke [<SPEC>]`：只消费已通过隔离回测的 execing SPEC；把当前 SPEC 变更重放至项目主工作空间，停止对应的本地 Docker 应用服务，只在主工作空间执行最终链路回测；涉及 UI/交互时追加真实页面审核验收与可执行修改意见，全部通过后进入 `/hub pr` 候选并异步更新本地 Docker 部署。
- smoke 及适用的 UI/交互验收必须记录真实操作路径、环境/版本、断言、结果和证据。只跑新增测试、静态检查、mock/dry-run、空数据、跳过步骤或仅暴露错误，都不能形成通过结论。

# SPEC Exec Mode

1. Prepare：选择 plan SPEC 或恢复 execing 执行包；创建或恢复 STDD、Bug Pool、Optimization Pool，冻结范围和验收信号。
2. Allocate：按项目实际需要分配资源、工作空间、work branch、服务、端口、数据或素材，并在 STDD 记录 owner、用途、占用与回收边界。默认使用隔离空间与隔离 work branch；`doc-hub` 直接使用主工作目录和短生命周期 work branch，不创建 linked worktree 或其它隔离空间。无对应运行资源的项目将该项标为 N/A，不为形式制造资源。
3. Implement：以正式 SPEC 为原本，用当前代码、配置、页面、API 和运行事实复核目标与 owner，只实施冻结范围内的最小 coherent diff。
4. Isolated Run：需要运行服务时，确认原服务及其端口，再用不同且经占用检查可用的端口部署本执行包的隔离服务；命令只授权操作该隔离服务，不得影响或抢占原服务及其端口。
5. Regression And Fix：在隔离运行面执行本次增量场景、受影响路径、适用存量回归及必要的失败与恢复路径。发现缺陷时完成复现、根因定位、最小修复并重跑所有受影响回测，持续闭环到无已知 blocker；不得把 mock、dry-run、空数据、跳步或仅错误暴露写为通过。
6. Checkpoint：在 STDD 记录实际 diff、资源清单、环境、端口、操作路径、断言、结果、证据、Bug 状态和服务收尾状态；完成后保持 `execing`，等待 `/hub spec-smoke`。该模式不额外授权 commit、push、PR 或 merge。

# SPEC Smoke Promotion Mode

1. Gate：确认目标为 execing SPEC，隔离执行闭环无已知 blocker，STDD、Bug Pool、源空间与源分支可定位；不满足时不开始提档，继续在现有隔离执行面补齐执行闭环。
2. Freeze Replay：冻结隔离空间/分支的源变更和项目主工作空间的目标现场，保护主工作空间已有无关改动。主工作空间表示项目的主工作目录，不等于固定 `main` 或其它 target 分支；`doc-hub` 已在主工作目录执行时，重放可以是经 diff 核验的 no-op。
3. Replay：只把当前 SPEC 的完整变更重放到主工作空间；核对源变更、目标 diff 和影响范围一致，不夹带其它任务。若冲突、内容漂移或实现缺口阻止完整重放，登记 Bug、停止本次提档并保留两端现场，在现有隔离执行面解决后重新重放。
4. Execution Surface Handoff：完整重放及 diff 一致性核验完成后，主工作空间成为该执行包唯一的回测执行面；隔离空间只保留为来源证据和恢复入口，后续实现修复不再物理迁回隔离空间。核对原资源清单，精确识别与受测应用对应、已记录或直接验证为当前主工作空间专属且具备可恢复更新方式的本地 Docker 服务，并在 STDD 记录容器 owner、原运行状态、版本或镜像、停止命令、恢复入口和部署入口。共享、生产、高风险外部、归属不明或不可恢复的服务不得纳入；无法排除其响应会污染回测时停在最小授权或恢复决策点。
5. Quiesce Docker：在回测前一次性成组停止上一步识别的应用服务，使旧容器不再接受响应或占用应用端口；不得停止 Docker daemon、数据库/缓存等必要依赖或无关容器。目标已经停止时将操作记为 no-op。停止后用容器状态、端口归属和被测进程来源核验请求只能落到主工作空间启动的当前代码。回测修复循环内保持这些旧应用容器停止，不在每轮测试之间重复 stop、build 或 deploy。
6. Main Workspace Regression：需要运行实例时直接从项目主工作空间启动当前代码或使用其构建产物；不得由隔离空间、已停止的 Docker 应用实例、线上或其它远程环境承接响应。按冻结 SPEC、实际 diff/影响范围、STDD 和项目 `ACCEPTANCE.md` 验证改动部分、受影响的未改动部分及必要的失败与恢复路径；没有共享运行资源冲突的独立检查可以并行，先聚焦受影响 gate，再执行一次最终完整 smoke，避免重复全量回测。
7. UI And Interaction Acceptance：实际 diff 或影响范围涉及 UI/交互时，加载 `skills/Domain/agent-team-router/references/product-ui-quality-gates.md`，由主 Agent 在主工作空间当前实例的真实页面完成端到端操作与视觉验收，并由未承担主要实现的 reviewer 独立复评。评审意见必须写明对象、阻塞原因、期望行为和可观察关闭证据，区分 blocking / non-blocking；blocking 项登记 Bug，在主工作空间修复后重跑受影响验证、页面复验、独立复评与最终 smoke，non-blocking 项进入 Optimization Pool。纯后端、纯文档或无 UI/交互影响的变更将本步记为 N/A，不为形式制造 UI 审核。
8. Result And Async Local Deploy：回测、UI/交互验收失败，证据不足或存量回归未执行时登记 Bug、保持 `execing`，直接在主工作空间完成最小修复并重跑受影响验证及最终 smoke；tracked 内容实质变化会使旧 smoke evidence 失效。结束本轮仍未通过或中断时，按记录入口异步恢复原 Docker 服务状态并回报恢复结果，不让对应本地服务长期停机。全部通过时记录重放与 fresh smoke evidence，进入 `/hub pr` 候选；先冻结本次已验证的不可变构建输入或产物及其来源/diff 指纹、精确 Docker 目标、恢复入口和健康断言，再只派发一次后台本地重建/更新任务，取得可追踪回执后立即返回，不轮询等待。后台任务不得重新读取随后可能变化的主工作空间；使用现有可恢复部署方式，完成后核对运行版本、健康状态和可观察行为并独立回报。失败时不得声称已部署，应自动回滚原状态或登记 Bug。该任务只更新本机 Docker，不授权 `docker push`、镜像仓库、远程或线上部署。不得撤销重放、把实现迁回隔离空间，或把连续修复链描述为“退回”。正式 SPEC 的基线更新、代码保存、单提交整理、push 与 PR 创建/更新统一由 `references/review-git-and-cleanup-gates.md` 的 PR Command 承接。该模式不额外授权 commit、push、PR 或 merge。

# Save Command Mode

- `/hub save` 在安全边界冻结新工作，用宿主任务事实和 Git workspace/worktree 状态快速保护现场；只对已有、归属明确的 `execing` 执行包登记真实进度、待办、验证缺口和恢复入口，只优雅停止归属明确的本地服务。
- 准备阶段按隔离空间整理代码记录和推送计划，但不执行 Git 写操作；每个有未合入内容的隔离空间都必须映射到对应远端 work branch 并创建或更新 PR。`doc-hub` 只在主工作目录使用短生命周期 work branch，不创建 linked worktree；历史 linked worktree 必须先建立远端恢复入口，再在干净门禁下收拢。确认单与结果统一使用仓库表格；其它仓库默认止于 PR，随后完成 `doc-hub` 的 `MR -> main`，最后把 Agent Hub canonical 主工作目录的现有改动直接提交并推送 `main`。Agent Hub 不新建 PR/work branch/linked worktree；历史隔离现场只在有恢复入口且为空时收拢。清理条件不满足时保留现场并登记 blocker，不强制删除。确认后直接执行，用户修正则更新并重发完整版本。
- 详细盘点、确认单、执行顺序和恢复门禁见 `references/workspace-save.md`；提交、push 与 PR 质量门禁见 `references/review-git-and-cleanup-gates.md`。

# Workflow

1. Receive：确认最新用户目标、非目标、硬边界和是否已有本地改动。
2. Clarify：只有高风险歧义才问；否则用最小合理假设推进。
3. Understand Context：读取直接相关入口，冻结 owner、事实源、影响范围和验收信号。
4. Execute：沿用现有架构，做最小 coherent diff；新增抽象必须有明确收益。
5. Validate：用最窄有效证据验证；高风险任务补真实 workflow smoke 或负向检查。
6. Knowledge Evolution：若暴露跨场景稳定缺口，交给 `knowledge-evolution` 判断是否沉淀。
7. Finish：报告完成内容、验证证据、范围自检和残余风险。

# Checklist

- 是否回答了当前最新请求，而不是旧上下文。
- 是否区分配置操作、运行 case、能力建设和公共契约变更。
- 是否有新鲜证据支撑“完成 / 可用 / 已打通”。
- 是否保持 diff 聚焦，未扰乱 owner 和领域边界。

# Escalation

- Skill 选择不清：`skills/Core/skill-router/WORKFLOW.md`
- 公共接口或模型工具：`skills/Engineering/interface-contract-audit/WORKFLOW.md` / `skills/Engineering/agent-tool-design/WORKFLOW.md`
- 项目上下文缺失：`skills/Context/project-context-rebuild/WORKFLOW.md`
- 知识沉淀：`skills/Knowledge/knowledge-evolution/WORKFLOW.md`

# References

- `references/completion-evidence-patterns.md`
- `references/case-execution-recovery.md`
- `references/execution-control-patterns.md`
- `references/parallel-agent-coordination.md`
- `references/root-cause-fix-patterns.md`
- `references/review-git-and-cleanup-gates.md`
- `references/workspace-save.md`
