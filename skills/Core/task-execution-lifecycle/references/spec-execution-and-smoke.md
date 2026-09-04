# SPEC 执行与主工作空间提档

用途：仅供 `/hub spec-exec` 与 `/hub spec-smoke` 按需加载；承载执行面、回测和本地部署边界，不负责 SPEC 内容评审或 Git/PR 收尾。

## 命令边界

`spec-exec -> spec-smoke -> pr` 是连续但权限隔离的三段：

- `spec-exec`：分配执行资源，实施变更，在隔离运行面完成回测与修复闭环；结束时仍为 `execing`。
- `spec-smoke`：把完整 SPEC diff 提档到项目主工作空间，只在该执行面形成最终链路及适用 UI/交互证据；通过后移除该 SPEC 已分配的隔离空间，并异步更新本地 Docker。
- `pr`：只消费 fresh 前序证据并完成 Git/PR 收尾；详细规则由 review/git reference 拥有。

任何一段都不自动授权相邻阶段。mock、dry-run、空数据、跳步、静态检查或仅暴露错误不能形成通过结论。

## `/hub spec-exec`

1. **Gate**：只选择最终定稿的 plan SPEC，或恢复已有执行包的 execing SPEC；IDEA、draft、init、update 和待迁移旧态不可执行。
2. **Prepare**：首次实现性修改前创建或恢复 STDD、Bug Pool、Optimization Pool，冻结范围、验收信号及代码/文档边界。
3. **Allocate**：只分配项目实际需要的工作空间、work branch、服务、端口、数据或素材；在 STDD 记录 owner、用途、占用和回收入口。默认隔离执行；文档工作区按其当前规则可直接使用主工作目录，不为形式创建 linked worktree。需要服务时使用已检查的独立端口，只操作本执行包资源，不影响原服务；无对应资源时记 N/A。
4. **Implement**：以 SPEC 为原本，用当前代码、配置、页面、API 和运行事实复核 owner，只做最小 coherent diff。
5. **Regress And Fix**：在隔离运行面覆盖新增、受影响存量、失败与恢复路径；缺陷留在原执行包，完成根因定位、最小修复和受影响回测重跑，直到无已知 blocker。
6. **Checkpoint**：STDD 记录实际 diff、资源、环境、操作路径、断言、结果、证据、问题状态和恢复入口；保持 `execing` 等待 `/hub spec-smoke`。本命令不授权 commit、push、PR 或 merge。

## `/hub spec-smoke`

1. **Promotion Gate**：确认隔离回测闭环无已知 blocker，STDD、问题池、源空间和源分支均可定位；不满足时继续在隔离执行面补齐，不开始提档。
2. **Freeze And Replay**：冻结源变更与项目主工作空间现场，保护无关改动，只重放当前 SPEC 的完整 diff 并核对影响范围。主工作空间不等于固定 target 分支；已在主工作目录执行时可形成经 diff 核验的 no-op。冲突、漂移或缺口阻止完整重放时登记 Bug、保留两端现场，在源执行面解决后重新重放。
3. **Handoff**：完整重放后，主工作空间成为唯一回测与后续修复执行面；隔离空间只临时保留到本次 smoke 终态，不撤销重放或迁回实现。
4. **Quiesce Owned Services**：只成组停止已记录或直接核验为受测应用专属、可恢复的本地 Docker 应用服务；不得停止 Docker daemon、依赖、共享、生产、外部、无关或归属不明服务。记录原状态、版本、停止与恢复入口，并核验请求只能落到主工作空间当前代码。
5. **Main Regression**：按冻结 SPEC、实际 diff、STDD 和项目验收规则，先跑受影响 gate，再执行一次最终完整 smoke；不得由旧 Docker、隔离空间或线上环境承接响应。修复循环内不重复 stop、build 或 deploy。
6. **UI Conditional**：仅当实际 diff 或影响范围涉及 UI/交互时，由主 Agent 在真实页面完成端到端和视觉验收，并由未承担主要实现者独立复评。blocking 项写 Bug Pool，在主工作空间修复、复验、复评并重跑 fresh smoke；非阻塞项写 Optimization Pool。
7. **Failure**：失败、证据缺失或存量回归未执行时保持 `execing`，使旧 smoke evidence 失效并在主工作空间修复。若本轮中断或仍未通过，按记录入口异步恢复原 Docker 状态并独立回报。
8. **Pass And Freeze**：记录重放与 fresh smoke evidence，冻结已验证的不可变构建输入或产物、来源/diff 指纹、Docker 目标、恢复入口和健康断言。
9. **Remove Isolation**：若执行包按项目规则未分配隔离空间，核对资源记录后将本步记为 N/A。否则，成功声明前解析并复核该 SPEC 登记的精确隔离空间；盘点 staged、unstaged、untracked、进程、服务、端口和临时资源，证明所有应保留内容已进入主工作空间或既有恢复入口，且没有无关或独有内容。记录清理证据后停止其专属运行资源并移除隔离空间；保留后续 `/hub pr` 所需的已有 work branch/ref 与来源指纹，不删除分支。无法证明等价、目标可能是主工作空间、清理失败或仍有独有内容时保留现场并报告 blocker，不得宣称 smoke 成功。
10. **Async Local Deploy**：隔离空间清理/N/A 门禁通过后只派发一次本地 Docker 更新，取得可追踪回执即返回。后台只消费第 8 步冻结的输入，不得重新读取随后变化的工作树；完成后独立回报健康核验，失败则回滚或登记 Bug。禁止 `docker push`、镜像仓库、远程或线上部署。

## 完成门禁

- STDD 能追溯源/目标 diff、执行面交接、服务 owner、真实操作路径、断言与结果。
- 变更内容、构建输入或运行面实质变化后，旧 smoke evidence 必须失效并 fresh 重跑。
- UI/交互影响未做真实页面验收，或 blocking 项未关闭时，不得进入 PR 候选。
- 成功终态必须记录已分配隔离空间的清理证据且该空间已不存在；未分配时记录 N/A 依据。已有 work branch/ref 仍可供 `/hub pr` 定位；清理门禁未通过时保持 `execing`。
- smoke 通过只产生 PR 候选和异步本地部署回执，不代表已提交、已推送、PR 已完成或远程已部署。
