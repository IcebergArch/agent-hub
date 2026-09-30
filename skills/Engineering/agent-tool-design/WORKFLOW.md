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
- 调用目标未知或未提供 canonical ref 时，先用现有 discovery/list/search 能力在当前已认证 scope 内取得真实授权候选，再按用户已给约束选择；候选仍不唯一时请求用户确认。名称、别名或 source hint 只作查询提示，不能要求用户填写 provider 底层 ID。确有能力缺口时，只在 owning adapter 补通用发现能力，不向 runtime 或单个业务下沉特例。
- Tool name 去重、授权集合和启停状态属于配置保存阶段或管理 owner；runtime/gateway 只消费已验证配置。
- 同步外部 provider 工具时，先区分 provider 拥有的定义字段与平台拥有的治理状态；刷新名称、schema、描述等远端投影不得重置本地启停、授权、可见范围或人工策略。只有契约明确规定某状态也由 provider 权威管理时，才允许随完整投影替换。
- 平台通过 MCP provider/gateway 调 MCP；MCP 通过平台 SDK/API、callback 或 artifact/resource refs 回到平台。
- 页面、Canvas、Chat、附件或 artifact 显示问题先确认真实消费者和 store/backend，不默认改 MCP 项目存储或 tool schema。
- 大结果或敏感正文只保留一个 canonical owner；按内容类型、单项大小、同轮/下一请求的模型可见聚合量、安全性、后端能力和消费预算做确定性 `inline / bounded projection / materialize / reject`。低于单项阈值的多个结果仍可能共同越过请求预算，不能只逐项准入。只有 owning backend 写入成功，或 owning provider 返回可校验 canonical ref 后，才能声明资源已托管；外部 URL、Base64 或普通字符串不因来源或外形被自动抓取、托管或升格为平台资源。显式的 fetch/import 动作可在完成授权、校验和容量门禁后托管外部内容；任何投影、摘要或分页都必须显式暴露不完整性和恢复 canonical 内容的入口，不伪装全量成功。
- 资源只能通过 tool result 中的显式 typed resource/resource-link 通道或已冻结的结构化字段被识别，不递归扫描任意文本猜 URI。URI/ref 只是定位符，不是授权或内容已加载证明；每次 read/mutate/reveal 都在当前已认证 scope 重新校验。
- Tool 执行契约与 model-facing 契约必须分离：canonical schema 约束真实执行，Provider adapter 只生成该模型协议可表达的投影，并在所有 middleware 生效后对实际待发送 payload 做 final conformance。若 canonical schema 已被证明完全属于目标 Provider 子集且中间层不改写，投影可以是 identity，但模型返回参数仍要回到 canonical schema、policy 和 authorization 校验；无法证明安全投影时明确拒绝或标记 unsupported，不能删除约束后假装等价。
- Tool 调用错误按确定性阶段归因，例如 JSON decode、envelope/protocol、model projection、canonical validation/policy、Provider request 与 downstream execution；不要用 Provider 文案正则或最终预算耗尽覆盖首因。可纠正错误使用 `tool + issue kind + normalized input` 等稳定 fingerprint 有界重试，系统注入或投影产生的无效参数不得伪装成模型错误。
- Runtime tool 并发是显式能力，不从名称、前缀或 provider 类型推断；默认串行，只有工具级声明和参数级 eligibility 同时证明无共享可变状态、顺序依赖、interrupt/async 或未界定副作用时，才进入有界并发。
- Tool 自身固定且跨环境一致的协议或实现边界归 owning tool/adapter，用代码常量或默认值表达；只有存在已验证的环境或运营差异、明确配置 owner、安全默认值以及兼容与回滚语义时，才提升为外部配置，不能为复制固定常量持续扩张系统 config。
- 同步执行的 deadline、取消、重试与并发上限等跨工具调度策略归 runtime/gateway，并按调用类别、显式能力或统一 policy 一致执行，不按 tool name、前缀或单个工具身份写特殊分支。下游 Provider 的协议级 deadline 可以留在 owning adapter，但不能借此改写系统调度层的统一终止语义。
- 授权后的 tool/Skill catalog 优先绑定到最小稳定生命周期形成只读快照，供 disclosure、schema 与 execute 共用；metadata 批量解析但不缓存大正文，不跨租户或跨授权生命周期共享。若运行中必须即时撤权，必须有 revision/fence 或重新建快照，不能把“不可变”解释为忽略撤权。

# Workflow

1. Capability：写清工具解决什么、不解决什么。
2. Surfaces：区分 catalog、model tool、gateway、runner、runtime loop 和 downstream provider。
3. Owners：明确 tool host、registry、gateway/policy、runner、runtime 和资源 owner。
4. Visibility：按 workspace、business、owner、preset 或 provider 关系收敛可见范围。
5. Discovery：目标未知时先盘点并调用现有 discovery/list/search surface，按用户约束解析候选并在歧义时请求选择；仅在无法返回授权候选且有真实 consumer 时，冻结 public surface、application owner、data/source owner 和 consumer，再把最小通用能力补到 owning adapter。
6. Inputs：参数贴近模型理解；服务端可推导的 scope 不让调用方传。冻结 canonical schema、各 Provider capability、model projection 和所有运行时注入参数的 owner；多分支工具用同一可证明的 branch resolver 约束投影与执行策略。
7. Outputs：先按内容类型、单项与聚合预算决定 inline/projection/materialize/reject，再通过显式 typed content 返回 status、message、resourceRef/receipt、诊断和长任务句柄；记录原始规模、模型可见规模、同轮聚合量、决策理由和失败阶段。
8. Errors：区分 decode/protocol/projection、validation、permission、not_found、conflict、unsupported、rate_limit、provider_request 和 downstream_failure；定义可纠正性、fingerprint、重试上限和首因保留。
9. Runtime Semantics：若允许并发，冻结串行屏障、call-scoped 状态、有界 worker、原调用顺序回执、部分失败、deadline、父取消和 worker 收敛；若允许重试，冻结幂等身份和恢复语义。
10. Safety：定义审批、dry-run、审计、观测和 eval case。

# Checklist

- schema/build 与 execute 是否闭合在同一 gateway/surface 语义下。
- canonical schema、Provider 投影、最终发送 payload 与执行校验是否形成可追踪双契约；final conformance 是否检查实际发送内容，歧义分支或不支持关键字是否安全退出。
- 目标未知时是否先复用现有发现能力并返回当前 scope 内的 canonical ref 与可辨识元数据，歧义时是否请求选择；新增 surface 是否有缺口证据、真实 consumer 和正确 owning adapter。
- provider reconcile 是否只覆盖远端事实，并保留现有本地治理状态；新发现、远端删除和身份变化是否分别有明确策略与回归测试。
- 项目/调试/客户工具是否没有被注册成全局可见。
- 没真实后端能力时是否明确 unsupported，而不是 mock 成功。
- 结果准入是否确定、有界且可审计；是否同时覆盖单项与同轮/下一请求聚合预算；大结果投影是否仍有唯一正文 owner、稳定引用、同 scope 恢复与生命周期清理；引用失败是否不会伪造摘要或成功。
- 错误是否按真实失败阶段归因并保留首因；相同可纠正错误是否有界，Runtime/adapter 注入失败是否不会错误要求模型重试。
- 资源是否只从 typed channel 识别，且每次操作都将 URI/ref 重新绑定到当前 scope 的真实 backend 能力与权限；普通文本和外部 URL 是否不会被静默托管或抓取。
- 并发候选是否默认串行、显式 opt-in、有界且保持 call/result 顺序与取消收敛；未证明安全的参数是否仍串行。
- 固定协议/实现边界是否留在 owning tool/adapter 且未无证据配置化；跨工具调度是否由 runtime/gateway 统一执行且没有 tool-name 特例；Provider 自有边界是否未冒充系统调度策略。
- catalog 快照是否绑定授权生命周期并有撤权/fence 语义，没有进程级跨 scope 脏缓存。

# Escalation

- 新增或修改公共接口面：`skills/Engineering/interface-contract-audit/WORKFLOW.md`
- 运行 case 与能力建设边界不清：`skills/Core/task-execution-lifecycle/WORKFLOW.md`

# References

- [Microsoft Azure Architecture Center — Claim-Check pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/claim-check)（大载荷正文与引用分离、访问控制和生命周期取舍；访问 2026-08-29）
- [Model Context Protocol 2026-07-28 schema](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/schema/2026-07-28/schema.ts)（`CallToolResult.content` 使用 typed `ContentBlock`，包含 `ResourceLink`；访问 2026-09-05）
- [Model Context Protocol — Resources](https://modelcontextprotocol.io/specification/2025-06-18/server/resources)（resource URI 需校验，敏感资源需访问控制且操作前复核权限；访问 2026-09-05）
- [OpenAI Function Calling](https://platform.openai.com/docs/guides/function-calling)（strict tool schema 只支持 JSON Schema 子集；访问 2026-09-12）
- [Gemini Function Calling](https://ai.google.dev/gemini-api/docs/function-calling)（Function Declaration 使用 OpenAPI schema 的选择性子集，大或深层 schema 可能被拒绝；访问 2026-09-12）
- [Anthropic Strict Tool Use](https://platform.claude.com/docs/en/agents-and-tools/tool-use/strict-tool-use)（strict input schema 经 Provider grammar 编译并受其 JSON Schema 子集限制；访问 2026-09-12）
