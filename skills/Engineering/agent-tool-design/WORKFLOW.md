---
name: agent-tool-design
description: 当用户设计 AI agent tool、MCP/server tool、function calling、tool gateway、schema、回执或权限时使用。
---

# Purpose

设计模型可调用工具，让模型容易正确调用，系统能授权、审计、验证和恢复。

# When to Use

- 设计 tool/function/MCP 工具、tool gateway、schema 或执行回执。
- 把 API 暴露给模型，或让 agent 操作某个系统。
- 需要判断模型可见工具集合、权限、scope 或执行语义。

# When NOT to Use

- 普通内部 API，不暴露给模型或 tool gateway。
- 只是在现有工具上修 bug，且不改变工具契约。

# Inputs

- 能力目标、调用主体、资源 owner、可见范围、输入输出、权限、错误模型、审计和验证场景。

# Decision Principles

- 一个工具一个清晰动作；不暴露万能执行器。
- 管理目录、模型可调用工具、runtime 内部动作不能混成一个 surface。
- Tool name 去重、授权集合和启停状态属于配置保存阶段或管理 owner；runtime/gateway 只消费已验证配置。
- 平台通过 MCP provider/gateway 调 MCP；MCP 通过平台 SDK/API、callback 或 artifact/resource refs 回到平台。
- 页面、Canvas、Chat、附件或 artifact 显示问题先确认真实消费者和 store/backend，不默认改 MCP 项目存储或 tool schema。

# Workflow

1. Capability：写清工具解决什么、不解决什么。
2. Surfaces：区分 catalog、model tool、gateway、runner、runtime loop 和 downstream provider。
3. Owners：明确 tool host、registry、gateway/policy、runner、runtime 和资源 owner。
4. Visibility：按 workspace、business、owner、preset 或 provider 关系收敛可见范围。
5. Inputs：参数贴近模型理解；服务端可推导的 scope 不让调用方传。
6. Outputs：返回 status、message、resourceRef/receipt、诊断和长任务句柄。
7. Errors：区分 validation、permission、not_found、conflict、unsupported、rate_limit、downstream_failure。
8. Safety：定义审批、dry-run、审计、观测和 eval case。

# Checklist

- schema/build 与 execute 是否闭合在同一 gateway/surface 语义下。
- 项目/调试/客户工具是否没有被注册成全局可见。
- 没真实后端能力时是否明确 unsupported，而不是 mock 成功。

# Escalation

- 新增或修改公共接口面：`skills/Engineering/interface-contract-audit/WORKFLOW.md`
- 运行 case 与能力建设边界不清：`skills/Core/task-execution-lifecycle/WORKFLOW.md`

# References

- None.
