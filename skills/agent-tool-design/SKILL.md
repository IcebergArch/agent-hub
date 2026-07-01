---
name: agent-tool-design
description: 当用户设计 AI agent 工具、MCP/server 工具、function calling、tool gateway、工具 schema、执行回执、权限或 agent workflow 工具接口时使用。
---

# Agent Tool Design

设计模型可调用工具。目标是模型容易正确调用，系统能授权、审计、验证和恢复。

## Trigger

- “设计 tool/function/MCP 工具”“工具 schema 怎么写”。
- “agent 怎么调用这个能力”“tool gateway”“执行回执”。
- “把 API 暴露给模型”“让模型操作某个系统”。

## Workflow

1. **Define Capability**：一个工具一个清晰动作；写清解决什么、不解决什么。
2. **Separate Surfaces**：管理目录、模型可调用工具、runtime/middleware 内部辅助动作不能混成一个 surface。
3. **Map Owners**：明确 tool host / MCP server、provider registry、gateway/policy、runner/scheduler、runtime loop、下游 provider 的 owner。
4. **Design Inputs**：参数贴近模型理解；必填越少越好；能由服务端授权和资源关系推导的 scope 不让调用方传。
5. **Design Outputs**：返回结构化结果，包含 status、message、resourceRef/receipt、诊断；长任务返回 task/session/receipt。
6. **Design Errors**：区分 validation、permission、not_found、conflict、unsupported、rate_limit、downstream_failure。
7. **Safety / Observability / Eval**：高风险动作需要审批、确认、dry-run 或最小权限；记录调用者、输入摘要、资源引用、结果和错误码。

## MCP Boundary

平台通过 MCP provider/gateway 调 MCP；MCP 通过平台 SDK/API、callback 或 artifact/resource refs 回到平台。共享本地路径、本机端口、脚本、runtime 内部 route 和测试 fixture 不能成为正式跨服务契约。

## Output

默认结构：Capability、Tool Contract、Input Schema、Output / Receipt、Error Model、Permission And Approval、Observability、Eval Cases。

## Quality Bar

- 不暴露万能执行器。
- schema/build 和 execute 必须闭合在同一 gateway/surface 语义下。
- 没有真实后端能力时返回明确 unsupported，不用 mock 假装成功。
- 资源 CRUD 默认走 owner service 或管理 API，不让管理台调用模型 tool runner。
