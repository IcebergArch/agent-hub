# Review, Git And Cleanup Gates

用途：作为 `task-execution-lifecycle` 的按需参考；当任务进入 cleanup、`git diff`、`git update`、提交/PR 前审查或完成状态校准时读取。默认入口只保留触发和硬边界，本文件承接细节。

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

## Git Diff Boundary Review

用户说 `git diff` 时，默认只读审查：

1. 不修改文件、不暂存、不提交。
2. 先推断预期范围，再对照 staged、unstaged、untracked、base/head 或远端 PR 差异。
3. 回复先给边界结论：预期范围、实际范围、是否越界、是否有预期外影响面、哪些仍待确认。
4. 共享面变更必须搜索真实消费者：API、schema、公共组件、配置、runtime、tool、gateway、生成 client 和 UI 入口。
5. 生成产物要追溯事实源和生成命令；生成 diff 过大时，按本轮目标建立 allowlist。
6. 工作区干净不等于没有分支差异；必要时补充 merge-base、`origin/main...HEAD` 或 PR diff。

提交、暂存、amend、PR、合并或“只提交本次改动”前，完整 diff 审查是必需步骤。无关用户改动保持原样，不借机回滚。

## Git Update

`git update` 表示同步目标分支到当前分支，不是 Git 子命令。

1. 默认方向：`当前分支 <- origin/main` 或 `当前分支 <- main`，除非用户指定其它目标。
2. merge 前确认当前仓库、当前分支、目标分支、ahead/behind、dirty worktree。
3. dirty worktree 下先检查本地改动文件与目标分支新增改动文件是否重叠；无重叠且无生成产物/API client 风险时，可以 merge 并保留本地改动。
4. 有重叠、生成产物风险、merge 被 dirty worktree 阻塞，或需要 stash/index 操作时，停止并请求用户决策。
5. 需要预判风险时优先只读检查，例如 merge-base、merge-tree、diff name-status 和冲突信号搜索；不要把大型输出原样倾倒给用户。
6. fetch 后目标分支已包含时，可用 no-op merge 验证，但不为流程制造 stash、重排 index 或长报告。

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
