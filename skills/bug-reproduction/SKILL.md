---
name: bug-reproduction
description: 当用户报告 bug、报错、测试失败、flaky/timeout/hang、线上异常、行为不符合预期，或要求定位 root cause 并修复时使用。
---

# Bug Reproduction

Bug 处理 Skill。先确认症状，再定位根因，最后最小修复和回归验证。

## Trigger

- 报错、测试失败、页面不对、接口返回错、行为异常。
- flaky、timeout、hang、CI 偶现失败。
- “debug”“定位原因”“修一下 bug”“为什么会这样”。

## Workflow

1. **Capture Symptom**：记录错误、触发步骤、输入、环境、版本、最近改动。
2. **Reproduce**：运行最小命令、单测、页面路径或 API 请求；不能复现时说明缺口和替代证据。
3. **Trace Cause**：沿入口、调用链、状态、数据源、配置、并发/时序和外部依赖排查。
4. **Test Hypothesis**：一次验证一个假设；被证伪的假设不能继续支撑修复。
5. **Fix Minimally**：修根因，不扩大 scope，不用 sleep / fallback / mock 掩盖问题。
6. **Verify**：跑能证明 bug 修复的窄测试或 smoke；必要时补回归测试。

## References

- `references/debugging-patterns.md`：flaky、timeout、polling、根因账本、参考实现比较。

## Output

默认结构：Symptom、Root Cause、Fix、Verification、Remaining Risk。

## Quality Bar

- 未定位根因时写 root cause unresolved，不编造“很可能”。
- 修复证据必须来自当前代码和当前命令输出。
- 测试失败若来自测试表达、fixture 或环境，不当作业务 RED。
