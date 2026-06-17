# Agents 工具调用中文验证文档

本文用于验证 Knowledge 导入链路中的 Markdown 解析、摘要、切分和召回。内容围绕 Agent 工具调用设计，包含若干容易检索的中文关键词。

## 工具选择原则

Agent 不应该把所有问题都交给模型凭空回答。遇到外部事实、业务状态、文件内容、数据库记录或需要执行动作的任务时，应优先考虑工具。工具描述必须说明输入字段、输出结构、失败条件和可恢复策略。

## Tool Schema

工具 schema 应保持稳定、短小、明确。字段命名推荐使用小驼峰，例如 `knowledgeBaseIds`、`topK`、`mode`。如果工具支持多种执行方式，应在枚举字段里写清楚，例如 semantic、vector、keyword。

## Handoff 与 Guardrail

当任务超出当前 Agent 能力时，可以通过 handoff 转交给更合适的 Agent。Guardrail 用于阻止不合规请求、校验工具参数、限制敏感操作。测试问题可以问：“哪一份文档提到了 handoff 和 guardrail？” 应命中本文档。

## 验证锚点

锚点短语：工具蓝图、handoff 转交、guardrail 校验、schema 稳定性、模型不应编造工具结果。
