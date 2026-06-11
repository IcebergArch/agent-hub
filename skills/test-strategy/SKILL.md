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

3. **Choose Test Timing**
   - 对 bugfix、核心逻辑、接口契约和行为变化，优先写回归测试或验收脚本，先确认它在修复前失败，再实现修复。
   - RED 失败原因必须对准目标行为缺失；如果失败来自导入、语法、fixture、mock、环境或错误命令，先修测试表达，不能进入 GREEN。
   - 若认为不适合测试先行，先归类为文档、配置、生成代码、低风险样式、一次性探索或工具不可用，并写出等价验证信号；行为变化和 bugfix 不能只用“太小/太急/手动测过”豁免。
   - 已经先写了实现时，也要用“临时回退/关闭修复 -> 测试失败 -> 恢复修复 -> 测试通过”证明测试能抓住问题。
   - 如果实现代码已经先于测试写出，先把它当作探索结果而不是完成实现：记录学到的不变量，隔离或临时回退代码，再用失败测试重新驱动正式实现；不要边看已有实现边补一个会立即变绿的测试。
   - 对声称“不改行为”的重构，先跑现有相关测试或补 characterization test 捕获当前行为；重构后同一证据必须保持通过，不能在 refactor 阶段顺手改功能。
   - 文档、配置、低风险样式或没有测试框架的任务，说明采用的等价验证信号。

4. **Choose Test Levels**
   - Unit：纯逻辑、转换、校验、边界。
   - Integration：数据库、API、工具 gateway、service 组合。
   - E2E/Smoke：关键用户路径、真实路由、真实上下文。
   - Manual：只有自动化成本过高或工具不可用时使用。

5. **Add Negative Tests**
   - 缺权限、缺数据、非法输入、超时、外部失败、旧入口、unsupported/fallback/mock。
   - 防止错误态被显示成成功。

6. **Keep Data Deterministic**
   - 使用 fixture 或工厂函数。
   - 不依赖当前时间、网络、随机顺序或生产数据，除非测试目标就是这些。

7. **Mock Discipline**
   - 测真实行为，不断言 mock 自己存在或被调用得“看起来对”。
   - mock 前先确认真实依赖的副作用；mock 应尽量靠近慢/外部边界，保留被测路径需要的真实行为。
   - mock 数据结构要与真实 schema 足够一致，避免只填当前断言字段造成集成误报。

8. **Use Testability As Design Feedback**
   - 如果测试很难写、setup 过大、必须 mock 一切，先怀疑接口、依赖边界或行为切分不清。
   - 先写期望使用方式和断言，再调整设计；不要用更复杂的 mock 世界掩盖难用 API。

9. **Run Narrow Then Broad**
   - 先跑最小相关测试。
   - 再按风险跑类型检查、lint、集成测试或端到端 smoke。

## References

- `references/testing-anti-patterns.md`：当测试涉及 mock、测试先后顺序、测试专用生产 API、复杂 fixture 或误报风险时读取。

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
- 不能用“测试通过”掩盖测试没有先失败、只测试 mock 或只验证错误态暴露的问题。
- 不能把“测试红了”直接当 RED；RED 必须证明目标行为缺失，而不是测试环境、fixture 或 mock 坏了。
- TDD 例外必须有类别和等价验证；“太简单”“先手动测”“之后补测试”“这次特殊”都不是有效豁免理由。
- 探索代码可以帮助理解问题，但不能直接升级为正式实现；正式实现必须有失败测试、临时回退 proof 或用户明确接受的等价验证。
