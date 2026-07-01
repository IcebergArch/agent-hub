---
name: test-strategy
description: 当用户要求设计测试、补测试、验证方案、回归覆盖、端到端验收、评估质量门槛或防止误报时使用。
---

# Test Strategy

为变更设计最少但有效的测试和验证，证明目标行为成功，并降低误报。

## Trigger

- “怎么测”“补测试”“验证方案”“回归测试”“测试 checklist”。
- “端到端验收”“防止误报”“怎么算通过”。

## Workflow

1. **Identify Risk**：行为变化、共享接口、数据迁移、权限、安全、并发、UI workflow、外部依赖；风险越高，越靠近真实链路。
2. **Define Acceptance Signals**：区分入口可打开、请求到达、下游支持、业务成功、结果可见。
3. **Choose Timing**：bugfix、核心逻辑、接口契约和行为变化优先先写失败测试或验收脚本。已先写实现时，用临时回退、关闭修复或等价 proof 证明测试能抓住问题。
4. **Choose Levels**：unit、integration、E2E/smoke、manual；按风险从窄到宽。
5. **Add Negative Cases**：权限、缺数据、非法输入、超时、外部失败、旧入口、unsupported/fallback/mock。
6. **Keep Data Deterministic**：避免时间、网络、随机顺序或生产数据，除非它们就是测试目标。
7. **Use Testability As Design Feedback**：测试过难、setup 过大或必须 mock 多层时，先怀疑接口和依赖边界。

## References

- `references/testing-anti-patterns.md`：mock、测试先后顺序、测试专用生产 API、复杂 fixture、RED 误报。

## Output

默认结构：Acceptance Signals、Test Matrix、Negative Cases、Commands / Manual Steps、Gaps。

## Quality Bar

- “通过”只能用于目标业务结果真的成功。
- RED 必须来自目标行为缺失；语法、导入、fixture、mock、命令 scope、0 tests/skipped 只是测试表达问题。
- TDD 例外必须有类别和等价验证；“太简单”“先手动测”“之后补测试”不是有效豁免。
