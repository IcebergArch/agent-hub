---
name: prompt-improvement
description: 当用户要求优化 prompt、system/developer instructions、agent 指令、提示词模板、输出格式、few-shot 示例或减少幻觉/跑偏时使用。
---

# Prompt Improvement

用这个技能优化提示词和 agent 指令。目标是让模型更稳定地完成真实任务，而不是把 prompt 写得更长。

## Trigger

当用户说以下任一类需求时使用：

- “优化这个 prompt/提示词/系统指令”“帮我写 agent instructions”
- “模型老跑偏/幻觉/格式不稳定”“输出不符合要求”
- “给这个任务写一个可复用 prompt 模板”

## Workflow

1. **Identify Failure Mode**
   - 是目标不清、上下文缺失、约束冲突、输出格式不稳、工具误用、幻觉，还是缺少验证。
   - 如果有失败样例，先读失败样例。

2. **Define Task Contract**
   - 写清角色、目标、输入、输出、边界、禁止事项和成功标准。
   - 把稳定规则放前面，把动态用户内容放后面。

3. **Add Context**
   - 只加入完成任务必要的背景。
   - 对长上下文使用分段标题和明确优先级。
   - 不把一次性细节写入长期系统指令。

4. **Constrain Output**
   - 给出结构化输出格式。
   - 对必须机器解析的内容使用 JSON schema 或严格字段。
   - 对人读文档使用 Markdown 结构和示例。

5. **Use Examples Carefully**
   - 加 1 到 3 个高质量例子，覆盖最常见和最容易错的情况。
   - 例子要短，避免和真实任务冲突。

6. **Add Verification**
   - 要求模型在最终前检查关键约束。
   - 对事实性任务要求来源、日期或不确定性标注。
   - 对工具任务要求执行结果和错误处理。

## Output

默认输出：

```markdown
## Failure Mode

## Improved Prompt

## Why It Works

## Test Cases
```

## Quality Bar

- 不用堆规则替代清晰任务合同。
- 示例和输出格式要服务真实失败点。
- 对高风险任务不靠 prompt 单点防护，要建议工具约束、权限或 eval。
