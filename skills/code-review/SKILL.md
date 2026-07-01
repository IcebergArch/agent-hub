---
name: code-review
description: 当用户要求 review、审查 diff/PR、检查改动风险、找 bug、评估测试缺口、提交前检查，或任务卡住需要新视角时使用。
---

# Code Review

代码审查 Skill。优先找真实 bug、行为回归、接口风险和测试缺口；总结和风格建议靠后。

## Trigger

- “review 一下”“看下 diff/PR”“提交前检查”“有没有风险”。
- 用户只说 `git diff`：只读审查，不改文件、不暂存、不提交。
- 任务卡住、复杂 bug 修完后仍缺把握，需要换视角。

## Workflow

1. **Scope**：确认审查对象是工作区 diff、staged diff、PR、目录还是提交。
2. **Collect Evidence**：看状态、文件清单、stat、关键 diff；实现报告、PR 描述、subagent 状态和测试摘要都只是 claims。
3. **Understand Behavior**：找入口、调用者、测试、文档；共享接口、schema、配置、公共组件和 runtime 入口要搜真实消费者。
4. **Review Risk**：检查行为回归、边界条件、错误处理、权限/租户/数据隔离、并发、资源释放、生成产物来源、mock/fallback。
5. **Check Tests**：确认成功路径、失败路径、边界输入、旧行为防回归和必要 smoke。
6. **Report Findings First**：问题按严重度排序；每条包含文件/行、影响、证据路径和建议修复方向。
7. **Handle Feedback**：反馈按来源、状态、依赖和验证方式建模；人类反馈优先但仍核对影响面；外部/agent/tool 反馈先验证再采纳或反驳。

## References

- `references/review-checklists.md`：多文件实现、执行计划、subagent 产物、PR 反馈或交付前风险。

## Output

默认结构：Findings、Open Questions、Test Gaps / Residual Risk、Verdict、Change Summary。

Verdict 只能是：`blocked`、`with fixes`、`ready with risk`、`ready`、`insufficient evidence`。

## Quality Bar

- 没看过的文件或没跑过的测试必须说明。
- 有 Critical、未澄清 blocker 或证据不足时不能写 ready。
- reviewer 结论不能替代本地 diff 和验证证据。
