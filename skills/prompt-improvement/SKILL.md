---
name: prompt-improvement
description: 当用户要求优化 prompt、system/developer instructions、agent 指令、提示词模板、输出格式、few-shot 示例或减少幻觉/跑偏时使用。
---

# Prompt Improvement

优化 prompt 和 agent 指令。目标是提高稳定性，不是写得更长。

## Trigger

- “优化 prompt/提示词/系统指令”“写 agent instructions”。
- 模型跑偏、幻觉、格式不稳、工具误用。
- 需要可复用 prompt 模板或 few-shot。

## Workflow

1. **Failure Mode**：判断是目标不清、上下文缺失、约束冲突、格式不稳、工具误用、幻觉还是缺验证。
2. **Task Contract**：写清角色、目标、输入、输出、边界、禁止事项和成功标准。
3. **Context Design**：稳定规则在前，动态用户内容在后；必要上下文给够，避免大段无关背景。
4. **Output Shape**：明确结构、字段、长度、语气和必须/禁止内容。
5. **Tool / Evidence Rules**：需要工具时写触发条件、输入约束、失败处理和验证方式。
6. **Test Cases**：用 2-4 个代表性输入检查是否减少跑偏。

## Output

默认结构：Failure Mode、Improved Prompt、Why It Works、Test Cases。

## Quality Bar

- 不用更长掩盖目标不清。
- 不把一次性上下文写成长期规则。
- prompt 必须能被样例验证。
