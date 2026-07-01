# Execution Control Patterns

日期：2026-06-11
来源：吸收外部 plan execution、worktree、branch finish 和 delegated development 机制后整理。
用途：作为 `task-execution-lifecycle` 的按需参考；用于长任务、计划执行、工作区隔离、分支收尾和恢复执行。

## Core Rule

执行控制保护四件事：计划不跑偏、用户本地改动不被污染、完成结论有 fresh evidence、收尾动作由用户明确选择。

## Intake Checklist

开始前确认：

- 当前目标：实现、修复、重构、审查、同步、归档？
- 仓库边界：路径、模块、禁止修改范围、相近目录。
- Git 状态：branch、upstream、staged、unstaged、untracked、是否与任务重叠。
- 隔离状态：workspace/worktree、linked worktree、detached HEAD。
- 计划状态：是否有 plan/spec/checklist，是否含占位或验证缺口。
- 验收信号：最小业务成功、负向行为、回归风险和必要验证。

不清楚时先只读补证据；只有会导致误改时才问用户。

## Plan Execution Gate

用户给计划或要求按计划执行时：

1. 读完整计划和相关代码。
2. 标出目标、文件、步骤、验证、风险、非目标。
3. 检查计划是否能被新执行者独立完成：路径、命令、期望结果、回滚/验证点。
4. 搜索隐含上下文和占位词：`如上`、`按之前方案`、`TBD`、`TODO`、`适当处理`、`补测试`。
5. 未经用户确认的 commit、push、PR、merge、discard、stash 只能作为可选收尾。
6. 高风险、多模块、外部来源或交给其它 agent 的计划，按 plan review reference 审查。
7. 计划可靠则连续推进；不可靠则先修计划或问最小阻塞问题。

## Progress / Resume Ledger

| Field | Meaning |
| --- | --- |
| Task | 计划编号或短标题 |
| Scope | 预期文件、模块或接口 |
| Status | pending / in_progress / completed / blocked / skipped |
| Evidence | 完成文件、命令、输出、diff、人工信号 |
| Blocker / risk | 阻塞、失败、未覆盖风险、需确认问题 |

规则：

- 一次只推进一个 `in_progress`。
- 完成必须有验证、证据或用户接受风险。
- 中断后先用 status、diff、账本和最近验证输出对账。
- 用户修改计划、代码状态与计划不一致或验证反复失败时，回到 Plan Execution Gate。

## Execution Mode Matrix

| 场景 | 默认模式 |
| --- | --- |
| 小修、单文件、文档整理 | inline execution |
| 多文件强顺序依赖 | inline with checkpoints |
| 多个独立问题域 | parallel tools/subagents when available |
| 完整计划 | plan execution |
| 计划含独立任务且支持 subagent | delegated execution |
| owner、接口、数据源不清 | pause for boundary |

并行不是速度按钮；文件、状态、验证和决策互不阻塞才并行。

## Branch / Workspace Gates

### Default Branch

非平凡实现前判断当前分支是否是 `main`、`master`、默认分支、受保护分支或用户指定集成分支。

- 多文件功能、bugfix、接口/DB/runtime/UI 改动：默认需要隔离 branch/workspace。
- Hub 规则、Skill、reference、报告维护且用户指定当前仓库：可原地维护。
- 小 typo 或单点文档：可原地维护，但仍做 diff 边界检查。

### Workspace Isolation

1. 先检测是否已在宿主隔离 workspace 或 linked worktree。
2. submodule 不误判为 linked worktree。
3. 已隔离则沿用，不创建嵌套 worktree。
4. 未隔离时，只有任务风险和收益明确才考虑手动 worktree。
5. 手动 worktree 必须确认目录被 ignore，并记录 baseline。

## Clean Baseline

长任务、多文件功能、手动 worktree 或隔离 workspace 开始前，证明起点状态：

- 路径、branch、worktree/detached、dirty 状态。
- 最小 baseline：quick test、typecheck、build、lint、API/page smoke 或相关最窄命令。
- baseline 失败：记录失败是否与任务相关，先问修 baseline、带风险继续，还是缩小范围。
- 无法运行：说明权限、依赖、网络、环境或成本缺口和替代证据。

Baseline 只证明起点，不证明最终完成。

## Verification Ledger

| 证据 | 记录 |
| --- | --- |
| Static | typecheck、lint、format、diff check、引用搜索 |
| Test | 单测/集成、失败/通过、red/green proof |
| Runtime/UI | 页面、请求、业务结果、错误态 |
| Diff boundary | staged、unstaged、untracked、生成物、无关改动 |
| Review | spec compliance、quality、反馈、残余风险 |

声明完成、修好、通过、可用、打通前，读取 completion evidence reference 校准状态词。

## Finish Gates

### Branch Finish

实现后不自动 merge、push、PR 或 discard。先确认：

1. 必要验证已跑，失败项已说明。
2. diff 边界清楚。
3. 当前环境：普通 checkout、linked worktree、detached HEAD、宿主 workspace。
4. base branch / PR target 已确认。
5. staged/untracked 文件看过。
6. 用户明确要求哪种收尾动作。

### Failed Verification Stop

验证失败、未跑或证据不足时，只能提供：

- 报告失败命令、关键错误和风险。
- 继续修复失败项。
- 继续运行缺失验证。
- 保留工作区给用户审查。

不能提供 commit、PR、merge、discard 快捷项。

### Structured Finish Options

验证和 diff 边界清楚后才给有限选项，并按环境裁剪：

```markdown
当前状态：
- Branch/workspace:
- Verification:
- Diff boundary:

可选下一步：
1. 保留当前工作区给你审查
2. 暂存/提交本次必要改动
3. 推送并创建 PR
4. 合并回 <base>
5. 丢弃本轮工作（需要二次确认）
```

detached HEAD、宿主托管 workspace、验证失败、diff 不清时删掉不适用项。

## Worktree Cleanup

清理前必须证明 worktree 由当前流程拥有：

1. 合并成功且合并后验证通过，或用户已二次确认丢弃。
2. 记录 path、branch、未合入 commits、staged/unstaged/untracked。
3. 只清理由当前 agent 创建、项目约定目录且不会污染仓库，或用户授权的 worktree。
4. 不清理 Codex/Cursor/CI/harness 或来源不明 workspace。
5. 删除前切回主 repo/root。

缺任一证据时保留 workspace。

## Stop Rules

- 计划与当前代码不一致。
- 本地未提交改动与任务或目标分支重叠。
- 验证连续失败且信号不变窄。
- 用户中断、提速或覆盖旧请求。
- scope、owner、数据源或权限语义不清。
- 需要破坏性命令、安装依赖、联网或写出工作区外内容。

停止点要留下当前状态、已改文件、风险和最小下一步。
