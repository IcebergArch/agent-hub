---
name: task-execution-lifecycle
description: 当用户发布非机械实现、重构、UI、架构、协议、数据、工具链或跨模块任务时使用；按风险完成定位、实现、验证和交付。
---

# Task Execution Lifecycle

任务执行总控。它负责决定做多大、读多少、何时验证、何时交付；不替代具体领域 Skill。

## Trigger

- 用户要求实现、修复、重构、UI 调整、架构/协议/数据/工具链改造。
- 用户说 `do it`、开始做、继续、按计划执行。
- 任务进入收尾、交付、提交、PR 或合并前。

小 typo、单点文档和机械改动走轻量路径，不加载长 reference。

## Core SOP

1. **Orient**：读直接相关上下文，冻结目标、非目标、owner、事实源、验收信号和本地改动边界。
2. **Scale**：按风险选择 light / standard / high-risk；只加载能改变本轮决策的 Skill 或 reference。
3. **Change**：沿用现有架构，做最小 coherent diff；新增抽象必须降低复杂度或匹配既有模式。
4. **Verify**：用最窄有效证据证明目标；高风险任务补 review、负向测试或真实 workflow smoke。
5. **Close**：按证据报告完成内容、验证、diff 边界和残余风险；git/PR/merge 只在用户要求时进入。

## Composition Rules

- 特殊场景优先视为 Core SOP 的组合，不新增条件树。
- Case 上下文被纠正时，重新 **Orient**，必要时读 `case-execution-recovery.md`。
- 公共契约、tool、runtime、权限或 DB 变更，在 **Scale** 阶段升级到对应领域 Skill。
- 验证失败时回到 **Change / Verify** 循环；不能把错误暴露写成业务成功。
- 收尾、commit、PR、merge 是 **Close** 的扩展，不是默认步骤。

## References

- `references/completion-evidence-patterns.md`：完成状态和证据校准。
- `references/case-execution-recovery.md`：页面/Chat/MCP/多素材 case 纠偏。
- `references/execution-control-patterns.md`：计划执行、worktree、分支收尾、本地改动保护。
- `references/parallel-agent-coordination.md`：并行任务和子 agent 协作。
- `references/review-git-and-cleanup-gates.md`：cleanup、`git diff`、`git update`、提交/PR 前审查。

## Output

按实际执行面裁剪：完成内容、验证、范围自检、残余风险、建议 commit 或待审查。

## Quality Bar

- 不用测试绿或局部 smoke 代替需求验收。
- 不提交或建议提交前，必须完成 diff 边界审查和最窄有效验证。
- 没有 fresh evidence 时，只能报告事实状态和缺口。
