# Tool Connectivity Patterns

日期：2026-06-24
用途：作为 `agent-team-router` 的按需参考；用于 MCP、HTTP API、terminal/CLI、MCP server、worker/job 等工具连通架构或协议设计任务。

只有当任务命中工具 schema、执行回执、MCP server、HTTP API、terminal、worker/job、取消/恢复、审计或 gateway 语义时读取本文件。普通实现、UI 调整和路径明确的小修不需要加载。

## MCP Tool Connectivity View

MCP 可以作为统一的工具联通架构，把不同能力包装成模型可发现、可授权、可调用的工具面，但它不替代 runtime、权限网关、任务调度和审计系统。

| Tool Type | 接入方式 | 关键约束 |
| --- | --- | --- |
| HTTP API | 封装为 tool，声明输入 schema、鉴权方式、超时、重试和错误映射 | 不把任意 URL 暴露给模型；按业务能力暴露稳定动作 |
| terminal/CLI | 封装为本地或受控远程执行工具，显式声明命令、工作目录、环境变量、输出解析和超时 | 默认高风险；需要 sandbox、allowlist、审批、stdout/stderr 分离和可取消语义 |
| MCP Server | 作为上游 MCP server 接入，或通过 gateway 聚合后再暴露 curated tools/resources/prompts | 不盲目透传全部工具；需要命名空间、权限收敛和结果归一化 |
| Worker/Job | 封装为异步 task/session，返回 receipt、进度、artifact refs、cancel/resume 入口 | 长任务不能伪装成普通同步 tool call；必须可恢复、可审计 |

MCP 本地模拟遵循最小可用原则：如果目标是模拟固定三台 MCP server，就直接显式启动三台并暴露三组 tools；不要先做通用多 server hub。只保留后续可拆服务需要的边界字段，例如 `server_id`、`tool_name`、`call_mode`、`receipt/task`，不提前实现完整控制面。
