# SPEC 执行与主工作空间提档

用途：仅供 `/hub spec-exec` 与 `/hub spec-smoke` 按需加载；承载执行面、回测和本地部署边界，不负责 SPEC 内容评审或 Git/PR 收尾。

## 命令边界

`spec-exec -> spec-smoke -> pr` 是连续但权限隔离的三段：

- `spec-exec`：在隔离工作空间、隔离分支和隔离端口实施变更，完成局部功能验证与修复；结束时仍为 `execing`。
- `spec-smoke`：把同一隔离分支及其完整变更交接到项目主工作空间，在项目主端口完成相对完整的功能验收与修复；通过后移除隔离工作空间，并通过项目固定入口异步更新本地 Docker。
- `pr`：消费 fresh 前序验证证据，并在 final `git update` 后完成强制代码/架构审查与 Git/PR 收尾；详细规则由 review/git reference 拥有。

任何一段都不自动授权相邻阶段。mock、dry-run、空数据、跳步、静态检查或仅暴露错误不能形成通过结论。

## `/hub spec-exec`

1. **Gate**：只选择最终定稿的 plan SPEC，或恢复已有执行包的 execing SPEC；IDEA、draft、init、update 和待迁移旧态不可执行。
2. **Prepare**：首次实现性修改前创建或恢复 STDD、Bug Pool、Optimization Pool，冻结范围、验收信号及代码/文档边界。
3. **Allocate**：业务代码默认同时分配隔离工作空间、该 SPEC 唯一隔离分支和不会占用项目主端口的隔离端口；在 STDD 记录精确路径、branch/ref、服务 owner、端口、用途、占用和回收入口。只操作本执行包资源，不影响主工作空间或主端口。某类资源经项目规则核验确实不适用时才记 N/A；文档工作区可按自身规则不创建 linked worktree。
4. **Implement**：以 SPEC 为原本，用当前代码、配置、页面、API 和运行事实复核 owner，只做最小 coherent diff。
5. **Local Verify And Fix**：只在隔离工作空间、隔离分支和隔离端口做本 SPEC 新增能力、直接受影响路径、关键失败/恢复路径的局部功能验证。缺陷留在原执行包，完成根因定位、最小修复和受影响用例重跑，直到无已知 blocker；此阶段不冒充项目级完整验收，不占用主端口，也不替换主工作空间 Docker。
6. **Checkpoint**：STDD 记录实际 diff、资源、环境、操作路径、断言、结果、证据、问题状态和恢复入口；保持 `execing` 等待 `/hub spec-smoke`。本命令不授权 commit、push、PR 或 merge。

## `/hub spec-smoke`

1. **Promotion Gate**：确认隔离局部验证闭环无已知 blocker，STDD、问题池、隔离工作空间、隔离分支与隔离端口均可定位；不满足时继续在隔离执行面补齐，不开始提档。
2. **Freeze And Handoff Branch**：冻结隔离工作空间的 staged、unstaged、untracked、HEAD 与分支占用，保护主工作空间无关改动，把同一隔离分支及本 SPEC 完整变更交接到项目主工作空间。Git 不允许同一分支同时 checkout 到两个 worktree 时，先建立可验证恢复指纹并安全释放源 worktree 的分支占用，再由主工作空间 checkout 该分支；不得复制出第二条 smoke 分支、改用 target/baseline 分支或丢失未提交内容。冲突、漂移、主工作空间无关脏改或无法证明完整等价时保留现场并登记 blocker。
3. **Switch Runtime Surface**：分支交接完成后，主工作空间成为唯一验收与后续修复执行面；隔离工作空间只作为只读恢复现场保留到 smoke 终态，隔离端口停止使用。后续修复直接写在主工作空间当前隔离分支，不迁回源空间。
4. **Quiesce Owned Services**：只成组停止已记录或直接核验为受测应用专属、可恢复的本地 Docker 应用服务；不得停止 Docker daemon、依赖、共享、生产、外部、无关或归属不明服务。记录原状态、版本、停止与恢复入口，释放并使用项目登记的主端口，核验请求只能落到主工作空间当前隔离分支的代码。
5. **Main Acceptance And Fix**：按冻结 SPEC、实际 diff、STDD 与项目 `ACCEPTANCE.md`，覆盖本次功能、受影响存量、适用主干和承诺旁路，形成相对完整且 fresh 的功能验收；不得由旧 Docker、隔离端口、隔离工作空间或线上环境承接响应。失败项写 Bug Pool，在主工作空间当前隔离分支修复并重跑受影响范围；修复循环内不重复 stop、build 或部署。
6. **UI Conditional**：仅当实际 diff 或影响范围涉及 UI/交互时，由主 Agent 在真实页面完成端到端和视觉验收，并由未承担主要实现者独立复评。blocking 项写 Bug Pool，在主工作空间修复、复验、复评并重跑 fresh smoke；非阻塞项写 Optimization Pool。
7. **Failure**：失败、证据缺失或存量回归未执行时保持 `execing`，使旧 smoke evidence 失效并在主工作空间修复。若本轮中断或仍未通过，按记录入口异步恢复原 Docker 状态并独立回报。
8. **Pass And Freeze**：记录分支交接与 fresh smoke evidence，从主工作空间当前隔离分支冻结已验证的不可变构建输入或产物、来源/diff 指纹、Docker 目标、固定部署入口、恢复入口和健康断言。
9. **Remove Isolation Workspace**：若执行包按项目规则未分配隔离工作空间，核对资源记录后将本步记为 N/A。否则，成功声明前解析并复核该 SPEC 登记的精确隔离工作空间；盘点 staged、unstaged、untracked、进程、服务、隔离端口和临时资源，证明所有应保留内容已完整进入主工作空间当前隔离分支或既有恢复入口，且没有无关或独有内容。记录清理证据后停止其专属运行资源并移除隔离工作空间；保留后续 `/hub pr` 所需的隔离分支/ref 与来源指纹，不删除分支。无法证明等价、目标可能是主工作空间、清理失败或仍有独有内容时保留现场并报告 blocker，不得宣称 smoke 成功。
10. **Async Local Deploy**：隔离工作空间清理/N/A 门禁通过后，只通过文档工作区登记的项目固定脚本或等价固定命令，从第 8 步冻结的输入派发一次本地 Docker 更新，取得可追踪回执即返回。固定入口必须同时可供用户手工执行，具备目标校验、候选健康检查、可恢复替换和状态回报；不存在、不适用或拓扑漂移时保持未部署并报告 blocker，不得临时生成一次性脚本绕过。完成后独立回报健康核验，失败则回滚或登记 Bug。禁止 `docker push`、镜像仓库、远程或线上部署。

## 完成门禁

- STDD 能追溯隔离工作空间、同一隔离分支、隔离/主端口、分支占用交接、服务 owner、真实操作路径、断言与结果。
- 变更内容、构建输入或运行面实质变化后，旧 smoke evidence 必须失效并 fresh 重跑。
- UI/交互影响未做真实页面验收，或 blocking 项未关闭时，不得进入 PR 候选。
- 成功终态必须记录已分配隔离工作空间的清理证据且该工作空间已不存在；未分配时记录 N/A 依据。隔离分支/ref 仍可供 `/hub pr` 定位；清理门禁未通过时保持 `execing`。
- smoke 通过只产生 PR 候选和异步本地部署回执，不代表已提交、已推送、PR 已完成或远程已部署。
