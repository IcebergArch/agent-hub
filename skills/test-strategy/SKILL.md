---
name: test-strategy
description: 当用户要求设计测试、补测试、验证方案、回归覆盖、端到端验收、评估质量门槛或防止误报时使用。
---

# Test Strategy

用这个技能为变更设计测试和验证。目标是用最少但有效的测试证明目标行为成功，并防止常见误报。

## Trigger

当用户说以下任一类需求时使用：

- “怎么测”“补一下测试”“验证方案”“回归测试”
- “端到端验收”“测试 checklist”“防止误报”
- “这个功能算通过的标准是什么”

## Workflow

1. **Identify Risk**
   - 行为变化、共享接口、数据迁移、权限、安全、并发、UI workflow、外部依赖。
   - 风险越高，测试越靠近真实链路。

2. **Define Acceptance Signals**
   - 写清最小成功信号。
   - 区分“入口可打开”“请求到达”“下游支持”“业务成功”“结果可见”。

3. **Choose Test Levels**
   - Unit：纯逻辑、转换、校验、边界。
   - Integration：数据库、API、工具 gateway、service 组合。
   - E2E/Smoke：关键用户路径、真实路由、真实上下文。
   - Manual：只有自动化成本过高或工具不可用时使用。

4. **Add Negative Tests**
   - 缺权限、缺数据、非法输入、超时、外部失败、旧入口、unsupported/fallback/mock。
   - 防止错误态被显示成成功。

5. **Keep Data Deterministic**
   - 使用 fixture 或工厂函数。
   - 不依赖当前时间、网络、随机顺序或生产数据，除非测试目标就是这些。

6. **Run Narrow Then Broad**
   - 先跑最小相关测试。
   - 再按风险跑类型检查、lint、集成测试或端到端 smoke。

## Output

默认输出：

```markdown
## Acceptance Signals

## Test Matrix

## Negative Cases

## Commands / Manual Steps

## Gaps
```

## Quality Bar

- “通过”只能用于目标业务结果真的成功。
- 测试计划要能被执行，不只列测试类型。
- 对 agent/tool/runtime 链路必须明确 mock 与真实链路的边界。
