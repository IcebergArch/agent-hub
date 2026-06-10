---
name: code-review
description: 当用户要求 review、审查 diff、看 PR、检查改动风险、找 bug、评估测试缺口或提交前检查时使用。
---

# Code Review

用这个技能做代码审查。优先找真实 bug、行为回归、接口风险和测试缺口，而不是泛泛夸代码。

## Trigger

当用户说以下任一类需求时使用：

- “review 一下”“看下这个 diff/PR”“帮我检查改动”
- “提交前看一下”“有没有风险”“代码审查”
- “为什么这个改动可能不对”

## Workflow

1. **Define Review Scope**
   - 确认审查对象：当前 diff、staged diff、某个 PR、某个目录或某次提交。
   - 如果用户只说 `git diff`，只读审查，不改文件、不暂存、不提交。

2. **Collect Diff**
   - 查看状态、文件清单、stat、staged/unstaged。
   - 对高风险文件看完整 diff，不只看文件名。

3. **Understand Behavior**
   - 找入口、调用者、测试和文档。
   - 对 API、schema、公共组件、配置、运行时入口，搜索真实消费者。

4. **Review For Risk**
   - 行为回归、边界条件、错误处理、权限/租户/数据隔离、并发、资源释放。
   - 生成产物是否来自事实源。
   - mock/fallback 是否掩盖真实链路。

5. **Check Tests**
   - 是否覆盖成功路径、失败路径、边界输入、旧行为防回归。
   - UI 或端到端变更是否有 smoke 信号。

6. **Report Findings First**
   - 发现问题时按严重度排序。
   - 每条问题包含文件/行、影响、复现或推理路径、建议修复方向。
   - 没有发现问题时明确说没有发现阻塞项，并说明残余风险。

## Output

默认输出：

```markdown
## Findings

## Open Questions

## Test Gaps / Residual Risk

## Change Summary
```

## Quality Bar

- 不把风格建议放在真正 bug 前面。
- 不用“可能有问题”结束；要说明为什么、影响什么、怎么验证。
- 没看过的文件或没跑过的测试必须诚实说明。
