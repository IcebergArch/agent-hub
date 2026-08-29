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
- 大结果或敏感正文只保留一个 canonical owner；模型视图、事件、队列和追踪面优先传有界 receipt/resourceRef。只有引用在同一权限边界可恢复且消费语义已满足时才允许投影或缩短，派生视图不得变成第二事实源。
- Runtime tool 并发是显式能力，不从名称、前缀或 provider 类型推断；默认串行，只有工具级声明和参数级 eligibility 同时证明无共享可变状态、顺序依赖、interrupt/async 或未界定副作用时，才进入有界并发。
- 授权后的 tool/Skill catalog 优先绑定到最小稳定生命周期形成只读快照，供 disclosure、schema 与 execute 共用；metadata 批量解析但不缓存大正文，不跨租户或跨授权生命周期共享。若运行中必须即时撤权，必须有 revision/fence 或重新建快照，不能把“不可变”解释为忽略撤权。

# Workflow

1. Capability：写清工具解决什么、不解决什么。
2. Surfaces：区分 catalog、model tool、gateway、runner、runtime loop 和 downstream provider。
3. Owners：明确 tool host、registry、gateway/policy、runner、runtime 和资源 owner。
4. Visibility：按 workspace、business、owner、preset 或 provider 关系收敛可见范围。
5. Inputs：参数贴近模型理解；服务端可推导的 scope 不让调用方传。
6. Outputs：返回 status、message、resourceRef/receipt、诊断和长任务句柄。
7. Errors：区分 validation、permission、not_found、conflict、unsupported、rate_limit、downstream_failure。
8. Runtime Semantics：若允许并发，冻结串行屏障、call-scoped 状态、有界 worker、原调用顺序回执、部分失败、deadline、父取消和 worker 收敛；若允许重试，冻结幂等身份和恢复语义。
9. Safety：定义审批、dry-run、审计、观测和 eval case。

# Checklist

- schema/build 与 execute 是否闭合在同一 gateway/surface 语义下。
- 项目/调试/客户工具是否没有被注册成全局可见。
- 没真实后端能力时是否明确 unsupported，而不是 mock 成功。
- 大结果投影是否仍有唯一正文 owner、稳定引用、同 scope 恢复与生命周期清理；引用失败是否不会伪造摘要或成功。
- 并发候选是否默认串行、显式 opt-in、有界且保持 call/result 顺序与取消收敛；未证明安全的参数是否仍串行。
- catalog 快照是否绑定授权生命周期并有撤权/fence 语义，没有进程级跨 scope 脏缓存。

# Escalation

- 新增或修改公共接口面：`skills/Engineering/interface-contract-audit/WORKFLOW.md`
- 运行 case 与能力建设边界不清：`skills/Core/task-execution-lifecycle/WORKFLOW.md`

# References

- [Microsoft Azure Architecture Center — Claim-Check pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/claim-check)（大载荷正文与引用分离、访问控制和生命周期取舍；访问 2026-08-29）
